---
editable: false
apiPlayground:
  - url: https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/{clusterId}/shardGroups
    method: get
    path:
      type: object
      properties:
        clusterId:
          description: |-
            **string**
            Required field. ID of the cluster that the shard group belongs to.
            To get the cluster ID, make a [ClusterService.List](/docs/managed-clickhouse/api-ref/Cluster/list#List) request.
          type: string
      required:
        - clusterId
      additionalProperties: false
    query:
      type: object
      properties:
        pageSize:
          description: |-
            **string** (int64)
            The maximum number of results per page to return.
            If the number of available results is larger than `pageSize`, the service returns a [ListClusterShardGroupsResponse.nextPageToken](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsResponse) that can be used to get the next page of results in subsequent list requests.
          type: string
          format: int64
        pageToken:
          description: |-
            **string**
            Page token.
            To get the next page of results, set `pageToken` to the [ListClusterShardGroupsResponse.nextPageToken](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsResponse) returned by the previous list request.
          type: string
      additionalProperties: false
    body: null
    definitions: null
sourcePath: en/_api-ref/mdb/clickhouse/v1/api-ref/Cluster/listShardGroups.md
---

# Managed Service for ClickHouse API, REST: Cluster.ListShardGroups

Retrieves a list of shard groups that belong to specified cluster.

## HTTP request

```
GET https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/{clusterId}/shardGroups
```

## Path parameters

#|
||Field | Description ||
|| clusterId | **string**

Required field. ID of the cluster that the shard group belongs to.

To get the cluster ID, make a [ClusterService.List](/docs/managed-clickhouse/api-ref/Cluster/list#List) request. ||
|#

## Query parameters {#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsRequest}

#|
||Field | Description ||
|| pageSize | **string** (int64)

The maximum number of results per page to return.

If the number of available results is larger than `pageSize`, the service returns a [ListClusterShardGroupsResponse.nextPageToken](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsResponse) that can be used to get the next page of results in subsequent list requests. ||
|| pageToken | **string**

Page token.

To get the next page of results, set `pageToken` to the [ListClusterShardGroupsResponse.nextPageToken](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsResponse) returned by the previous list request. ||
|#

## Response {#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsResponse}

**HTTP Code: 200 - OK**

```json
{
  "shardGroups": [
    {
      "name": "string",
      "clusterId": "string",
      "description": "string",
      "shardNames": [
        "string"
      ]
    }
  ],
  "nextPageToken": "string"
}
```

#|
||Field | Description ||
|| shardGroups[] | **[ShardGroup](#yandex.cloud.mdb.clickhouse.v1.ShardGroup)**

List of ClickHouse cluster's shard groups. ||
|| nextPageToken | **string**

This token allows you to get the next page of results for list requests.

If the number of results is larger than [ListClusterShardGroupsRequest.pageSize](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsRequest), use the `nextPageToken` as the value for the [ListClusterShardGroupsRequest.pageToken](#yandex.cloud.mdb.clickhouse.v1.ListClusterShardGroupsRequest) parameter in the next list request.
Each subsequent list request will have its own `nextPageToken` to continue paging through the results. ||
|#

## ShardGroup {#yandex.cloud.mdb.clickhouse.v1.ShardGroup}

#|
||Field | Description ||
|| name | **string**

Name of the shard group. ||
|| clusterId | **string**

ID of the ClickHouse cluster that the shard group belongs to. ||
|| description | **string**

Description of the shard group. 0-256 characters long. ||
|| shardNames[] | **string**

List of shard names contained in the shard group. ||
|#