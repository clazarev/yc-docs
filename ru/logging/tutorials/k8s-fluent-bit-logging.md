---
title: Передача логов кластера {{ managed-k8s-full-name }} в {{ cloud-logging-name }}
description: Обработчик логов Fluent Bit позволяет транслировать логи кластера {{ managed-k8s-name }} в сервис {{ cloud-logging-name }}. Для передачи логов используется модуль Fluent Bit plugin for {{ cloud-logging-full-name }}.
canonical: '{{ link-docs }}/tutorials/container-infrastructure/k8s-fluent-bit-logging'
sourcePath: ru/logging_includes/tutorials/k8s-fluent-bit-logging.md
---

# Передача логов кластера {{ managed-k8s-full-name }} в {{ cloud-logging-name }}

{% include notitle [k8s-logs-to-logging](../../_tutorials/containers/k8s-logs-to-logging.md) %}


## Отправка логов подов и сервисов с помощью Fluent Bit {#fluent-bit}

{% include notitle [Настройка Managed K8S Fluent Bit](../../_tutorials/containers/config-k8s-fluent-bit-logging.md) %}
