---
title: How to get information about a {{ metadata-hub-name }} connection. Step-by-step guides
description: Step-by-step guides on using {{ connection-manager-name }} in {{ yandex-cloud }}. In this tutorial, you will learn how to view connections.
---

# Viewing connection information



{% include notitle [preview](../../_includes/note-preview.md) %}



To view created connections:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select a [folder](../../resource-manager/concepts/resources-hierarchy.md#folder).
  1. Select **{{ metadata-hub-full-name }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/plug-connection.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_connection-manager }}**.
  1. Click the line with the connection to view its details.

{% endlist %}

For the selected connection, you can view information about its dependencies on other services and the history of operations.


You can open the selected connection in [{{ websql-full-name }}](../../websql/concepts/index.md) to make database requests. Click the **{{ ui-key.yacloud.connection-manager.label_open-websql }}** link in the top-right corner.

{% note info %}

The connection list only shows those connections for which the user has access permissions (the [`connection-manager.auditor`](../security/connection-manager-roles.md#connection-manager-auditor) or [`connection-manager.viewer`](../security/connection-manager-roles.md#connection-manager-viewer) role). A user with access permissions for a folder will be able to view all connections in that folder.

{% endnote %}


## Viewing connection dependencies {#dependencies}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select a [folder](../../resource-manager/concepts/resources-hierarchy.md#folder).
  1. Select **{{ metadata-hub-full-name }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/plug-connection.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_connection-manager }}** and click the name of the connection you need.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/nodes-right.svg) **{{ ui-key.yacloud.connection-manager.label_dependences }}**.

     The dependency list shows managed database clusters and service instances that use {{ connection-manager-name }} to store connection information. Click a line in the dependency list to go to a cluster or service instance.

{% endlist %}

## Viewing the history of operations {#operations}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select a [folder](../../resource-manager/concepts/resources-hierarchy.md#folder).
  1. Select **{{ metadata-hub-full-name }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/plug-connection.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_connection-manager }}** and click the name of the connection you need.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/list-check.svg) **{{ ui-key.yacloud.common.operations-key-value }}**.

     In the list of operations, you can view all the operations for the selected connection since its creation:

     * `Create`: Connection created.
     * `Update`: Connection updated.
     * `Delete`: Connection deleted.
     * `Set access bindings`: Connection user roles assigned.
     * `Update access bindings`: Connection user roles updated.

     You can also view the technical operations:

     * `Bump version`: Secret version for MDB connections updated.
     * `Update references`: Connection references updated.
     * `Update version references`: Updated the connection version references used when creating or deleting an MDB cluster backup.
     * `Delete version`: Connection version deleted after removing the most recent MDB cluster backup.

{% endlist %}