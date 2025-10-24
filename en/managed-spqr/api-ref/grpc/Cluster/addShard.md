---
editable: false
sourcePath: en/_api-ref-grpc/mdb/spqr/v1/api-ref/grpc/Cluster/addShard.md
---

# Managed Service for SPQR API, gRPC: ClusterService.AddShard

Creates a new shard.

## gRPC request

**rpc AddShard ([AddClusterShardRequest](#yandex.cloud.mdb.spqr.v1.AddClusterShardRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## AddClusterShardRequest {#yandex.cloud.mdb.spqr.v1.AddClusterShardRequest}

```json
{
  "cluster_id": "string",
  "shard_spec": {
    "shard_name": "string",
    // Includes only one of the fields `mdb_postgresql`
    "mdb_postgresql": {
      "cluster_id": "string"
    }
    // end of the list of possible fields
  }
}
```

#|
||Field | Description ||
|| cluster_id | **string**

Required field. ID of the SPQR cluster to add a shard to.
To get the cluster ID, use a [ClusterService.List](/docs/managed-spqr/api-ref/grpc/Cluster/list#List) request. ||
|| shard_spec | **[ShardSpec](#yandex.cloud.mdb.spqr.v1.ShardSpec)**

Required field. Properties of the shard to be created. ||
|#

## ShardSpec {#yandex.cloud.mdb.spqr.v1.ShardSpec}

#|
||Field | Description ||
|| shard_name | **string**

Required field. Name of the SPQR shard to create. ||
|| mdb_postgresql | **[MDBPostgreSQL](#yandex.cloud.mdb.spqr.v1.MDBPostgreSQL)**

Properties of the MDB PostgreSQL cluster

Includes only one of the fields `mdb_postgresql`. ||
|#

## MDBPostgreSQL {#yandex.cloud.mdb.spqr.v1.MDBPostgreSQL}

TODO move to hosts.proto when it's created
Configuration for MDB PostgreSQL host

#|
||Field | Description ||
|| cluster_id | **string**

Required field.  ||
|#

## operation.Operation {#yandex.cloud.operation.Operation}

```json
{
  "id": "string",
  "description": "string",
  "created_at": "google.protobuf.Timestamp",
  "created_by": "string",
  "modified_at": "google.protobuf.Timestamp",
  "done": "bool",
  "metadata": {
    "cluster_id": "string",
    "shard_name": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "name": "string",
    "cluster_id": "string"
  }
  // end of the list of possible fields
}
```

An Operation resource. For more information, see [Operation](/docs/api-design-guide/concepts/operation).

#|
||Field | Description ||
|| id | **string**

ID of the operation. ||
|| description | **string**

Description of the operation. 0-256 characters long. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| created_by | **string**

ID of the user or service account who initiated the operation. ||
|| modified_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

The time when the Operation resource was last modified. ||
|| done | **bool**

If the value is `false`, it means the operation is still in progress.
If `true`, the operation is completed, and either `error` or `response` is available. ||
|| metadata | **[AddClusterShardMetadata](#yandex.cloud.mdb.spqr.v1.AddClusterShardMetadata)**

Service-specific metadata associated with the operation.
It typically contains the ID of the target resource that the operation is performed on.
Any method that returns a long-running operation should document the metadata type, if any. ||
|| error | **[google.rpc.Status](https://cloud.google.com/tasks/docs/reference/rpc/google.rpc#status)**

The error result of the operation in case of failure or cancellation.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|| response | **[Shard](#yandex.cloud.mdb.spqr.v1.Shard)**

The normal response of the operation in case of success.
If the original method returns no data on success, such as Delete,
the response is [google.protobuf.Empty](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#google.protobuf.Empty).
If the original method is the standard Create/Update,
the response should be the target resource of the operation.
Any method that returns a long-running operation should document the response type, if any.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|#

## AddClusterShardMetadata {#yandex.cloud.mdb.spqr.v1.AddClusterShardMetadata}

#|
||Field | Description ||
|| cluster_id | **string**

ID of the SPQR cluster that a shard is being added to. ||
|| shard_name | **string**

Name of the shard being added. ||
|#

## Shard {#yandex.cloud.mdb.spqr.v1.Shard}

#|
||Field | Description ||
|| name | **string**

Name of the shard. ||
|| cluster_id | **string**

ID of the cluster that the shard belongs to. ||
|#