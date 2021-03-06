+++
title = "Новая тема DDV для Zola"
description = "Рассказ про очень быструю утилиту для поиска файлов в Windows. Альтернатива стандартному механизму поиска."
date = 2021-03-04
updated = 2021-03-04

[taxonomies]
tags = ["zola", "theme"]
categories = ["zola"]
+++

Как-то раз в рассылке Hackernews наткнулся на [блог Drew DeVault](https://drewdevault.com/) (даже не буду пытаться перевести на русский :)).
Мне понравилась визуальная чистота и простота его блога. Вот как он выглядел в тот момент:

![Блог Drew DeVault](/images/zola/ddv-drew-devault-blog.png "Блог Drew DeVault")

И я решил собрать такой же внешний вид, но для [Zola](/zola-intro/). К счастью, Drew не использовал никаких framework'ов,
кучи внешних зависимостей и сделал всё сам. Портировать было приятно.

## Отличия

Я не стал портировать тему один в один, т. к. автор сделал правую панель специфичной. 
Думаю мало кому нужны ссылки на sourcehut, peertube, pgp и gemini. Поэтому я добавил ссылки на более популярные профили:
web-сайт, github, twitter и instagram. Дополнительно добавил страницу "О блоге".

Демо: [https://ddv-theme.tinyops.ru](https://ddv-theme.tinyops.ru)

## Как установить тему

1. Заходим в каталог с вашим блогом
2. Клонируем репозитарий с темой:
   ```shell
   git clone https://github.com/tinyops-ru/ddv-theme themes/ddv
   ```
3. В файле конфигурации `config.toml` указываем:
   ```toml
   theme="ddv"
   ```

## Как настроить тему

Все переменные конфигурации в секции `[extra]` файла `themes/ddv/config.toml`
   
## Другие статьи

С тэгом [zola](/tags/zola/).