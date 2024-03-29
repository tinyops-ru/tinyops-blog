+++
title = "Как подключить готовую тему к проекту на Zola"
description = "Подключаем к нашему сайту готовую тему для Zola."
date = 2020-01-03
updated = 2020-01-03
draft = true

[taxonomies]
tags = ["zola"]
categories = ["tutorial"]
+++

Прежде чем рассказать о том как подключить готовую тему стоит отметить что каждая тема в Zola это по сути такой же проект
как и ваш сайт. Одно из незначительных отличий - наличие файла `theme.toml`, в котором описывается конфигурация темы.

Меня порадовал такой подход, поэтому у меня не вызвало каких-либо проблем собрать 
[свою тему](https://github.com/lebe-dev/tinyblog-zola-theme).

## Где найти готовые темы для Zola

К сожалению, Zola еще молодой проект поэтому пока еще нет такого изобилия тем как у конкурентов.

Каталог тем с preview - [https://www.getzola.org/themes/](https://www.getzola.org/themes/)

## Как установить тему для своего сайта

Дальнейшая инструкция расчитана на то что вы имеете опыт работы с [git](https://git-scm.com/book/ru/v2/%D0%92%D0%B2%D0%B5%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5-%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D1%8B-Git).

1. Выбираем тему из галереи
    Я буду рассказывать на примере моей темы - [TinyBlog](https://github.com/lebe-dev/tinyblog-zola-theme), 
    она есть в свободном доступе на github.

2. Создадим подкаталог в котором будут хранится файлы темы которую выбрали:

    ```shell script
    mkdir themes\tinyblog
    ```
   
3. Добавим модуль тему как git модуль:
    ```shell script
    git submodule add https://github.com/lebe-dev/tinyblog-zola-theme themes/tinyblog
    ```
   
4. Подкачаем актуальную версию темы:
   ```shell script
   git submodule update --init --recursive 
   ```
   
   либо заходим в каталог тем и делаем pull:
   ```shell script
   cd themes
   git clone https://github.com/lebe-dev/tinyblog-zola-theme tinyblog
   ``` 
   
5. Настраиваем файл конфигурации сайта

   В файле конфигурации `config.toml` указываем тему:
   
   ```toml
   theme = "tinyblog" 
   ```
   
6. Открываем сайт в браузере и видим что тема применилась.

## Если сайт перестал собираться

Возможно тема требует каких-то специфичных настроек, про которые можно прочитать на github страничке темы.

Либо проблема из разряда популярных, я описал [пару решений](/zola-issues/).

## Другие статьи про Zola

По тэгу [zola](/tags/zola) вы можете найти другие статьи из этого цикла.
