---
title: Java-артефакты в {{ cloud-registry-full-name }}
description: Java-артефакт — это файл, содержащий скомпилированный код на языке Java и информацию о нем. Артефакты используются для распространения библиотек, фреймворков и других компонентов, которые можно включить в проекты на Java.
---

# Java-артефакт

_Java-артефакт_ — это файл скомпилированного кода на языке [Java](https://ru.wikipedia.org/wiki/Java) и информация о нем. [Артефакты](artifacts.md) используются для распространения библиотек, фреймворков и других компонентов, которые можно включить в проекты на Java.

## Maven {#maven-inf}

[Maven](https://maven.apache.org/) — это инструмент автоматизации сборки и система управления проектами. Для управления артефактами проекта Maven использует концепцию «зависимостей». Зависимости описываются в основном файле конфигурации проекта — `pom.xml` .

Артефакты в Maven это [ZIP-файлы](https://ru.wikipedia.org/wiki/.ZIP) со специальным содержимым, которые используются как зависимости в других проектах. Примеры артефактов: 
* [JAR (Java Archive)](https://ru.wikipedia.org/wiki/JAR);
* [WAR (Web Application Archive)](https://ru.wikipedia.org/wiki/WAR_(тип_файла)).

Подробнее о Maven читайте в [документации](https://maven.apache.org/guides/index.html).

#### См. также {#see-also}

* [Docker-образ](./docker-image.md)
* [{#T}](./art-nodejs.md)
* [{#T}](./art-nuget.md)
* [{#T}](./art-python.md)