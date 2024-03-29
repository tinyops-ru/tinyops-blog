+++
title = "Генератор статических сайтов Zola"
description = "Вводный пост про генератор статических сайтов Zola."
date = 2020-01-01
updated = 2020-01-01
draft = true

[taxonomies]
tags = ["zola"]
categories = ["tutorial"]
+++

Я узнал о [Zola](https://www.getzola.org/) из пятничной рассылки HackerNews. Так как в описании проекта значился Rust, 
то я предположил что это должно быть как минимум очень быстрое решение.

![alt text](/images/zola/zola-official-site.png "Официальный сайт Zola")

В 2019-м я собрал 2 проекта на базе генератора [Hugo](https://gohugo.io/) и не сказал бы что это был приятный опыт.
Одна из главных бед Hugo это наследие языка Go. Вроде бы они и позиционируют себя как 
*"самый быстрый генератор статических сайтов в мире"* (хотя на момент существования Zola это спорно), 
но борьба с шаблонизатором Go изнуряет.

Поэтому я решил попробовать Zola, да и язык Rust очень люблю. Официальный сайт гордо сообщает о преимуществах:

1. **У нас нет зависимостей**  
    Генератор поставляется в виде 1 файла, со встроенным процессором [Sass](https://sass-lang.com/), 
    подсветкой синтаксиса, списком содержанием и т.д.
2. **Мы чертовски быстрые**  
    Средний по размеру сайт собирается меньше чем за 1 секунду, включая компиляцию Sass-файлов и подсветку синтаксиса
    
3. **Масштабируемые**  
    Zola строит весь сайт в виде статических файлов, тем самым делая сайт готовым к очень высоким нагрузкам. Не нужно думать
    об администрировании серверов или баз данных 
4. **Простые в использовании**
5. **Гибкие**  
    Zola решает множество популярных проблем, тем самым не мешая вам сосредоточиться на контенте который вы создаете для
    сайта, блога, базы знаний и т.д.
6. **У нас дополненный Markdown**  
    Приложение поддерживает расширения для Markdown, позволяющие добавить еще больше возможностей в разметку. Например,
    вставлять видео с YouTube с пререндерингом или делать ссылки-якоря.
    
Круто! Я прежде всего купился на скорость, встроенный Sass-компилятор и простоту шаблонизатора.

## Как установить Zola

Респект авторам за то что позаботились о всем том зоопарке операционных систем который есть на момент 2020 года. 
Мне не нужно поднимать окружение разработчика Rust чтобы собрать исполняемый файл под мою ОС. 
Поддерживаются самые популярные ОС (Windows, MacOS), различные редакции Linux и даже FreeBSD.

Для Windows предпочитаю [Scoop](http://scoop.sh/), то и ставил через него:

```shell script
scoop install zola
```

Вот и все :)

## Как создать новый проект

Достаточно запустить zola с командой `init`:

```shell script
zola init
```

Zola спросит пару вопросов в интерактивном режиме и скелет проекта сайта будет готов.
Чтобы задать имя каталога, то его можно указать последним аргументом:

```shell script
zola init awesome-blog
```

## Как запустить Zola в режиме разработки

Режим разработки позволяет смотреть на итоговый результат в режиме реального времени. Любые изменения в каталоге проекта
будут мгновенно отображаться в браузере.

```shell script
zola serve
```

По умолчанию приложение будет доступно в браузере по адресу [http://127.0.0.1:1111](http://127.0.0.1:1111)

## Как собрать production-ready сборку

Production-ready сборка это сборка которая скомпонована генератором с учетом всех возможных оптимизаций. Она готова
для того чтобы загрузить на ваш сайт.

```shell script
zola build --base-url $DEPLOY_URL
```

Где `$DEPLOY_URL` - адрес по которому будет крутиться сайт.

## Другие статьи про Zola

По тэгу [zola](/tags/zola) вы можете найти другие статьи из этого цикла.
