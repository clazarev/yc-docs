---
title: Getting a list of metadata catalogs
description: Follow this guide to get a list of metadata catalogs in {{ data-catalog-full-name }}.
---

# Getting a list of catalogs


{% note info %}

{% include [preview-tp](../../../_includes/preview-tp.md) %}

{% endnote %}


{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), select the [resource folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to get a list of metadata catalogs.
    1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}**.
    1. In the left-hand panel, select ![image](../../../_assets/console-icons/folder-magnifier.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_data-catalog }}**.

{% endlist %}

## Setting up the list of catalogs {#setting-list-catalogs}

You can use the management console to sort catalogs by name, status, or creation date and customize the columns in the list.

To sort catalogs, click the **{{ ui-key.yacloud.common.name }}**, **{{ ui-key.yacloud.common.status }}**, or **{{ ui-key.yacloud.common.created-at }}** column header. This will sort catalogs alphabetically by name, status or in ascending date order. Second click on the same heading reverses the sorting order. Clicking a third time clears the applied sorting.

To customize the columns:

1. Click ![image](../../../_assets/console-icons/gear.svg) in the column header row. This will open the list of all columns.
1. Do one of the following in the list of columns:

    * Uncheck the columns you do not need.
    * Check the hidden columns you want displayed.
    * To reorder columns, click ![image](../../../_assets/console-icons/grip.svg) to the left of the column name and drag the column up or down to a new position in the list.

1. Click **{{ ui-key.yacloud.common.apply }}**.
