* {% include [Field Shards](../../fields/clickhouse/ui/shards.md) %}
* **Cluster**: Specify the name of the cluster to transfer the data to. If this parameter is not set, the data will go to the default cluster (the `{cluster}` macro).
* {% include [Field HTTP-port](../../fields/clickhouse/ui/http-port.md) %}
* {% include [Field Native Port](../../fields/clickhouse/ui/native-port.md) %}
* {% include [Field SSL connect](../../fields/clickhouse/ui/ssl-connect.md) %}
* {% include [Field CA Certificate](../../fields/clickhouse/ui/ca-certificate.md) %}
* 
  **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseOnPremise.subnet_id.title }}**: Select or [create](../../../../vpc/operations/subnet-create.md) a subnet in the required [availability zone](../../../../overview/concepts/geo-scope.md). The transfer will use this subnet to access the cluster.


  If this field has a value specified for both endpoints, both subnets must be hosted in the same availability zone.
* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.user.title }}**: Specify the username {{ data-transfer-name }} will use to connect to the database.
* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.password.title }}**: Enter the user password for access to the database.
* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Specify the name of the database in the selected cluster.
* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.security_groups.title }}**: Select the cloud network to host the endpoint and security groups for network traffic.

  Thus, you will be able to apply the specified security group rules to the VMs and clusters in the selected network without changing the settings of these VMs and clusters. For more information, see [{#T}](../../../../data-transfer/concepts/network.md).
