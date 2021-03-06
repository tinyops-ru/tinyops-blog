+++
title = "Передача файлов и текста с помощью croc"
description = "Консольная утилита croc для безопасной и удобной передачи файлов и текста между двумя устройствами"
date = 2021-03-06
updated = 2021-03-06

[taxonomies]
tags = ["awesomeware"]
categories = ["awesomeware"]
+++

[croc](https://github.com/schollz/croc) - это консольная утилита для удобной и безопасной передачи данных между двумя устройствами.

![Репозитарий croc на Github](/images/awesomeware/croc.png "Репозиторий croc на Github")

Основные возможности:
- Передача данных (файлы, текст) между устройствами (с использованием relay);
- End-to-end шифрование (авторы утилиты не смогут прочитать, то что вы передаёте);
- Кросс-платформа (поддерживаются все популярные ОС);
- Можно передавать сразу несколько файлов;
- Поддержка докачки;
- Не нужно открывать порты и настраивать port-forwarding;
- С ориентиром на IPV6 и поддержкой IPV4;
- Поддержка работы через Proxy типа Tor.

## Как установить

### Linux

```shell
curl https://getcroc.schollz.com | bash
```

### MacOS

```shell
brew install croc
```

### Windows

Если вы используете менеджер пакетов [Chocolatey](https://chocolatey.org/):

```shell
choco install croc
```

Если вы используете менеджер пакетов [Scoop](https://scoop.sh/):

```shell
scoop install croc
```

## Как использовать

Например, нам нужно передать файл `archive.zip` с одного устройства на другое:

Инициируем передачу на устройстве с которого отправляем файл:

```shell
croc send archive.zip
Sending 'archive.zip' (3.3 kB)
Code is: sweden-light-jacket
On the other computer run

croc sweden-light-jacket
```

Утилита сгенерировала секретное слово, которое мы должны использовать на принимающей стороне. Также croc подсказывает 
какую команду мы должны запустить на втором устройстве:

```shell
croc sweden-light-jacket
```

Запускаем:

```shell
croc sweden-light-jacket
Accept 'archive.zip' (3.3 kB)? (y/n) y

Receiving (<-123.100.94.21:12345)
archive.zip 100% |████████████████████| (3.3/3.3 kB, 12.864 kB/s)
```

### Как отправить текст

Аналогично файлу, но команда запуска на исходном устройстве будет такой:

```shell
croc send --text "ВАШ ТЕКСТ"
```

## Полезные ссылки

- [Репозиторий на GitHub](https://github.com/schollz/croc)
- [Пост автора](https://schollz.com/software/croc6) с подробностями об утилите (на английском)
- [Другие посты про крутые приложения](/tags/awesomeware/) 