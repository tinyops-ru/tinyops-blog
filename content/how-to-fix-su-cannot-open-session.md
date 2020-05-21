+++
title = "Как исправить ошибку su: cannot open session: Permission denied для CentOS 7"
description = "Подробное описание и ход расследования по исправлению ошибки \"su: cannot open session: Permission denied\"."
date = 2020-05-21

[taxonomies]
tags = ["linux", "kernel", "centos7", "pam"]
categories = ["linux", "centos", "troubleshooting"]
+++

При попытке войти из `root` под пользователем `zero` получаем ошибку:

```sh
[root@hercules]# su - zero
Last login: Thu May 21 12:24:16 MSK 2020 on pts/0
su: cannot open session: Permission denied
```

При этом если попытаться запустить какую-нибудь команду `id` через `sudo` от имени `zero` получаем уже другую ошибку:

```sh
[root@hercules]# sudo -u zero id
sudo: pam_open_session: Permission denied
sudo: policy plugin failed session initialization
```

Проверяем не заблокирован ли пользователь:

```sh
[root@hercules]# passwd -S zero
zero PS 2019-09-12 0 99999 7 -1 (Password set, SHA512 crypt.)
```

Все окей. Проверяем не истёк ли пароль:

```sh
[root@hercules]# chage -l zero
Last password change                                    : Sep 12, 2019
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

Здесь тоже все в порядке, пароль не истёк. Смотрим лог аутентификации `/var/log/secure`:

```sh
[root@hercules]# tail /var/log/secure
May 21 15:22:46 hercules sudo: root : TTY=pts/0 ; PWD=/root ; USER=zero ; COMMAND=/bin/id
May 21 15:22:46 hercules sudo: pam_limits(sudo:session): Could not set limit for 'nofile': Operation not permitted
May 21 15:22:46 hercules sudo: root : pam_open_session: Permission denied ; TTY=pts/0 ; PWD=/root ; USER=zero ; COMMAND=/bin/id
```

Видим что есть проблема с настройкой `nofile` для модуля `pam_limits`. 

Смотрим конфиг `/etc/security/limits.conf`:
 
```text
...
zero         soft   nofile 1500000
zero         hard   nofile 1500000
...
```

Для пользователя `zero` установлен лимит на максимально количество открытых файлов — `1500000`. Допустимый диапазон 
значений для `nofile`: `1-999999`.

Меняем на любое число из разрешенного диапазона, сохраняем изменения в файле и `su` начинает работать.

```sh
[root@hercules ~]# su - zero
Last login: Thu May 21 15:29:14 MSK 2020 on pts/0
[zero@hercules ~]$
```
