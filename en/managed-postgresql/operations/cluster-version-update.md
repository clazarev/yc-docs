# {{ PG }} version upgrade

You can upgrade a {{ mpg-name }} cluster to version 17 or lower.

{% note info %}

You cannot upgrade a regular cluster version to versions optimized for _1C:Enterprise_ (e.g., from version 14 to version 14-1c).

{% endnote %}

{% note warning %}

To upgrade the version, the cluster storage must have at least 10% free space, with a minimum of 10 GB.

{% endnote %}

You can only upgrade to a version that immediately follows the current one, e.g., from version 14 to 15. Upgrades to higher versions are performed in steps. For example, for {{ PG }}, the upgrade sequence from version 13 to 15 is: 13 → 14 → 15.

In single-host clusters, the only master host is brought out of its running state for upgrades. During an upgrade, these clusters will be unavailable for reading and writing.

In multi-host clusters, upgrades follow the procedure below:

1. The master is unavailable during upgrades. During this time, the replicas continue running in read-only mode. No [failover](../concepts/replication.md#replication-auto) occurs. After an upgrade, the master is not returned to a running state until all the replicated hosts are upgraded. It is temporarily unavailable even for reading.
1. The replicas are sequentially made unavailable and upgraded. The replicas are queued randomly. Following an upgrade, the replicas are returned to a running state in read-only mode.

    A two-host cluster is unavailable while its replica is upgrading. In a cluster of three or more hosts, at least one replica will be available for reading.

1. The master returns to a running state.

To learn more about updates within a single version and host maintenance, see [Maintenance](../concepts/maintenance.md).

## Before a version upgrade {#before-update}

Make sure the upgrade does not disrupt your applications:

1. See {{ PG }} [changelog](https://www.postgresql.org/docs/release/) for how upgrades may affect your applications or installed [extensions](./extensions/cluster-extensions.md).
1. Try a version upgrade on a test cluster. You can [deploy](cluster-backups.md#restore) it from a backup of the main cluster.
1. [Create a backup](cluster-backups.md) of the main cluster directly before the version upgrade.

## Upgrading a cluster {#start-update}

{% note alert %}

* Once your DBMS is upgraded, you cannot roll a cluster back to the previous version.
* The success of a {{ PG }} version upgrade depends on multiple factors, including cluster settings and data stored in databases. We recommend that you begin by [upgrading a test cluster](#before-update) that has the same data and settings.

{% endnote %}

{% list tabs group=instructions %}

- Management console {#console}

  1. Navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Select the cluster you need from the list and click ![image](../../_assets/console-icons/pencil.svg) **{{ ui-key.yacloud.mdb.clusters.button_action-edit }}**.
  1. In the **{{ ui-key.yacloud.mdb.forms.base_field_version }}** field, select a new version number.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To upgrade a cluster to {{ PG }} 15 or lower:

  1. Get a list of your {{ PG }} clusters using this command:

     ```bash
     {{ yc-mdb-pg }} cluster list
     ```

  1. Get information about the cluster you need and check the {{ PG }} version in the `config.version` parameter:

     ```bash
     {{ yc-mdb-pg }} cluster get <cluster_name_or_ID>
     ```

  1. Run the {{ PG }} upgrade:

     ```bash
     {{ yc-mdb-pg }} cluster update <cluster_name_or_ID> \
        --postgresql-version <new_version_number>
     ```

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

       For more information about creating this file, see [this guide](cluster-create.md).

       For a complete list of available {{ mpg-name }} cluster configuration fields, see the [{{ TF }} provider documentation]({{ tf-provider-mpg }}).

    1. In the `cluster_config` section of the required {{ mpg-name }} cluster, add the `version` field or edit the existing one:

       ```hcl
       resource "yandex_mdb_postgresql_cluster" "<cluster_name>" {
         ...
         cluster_config {
           version = "<{{ PG }}_version>"
         }
       }
       ```

    1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

         {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- REST API {#api}

  1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Use the [Cluster.Update](../api-ref/Cluster/update.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

     {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

     ```bash
     curl \
       --request PATCH \
       --header "Authorization: Bearer $IAM_TOKEN" \
       --header "Content-Type: application/json" \
       --url 'https://{{ api-host-mdb }}/managed-postgresql/v1/clusters/<cluster_ID>' \
       --data '{
                 "updateMask": "configSpec.version",
                 "configSpec": {
                   "version": "<{{ PG }}_version>"
                 }
               }'
     ```

     Where:

     * `updateMask`: List of parameters to update as a single string, separated by commas.

       In this case, only one parameter is provided.

     * `configSpec.version`: New {{ PG }} version.

     You can get the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

  1. View the [server response](../api-ref/Cluster/update.md#yandex.cloud.operation.Operation) to make sure your request was successful.

- gRPC API {#grpc-api}

  1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
  1. Use the [ClusterService.Update](../api-ref/grpc/Cluster/update.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

     {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

     ```bash
     grpcurl \
       -format json \
       -import-path ~/cloudapi/ \
       -import-path ~/cloudapi/third_party/googleapis/ \
       -proto ~/cloudapi/yandex/cloud/mdb/postgresql/v1/cluster_service.proto \
       -rpc-header "Authorization: Bearer $IAM_TOKEN" \
       -d '{
             "cluster_id": "<cluster_ID>",
             "update_mask": {
               "paths": [
                 "config_spec.version"
               ]
             },
             "config_spec": {
               "version": "<{{ PG }}_version>"
             }
           }' \
       {{ api-host-mdb }}:{{ port-https }} \
       yandex.cloud.mdb.postgresql.v1.ClusterService.Update
     ```

     Where:

     * `update_mask`: List of parameters to update as an array of `paths[]` strings.

       Only one parameter is provided in this case.

     * `config_spec.version`: New {{ PG }} version.

     You can get the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

  1. View the [server response](../api-ref/grpc/Cluster/update.md#yandex.cloud.operation.Operation) to make sure your request was successful.

{% endlist %}

As soon as you run the upgrade, the cluster status will change to **UPDATING**. Wait for the operation to complete and then check the cluster version.

Cluster upgrade time depends on the database size.

{% note tip %}

Contact [support](../../support/overview.md) if you have issues upgrading to version 17.

{% endnote %}

## Examples {#examples}

Let's assume you need to upgrade a cluster from version 14 to version 15.

{% list tabs group=instructions %}

- CLI {#cli}

   1. To get a list of clusters and find out their IDs and names, run this command:

      ```bash
      {{ yc-mdb-pg }} cluster list
      ```

      ```text
      +----------------------+---------------+---------------------+--------+---------+
      |          ID          |     NAME      |     CREATED AT      | HEALTH | STATUS  |
      +----------------------+---------------+---------------------+--------+---------+
      | c9q8p8j2gaih******** |   postgre406  | 2021-10-23 12:44:17 | ALIVE  | RUNNING |
      +----------------------+---------------+---------------------+--------+---------+
      ```

   1. To get information about a cluster named `postgre406`, run the following command:

      ```bash
      {{ yc-mdb-pg }} cluster get postgre406
      ```

      ```text
        id: c9q8p8j2gaih********
        ...
        config:
          version: "14"
          ...
      ```

   1. To upgrade the `postgre406` cluster to version 15, run this command:

      ```bash
      {{ yc-mdb-pg }} cluster update postgre406 --postgresql-version 15
      ```

{% endlist %}

