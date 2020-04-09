+++
title = "Настройка Nginx для проксирования Web-сокетов"
description = "Как настроить проксирование Web-сокетов с помощью Nginx"
date = 2020-04-09

[taxonomies]
tags = ["nginx", "web-sockets"]
categories = ["tutorial"]
+++

Nginx легко настроить для проксирования Web-сокетов, эта функциональность поддерживается с версии 1.3. Для переключения
HTTP запросов в Web-сокеты используется 
механизм [смены протокола](https://tools.ietf.org/html/rfc2616#section-14.42) доступный в HTTP/1.1. 
Передаются два заголовка: Connection и Upgrade.

Например, ваше приложение слушает tcp-порт `12345` и поддерживает Web-сокеты, вот как это настраивается:

```
location / {
    proxy_pass http://localhost:12345;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

### Когда обрывает Web-сокеты

Как-то раз на production сервере мы поймали проблему - Web-сокеты обрывались каждые 2 минуты. 
По умолчанию nginx закрывает проксируемые соединения после 60 секунд неактивности. Эта величина регулируется параметром
[proxy_read_timeout](https://nginx.org/ru/docs/http/ngx_http_proxy_module.html#proxy_read_timeout). На нашем сервере
был настроен `proxy_read_timeout` равный как раз двум минутам.
 
Поэтому чтобы Web-сокеты не обрывало приложение должно уметь отправлять условные ping-запросы.

## Официальная документация

[Проксирование WebSocket](https://nginx.org/ru/docs/http/websocket.html)
