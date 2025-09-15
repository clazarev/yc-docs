---
title: How to set up a {{ yds-full-name }} source endpoint in {{ data-transfer-full-name }}
description: Follow this guide to configure an {{ yds-full-name }} source endpoint in {{ data-transfer-full-name }}.
---

# Transferring data from a {{ yds-full-name }} source endpoint


{{ data-transfer-full-name }} enables you to migrate data from an {{ yds-name }} queue and implement various data transfer, processing, and transformation scenarios. To implement a transfer:

1. [Explore possible data transfer scenarios](#scenarios).
1. [Prepare the {{ yds-name }}](#prepare) database for the transfer.
1. [Set up a source endpoint](#endpoint-settings) in {{ data-transfer-full-name }}.
1. [Set up one of the supported data targets](#supported-targets).
1. [Create](../../transfer.md#create) a transfer and [start](../../transfer.md#activate) it.
1. Perform required operations with the database and [control the transfer](../../monitoring.md).
1. In case of any issues, [use ready-made solutions](#troubleshooting) to resolve them.

## Scenarios for transferring data from {{ yds-name }} {#scenarios}

1. {% include [migration](../../../../_includes/data-transfer/scenario-captions/migration.md) %}

   Mirroring data across {{ yds-name }} queues is a separate migration task.

1. {% include [queue](../../../../_includes/data-transfer/scenario-captions/queue.md) %}

    * [{{ DS }} to {{ CH }}](../../../tutorials/yds-to-clickhouse.md).
    * [{{ DS }} to {{ objstorage-name }}](../../../tutorials/yds-to-objstorage.md).
    * [{{ DS }} to {{ ydb-name }}](../../../tutorials/yds-to-ydb.md).
    * [{{ DS }} in {{ mkf-name }}](../../../tutorials/yds-to-kafka.md).

For a detailed description of possible {{ data-transfer-full-name }} scenarios, see [Tutorials](../../../tutorials/index.md).

## Preparing the source database {#prepare}

{% include [prepare yds db](../../../../_includes/data-transfer/endpoints/sources/yds-prepare.md) %}

## Configuring the {{ yds-name }} source endpoint {#endpoint-settings}

When [creating](../index.md#create) or [updating](../index.md#update) an endpoint, you can define:

* Stream [connection settings](#managed-service) in [{{ yds-full-name }}](#managed-service). These are required parameters.
* [Additional settings](#additional-settings).

### Basic settings {#managed-service}

{% list tabs group=instructions %}

- Management console {#console}

    {% include [Data Streams UI](../../../../_includes/data-transfer/necessary-settings/ui/yandex-data-streams-source.md) %}

{% endlist %}

### Advanced settings {#additional-settings}

{% include [Note processing order](../../../../_includes/data-transfer/notes/kafka-yds-evhub-processing-order.md) %}

{% list tabs group=instructions %}

- Management console {#console}

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSourceAdvancedSettings.transformer.title }}**:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.cloud_function.title }}**: Select one of the functions created in {{ sf-name }}.

            
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSConnection.service_account_id.title }}**: Select or [create](../../../../iam/operations/sa/create.md) a service account you are going to use to start the processing function.


        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.number_of_retries.title }}**: Set the number of attempts to invoke the processing function.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_size.title }}**: Set the size of the buffer (in bytes) which when full data will be transferred to the processing function.

            The maximum buffer size is 3.5 MB. For more information about restrictions that apply to functions in {{ sf-name }}, see the [relevant section](../../../../functions/concepts/limits.md).

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_flush_interval.title }}**: Set the interval (in seconds) to wait before transferring stream data to the processing function.

            {% note info %}

            If the buffer is full or the sending interval expires, data will be transferred to the processing function.

            {% endnote %}

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.invocation_timeout.title }}**: Set the allowed timeout of the response from the processing function (in seconds).

        {% note warning %}

        Values in the **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_flush_interval.title }}** and **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.invocation_timeout.title }}** fields are specified with the `s` postfix, e.g., `10s`.

        {% endnote %}

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSourceAdvancedSettings.converter.title }}**:

       * {% include [conversion-rules](../../../../_includes/data-transfer/fields/yds/ui/conversion-rules.md) %}    

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSourceAdvancedSettings.supported_codecs.title }}**: Set the required data compression format (`{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YdsSupportedCodecs.gzip.title }}`, `{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YdsSupportedCodecs.zstd.title }}`, or `{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YdsSupportedCodecs.raw.title }}`).

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSourceAdvancedSettings.allow_ttl_rewind.title }}**: Enable this setting to allow the transfer to start or continue reading from a topic where some data was deleted after its retention period had expired. If this setting is off, your transfer will stop with an error in those cases where topic data is deleted after reaching the end of its retention period. This usually happens when a transfer lacks time to migrate all data and the read delay exceeds the retention period.
    
        {% note warning %}   

        {% include [ttl](../../../../_includes/data-transfer/notes/ttl.md) %} 

        {% endnote %}

{% endlist %}


## Configuring the data target {#supported-targets}

Configure one of the supported data targets:

* [{{ PG }}](../target/postgresql.md)
* [{{ MY }}](../target/mysql.md)
* [{{ MG }}](../target/mongodb.md)
* [{{ CH }}](../target/clickhouse.md)
* [{{ GP }}](../target/greenplum.md)
* [{{ ydb-full-name }}](../target/yandex-database.md)
* [{{ objstorage-full-name }}](../target/object-storage.md)
* [{{ KF }}](../target/kafka.md)
* [{{ DS }}](../target/data-streams.md)
* [{{ OS }}](../target/opensearch.md)

For a complete list of supported sources and targets in {{ data-transfer-full-name }}, see [Available transfers](../../../transfer-matrix.md).

After configuring the data source and target, [create and start the transfer](../../transfer.md#create).

## Troubleshooting data transfer issues {#troubleshooting}

* [Transfer failure](#overloaded)
* [{{ sf-short-name }} redirects](#redirects)

For more troubleshooting tips, see [Troubleshooting](../../../troubleshooting/index.md).

{% include [overloaded](../../../../_includes/data-transfer/troubles/overloaded.md) %}

{% include [redirects](../../../../_includes/data-transfer/troubles/data-streams/data-streams-redirects.md) %}

