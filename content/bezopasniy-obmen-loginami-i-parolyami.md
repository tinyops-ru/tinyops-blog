+++
title = "Как безопасно обмениваться логинами и паролями"
description = "Как безопасно обмениваться логинами, паролями и прочими чувствительными данными с друзьями и коллегами."
date = 2023-09-11
updated = 2023-09-11

[taxonomies]
tags = ["безопасность", "security"]
categories = ["security"]
+++

Всем знакома ситуация в рабочей почте и чатах появляются логины, пароли, сертификаты и прочие чувствительные данные.
Это вполне закономерное явление, которое "исторически сложилось". Безопасники негодуют, что пользователи не заботятся о безопасности, но сами не предлагают ничего взамен.
Что делать сотруднику компании? Ставить себе софт аля PGP? Никто не будет с этим заморачиваться, у всех хватает собственных забот по работе.

К счастью, есть сервисы, которые реализуют возможность безопасного обмена чувствительными данными (далее - секретами).
С одной стороны - безопасники спокойны, с другой - пользователи получают простой и быстрый инструмент для обмена секретами.

Само собой такие сервисы не гарантируют 100% безопасности, но уменьшают риск утечки данных. 
Ссылки, которые окажутся в чате или в почте будут иметь ограниченный срок жизни и довольно быстро станут неактуальными.

**Основные особенности:**
- Шифрование на клиенте (в браузере)
- Ключ хранится внутри ссылки
- Сервер хранит только зашифрованные данные
- Данные хранятся в оперативной памяти и не попадают на диск
- Ограниченный период времени жизни секретов (по времени и количеству открытий ссылки)
- Открытый исходный код, возможность аудита

Один из таких сервисов - [PW](https://github.com/lebe-dev/pw).

## PW

![Интерфейс приложения PW](/images/security/pw.png "Интерфейс приложения PW")

Интерфейс предельно простой и пояснений не требует.

Помимо вышеназванных особенностей PW:
- Шифрует в AES 256, с ключом размером 32-символа
- Написан на Rust, что ликвидирует ряд классических уязвимостей связанных с оперативной памятью и ~~blazing fast~~ даёт высокую производительность
- Использует Redis в качестве in-memory хранилища секретов
- Потребляет минимальное количество ресурсов. Образ весит 25 МБ, потребляет 21 МБ ОЗУ (включая redis)

### Установка

Проще всего [установить docker'ом](https://github.com/lebe-dev/pw/blob/main/docs/install/DOCKER.md):

```yaml
version: '3.3'

services:
  app:
    container_name: pw
    image: tinyops/pw:1.1.0
    restart: always
    volumes:
      # - ./pw.yml:/app/app.yml
      #- ./locale.d:/app/locale.d
    ports:
      - "8080:8080"

  cache:
    container_name: pw-cache
    image: redis:7.2.1-alpine3.18
    restart: always
    command: 'redis-server --save "" --appendonly no --maxmemory 128mb'
```

Если конфигурация по умолчанию не подходит, то можно подложить свой конфиг на базе [примера](https://github.com/lebe-dev/pw/blob/main/backend/pw.yml-dist).

#### Локализация

По умолчанию включен английский язык, чтобы поменять на русский, достаточно подправить опцию в конфиге:

```yaml
locale-id: 'ru'
```

И перезапустить приложение.

Перевод не прибит гвоздями к коду, поэтому можно изменить перевод, файлы локализации хранятся в каталоге `locale.d`.

## Полезные ссылки

- [Вопросы и ответы про безопасность PW](https://github.com/lebe-dev/pw/blob/main/docs/faq/FAQ.ru.md)
- [Официальная документация](https://github.com/lebe-dev/pw/blob/main/docs/install/INSTALL.md)