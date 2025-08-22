{{ yq-full-name }} is an interactive service for serverless data analysis. You can use it to process information from various storages without having to to create a dedicated cluster. {{ yq-full-name }} supports [{{ objstorage-full-name }}](../../storage/), [{{ mpg-full-name }}](../../managed-postgresql/), and [{{ mch-full-name }}](../../managed-clickhouse/) data storages.

You can process data from these systems either individually or as part of one common query, referred to as a _federated_ query.

In this tutorial, you will create three dedicated data storages: customers, purchased items, and purchase dates. You can get data from all storages at the same time using a federated query from a notebook cell.

1. [Set up your infrastructure](#infra).
1. [Get started in {{ yq-name }}](#yq-begin).
1. [Connect to the {{ objstorage-name }} data](#storage-connect).
1. [Connect to the {{ mch-name }} data](#ch-connect).
1. [Connect to the {{ mpg-name }} data](#pg-connect).
1. [Run a federated query](#federated_query).

If you no longer need the resources you created, [delete them](#clear-out).


## Getting started {#before-you-begin}

{% include [before-you-begin](../../_tutorials/_tutorials_includes/before-you-begin-datasphere.md) %}

### Required paid resources {#paid-resources}

The cost of supporting the infrastructure for running federated queries in this tutorial includes:

* Fee for using [{{ ml-platform-name }} computing resources](../../datasphere/pricing.md).
* Fee for [storing data in a bucket](../../storage/pricing.md#prices-storage).
* Fee for a running [{{ mch-name }} cluster](../../managed-clickhouse/pricing.md).
* Fee for a running [{{ mpg-name }} cluster](../../managed-postgresql/pricing.md).
* Fee for the amount of read data when running [{{ yq-name }} queries](../../query/pricing.md).

## Set up your infrastructure {#infra}

{% include [intro](../../_includes/datasphere/infra-intro.md) %}

{% include [intro](../../_includes/datasphere/federation-disclaimer.md) %}

### Create a folder {#create-folder}

{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), select a cloud and click ![create](../../_assets/console-icons/plus.svg)**{{ ui-key.yacloud.component.console-dashboard.button_action-create-folder }}**.
   1. Name your folder, e.g., `data-folder`.
   1. Click **{{ ui-key.yacloud.iam.cloud.folders-create.button_create }}**.

{% endlist %}

### Create a service account for the {{ ml-platform-name }} project {#create-sa}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_iam }}**.
  1. Click **{{ ui-key.yacloud.iam.folder.service-accounts.button_add }}**.
  1. Name the [service account](../../iam/concepts/users/service-accounts.md), e.g., `yq-sa`.
  1. Click **{{ ui-key.yacloud.iam.folder.service-account.label_add-role }}** and assign the following roles to the service account:
     * `yq.editor` to run {{ yq-name }} queries.
     * `storage.viewer` to view the contents of the {{ objstorage-name }} bucket and objects.
     * `managed-clickhouse.viewer` to view the contents of the {{ mch-name }} cluster.
     * `managed-postgresql.viewer` to view the contents of the {{ mpg-name }} cluster.
  1. Click **{{ ui-key.yacloud.iam.folder.service-account.popup-robot_button_add }}**.

{% endlist %}

### Add the service account to the project {#sa-to-project}

To enable the service account to run a {{ ml-platform-name }} project, add it to the list of project members.

1. {% include [find project](../../_includes/datasphere/ui-find-project.md) %}
1. In the **{{ ui-key.yc-ui-datasphere.project-page.tab.members }}** tab, click **{{ ui-key.yc-ui-datasphere.common.add-member }}**.
1. Select the `yq-sa` account and click **{{ ui-key.yc-ui-datasphere.common.add }}**.
1. Switch your service account role to **Editor**.

### Create an authorized key for a service account {#create-key}

To allow the service account to send queries using {{ yq-name }}, create an [authorized key](../../iam/concepts/authorization/key.md).

{% include [disclaimer](../../_includes/iam/authorized-keys-disclaimer.md) %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_iam }}**.
  1. In the left-hand panel, select ![FaceRobot](../../_assets/console-icons/face-robot.svg) **{{ ui-key.yacloud.iam.label_service-accounts }}**.
  1. In the list that opens, select `yq-sa`.
  1. Click **{{ ui-key.yacloud.iam.folder.service-account.overview.button_create-key-popup }}** in the top panel and select **{{ ui-key.yacloud.iam.folder.service-account.overview.button_create_key }}**.
  1. Select the encryption algorithm and click **{{ ui-key.yacloud.iam.folder.service-account.overview.popup-key_button_create }}**.
  1. Click **{{ ui-key.yacloud.iam.folder.service-account.overview.action_download-keys-file }}**.

{% endlist %}

### Create a secret {#create-secret}

To get an authorized key from the notebook, create a [secret](../../datasphere/concepts/secrets.md) with the contents of the authorized key file.

1. {% include [find project](../../_includes/datasphere/ui-find-project.md) %}
1. Under **{{ ui-key.yc-ui-datasphere.project-page.project-resources }}**, click ![secret](../../_assets/console-icons/shield-check.svg)**{{ ui-key.yc-ui-datasphere.resources.secret }}**.
1. Click **{{ ui-key.yc-ui-datasphere.common.create }}**.
1. In the **{{ ui-key.yc-ui-datasphere.secret.name }}** field, enter a name for the secret: `yq_access_key`.
1. In the **{{ ui-key.yc-ui-datasphere.secret.content }}** field, paste the full contents of the authorized key file you downloaded.
1. Click **{{ ui-key.yc-ui-datasphere.common.create }}**.

### Create a notebook {#create-notebook}

Queries to the {{ mpg-name }} database through {{ yq-name }} will be sent from the notebook.

1. {% include [find project](../../_includes/datasphere/ui-find-project.md) %}
1. Click **{{ ui-key.yc-ui-datasphere.project-page.project-card.go-to-jupyter }}** and wait for the loading to complete.
1. In the top panel, click **File** and select **New** ⟶ **Notebook**.
1. Select a kernel and click **Select**.

## Get started in {{ yq-name }} {#yq-begin}

{% include [yq-begin](../../_tutorials/_tutorials_includes/yq-begin.md) %}

## Connect to the {{ objstorage-name }} data {#storage-connect}

For operations with {{ objstorage-name }} data, you will need a bucket with a table, a {{ yq-name }} [connection](../../query/concepts/glossary.md#connection), and a data [binding](../../query/concepts/glossary.md#binding).

### Create a data bucket {#storage-data}

The {{ objstorage-name }} [bucket](../../storage/concepts/bucket.md) will contain the purchase dates.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
  1. At the top right, click **{{ ui-key.yacloud.storage.buckets.button_create }}**.
  1. In the **{{ ui-key.yacloud.storage.bucket.settings.field_name }}** field, enter a name for the bucket.
  1. In the **{{ ui-key.yacloud.storage.bucket.settings.field_access-read }}**, **{{ ui-key.yacloud.storage.bucket.settings.field_access-list }}**, and **{{ ui-key.yacloud.storage.bucket.settings.field_access-config-read }}** fields, select **{{ ui-key.yacloud.storage.bucket.settings.access_value_private }}**.
  1. Click **{{ ui-key.yacloud.storage.buckets.create.button_create }}**.
  1. Create a file named `visits.csv` and add a table with purchase dates to it:

     ```text
     date|persons_id|item_id
     2024-05-14 12:12:00|1|2
     2024-05-15 13:13:00|2|1
     ```

  1. Navigate to the bucket you created and click **{{ ui-key.yacloud.storage.bucket.button_upload }}**.
  1. In the window that opens, select `visits.csv` and click **Open**.
  1. Click **{{ ui-key.yacloud.storage.button_upload }}**.

{% endlist %}

### Create a connection to {{ objstorage-name }} {#yq-storage}

{% list tabs group=instructions %}

- Management console {#console}
  
  1. In the [management console]({{ link-console-main }}), navigate to `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_yq_ru }}**.
  1. In the left-hand panel, select **{{ ui-key.yql.yq-ide-aside.connections.tab-text }}**.
  1. Click ![info](../../_assets/console-icons/plus.svg)**{{ ui-key.yql.yq-connection-form.action_create-new }}**.
  1. Enter a name for the connection, e.g., `storage-connection`.
  1. Select the **{{ ui-key.yql.yq-connection.action_object-storage }}** connection type and specify the **{{ ui-key.yql.yq-connection-form.connection-type-parameters.section-title }}**.
  1. In the **{{ ui-key.yql.yq-connection-form.bucket-auth.input-label }}** field, select `{{ ui-key.yql.yq-connection-form.private.button-text }}` and set these properties:

     * **{{ ui-key.yql.yq-connection-form.cloud.input-label }}**: `data-folder`.
     * **{{ ui-key.yql.yq-connection-form.bucket.input-label }}**: Select the bucket you created.
     * **{{ ui-key.yql.yq-connection-form.service-account.input-label }}**: `yq-sa`.
  
  1. Click **{{ ui-key.yql.yq-connection-form.create.button-text }}**.

{% endlist %}

### Create a data binding {#create-binding}

A data binding contains information about file formats and locations in the bucket, as well as a list of data fields and their types.

To create a data binding:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_yq_ru }}**.
  1. In the left-hand panel, select **{{ ui-key.yql.yq-ide-aside.bindings.tab-text }}**.
  1. Click ![info](../../_assets/console-icons/plus.svg) **{{ ui-key.yql.yq-binding-add.create-binding.button-text }}**.
  1. Under **{{ ui-key.yql.yq-binding-form.connection-parameters.title }}**:

     * **{{ ui-key.yql.yq-binding-form.connection-type.title }}**: Select **{{ ui-key.yql.yq-connection.action_object-storage }}**.
     * **{{ ui-key.yql.yq-binding-form.connection.title }}**: Select `storage-connection`.

  1. Under **{{ ui-key.yql.yq-binding-form.binding-parameters.title }}**:
  
     1. Enter a name for the binding, e.g., `visits`.
     1. In the **{{ ui-key.yql.yq-binding-form.binding-path-pattern.title }}** field, specify the [path to the table](../../query/sources-and-sinks/object-storage.md#path_format) in the bucket: `/visits.csv`.
  
  1. Under **{{ ui-key.yql.yq-binding-form.binding-format-settings.title }}**:
     
     * **{{ ui-key.yql.yq-binding-form.binding-format.title }}**: Select `csv_with_names`.
     * **{{ ui-key.yql.yq-binding-form.binding-format-settings-delimiter.title }}**: `|`.

  1. Under **{{ ui-key.yql.yq-binding-form.binding-fields.title }}**, add three columns with the following names and data types:

     * `date`: `DATETIME`
     * `person_id`: `INT32`
     * `items_id`: `INT32`
  
  1. To verify the data you specified is correct, click **{{ ui-key.yql.yq-binding-form.binding-preview.button-text }}**. Your table should appear below.
  1. Click **{{ ui-key.yql.yq-binding-form.binding-create.button-text }}**.

{% endlist %}

To check the connection, get the table data from the notebook cell:

```sql
%yq SELECT * FROM visits LIMIT 100;
```

## Connect to the {{ mch-name }} data {#ch-connect}

For operations with {{ mch-name }} data, you will need a cluster with a table and a {{ yq-name }} connection.

### Create a {{ mch-name }} cluster {#cluster-ch}

Any running {{ mch-name }} cluster with the **{{ ui-key.yacloud.mdb.forms.additional-field-yandex-query_ru }}** option enabled is suitable for sending queries.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select `data-folder`.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
  1. In the **{{ ui-key.yacloud.mdb.forms.base_field_name }}** field, enter the cluster name, e.g., `clickhouse`.

  1. Under **{{ ui-key.yacloud.mdb.forms.section_settings }}**:

      * In the **{{ ui-key.yacloud.mdb.forms.database_field_sql-user-management }}** field, select **{{ ui-key.yacloud.common.enabled }}** from the drop-down list.
      * Specify **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}** and **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}**.

  1. Under **{{ ui-key.yacloud.mdb.forms.section_service-settings }}**:

      * Select the `yq-sa` service account.
      * Enable the **{{ ui-key.yacloud.mdb.forms.additional-field-yandex-query_ru }}** and **{{ ui-key.yacloud.mdb.forms.additional-field-websql }}** options.

  1. You can leave the other settings at their defaults.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_create }}**.

{% endlist %}

### Create a table {#ch-data}

The {{ mch-name }} table will contain the item names.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), open the `clickhouse` cluster page and navigate to the **{{ ui-key.yacloud.clickhouse.cluster.switch_explore }}** tab.
  1. Enter **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}** and **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}** you specified when creating the cluster.
  1. In the input window on the right, run the following SQL queries one by one:

     ```sql
     CREATE TABLE items(id INT, description VARCHAR) ENGINE=MergeTree ORDER BY id;
     ```
 
     ```sql
     INSERT INTO items(id, description) VALUES(1, 'Refrigerator')
     ```
 
     ```sql
     INSERT INTO items(id, description) VALUES(2, 'TV')
     ```

  1. Click **{{ ui-key.yacloud.clickhouse.cluster.explore.button_execute }}**.

{% endlist %}

### Create a connection to {{ mch-name }} {#yq-ch}

{% list tabs group=instructions %}

- Management console {#console}
  
  1. In the [management console]({{ link-console-main }}), select `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_yq_ru }}**.
  1. In the left-hand panel, select **{{ ui-key.yql.yq-ide-aside.connections.tab-text }}**.
  1. Click ![info](../../_assets/console-icons/plus.svg)**{{ ui-key.yql.yq-connection-form.action_create-new }}**.
  1. Name the connection, e.g., `clickhouse`.
  1. Select the **{{ ui-key.yql.yq-connection.action_clickhouse }}** connection type.
  1. Under **{{ ui-key.yql.yq-connection-form.connection-type-parameters.section-title }}**:

     * **{{ ui-key.yql.yq-connection-form.cluster.input-label }}**: Select the `clickhouse` cluster you created earlier.
     * **{{ ui-key.yql.yq-connection-form.service-account.input-label }}**: Select the `yq-sa` service account.
     * Enter **{{ ui-key.yql.yq-connection-form.login.input-label }}** and **{{ ui-key.yql.yq-connection-form.password.input-label }}** you specified when creating the cluster.

  1. Click **{{ ui-key.yql.yq-connection-form.create.button-text }}**.

{% endlist %}

To check the connection, run a query in the notebook cell. Here is an example:

```sql
%yq SELECT * FROM clickhouse.items
```

## Connect to the {{ mpg-name }} data {#pg-connect}

For operations with {{ mpg-name }} data, you will need a cluster with a table and a {{ yq-name }} connection.

### Create a {{ mpg-name }} cluster {#cluster-pg}

Any running {{ mpg-name }} cluster with the **{{ ui-key.yacloud.mdb.forms.additional-field-yandex-query_ru }}** option enabled is suitable for sending queries.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select `data-folder`.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
  1. In the **{{ ui-key.yacloud.mdb.forms.base_field_name }}** field, enter the cluster name, e.g., `postgresql`.
  1. Under **{{ ui-key.yacloud.mdb.forms.section_database }}**:
     * Specify **{{ ui-key.yacloud.mdb.forms.database_field_name }}**, e.g., `db1`.
     * Specify **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}** and **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}**.
  1. Under **{{ ui-key.yacloud.mdb.forms.section_service-settings }}**, enable **{{ ui-key.yacloud.mdb.forms.additional-field-yandex-query_ru }}** and **{{ ui-key.yacloud.mdb.forms.additional-field-websql }}**.
  1. You can leave the other settings at their defaults.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_create }}**.

{% endlist %}

### Create a table {#table-pg}

The {{ mpg-name }} table will contain the names of the customers.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), open the `postgresql` cluster page and navigate to the **{{ ui-key.yacloud.postgresql.cluster.switch_explore }}** tab.
  1. Enter **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}** and **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}** you specified when creating the cluster.
  1. In the input window on the right, run the following SQL queries one by one:

     ```sql
     CREATE TABLE persons(person_id INT, name VARCHAR);
     ```
 
     ```sql
     INSERT INTO persons(person_id, name) values(1, 'Ivan')
     ```
 
     ```sql
     INSERT INTO persons(person_id, name) values(2, 'Peter')
     ```

{% endlist %}

### Create a connection to {{ mpg-name }} {#yq-pg}

{% list tabs group=instructions %}

- Management console {#console}
  
  1. In the [management console]({{ link-console-main }}), select `data-folder`.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_yq_ru }}**.
  1. In the left-hand panel, select **{{ ui-key.yql.yq-ide-aside.connections.tab-text }}**.
  1. Click ![info](../../_assets/console-icons/plus.svg)**{{ ui-key.yql.yq-connection-form.action_create-new }}**.
  1. Name the connection, e.g., `postgresql`.
  1. Select the **{{ ui-key.yql.yq-connection.action_postgersql }}** connection type.
  1. Under **{{ ui-key.yql.yq-connection-form.connection-type-parameters.section-title }}**:

     * **{{ ui-key.yql.yq-connection-form.cluster.input-label }}**: Select the `postgresql` cluster you created earlier.
     * **{{ ui-key.yql.yq-connection-form.service-account.input-label }}**: `yq-sa`.
     * **{{ ui-key.yql.yq-connection-form.database.input-label }}**: `db1`.
     * Enter **{{ ui-key.yql.yq-connection-form.login.input-label }}** and **{{ ui-key.yql.yq-connection-form.password.input-label }}** you specified when creating the cluster.

  1. Click **{{ ui-key.yql.yq-connection-form.create.button-text }}**.

{% endlist %}

To check the connection, run a query in the notebook cell. Here is an example:

```sql
%yq SELECT * FROM postgresql.persons
```

## Run a federated query {#federate-query}

Operations with federated data sources (cross-service analytics) are no different than those with regular data sources. You can simultaneously access external data sources, such as tables in the DB and data in {{ objstorage-name }}, from a query and perform any allowed YQL operations on them.

To combine and get data from all the three tables, run a federated query in a notebook cell:

```sql
%%yq SELECT
    v.person_id,
    date,
    p.name,
    c.description
FROM visits AS v
    INNER JOIN postgresql.persons AS p ON
        v.person_id = p.person_id
    INNER JOIN clickhouse.items AS c ON
        v.item_id = c.id
```

## How to delete the resources you created {#clear-out}

To stop paying for the resources you created:

* [Delete the bucket](../../storage/operations/buckets/delete.md).
* [Delete the {{ mch-name }} cluster](../../managed-clickhouse/operations/cluster-delete.md).
* [Delete the {{ mpg-name }} cluster](../../managed-postgresql/operations/cluster-delete.md).
* [Delete the project](../../datasphere/operations/projects/delete.md).

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}