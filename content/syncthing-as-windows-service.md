+++
title = "Запуск Syncthing в виде службы Windows"
description = "Как настроить запуск Syncthing в качестве службы Windows."
date = 2020-11-11
updated = 2023-07-27

[taxonomies]
tags = ["software", "awesomeware", "syncthing", "windows"]
categories = ["software", "windows", "awesome"]
+++

Syncthing - это приложение для синхронизации каталогов между сетевыми устройствами. В качестве сетевых устройств могут
выступать настольные системы, ноутбуки и мобильные устройства. Syncthing можно запустить во всех популярных операционных системах.

Если на мобильных устройствах приложение стартует самостоятельно как служба, то на настольных системах необходима настройка.
Вы можете запустить приложение в сеансе текущего пользователя, но запускать при каждом запуске компьютера неудобно.

Официальное руководство [предлагает](https://docs.syncthing.net/users/autostart.html) два способа:
1. Запускать через планировщик задач Windows;
2. Запускать в виде службы.

Первый способ костыльный и перестанет работать если пользователь поменяет пароль, поэтому лучше настроить службу Windows.

### Установка NSSM

Для настройки необходимо использовать утилиту которая умеет запускать Windows-приложения как службы. 
Подобных утилит достаточно много, но мы возьмём одну из самых удобных - NSSM (The Non-Sucking Service Manager).
Скачиваем с официального сайта дистрибутив [https://nssm.cc/download](https://nssm.cc/download).

Распаковываем куда-нибудь, например, в `c:\apps\nssm`.

Затем складываем файл `nssm.exe` так, чтобы полный путь до приложения был следующим - `c:\apps\nssm\nssm.exe`.

### Создание службы Windows

Допустим полный путь до исполняемого файла Syncthing - `c:\apps\syncthing\syncthing.exe`.

Запускаем командную строку Windows с правами локального администратора и создаём службу:

```shell
c:\apps\nssm\nssm.exe install Syncthing c:\apps\syncthing\syncthing.exe
c:\apps\nssm\nssm.exe set Syncthing AppDirectory c:\apps\syncthing
c:\apps\nssm\nssm.exe set Syncthing start SERVICE_DELAYED_AUTO_START
```

### Настраиваем пользователя

В целях безопасности рекомендуется запускать Syncthing от имени пользователя без привилегий администратора. 

Заходим в оснастку служб Windows, переходим в свойств службы `Syncthing`.

![Свойства службы Syncthing](/images/awesomeware/syncthing/service-properties.png "Свойства службы Syncthing")

Выбираем существующего пользователя и указываем пароль.

![Выбор пользователя для запуска службы](/images/awesomeware/syncthing/service-run-as.png "Выбор пользователя для запуска службы")

Жмём "ОК" и перезапускаем службу.

Приложение станет доступно по ссылке [http://127.0.0.1:8384](http://127.0.0.1:8384).

## Другие статьи

- [Установка Syncthing для CentOS 7](/install-syncthing-centos7/)
- Статьи про другие приложения с тэгом [#awesomeware](/tags/awesomeware/)
