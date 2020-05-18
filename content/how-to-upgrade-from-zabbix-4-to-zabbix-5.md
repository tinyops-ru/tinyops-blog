+++
title = "Как обновить Zabbix сервер до версии 5 для CentOS 7"
description = "Подробное руководство по обновлению Zabbix сервера до 5-й версии для CentOS 7."
date = 2020-05-18

[taxonomies]
tags = ["zabbix", "upgrade"]
categories = ["tutorial"]
+++

На днях вышла новая мажорная версия Zabbix сервера. На Opennet в комментариях к новости развернулся холивар, какой
мониторинг лучше. Для своих задач я предпочитаю Zabbix, поэтому будем обновлять именно его.

#### Важные изменения между версиями

1. Web-интерфейс требует PHP 7.

2. Zabbix Proxy больше не поддерживает CentOS 6.  
   В официальном [репозитории](http://repo.zabbix.com/zabbix/5.0/rhel/6/x86_64/) нет пакетов для его установки. 

#### Технические параметры сервера

Информация о сервере, который мы будем обновлять:

- ОС - CentOS 7.x.
- PHP 5.6.
- MariaDB 10
- Web-сервер - Nginx.

#### План обновления

Краткий план этапов, которые мы пройдем в процессе обновления:

1. Создаем резервные копии.
2. Обновляем PHP с 5.6 до 7.2.
3. Обновляем Zabbix сервер.
4. Обновляем Zabbix Frontend.

#### 1. Создаем резервные копии

**1. 1. Резервная копия базы**

В моем случае в качестве сервера баз данных используется MariaDB, поэтому дамп базы создается вот так:

```shell script
mysqldump zabbix_server | gzip > zabbix_server.sql.gz
```

**1. 2. Резервная копия Zabbix Frontend**

На текущем сервере frontend расположен в `/var/www/zabbix/html`, поэтому копируем эту папку куда-нибудь.

#### 2. Обновляем PHP с 5.6 до 7.2

К сожалению, в мире RHEL-дистрибутивов одной командой PHP не обновить.

Версии пакетов в официальных репозиториях CentOS и в EPEL меняются очень неторопливо, поэтому приходится ходить на "сторону".
Для CentOS 7 есть два неофициальных репозитория, из которых все берут новые версии PHP:

- [Remi](https://rpms.remirepo.net/)
- [Webtatic](https://webtatic.com/projects/yum-repository/)

Так как на моем сервере крутится только Zabbix, то список пакетов для обновления можно подсмотреть из установленных: 

```shell script
yum list installed | grep php
```

В моем случае был уже подключен репозиторий Webtatic, поэтому ориентируемся на его наименование пакетов. 
Все пакеты с PHP 7.2 начинаются с префикса `php72w`. 

Берем список тех пакетов которые у нас установлены и копируем в текстовый редактор, затем меняем префикс на `php72w`.

```shell script
yum install --enablerepo=webtatic php72w.x86_64 php72w-bcmath.x86_64 php72w-cli.x86_64 php72w-common.x86_64 \
php72w-devel.x86_64 php72w-embedded.x86_64 php72w-fpm.x86_64 php72w-gd.x86_64 php72w-ldap.x86_64 \ 
php72w-mbstring.x86_64 php72w-mcrypt.x86_64 php72w-mysqlnd.x86_64 php72w-opcache.x86_64 php72w-pdo.x86_64 \
php72w-pear.noarch php72w-pecl-apcu.x86_64 php72w-process.x86_64 php72w-xml.x86_64
```

Часть пакетов yum не найдет, но это можно игнорировать. В качестве средства для запуска php 
мы будем использовать `php-fpm`.

**Настраиваем php-fpm**

Редактируем конфигурацию `/etc/php-fpm.d/www.conf`:

- Меняем пользователя с `apache` на `nginx`.
- Меняем тип подключения на файл:  
  Было:
  ```
  listen = 127.0.0.1:9000
  ```
  Стало:
  ```
  listen = /var/run/php5-fpm.sock
  ```

Включаем автозапуск сервиса и стартуем:

```shell script
systemctl enable php-fpm && systemctl start php-fpm
```

#### 3. Обновляем Zabbix сервер

```shell script
service zabbix-server stop

yum localinstall http://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
yum upgrade zabbix-server-mysql zabbix-web-mysql zabbix-agent

chown -R zabbix: /etc/zabbix
```

Очищаем содержимое `/var/www/zabbix/html`, затем копируем самый свежий 
frontend из `/usr/share/zabbix` в `/var/www/zabbix/html`.

Смотрим лог сервера, там побегут строчки `database upgrade...`.

Заходим в интерфейс `http://zabbix/`.
