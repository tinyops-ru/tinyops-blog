+++
title = "Как настроить chroot для sftp в CentOS 7"
description = "Ограничить доступ через SFTP домашним каталогом пользователя"
date = 2020-10-12
updated = 2023-07-27

[taxonomies]
tags = ["linux", "security", "chroot", "sftp"]
categories = ["centos", "linux", "security"]
+++

В современных дистрибутивах Linux очень легко дать доступ пользователю через SFTP.
Для этого достаточно создать пользователя. По умолчанию пользователь сможет перемещаться 
по всем каталогам файловых систем к которым имеет доступ на чтение.

К счастью, `sshd` позволяет указать корневой каталог для пользователя (механизм chroot).

Дальнейшая настройка будет актуальна для CentOS 7 и OpenSSH 7.4, но скорей всего сработает и для других версий.

В качестве корневого каталога будет путь `/sftp/bob`.

### Настраиваем службу sshd

Добавляем в конец конфигурационного файла sshd `/etc/ssh/sshd_config` следующий текст (комментарии можно не добавлять):

```
Match User bob
 # Запрещаем 
 X11Forwarding no
 # Запрещаем проксирование tcp-трафика
 AllowTcpForwarding no
 # Мы будем использовать только подсистему sftp
 ForceCommand internal-sftp
 # Корневой каталог для пользователя
 ChrootDirectory /sftp/bob
```

Проверяем что конфигурация без ошибок:

```shell
sshd -t
```

Если ошибок нет, то ответа от команды не последует. Затем рестартуем службу:

```shell
systemctl restart sshd
```

### Устанавливаем корректные права

У механизма chroot есть требование для владельцев по пути к корневому каталогу:

>**ChrootDirectory**  
Specifies the pathname of a directory to chroot(2) to after authentication. All components of the pathname must 
>be root-owned directories that are not writable by any other user or group. After the chroot, sshd(8) 
>changes the working directory to the user's home directory.

Владельцем на пути до корневого каталога (включительно) должен быть `root`.

Поэтому делаем так:

```shell script
chown -R root: /vfs
```

Если с правами что-то не так то в файле `/var/log/secure` будет ошибка `fatal: bad ownership or modes for chroot directory`.

### Тестируем

```shell script
$ sftp bob@your-server

Connected to your-server.
sftp> ls
lost+found  test
sftp> pwd
Remote working directory: /
```

Всё работает.
