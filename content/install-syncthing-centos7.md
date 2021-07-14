+++
title = "Установка Syncthing для CentOS 7"
description = "Рассказ про очень быструю утилиту для поиска файлов в Windows. Альтернатива стандартному механизму поиска."
date = 2021-05-14
updated = 2021-07-14

[taxonomies]
tags = ["syncthing", "centos", "awesomeware"]
categories = ["awesomeware", "syncthing", "linux"]
+++

![Логотип приложения Syncthing](/images/awesomeware/syncthing/syncthing-logo.png "Логотип приложения Syncthing")

Syncthing — это приложение для синхронизации файлов между устройствами. Авторы предпочитают дистрибутивы аля Debian\Ubuntu, 
поэтому для большинства остальных ОС требуется ручная установка.

### 1. Скачиваем дистрибутив 

Скачиваем свежую версию с GitHub [https://github.com/syncthing/syncthing/releases/latest](https://github.com/syncthing/syncthing/releases/latest)

Файл будет выглядеть подобным образом `syncthing-linux-arm64-v1.16.1.tar.gz`.   

### 2. Распаковываем

```bash
tar xvzf syncthing-linux-arm64-v1.16.1.tar.gz
cd syncthing-linux-arm64-v1.16.1
```

Будет примерно такое содержимое (лишнее убрал):

```bash
├── etc
│   ├── linux-systemd
│   │   ├── README.md
│   │   ├── system
│   │   │   ├── syncthing-resume.service
│   │   │   └── syncthing@.service  // скрипт для установки от имени пользователя
│   │   └── user
│   │       └── syncthing.service // скрипт для установки от имени системы
└── syncthing
```
В архиве присутствует два вида конфигурации:
- Системная служба
- Пользовательская служба

В первом случае Syncthing будет запускаться сразу при загрузке операционной системы и 
работать независимо от сессии пользователя.

Пользовательская служба ограничена активной сессией (ssh или графической). Например, пользователь зашёл 
в графический интерфейс (Gnome, KDE и т. д.) и запустился Syncthing.

В первом варианте данные будут синхронизированы быстрей.

### 3. Устанавливаем исполняемый файл

```bash
sudo cp syncthing /usr/local/bin/syncthing
sudo chmod +x /usr/local/bin/syncthing
```

### 4. Устанавливаем службу

На примере пользователя `eugene`.

Копируем файлы службы из дистрибутива:
```bash
cp etc/linux-systemd/system/*.service /etc/systemd/system/
```

Разрешаем автозапуск при загрузке ОС и запускаем службу:

```bash
systemctl enable syncthing@myuser.service
systemctl start syncthing@myuser.service
```

### 5. Проверяем работу Syncthing

Заходим в Web-интерфейс `http://имя-сервера:8384/`.

## Другие статьи на тему

- [Запуск Syncthing в виде службы Windows](/syncthing-as-windows-service/)
- Статьи про другие приложения с тэгом [#awesomeware](/tags/awesomeware/)
