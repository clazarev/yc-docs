---
title: Migrating {{ CH }} cluster hosts to a different availability zone
description: Follow this guide to move hosts in a {{ CH }} cluster to a different availability zone.
---

# Migrating {{ CH }} cluster hosts to a different availability zone


{{ CH }} and {{ ZK }} hosts of the {{ mch-name }} cluster are located in {{ yandex-cloud }} [availability zones](../../overview/concepts/geo-scope.md). Follow this guide to migrate {{ CH }} and {{ ZK }} hosts to a different availability zone. If you want to migrate {{ CK }} hosts, contact [support]({{ link-console-support }}).

{% include [zone-d-restrictions](../../_includes/mdb/ru-central1-d-restrictions.md) %}

## Migrating {{ CH }} hosts {#clickhouse-hosts}

1. Make sure the migration will only include [replicated tables](../concepts/replication.md#replicated-tables) on the `ReplicatedMergeTree` family engine.

   Non-replicated tables will be lost during migration.

1. If you have created a cluster without [{{ CK }}](../concepts/replication.md#ck) support, make sure to [add at least three {{ ZK }} hosts](zk-hosts.md#add-zk) to ensure its fault-tolerance. Otherwise, you will not be able to add new hosts to [shards](../concepts/sharding.md) and perform migration.
1. [Create a subnet](../../vpc/operations/subnet-create.md) in the availability zone you want to move your hosts to.
1. Add a host to your cluster:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
      1. Click the cluster name and go to the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click **{{ ui-key.yacloud.mdb.cluster.hosts.action_add-host }}**.
      1. Specify the host parameters:

         * Availability zone to which you want to move the hosts.
         * New subnet.
         * Select **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}** if the host must be accessible from outside {{ yandex-cloud }}.

      1. Click **{{ ui-key.yacloud.mdb.hosts.dialog.button_choose }}**.

   - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      Run this command:

      ```bash
      {{ yc-mdb-ch }} host add \
         --cluster-name <cluster_name> \
         --host type=clickhouse,`
               `zone-id=<availability_zone>,`
               `subnet-id=<new_subnet_ID>,`
               `assign-public-ip=<allow_public_access_to_host>
      ```

      You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters). In the `zone-id` parameter, specify the availability zone you are moving the hosts to.

   - {{ TF }} {#tf}

      1. Add a host manifest to the {{ TF }} configuration file with the infrastructure plan:

         ```hcl
         resource "yandex_mdb_clickhouse_cluster" "<cluster_name>" {
           ...
           host {
             type             = "CLICKHOUSE"
             zone             = "<availability_zone>"
             subnet_id        = "<new_subnet_ID>"
             assign_public_ip = <allow_public_access_to_host>
           }
         }
         ```

         In the `zone` parameter, specify the availability zone you are moving the hosts to.

      1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Confirm updating the resources.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - REST API {#api}

      1. [Get an IAM token for API authentication](../api-ref/authentication.md) and put it into the environment variable:

         {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

      1. Use the [Cluster.AddHosts](../api-ref/Cluster/addHosts.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

         ```bash
         curl \
             --request POST \
             --header "Authorization: Bearer $IAM_TOKEN" \
             --header "Content-Type: application/json" \
             --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/hosts:batchCreate' \
             --data '{
                       "hostSpecs": [
                         {
                           "type": "CLICKHOUSE",
                           "zoneId": "<availability_zone>",
                           "subnetId": "<subnet_ID>",
                           "assignPublicIp": <allow_public_access_to_host>
                         }
                       ]
                     }'
         ```

         Where `hostSpecs` is an array with settings for the new hosts. One array element contains settings for a single host and has the following structure:

         * `type`: Host type, which is always `CLICKHOUSE` for {{ CH }} hosts.
         * `zoneId`: Availability zone.
         * `subnetId`: Subnet ID.
         * `assignPublicIp`: Internet access to the host via a public IP address, `true` or `false`.

         You can request the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

      1. View the [server response](../api-ref/Cluster/addHosts.md#yandex.cloud.operation.Operation) to make sure the request was successful.

   - gRPC API {#grpc-api}

      1. [Get an IAM token for API authentication](../api-ref/authentication.md) and put it into the environment variable:

         {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

      1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
      1. Use the [ClusterService.AddHosts](../api-ref/grpc/Cluster/addHosts.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

         ```bash
         grpcurl \
             -format json \
             -import-path ~/cloudapi/ \
             -import-path ~/cloudapi/third_party/googleapis/ \
             -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
             -rpc-header "Authorization: Bearer $IAM_TOKEN" \
             -d '{
                     "cluster_id": "<cluster_ID>",
                     "host_specs": [
                         {
                             "type": "CLICKHOUSE",
                             "zone_id": "<availability_zone>",
                             "subnet_id": "<subnet_ID>",
                             "assign_public_ip": <allow_public_access_to_host>
                         }
                     ]
                 }' \
             {{ api-host-mdb }}:{{ port-https }} \
             yandex.cloud.mdb.clickhouse.v1.ClusterService.AddHosts
         ```

         Where `host_specs` is an array with settings for the new hosts. One array element contains settings for a single host and has the following structure:

         * `type`: Host type, which is always `CLICKHOUSE` for {{ CH }} hosts.
         * `zone_id`: Availability zone.
         * `subnet_id`: Subnet ID.
         * `assign_public_ip`: Internet access to the host via a public IP address, `true` or `false`.

         You can request the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

      1. View the [server response](../api-ref/grpc/Cluster/addHosts.md#yandex.cloud.operation.Operation) to make sure the request was successful.

   {% endlist %}

1. To successfully connect to the database after the migration is complete, specify the new host's FQDN in your backend or client (for example, in the code or graphical IDE). Delete the original host's FQDN in the source availability zone.

   To find out the FQDN, get a list of hosts in the cluster:

   ```bash
   {{ yc-mdb-ch }} host list --cluster-name <cluster_name>
   ```

   The FQDN is specified in the command output under `NAME`. You can also use a [special FQDN](connect/fqdn.md#auto) for a connection.

1. Delete the hosts in the source availability zone:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
      1. Click the cluster name and open the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click ![image](../../_assets/console-icons/ellipsis.svg) in the host's row, select **{{ ui-key.yacloud.common.delete }}**, and confirm the deletion.

   - CLI {#cli}

      Run the following command for each host:

      ```bash
      {{ yc-mdb-ch }} host delete <host_FQDN> --cluster-name <cluster_name>
      ```

   - {{ TF }} {#tf}

      1. In the {{ TF }} configuration file with the infrastructure plan, remove the `host` sections with the source availability zone from the cluster description.
      1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Type `yes` and press **Enter**.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - REST API {#api}

      {% include [delete-hosts-for-migration](../../_includes/mdb/mch/api/delete-hosts-for-migration-rest.md) %}

   - gRPC API {#grpc-api}

      {% include [delete-hosts-for-migration](../../_includes/mdb/mch/api/delete-hosts-for-migration-grpc.md) %}

   {% endlist %}

1. Wait until the cluster status changes to **Alive**. In the management console, go to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**. You can see the cluster status in the **{{ ui-key.yacloud.mdb.clusters.column_availability }}** column.

## Migrating {{ ZK }} hosts {#zookeeper-hosts}

1. [Create a subnet](../../vpc/operations/subnet-create.md) in the availability zone you want to move your hosts to.
1. Add a host to your cluster:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
      1. Click the cluster name and go to the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click **{{ ui-key.yacloud.mdb.cluster.hosts.button_add-zookeeper }}**.
      1. Specify the new subnet and the availability zone to move the hosts to.
      1. Click **{{ ui-key.yacloud.mdb.hosts.dialog.button_choose }}**.

   - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      Run this command:

      ```bash
      {{ yc-mdb-ch }} host add \
         --cluster-name <cluster_name> \
         --host type=zookeeper,`
               `zone-id=<availability_zone>,`
               `subnet-id=<new_subnet_ID>,`
               `assign-public-ip=<allow_public_access_to_host>
      ```

      You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters). In the `zone-id` parameter, specify the availability zone you are moving the hosts to.

   - {{ TF }} {#tf}

      1. Add a host manifest to the {{ TF }} configuration file with the infrastructure plan:

         ```hcl
         resource "yandex_mdb_clickhouse_cluster" "<cluster_name>" {
           ...
           host {
             type             = "ZOOKEEPER"
             zone             = "<availability_zone>"
             subnet_id        = "<new_subnet_ID>"
             assign_public_ip = <allow_public_access_to_host>
           }
         }
         ```

         In the `zone` parameter, specify the availability zone you are moving the hosts to.

      1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Confirm updating the resources.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - REST API {#api}

      1. [Get an IAM token for API authentication](../api-ref/authentication.md) and put it into the environment variable:

         {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

      1. Use the [Cluster.AddHosts](../api-ref/Cluster/addHosts.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

         ```bash
         curl \
             --request POST \
             --header "Authorization: Bearer $IAM_TOKEN" \
             --header "Content-Type: application/json" \
             --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/hosts:batchCreate' \
             --data '{
                       "hostSpecs": [
                         {
                           "type": "ZOOKEEPER",
                           "zoneId": "<availability_zone>",
                           "subnetId": "<subnet_ID>",
                           "assignPublicIp": <allow_public_access_to_host>
                         }
                       ]
                     }'
         ```

         Where `hostSpecs` is an array with settings for the new host. One array element contains settings for a single host and has the following structure:

         * `type`: `ZOOKEEPER` host type.
         * `zoneId`: Availability zone.
         * `subnetId`: Subnet ID.
         * `assignPublicIp`: Internet access to the host via a public IP address, `true` or `false`.

         You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

      1. View the [server response](../api-ref/Cluster/addHosts.md#yandex.cloud.operation.Operation) to make sure the request was successful.

   - gRPC API {#grpc-api}

      1. [Get an IAM token for API authentication](../api-ref/authentication.md) and put it into the environment variable:

         {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

      1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
      1. Use the [ClusterService.AddHosts](../api-ref/grpc/Cluster/addHosts.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

         ```bash
         grpcurl \
             -format json \
             -import-path ~/cloudapi/ \
             -import-path ~/cloudapi/third_party/googleapis/ \
             -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
             -rpc-header "Authorization: Bearer $IAM_TOKEN" \
             -d '{
                     "cluster_id": "<cluster_ID>",
                     "host_specs": [
                         {
                             "type": "ZOOKEEPER",
                             "zone_id": "<availability_zone>",
                             "subnet_id": "<subnet_ID>",
                             "assign_public_ip": <allow_public_access_to_host>
                         }
                 }' \
             {{ api-host-mdb }}:{{ port-https }} \
             yandex.cloud.mdb.clickhouse.v1.ClusterService.AddHosts
         ```

         Where `host_specs` is an array with settings for the new hosts. One `host_specs` array element contains settings for a single host and has the following structure:

         * `type`: `ZOOKEEPER` host type.
         * `zone_id`: Availability zone.
         * `subnet_id`: Subnet ID.
         * `assign_public_ip`: Internet access to the host via a public IP address, `true` or `false`.

         You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

      1. View the [server response](../api-ref/grpc/Cluster/addHosts.md#yandex.cloud.operation.Operation) to make sure the request was successful.

   {% endlist %}

1. Delete the hosts in the source availability zone:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
      1. Click the cluster name and open the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click ![image](../../_assets/console-icons/ellipsis.svg) in the host's row, select **{{ ui-key.yacloud.common.delete }}**, and confirm the deletion.

   - CLI {#cli}

      Run the following command for each host:

      ```bash
      {{ yc-mdb-ch }} host delete <host_FQDN> --cluster-name <cluster_name>
      ```

   - {{ TF }} {#tf}

      1. In the {{ TF }} configuration file with the infrastructure plan, remove the `host` sections with the source availability zone from the cluster description.
      1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Type `yes` and press **Enter**.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - REST API {#api}

      {% include [delete-hosts-for-migration](../../_includes/mdb/mch/api/delete-hosts-for-migration-rest.md) %}

   - gRPC API {#grpc-api}

      {% include [delete-hosts-for-migration](../../_includes/mdb/mch/api/delete-hosts-for-migration-grpc.md) %}

   {% endlist %}

1. Wait until the cluster status changes to **Alive**. In the management console, go to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**. You can see the cluster status in the **{{ ui-key.yacloud.mdb.clusters.column_availability }}** column.

{% include [migration-in-data-transfer](../../_includes/data-transfer/migration-in-data-transfer.md) %}

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}
