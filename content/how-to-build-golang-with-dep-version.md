+++
title = "Как собрать старый Golang-проект с определённой версией зависимости"
description = "Собираем проект для Golang без фичи с модулями и указанием специальной версии одной из зависимостей."
date = 2020-09-30
updated = 2020-09-30

[taxonomies]
tags = ["golang", "development", "devops"]
categories = ["development", "golang"]
+++

Возникла задача дописать фичу для утилиты написанной на Go 1.7. Одно из требований к утилите было то что она должна была
работать под Windows Server 2008 (без R2). К сожалению, более новые версии Go, в том числе те, в которые завезли модули,
использовать было нельзя. В отличие от Rust в Go старых версий с зависимостями всё было сделано плохо.

Нельзя было как в Rust просто указать какую версию зависимости ты хочешь. Поэтому в этой статье опишу как максимально 
просто решить эту проблему.

Итак, для проекта была нужна библиотека [grab](https://github.com/cavaliercoder/grab). 
С последнего мартовского коммита в проект `master` этой библиотеки само собой убежал и попутно поломал совместимость.
Проект перестал собираться.

Чтобы подсунуть необходимую версию нужно в `%GOPATH%/src/github.com/cavaliercoder` 
клонировать стабильную версию с нужным тэгом. В данном случае нужен был `2.0.0`.

1. Удаляем каталог `grab`.

2. Клонируем с тэгом 2.0.0:
    ```shell script
    git clone --depth 1 --branch v2.0.0 https://github.com/cavaliercoder/grab.git
    ```

В Idea может потребоваться реиндексация, чтобы она обновила свои кэши по зависимостям: `File > Invalidate caches`.

Готово.