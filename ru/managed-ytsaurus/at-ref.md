---
title: Справочник аудитных логов {{ myt-full-name }} в {{ at-full-name }}
description: На этой странице приведен справочник событий сервиса {{ myt-full-name }}, отслеживаемых в {{ at-name }}.
---


# Справочник аудитных логов {{ at-full-name }}

В {{ at-name }} поддерживается отслеживание [событий уровня конфигурации](../audit-trails/concepts/format.md) (Control Plane). Подробнее см. [{#T}](../audit-trails/concepts/format.md).

Общий вид значения поля `event_type` (_тип события_):

```text
{{ at-event-prefix }}.audit.video.<имя_события>
```


## Справочник событий уровня конфигурации {#control-plane-events}

{% include [ytsaurus-events](../_includes/audit-trails/events/ytsaurus-events.md) %}