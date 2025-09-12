---
title: Deleting an {{ AF }} cluster
description: You can delete an {{ AF }} cluster if you no longer need it.
keywords:
  - deleting an {{ AF }} cluster
  - '{{ AF }} cluster'
  - '{{ AF }}'
  - Airflow
---

# Deleting an {{ AF }} cluster

You can delete an {{ AF }} cluster if you no longer need it.

## Deleting a cluster {#delete}

Before deleting a cluster, disable its deletion protection if it is enabled.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder where you want to delete a cluster.
  1. Select **{{ maf-name }}**.
  1. Click ![image](../../_assets/console-icons/ellipsis.svg) for the cluster and select **{{ ui-key.yacloud.mdb.cluster.overview.button_action-delete }}**.
  1. In the window that opens, confirm the deletion and click **{{ ui-key.yacloud.mdb.dialogs.popup-confirm-delete-cluster_button }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To delete a cluster, run this command:

  ```bash
  {{ yc-mdb-af }} cluster delete <cluster_name_or_ID>
  ```

  You can get the cluster ID and name with the [list of clusters in the folder](../operations/cluster-list.md#list-clusters).

- {{ TF }} {#tf}

  {% include [terraform-delete-mdb-cluster](../../_includes/mdb/terraform-delete-mdb-cluster.md) %}

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [Cluster.Delete](../api-ref/Cluster/delete.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request DELETE \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --url 'https://{{ api-host-airflow }}/managed-airflow/v1/clusters/<cluster_ID>'
        ```

        You can get the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/Cluster/delete.md#yandex.cloud.operation.Operation) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [ClusterService.Delete](../api-ref/grpc/Cluster/delete.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/airflow/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                    "cluster_id": "<cluster_ID>"
                }' \
            {{ api-host-airflow }}:{{ port-https }} \
            yandex.cloud.airflow.v1.ClusterService.Delete
        ```

        You can get the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/grpc/Cluster/create.md#yandex.cloud.operation.Operation) to make sure your request was successful.

{% endlist %}
