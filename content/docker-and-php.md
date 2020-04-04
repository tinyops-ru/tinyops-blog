+++
title = "Как развернуть php приложение в Docker"
description = "Как развернуть php приложение в Docker"
date = 2020-04-04

[taxonomies]
tags = ["docker"]
categories = ["ops"]
+++

По ходу работы мне потребовалось мигрировать тройку php-приложений на новый сервер. На старом сервере был CentOS 6, зоопарк и набор
недокументированных хаков. Т.к. я из мира JVM-языков, то мир php для меня чужой. Раз на новом сервере был CentOS 7, 
то попутно возникла идея запаковать весь legacy зоопарк в Docker-образы.

Такое оборачивание дает дополнительное преимущества:
 - легко переключать версии приложения
 - если кто-то хакнул приложение, то можно без привлечения внешних security-средств (например, tripwire) легко сдампить текущее состояние в файл, откатить
 до нехакнутого варианта. А дальше уже звать разработчиков и пусть роются в дампе :))

За основу я взял образ с php 5.6 + Apache. Приложения требовали `ImageMagick`, `gd` и `zip`. Также требовалось установить
часовой пояс сервера.

Файлы приложения я сложил в подкаталог `app`. 

Файл сценария сборки - `Dockerfile`:

```yaml
FROM php:5.6-apache

RUN apt-get update && apt-get install -y \
                      libmagickwand-dev --no-install-recommends \
                   && pecl install imagick \
        && docker-php-ext-enable imagick

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) zip

RUN echo "date.timezone = Europe/Moscow" > /usr/local/etc/php/conf.d/timezone.ini

RUN a2enmod rewrite
RUN service apache2 restart
COPY app/ /var/www/html

RUN usermod -u 1001 www-data && groupmod -g 1001 www-data

RUN chown -R www-data.www-data /var/www
RUN chmod -R ug=rwx /var/www

RUN mkdir /tmp/webmodule
RUN chmod a=rwx /tmp/webmodule
EXPOSE 80

```

Собираем:

```shell script
docker build -t my-repo/my-app:1.0.0 .
```

## Как добавлять расширения для php

Авторы PHP любезно предоставили скрипт `docker-php-ext-install` внутри базового контейнера. В примере выше видно как это
использовать.

## Как использовать свой php.ini

Добавляем в `Dockerfile`:

```yaml
COPY php.ini /usr/local/etc/php/
```

## Как решить проблему с правами доступа

В `Dockerfile` есть пара команд, которые дают полные права для пользователя `www-data`, 
а также задают ID пользователя и группы для него равные `1001`.
На хосте где крутится докер я создал юзера с таким же uid\gid и сделал его полноправным владельцем папки 
которую использую как volume:

```yaml
...
volumes:
      - ./storage:/var/www/html/data
...
```

## Как использовать

С помощью docker compose можно завести вот так:

```yaml
version: '2'

services:
  phpfpm:
    image: "my-repo/my-app:1.0.0"
    volumes:
      - ./storage:/var/www/html/data
    ports:
      - "9123:80"
    environment:
      - APACHE_RUN_USER=#1001
      - APACHE_RUN_GROUP=#1001
```

Чтобы данные которые загружают юзеры сохранялись делаем volume.

Стартуем:

```shell script
docker-compose up -d
```

Приложение будет доступно на порту `9123`.
