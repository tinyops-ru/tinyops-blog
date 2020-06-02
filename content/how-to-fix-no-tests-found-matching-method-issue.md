+++
title = "Как исправить ошибку - No tests found matching Method в IntelliJ IDEA"
description = "Как исправить популярную проблему в JetBrains IntelliJ IDEA - No tests found matching Method."
date = 2020-01-15

[taxonomies]
tags = ["idea", "jetbrains"]
categories = ["troubleshooting"]
+++

Даже на момент написания статьи в связке JetBrains IDEA + Gradle хватает проблем с окривевшими кэшами. 
Причем текст ошибки никак не помогает понять в чем же проблема. Поэтому те кто давно работает 
со всея-IDE от JetBrains знают что скорей всего пришел момент проверки актуальности кэшей (инвалидации) Gradle, либо их очистки.

### Ошибка - No tests found matching Method

Это исключение можно словить при запуске unit-тестов. Вы запускали тесты и вдруг стало писать эту ошибку.
Опытным путем удалось выявить, что ноги проблемы растут из относительно свежей Gradle фичи - [Build cache](https://docs.gradle.org/current/userguide/build_cache.html).

> Gradle build cache - это механизм кэширования который нацелен на экономию времени, переиспользуя вывод от предыдущих сборок приложения.

Эта фича включается через файл конфигурации `gradle.properties`, вот так:

```properties
org.gradle.caching=true
```

На словах звучит круто и на деле сборки становятся быстрей, но проверка актуальности кэша (инвалидация) происходит далеко не всегда.
Чаще всего проблема проявляется после запуска анализа покрытия кода (coverage). Gradle создает каталог `out` и перестает
видеть изменения в тестах.

## Решение

Удалить каталоги `build` и `out` в корне проекта.