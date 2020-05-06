+++
title = "Как решать проблемы с Zola"
description = "Пост про то как решать популярные проблемы при создании сайтов с Zola."
date = 2020-01-10

[taxonomies]
tags = ["zola"]
categories = ["tutorial"]
+++

Основные ошибки при генерации Zola выдает прямо в консоль. Например, вот так:

```shell script
Change detected @ 2020-01-11 18:37:14
-> Content changed C:\pro\tinyops-blog\content/zola-issues.md
Failed to build the site
Error: Failed to render page 'd:\tinyops-blog\content/zola-issues.md'
Reason: Failed to render 'tinyblog/templates/page.html'
Reason: Variable `page.taxonomies['tags']` not found in context while rendering 'tinyblog/templates/page.html': the evaluated version was `page.taxonomies.tags`. Maybe the index is out of bounds?
Done in 31ms.
```

В контексте ошибки выше:
1. Ошибка связана с содержимым файла `zola-issues.md`
2. Движок не может отрендерить контент с шаблоном `page.html`
3. Переменная `page.taxonomies['tags']` не найдена

Смотрим начало `content/zola-issues.md`:

```toml
+++
title = "Как решать проблемы с Zola"
date = 2020-01-10
+++
```

и видим что секция `[taxonomies]` отсутствует. Если взялся использовать тэги и категории, то будь
добр их определить. Исправляем:

```toml
[taxonomies]
tags = ["zola"]
categories = ["tutorial"]
```

## Ошибка - The requested port is not available

Как это выглядит в консоли:

```shell script
d:\tinyops-blog>zola serve
The requested port is not available
```

Ошибка сообщает что Zola не может стартануть с выбранным tcp-портом (по умолчанию `1111`). 

Решения для Windows:
1. Убить процесс `zola.exe`
    В Windows после нажатия `CTRL+C` приложение продолжает работать.
    
2. Указать другой порт при запуске:
    ```shell script
    zola serve --port 12345
    ```

## Другие статьи про Zola

По тэгу [zola](/tags/zola) вы можете найти другие статьи из этого цикла.
