+++
title = "Утилита для быстрого поиска файлов Everything"
description = "Рассказ про очень быструю утилиту для поиска файлов в Windows. Альтернатива стандартному механизму поиска."
date = 2020-03-24
updated = 2020-03-24
draft = true

[taxonomies]
tags = ["software", "awesomeware", "search"]
categories = ["software", "windows", "awesome"]
+++

Продолжаю рубрику [Awesomeware](/tags/awesomeware), про качественное программное обеспечение, которое заслуживает внимания.
В прошлый раз была [утилита для скриншотов от Яндекс](/yandex-screenshot-tool/), а сегодня - рассказ про [Everything](https://www.voidtools.com/ru-ru/).

> Everything – это поисковая программа для Windows, мгновенно отыскивающая файлы и папки по их именам.
  В отличие от встроенной в Windows поисковой системы, Everything изначально показывает каждый файл и папку в компьютере (отсюда и название "Everything", т.е. "все").
  Вы вводите поисковый фильтр, после чего отображаются только нужные вам файлы и папки.

![alt text](/images/awesomeware/everything-1.png "Как выглядит главное окно программы")

Автор любезно предоставил [исчерпывающий FAQ](https://www.voidtools.com/ru-ru/faq/#%D0%A7%D1%82%D0%BE_%D1%82%D0%B0%D0%BA%D0%BE%D0%B5_everything) по архитектуре, особенностях работы и решении популярных проблем. 

### Чем полезна утилита

Поиск по именам файлов.

### Как настроить

Настройка для утилиты минимальна, она отлично работает с настройками по умолчанию. Я лишь добавил горячую клавишу
чтобы открывать и скрывать окно утилиты. 

В настройках указал комбинацию горячих клавиш - `Ctrl+F3`:

![alt text](/images/awesomeware/everything-2.png "Главная горячая комбинация")

По первому нажатию окно утилиты появляется, по следующему - исчезает.

### Оценка качества

- **Удобство использования**  
  - Интерфейс простой, отклик без видимых задержек.

- **Производительность**  
  - Запуск - менее 1 секунды
  - Поиск 600.000+ файлов менее чем за 1 секунду  
  
  ![alt text](/images/awesomeware/everything-3.gif "Скорость поиска файлов")

- **Потребление ресурсов**  
  - `1,37МБ` на диске для исполняемого файла 
  - `1,2МБ` в оперативной памяти - исполняемый файл
  - `176МБ` в оперативной памяти - хранение индекса
  
### Недостатки

В процессе написания статьи обнаружил недостаток. Приложение хранит свой поисковый индекс в оперативной памяти.
В моем случае для ранения 3,5 миллиона записей она использует `173МБ`.

> **Много ли системных ресурсов требуется Everything?**  
Нет, Everything потребляет очень мало системных ресурсов.
В свежеустановленной Windows 10 (около 120 тыс. файлов) будет занято порядка 14 МБ ОЗУ и менее 9 МБ на диске.
В системе с миллионом файлов будет занято примерно 75 МБ ОЗУ и 45 МБ на диске.

### Где скачать

Everything абсолютно бесплатная утилита и доступна для скачивания на официальном сайте - 
[https://www.voidtools.com/ru-ru/](https://www.voidtools.com/ru-ru/).

### Другие посты на эту тему

Другие посты про удобный и быстрый софт вы можете посмотреть под тэгом [Awesomeware](/tags/awesomeware).
