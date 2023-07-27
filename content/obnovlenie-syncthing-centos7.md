+++
title = "Автоматическое обновление Syncthing в CentOS 7"
description = "Рассказ про то, как настроить автоматическое обновление Syncthing в CentOS 7."
date = 2021-08-17
updated = 2023-07-27

[taxonomies]
tags = ["syncthing", "centos", "awesomeware"]
categories = ["awesomeware", "syncthing", "linux"]
+++

![Логотип приложения Syncthing](/images/awesomeware/syncthing/syncthing-logo.png "Логотип приложения Syncthing")

Проект [Syncthing](https://syncthing.net/) обновляется как минимум раз в месяц и в целях безопасности
рекомендуется устанавливать новые версии.

Для дистрибутивов, в которых приложение устанавливается пакетным менеджером (Arch, Ubuntu, Debian и т.д.),
проблем с установкой и обновлением нет.

Всем остальным дистрибутивам остаётся [установка](/install-syncthing-centos7/) и обновление вручную.

В этой статье описан механизм, который обеспечивает автоматическое обновление Syncthing.

### Алгоритм обновления

1. Скачиваем стабильную версию дистрибутива с GitHub
2. Распаковываем во временный каталог
3. Останавливаем службу Syncthing на сервере
4. Перезаписываем исполняемый файл `syncthing`
5. Запускаем службу Syncthing
6. Удаляем временный каталог

### Скрипт обновления

```bash
set -e

TARGET_USER=CHANGE-ME

# ---

TEMP_DIR=/tmp/syncthing-update
DISTRIB_FILE=syncthing-update.tar.gz
UPDATE_DIR=syncthing-update
SYNCTHING_BIN=syncthing
TARGET_FILE_PATH=/usr/local/bin/${SYNCTHING_BIN}

# Подготовка временных каталогов
mkdir -p $TEMP_DIR
cd $TEMP_DIR
mkdir -p $UPDATE_DIR

URL=$(curl --silent "https://api.github.com/repos/syncthing/syncthing/releases/latest" | jq -r '.assets[] | select(.name | contains("syncthing-linux-amd64")) | .browser_download_url ')

# Скачивание свежего дистрибутива
wget $URL -O $DISTRIB_FILE

# Распаковка
tar xvzf ${DISTRIB_FILE} -C ${UPDATE_DIR}
find ${UPDATE_DIR}/ -type f -name ${SYNCTHING_BIN}

# Поиск исполняемого файла в дистрибутиве
BINARY=$(find ${UPDATE_DIR}/ -maxdepth 2 -name ${SYNCTHING_BIN})

# Остановка службы
systemctl stop syncthing@${TARGET_USER}

# Обновление исполняемого файла
cp -f ${BINARY} ${TARGET_FILE_PATH}
chmod +x ${TARGET_FILE_PATH}

# Запуск службы
systemctl start syncthing@${TARGET_USER}

# Удаление временного каталога
rm -rf ${TEMP_DIR}
```

### Установка системы обновления

1. Устанавливаем утилиты необходимые для работы скрипта:

```bash
yum -y install curl wget jq tar
```

2. Создаём скрипт `/root/scripts/syncthing-update.sh`

Редактируем внутри скрипта переменную `TARGET_USER`, указываем пользователя, для которого будем обновлять Syncthing.

И даём ему право на запуск:
```bash
chmod +x /root/scripts/syncthing-update.sh
```

3. Добавляем в cron задачу:

```bash
crontab -e
```

Добавляем такую строчку:

```cron
0 13 * * * /root/scripts/syncthing-update.sh
```

Здесь важно указать то время, в которое будет обновляться приложение. Например, в `13:00`.

Готово. Теперь каждый день в 13:00 приложение Syncthing будет обновляться до последней версии.

## Другие статьи на тему

- [Установка Syncthing для CentOS 7](/install-syncthing-centos7/)
- [Запуск Syncthing в виде службы Windows](/syncthing-as-windows-service/)
- [Все статьи с тэгом syncthing](/tags/syncthing/)