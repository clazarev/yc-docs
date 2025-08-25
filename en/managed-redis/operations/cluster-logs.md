# Viewing {{ VLK }} cluster logs

{{ mrd-name }} allows you to [get a cluster log snippet](#get-log) for the selected period and [view logs in real time](#get-log-stream).

{% include [log-duration](../../_includes/mdb/log-duration.md) %}

## Getting a cluster log {#get-log}

{% list tabs group=instructions %}

- Management console {#console}

    1. Navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-redis }}**.
    1. Click the name of the cluster you need and select the ![image](../../_assets/console-icons/receipt.svg) **{{ ui-key.yacloud.mdb.cluster.logs.label_title }}** tab.
    1. Specify the time period you want the log info for: enter it manually or select in the calendar using the date input field.
    1. Select the hosts in the line with the date input field if needed.

    A list of log entries for the selected time period will be displayed. To view detailed information about an event, click the respective entry in the list.

    If there are too many entries and not all of them are displayed, click **{{ ui-key.yacloud.common.label_load-more }}** at the end of the list.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    1. View the description of the CLI command to view cluster logs:

        ```bash
        {{ yc-mdb-rd }} cluster list-logs --help
        ```

    1. Run the following command to get cluster logs (our example does not contain a complete list of available parameters):

        ```bash
        {{ yc-mdb-rd }} cluster list-logs <cluster_name_or_ID> \
           --limit <entry_number_limit> \
           --columns <list_of_columns_for_data_output> \
           --filter <entry_filtration_settings> \
           --since <time_range_left_boundary> \
           --until <time_range_right_boundary>
        ```

        Where:

        * {% include [logs output limit](../../_includes/cli/logs/limit.md) %}
        * `--columns`: List of data columns:
            * `hostname`: [Host name](hosts.md#list-hosts).
            * `message`: Message output by the service.
            * `pid`: ID of the current session’s server process.
            * `role`: Cluster component role, e.g., `X` or `M` (`Sentinel` and `Master`, respectively).
        * {% include [logs filter](../../_includes/cli/logs/filter.md) %}
        * {% include [logs since time](../../_includes/cli/logs/since.md) %}
        * {% include [logs until time](../../_includes/cli/logs/until.md) %}

    You can request the cluster name and ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

    {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [Cluster.ListLogs](../api-ref/Cluster/listLogs.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request GET \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --url 'https://{{ api-host-mdb }}/managed-redis/v1/clusters/<cluster_ID>:logs' \
            --url-query serviceType=REDIS \
            --url-query columnFilter=<column_list> \
            --url-query fromTime=<time_range_left_boundary> \
            --url-query toTime=<time_range_right_boundary>
        ```

        Where:

        * `serviceType`: Type of the service to request logs for. The possible value is `REDIS`.

        * `columnFilter`: List of data columns:

            * `hostname`: [Host name](hosts.md#list-hosts).
            * `message`: Message output by the service.
            * `pid`: ID of the current session’s server process.
            * `role`: Cluster component role, e.g., `X` or `M` (`Sentinel` and `Master`, respectively).

            {% include [column-filter-rest](../../_includes/mdb/api/column-filter-rest.md) %}

        {% include [from-time-rest](../../_includes/mdb/api/from-time-rest.md) %}

        * `toTime`: Right boundary of a time range, the format is the same as for `fromTime`.

        You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/Cluster/listLogs.md#yandex.cloud.mdb.redis.v1.ListClusterLogsResponse) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [ClusterService.ListLogs](../api-ref/grpc/Cluster/listLogs.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/redis/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<cluster_ID>",
                  "column_filter": [
                    "<column_1>", "<column_2>", ..., "<column_N>"
                  ],
                  "service_type": "<service_type>",
                  "from_time": "<time_range_left_boundary>",
                  "to_time": "<time_range_right_boundary>"
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.redis.v1.ClusterService.ListLogs
        ```

        Where:

        * `service_type`: Type of the service to request logs for. The possible value is `REDIS`.

        * `column_filter`: List of data columns:

            * `hostname`: [Host name](hosts.md#list-hosts).
            * `message`: Message output by the service.
            * `pid`: ID of the current session’s server process.
            * `role`: Cluster component role, e.g., `X` or `M` (`Sentinel` and `Master`, respectively).

            {% include [column-filter-grpc](../../_includes/mdb/api/column-filter-grpc.md) %}

        {% include [from-time-grpc](../../_includes/mdb/api/from-time-grpc.md) %}

        * `to_time`: Right boundary of a time range, the format is the same as for `from_time`.

        You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/grpc/Cluster/listLogs#yandex.cloud.mdb.redis.v1.ListClusterLogsResponse) to make sure your request was successful.

{% endlist %}

## Getting a cluster log stream {#get-log-stream}

This method allows you to get cluster logs in real time.

{% list tabs group=instructions %}

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To view cluster logs as they become available, run this command:

    ```bash
    {{ yc-mdb-rd }} cluster list-logs <cluster_name_or_ID> --follow
    ```

    You can request the cluster name and ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

    {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [Cluster.StreamLogs](../api-ref/Cluster/streamLogs.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request GET \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --url 'https://{{ api-host-mdb }}/managed-redis/v1/clusters/<cluster_ID>:stream_logs' \
            --url-query serviceType=<service_type> \
            --url-query columnFilter=<column_list> \
            --url-query fromTime=<time_range_left_boundary> \
            --url-query toTime=<time_range_right_boundary> \
            --url-query filter=<log_filter>
        ```

        Where:

        * `serviceType`: Type of the service to request logs for. The possible value is `REDIS`.

        * `columnFilter`: List of data columns:

            * `hostname`: [Host name](hosts.md#list-hosts).
            * `message`: Message output by the service.
            * `pid`: ID of the current session’s server process.
            * `role`: Cluster component role, e.g., `X` or `M` (`Sentinel` and `Master`, respectively).

            {% include [column-filter-rest](../../_includes/mdb/api/column-filter-rest.md) %}

        {% include [from-time-rest](../../_includes/mdb/api/from-time-rest.md) %}

        * `toTime`: Right boundary of a time range, the format is the same as for `fromTime`.

            {% include [tail-f-semantics](../../_includes/mdb/api/tail-f-semantics.md) %}

        * `filter`: Log filter, e.g., `message.hostname='node1.{{ dns-zone }}'`.

            For more information about filters and their syntax, see the [API reference](../api-ref/Cluster/streamLogs.md#query_params).

        You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/Cluster/streamLogs.md#yandex.cloud.mdb.redis.v1.StreamLogRecord) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [ClusterService.StreamLogs](../api-ref/grpc/Cluster/streamLogs.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/redis/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<cluster_ID>",
                  "column_filter": [
                    "<column_1>", "<column_2>", ..., "<column_N>"
                  ],
                  "service_type": "<service_type>",
                  "from_time": "<time_range_left_boundary>",
                  "to_time": "<time_range_right_boundary>",
                  "filter": "<log_filter>"
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.redis.v1.ClusterService.StreamLogs
        ```

        Where:

        * `service_type`: Type of the service to request logs for. The possible value is `REDIS`.

        * `column_filter`: List of data columns:

            * `hostname`: [Host name](hosts.md#list-hosts).
            * `message`: Message output by the service.
            * `pid`: ID of the current session’s server process.
            * `role`: Cluster component role, e.g., `X` or `M` (`Sentinel` and `Master`, respectively).

            {% include [column-filter-grpc](../../_includes/mdb/api/column-filter-grpc.md) %}

        {% include [from-time-grpc](../../_includes/mdb/api/from-time-grpc.md) %}

        * `to_time`: Right boundary of a time range, the format is the same as for `from_time`.

            {% include [tail-f-semantics](../../_includes/mdb/api/tail-f-semantics.md) %}

        * `filter`: Log filter, e.g., `message.hostname='node1.{{ dns-zone }}'`.

            For more information about filters and their syntax, see the [API reference](../api-ref/grpc/Cluster/streamLogs.md).

        You can request the cluster ID with the [list of clusters in the folder](cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/grpc/Cluster/streamLogs.md#yandex.cloud.mdb.redis.v1.StreamLogRecord) to make sure your request was successful.

{% endlist %}
