* **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.authorization_type.title }}**: Select a database connection option:

  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.authorization_plain.title }}**: Allows you to specify connection settings manually.

    Select **Managed Service for ClickHouse cluster** as the installation type and configure these settings:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}**: Select the cluster to connect to.

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.shard_group.title }}**: Specify the shard group to transfer the data to. If this value is not set, the data will go to all shards.

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Specify the name of the database in the selected cluster.

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.user.title }}**: Specify the username {{ data-transfer-name }} will use to connect to the database.

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.password.title }}**: Enter the user password for access to the database.

  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.authorization_connman.title }}**: Allows connecting to the cluster via [{{ connection-manager-full-name }}](../../../../metadata-hub/quickstart/connection-manager.md):

    * Select the folder with the {{ mch-name }} cluster.
    * Select **Managed DB cluster** as the installation type and configure these settings:

      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConnmanConnection.mdb_cluster_id.title }}**: Select the cluster to connect to.
      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConnmanConnection.connection_id.title }}**: Select or create a connection in {{ connection-manager-name }}.

      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Specify the name of the database in the selected cluster.

      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.shard_group.title }}**: Specify the shard group to transfer the data to. If this value is not set, the data will go to all shards.

    {% include [connection-manager-access](../../notes/connection-manager-access.md) %}


* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.security_groups.title }}**: Select the cloud network to host the endpoint and security groups for network traffic. This will allow you to apply the specified security group rules to the VMs and clusters in the selected network without changing their settings. For more information, see [{#T}](../../../../data-transfer/concepts/network.md).

  Make sure the selected security groups are [configured](../../../../managed-clickhouse/operations/connect/index.md#configuring-security-groups).
