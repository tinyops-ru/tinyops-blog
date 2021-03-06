+++
title = "Ошибка «Too many open files» в Linux"
description = "Всё что необходимо знать об ошибке «Too many open files»."
date = 2020-06-02
updated = 2020-06-02

[taxonomies]
tags = ["linux", "pam", "kernel"]
categories = ["troubleshooting", "linux"]
+++

Ошибка «Too many open files» означает, что процесс исчерпал доступные для него ресурсы по максимальному количеству
открытых файлов. Простыми словами — процессу было разрешено открыть максимум 100 файлов, а он попытался открыть 101-й.

Далее — разберёмся как работает это ограничение в операционных системах Linux. Все пути будут указаны для CentOS\RHEL-образных систем.

## Ограничения ядра Linux

Прежде всего ограничение на количество открытых файлов устанавливает ядро операционной системы. Посмотреть его можно
вот так:

```shell script
[root@dracula ~]# sysctl fs.file-max
fs.file-max = 3000000
```

Это суммарное значение на всю операционную систему. Чтобы посмотреть сколько сейчас открыто файлов достаточно 
прочитать файл `/proc/sys/fs/file-nr`:

```shell script
$ cat /proc/sys/fs/file-nr
1154    133     3000000
```

- `1154` — текущее значение открытых файлов.
- `133` — сколько из открытых файлов не используются.
- `3000000` — сколько разрешено открывать файлов.

## PAM

[PAM](http://www.linux-pam.org/Linux-PAM-html/sag-introduction.html) (Pluggable Authentication Modules for Linux) — 
это набор динамически-подключаемых библиотек для аутентификации пользователей.

Конфигурация для PAM хранится в следующих каталогах и файлах:

- `/etc/security/limits.conf`
- `/etc/security/limits.d`
- `/etc/pam.d`

Формат записи такой:

```shell script
nginx           soft   nofile 50000
nginx           hard   nofile 50000
```

Существует два вида ограничений: `soft` (мягкий) и `hard` (жесткий). Soft-ограничения носят рекомендательный характер, 
в отличие от hard-ограничений. Процесс пользователя который превысил допустимое количество открытых файлов (`nofile`) по hard-ограничению
не сможет открывать новые файлы, до тех пор пока не закроет как минимум один из ранее открытых.

## Процесс, ядро и PAM

Все что описано выше показано на схеме:

![alt text](/images/linux/too-many-open-files.png "Ядро, PAM и процесс")

Стартует процесс, при каждой попытке открыть файл ядро операционной системы проверяет не исчерпан ли общий лимит. 
Далее — если процесс поддерживает PAM, делается вызов через библиотеку, не исчерпались ли лимиты.

Если процесс не поддерживает PAM, какие бы лимиты ему не установили в конфигурационных файлах, он будет их игнорировать.

## Ошибка «Too many open files»

Например, мы получили такую ошибку для `nginx`. Nginx поддерживает PAM, поэтому на него влияют настройки 
в `/etc/security/limits.conf` и упомянутых выше файлах.

Смотрим сколько пользователю `nginx` разрешено:

```shell script
$ sudo -u nginx bash -c 'ulimit -n'

1024
```

Видимо что всего лишь `1024`, для нагруженного сервера это может быть недостаточным. Добавляем в `/etc/security/limits.conf` строчки:

```
nginx           soft   nofile 10000
nginx           hard   nofile 10000
``` 

Тем самым разрешая лимит до 10000 файлов.

В старых ядрах Linux значение `fs.file-max` может быть равно 10000. Если требуется дать `nginx` больше 10000 файлов, 
то необходимо добавить в `/etc/sysctl.conf` значение с запасом:

```
fs.file-max = 100000
```

Операционная система перечитает пераметр при следующей загрузке. Чтобы не ждать ближайшей перезагрузки и 
применить его «на лету», делаем вот так:

```shell script
$ sysctl -w fs.file-max=100000
```

В отличие от первого способа, эта настройка будет актуальна до первой перезагрузки операционной системы.

После изменения настроек в конфигурации ядра и PAM необходимо перезапустить процесс `nginx`.

## Полезные ссылки

- [https://www.kernel.org/doc/Documentation/sysctl/fs.txt](https://www.kernel.org/doc/Documentation/sysctl/fs.txt) - описание параметров ядра.
- [Статья про то как исправить ошибку «su: cannot open session: Permission denied»](/how-to-fix-su-cannot-open-session/).
