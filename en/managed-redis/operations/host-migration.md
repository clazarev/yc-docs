---
title: Migrating {{ VLK }} cluster hosts to a different availability zone
description: Follow this guide to move hosts in a {{ VLK }} cluster to a different availability zone.
---

# Migrating {{ VLK }} cluster hosts to a different availability zone


{% include [zone-d-restrictions](../../_includes/mdb/ru-central1-d-restrictions.md) %}


{{ mrd-name }} cluster hosts reside in [availability zones](../../overview/concepts/geo-scope.md) {{ yandex-cloud }}. To move hosts from one zone to another, add hosts residing in the target availability zone to the cluster and delete the hosts in the source availability zone:

1. [Create a subnet](../../vpc/operations/subnet-create.md) in the availability zone you want to move your hosts to.
1. If your cluster uses the `b2.medium` or `b3-c1-m4` [host class](../concepts/instance-types.md#available-flavors), [change it](update.md#change-resource-preset). Otherwise, you will not be able to add hosts to the cluster and perform migration.

   The cluster is unavailable for about five to seven minutes after changing the host class.

   You can restore the original class after migration.

1. Add a host to your cluster:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-redis }}**.
      1. Click the cluster name and go to the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click ![image](../../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.mdb.cluster.hosts.action_add-host }}**.
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
      {{ yc-mdb-rd }} host add \
         --cluster-name <cluster_name> \
         --host zone-id=<availability_zone>,`
               `subnet-id=<new_subnet_ID>,`
               `assign-public-ip=<allow_public_access_to_host>
      ```

      You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

      In the `zone-id` parameter, specify the availability zone you are moving the hosts to.

   - {{ TF }} {#tf}

      1. Add a host manifest to the {{ TF }} configuration file with the infrastructure plan:

         ```hcl
         resource "yandex_mdb_redis_cluster" "<cluster_name>" {
           ...
           host {
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

       {% include [rest-add-hosts](../../_includes/mdb/mrd/api/rest-add-hosts.md) %}

   - gRPC API {#grpc-api}

       {% include [grpc-add-hosts](../../_includes/mdb/mrd/api/grpc-add-hosts.md) %}

   {% endlist %}

1. To successfully connect to the database after the migration is complete, specify the new host's FQDN in your backend or client (for example, in the code or graphical IDE). Delete the original host's FQDN in the source availability zone.

   To find out the FQDN, get a list of hosts in the cluster:

   ```bash
   {{ yc-mdb-rd }} host list --cluster-name <cluster_name>
   ```

   The FQDN is specified in the command output under `NAME`.

1. Delete the hosts in the source availability zone:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-redis }}**.
      1. Click the cluster name and open the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
      1. Click ![image](../../_assets/console-icons/ellipsis.svg) in the host's row, select **{{ ui-key.yacloud.common.delete }}**, and confirm the deletion.

   - CLI {#cli}

      Run the following command for each host:

      ```bash
      {{ yc-mdb-rd }} host delete <host_FQDN> --cluster-name <cluster_name>
      ```

   - {{ TF }} {#tf}

      1. In the {{ TF }} configuration file with the infrastructure plan, remove the `host` sections with the source availability zone from the cluster description.
      1. Make sure the settings are correct.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Type `yes` and press **Enter**.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - REST API {#api}

       {% include [rest-remove-hosts](../../_includes/mdb/mrd/api/rest-remove-hosts.md) %}

   - gRPC API {#grpc-api}

       {% include [grpc-remove-hosts](../../_includes/mdb/mrd/api/grpc-remove-hosts.md) %}

   {% endlist %}

1. Wait until the cluster status changes to **Alive**. In the management console, go to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-redis }}**. You can see the cluster status in the **{{ ui-key.yacloud.mdb.clusters.column_availability }}** column.
