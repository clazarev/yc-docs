---
title: Как визуализировать метрики в {{ monitoring-name }}
description: Следуя данной инструкции, вы сможете прочитать и визуализировать метрики {{ prometheus-name }} в {{ monitoring-name }}.
sourcePath: ru/monitoring_includes/operations/prometheus/querying/monitoring.md
---

# Чтение и визуализация метрик в {{ monitoring-name }}

На основе метрик {{ prometheus-name }} можно построить графики:

* В разделе **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}** — для быстрого просмотра состояния ресурсов.
* В разделе **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.dashboards.title }}** — для периодического мониторинга определенных показателей.


## Добавление графиков в {{ monitoring-name }} {#prometheus-metrics}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Если вы еще не используете {{ managed-prometheus-name }}, настройте [запись метрик](../ingestion/index.md).
  1. Откройте [главную страницу сервиса]({{ link-monitoring }}).
  1. Слева выберите раздел **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}**.
  1. Вверху в списке **Источник данных** выберите **{{ prometheus-name }}**.
  1. Выберите воркспейс. Если воркспейс только один, он будет выбран по умолчанию.
  1. В строке запроса введите параметры отображения метрик на языке [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/).
  1. Чтобы отобразить на графике несколько линий, нажмите кнопку **{{ ui-key.yacloud_monitoring.querystring.action.add-query }}** и введите новый запрос.
  1. Нажмите кнопку **{{ ui-key.yacloud_monitoring.querystring.action.execute-query }}** или сочетание клавиш **Cmd/Ctrl + Enter**.

  {% note info %}

  Для метрик {{ prometheus-name }} доступны все возможности, описанные в разделе [{#T}](../../metric/metric-explorer.md), кроме добавления параметров. Поэтому разбивка графиков по параметрам недоступна.

  {% endnote %}

{% endlist %}

## Добавление графиков на дашборд {#prometheus-dashboard}

Вы можете добавить графики на дашборд из раздела **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}** или в разделе **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.dashboards.title }}**.

Добавление графика из раздела **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}**:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. [Постройте график](#prometheus-metrics) по метрикам {{ prometheus-name }}.
  1. Справа вверху нажмите кнопку **{{ ui-key.yacloud_monitoring.wizard.wizard.mx.save-as }}**.
  1. Введите название графика.
  1. Выберите облако и каталог.
  1. Выберите существующий дашборд или создайте новый.
  1. Выберите один из вариантов добавления графика:
     * **{{ ui-key.yacloud_monitoring.component.add-to-dashboard-form.action.add }}** — остаться в разделе **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}**.
     * **{{ ui-key.yacloud_monitoring.component.add-to-dashboard-form.action.add-and-go }}** — перейти в раздел **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.dashboards.title }}**. График в **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.explorer.title }}** не сохранится.

{% endlist %}

Добавление графика в разделе **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.dashboards.title }}**:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Откройте [главную страницу сервиса]({{ link-monitoring }}).
  1. Выберите раздел **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.dashboards.title }}**.
  1. Выберите существующий дашборд или [создайте новый](../../dashboard/create.md).
  1. Справа вверху нажмите ![image](../../../../_assets/console-icons/plus.svg) и выберите **{{ ui-key.yacloud_monitoring.dashboard.widget-placeholder.add-graph }}**.
  1. Вверху в списке **Источник данных** выберите **{{ prometheus-name }}**.
  1. Выберите воркспейс. Если воркспейс только один, он будет выбран по умолчанию.
  1. В строке запроса введите параметры отображения метрик на языке [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/).
  1. Справа вверху нажмите ![image](../../../../_assets/console-icons/floppy-disk.svg) **{{ ui-key.yacloud_monitoring.actions.common.save }}**. График будет добавлен на дашборд.

{% endlist %}

