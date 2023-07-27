+++
title = "Тема DDV для Zola"
description = "Тема DDV для Zola"
date = 2021-03-04
updated = 2023-07-27

[taxonomies]
tags = ["zola", "theme"]
categories = ["zola"]
+++

Как-то раз в рассылке Hackernews наткнулся на [блог Drew DeVault](https://drewdevault.com/).
Мне понравилась визуальная чистота и простота блога. Вот как блог выглядел в тот момент:

![Блог Drew DeVault](/images/zola/ddv-drew-devault-blog.png "Блог Drew DeVault")

Решил собрать такой же внешний вид, но для [Zola](/zola-intro/). К счастью, Drew не использовал никаких framework'ов,
кучи внешних зависимостей и сделал всё сам. Портировать было легко.

## Отличия

Я не стал портировать тему один в один, т. к. автор сделал правую панель специфичной. 
Думаю мало кому нужны ссылки на sourcehut, peertube, pgp и gemini. Поэтому я добавил ссылки на более популярные профили:
web-сайт, github, twitter и instagram. Дополнительно добавил страницу "О блоге".

Демо: [https://ddv-theme.tinyops.ru](https://ddv-theme.tinyops.ru)

## Как установить тему

1. Заходим в каталог с вашим блогом
2. Клонируем репозиторий с темой:
   ```shell
   git clone https://github.com/tinyops-ru/ddv-theme themes/ddv
   ```
3. В файле конфигурации `config.toml` указываем:
   ```toml
   theme="ddv"
   ```

## Как настроить тему

Все переменные конфигурации в секции `[extra]` файла `themes/ddv/config.toml`
