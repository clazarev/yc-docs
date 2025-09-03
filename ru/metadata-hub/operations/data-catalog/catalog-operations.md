---
title: Получение списка операций с каталогом метаданных
description: Следуя данной инструкции, вы сможете получить список операций в {{ data-catalog-full-name }}.
---

# Получение списка операций


{% note info %}

{% include [preview-tp](../../../_includes/preview-tp.md) %}

{% endnote %}


{% list tabs group=instructions %}

- Консоль управления {#console}

    1. В [консоли управления]({{ link-console-main }}) выберите [каталог ресурсов](../../../resource-manager/concepts/resources-hierarchy.md#folder), в котором создан каталог метаданных.
    1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}**.
    1. На панели слева выберите ![image](../../../_assets/console-icons/folder-magnifier.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_data-catalog }}**.
    1. В открывшемся списке выберите каталог метаданных, для которого вы хотите получить список операций.
    1. На панели слева выберите ![image](../../../_assets/console-icons/list-check.svg) **{{ ui-key.yacloud.data-catalog.label_ingestion-tab-operations }}**.

{% endlist %}

## Настройка списка операций {#setting-list-operations}

### Найти операции по их статусу или типу {#search-operations}

В полях над списком источников выберите статус или тип операции.

### Отсортировать операции {#sort-operations}

Нажмите на заголовок нужной колонки:

* **{{ ui-key.yacloud.common.started-at }}** — сортировка по дате и времени начала операции в порядке возрастания;
* **{{ ui-key.yacloud.common.updated-at }}** — сортировка по дате и времени изменения каталога в порядке возрастания.

Второе нажатие на тот же заголовок сменит направление сортировки. Третье нажатие отменит сортировку.

### Изменить отображение колонок {#setting-columns}

Чтобы настроить отображение колонок:

1. В строке заголовков колонок нажмите кнопку ![image](../../../_assets/console-icons/gear.svg). Откроется список всех колонок.
1. Выполните в списке колонок нужные действия:

    * Чтобы скрыть ненужные колонки, снимите для них отметки.
    * Чтобы отобразить скрытые колонки, отметьте их.
    * Чтобы изменить порядок колонок, слева от названия колонки нажмите ![image](../../../_assets/console-icons/grip.svg) и перетяните колонку вверх или вниз на нужную позицию в списке.

1. Нажмите кнопку **{{ ui-key.yacloud.common.apply }}**.
