+++
title = "Как настроить резервное копирование файлов в Linux с помощью Borg Backup"
description = "Настройка полного и инкрементного резервного копирования файлов в Linux. На примере CentOS 7."
date = 2020-06-16
updated = 2021-03-29

[taxonomies]
tags = ["borg", "linux", "backup", "centos"]
categories = ["linux", "backup", "centos"]
+++

[BorgBackup](https://www.borgbackup.org/) — это приложение для резервного копирования файлов 
с поддержкой [дедупликации](https://ru.wikipedia.org/wiki/%D0%94%D0%B5%D0%B4%D1%83%D0%BF%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F).

### Основные возможности

1. **Эффективное хранение**  
   Каждый файл разбивается на порции (chunks) и добавляется в репозиторий резервных копий если его там ещё не присутствовало.
2. **Скорость работы**  
   Приложение написано на Python, но критические участки реализованы на C/Python. 
   Используется кэширование и индексирование данных.
3. **Поддержка сжатия**  
   Поддерживаются четыре вида сжатия данных: lz4, zstd, zlib и lzma.
4. **Поддержка шифрования**  
   Данные можно зашифровать с помощью 256-битного AES, целостность данных проверяется с помощью HMAC-SHA256.

![alt text](/images/linux/borg/borg-site.png "Документация к Borg")


### Установка

Авторы Borg позаботились о своих пользователях, поэтому он доступен под все популярные Linux\Unix платформы.

Для CentOS 7 приложение доступно в [EPEL](https://fedoraproject.org/wiki/EPEL) репозитарии:

```shell script
yum -y install epel-release
yum -y install borgbackup
```

### Настройка архивирования

В качестве примера настроим резервное копирование для сайта [cronbox.ru](https://cronbox.ru).
Файлы сайта расположены в каталоге `/var/www/cronbox.ru/html`.

Borg использует понятие репозитория — это хранилище резервных копий. Создадим для нашего сайта отдельный репозиторий, 
без шифрования данных:

```shell script
borg init --encryption=none  /mnt/backups/cronbox.ru
```

Смотрим информацию по репозиторию:

```shell script
[root@server ~]# borg info /mnt/backups/cronbox.ru
Repository ID: df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
Location: /mnt/backups/cronbox.ru
Encrypted: No
Cache: /root/.cache/borg/df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
Security dir: /root/.config/borg/security/df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
------------------------------------------------------------------------------
                       Original size      Compressed size    Deduplicated size
All archives:                    0 B                  0 B                  0 B

                       Unique chunks         Total chunks
Chunk index:                       0                    0
```

Видим что пока пусто. Сделаем первую резервную копию:

```shell script
borg create /mnt/backups/cronbox.ru::site /var/www/cronbox.ru/html
```

Внутри каждого репозитария может храниться множество архивов. Например, мы можем отдельно архивировать папку uploads для сайта.
Поэтому в команде мы указали имя архива — `site`. Для отдельного архивирования uploads можно было указать вот так:

 ```shell script
borg create /mnt/backups/cronbox.ru::uploads /var/www/cronbox.ru/html/uploads
 ```

После создания копия смотрим изменения в репозитории:

```shell script
borg list /mnt/backups/cronbox.ru/site
site-2020-06-16T03:25:02 Tue, 2020-06-16 03:25:03 [f0ebe8acf744ea65b2f340657fe6c251d86620284aad008b55afc7dd2a65a7c3]
```

Видим что появилась резервная копия. Теперь можем посмотреть общую информацию о репозитории:

```shell script
[root@server backups-sites]# borg info /mnt/backups/cronbox.ru
Repository ID: df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
Location: /mnt/backups/cronbox.ru
Encrypted: No
Cache: /root/.cache/borg/df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
Security dir: /root/.config/borg/security/df114c32702740844c3f05b38e0192d68af0670e4a1a381214394186fd1537eb
------------------------------------------------------------------------------
                       Original size      Compressed size    Deduplicated size
All archives:              644.93 kB            277.42 kB            277.42 kB

                       Unique chunks         Total chunks
Chunk index:                     133                  133
```

Как мы видим, общий объём данных (Original size) — 644КБ, хранится на диске (Deduplicated size) — 277КБ.

### Архивирование по расписанию

```shell script
[root@server]# crontab -e

0 1 * * * borg create /mnt/backups/cronbox.ru::site /var/www/cronbox.ru/html
```

Архив будет создаваться каждый день в час ночи. Для надёжности рекомендуется настроить [мониторинг](https://blog.cronbox.ru/kak-monitorit-borg-backup/).

### Полезные ссылки

- [Официальный сайт](https://www.borgbackup.org)
- [Документация](https://borgbackup.readthedocs.io/en/stable/)
- [Как настроить мониторинг для borg](https://blog.cronbox.ru/kak-monitorit-borg-backup/)
