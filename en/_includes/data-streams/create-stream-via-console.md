{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to create a [data stream](../../data-streams/concepts/glossary.md#stream-concepts).
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_data-streams }}**.
  1. Click **{{ ui-key.yacloud.data-streams.button_create-stream }}**.
  1. Specify the {{ ydb-full-name }} database or [create](../../ydb/quickstart.md#create-db) a new one. If you chose to create a new database, click ![refresh-button](../../_assets/console-icons/arrow-rotate-right.svg) after creating it to refresh the list of databases.
  1. Name the data stream. Follow these naming requirements:

     {% include [name-format](../name-format.md) %}

  1. Select a [pricing plan](../../data-streams/pricing.md) (for [serverless databases](../../ydb/concepts/serverless-and-dedicated.md#serverless)).
  1. Set the maximum [shard throughput](../../data-streams/concepts/glossary.md#shard-thoughput). A stream's throughput is equal to the product of the number of shards by the throughput of each of them.
  1. Optionally, enable [autopartitioning](../../data-streams/concepts/glossary.md#autopartitioning) and specify the following parameters:

     {% include [autopartitioning-params](autopartitioning-params.md) %}

     {% note warning %}

     Once a data stream is created, you can enable autopartitioning or change its settings, but you cannot disable it.

     You cannot reduce the current number of shards, if automatically increased, for the created stream.

     {% endnote %}

  1. Specify the number of [data shards](../../data-streams/concepts/glossary.md#shard).

     {% note warning %}

     You cannot reduce the number of shards for the created stream.

     {% endnote %}

  1. Select data storage settings:

     * `{{ ui-key.yacloud.data-streams.label_data-storage-size-limit }}`: Specify the maximum data storage size.
     * `{{ ui-key.yacloud.data-streams.label_data-storage-time-limit }}`: Specify the maximum [data retention period](../../data-streams/concepts/glossary.md#retention-time) for the stream.

  1. Click **{{ ui-key.yacloud.common.create }}**.

  Wait for the stream to start. Once the stream is ready for use, its status will change from `CREATING` to `ACTIVE`.

{% endlist %}
