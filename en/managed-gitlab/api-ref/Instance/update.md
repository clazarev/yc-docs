---
editable: false
apiPlayground:
  - url: https://gitlab.{{ api-host }}/gitlab/v1/instances/{instanceId}
    method: patch
    path:
      type: object
      properties:
        instanceId:
          description: |-
            **string**
            ID of the GitLab instance to update.
          type: string
      additionalProperties: false
    query: null
    body:
      type: object
      properties:
        name:
          description: |-
            **string**
            Name of the instance (must be unique within the folder).
          type: string
        description:
          description: |-
            **string**
            Description of the instance.
          type: string
        labels:
          description: |-
            **object** (map<**string**, **string**>)
            Custom labels for the instance as `` key:value `` pairs. For example, "env": "prod"
          type: object
          additionalProperties:
            type: string
          maxProperties: 64
        backupRetainPeriodDays:
          description: |-
            **string** (int64)
            Number of days to retain backups.
          type: string
          format: int64
        resourcePresetId:
          description: |-
            **string**
            ID of the resource preset for computational resources.
          type: string
        maintenanceDeleteUntagged:
          description: |-
            **boolean**
            Whether to delete untagged resources during maintenance.
          type: boolean
        deletionProtection:
          description: |-
            **boolean**
            Whether deletion protection is enabled.
          type: boolean
        approvalRulesId:
          description: |-
            **string**
            ID of approval rules for the instance.
          type: string
        approvalRulesToken:
          description: |-
            **string**
            Token of approval rules for the instance.
          type: string
        diskSize:
          description: |-
            **string** (int64)
            Disk size in bytes.
          type: string
          format: int64
        updateMask:
          description: |-
            **string** (field-mask)
            A comma-separated names off ALL fields to be updated.
            Only the specified fields will be changed. The others will be left untouched.
            If the field is specified in `` updateMask `` and no value for that field was sent in the request,
            the field's value will be reset to the default. The default value for most fields is null or 0.
            If `` updateMask `` is not sent in the request, all fields' values will be updated.
            Fields specified in the request will be updated to provided values.
            The rest of the fields will be reset to the default.
          type: string
          format: field-mask
      additionalProperties: false
    definitions: null
sourcePath: en/_api-ref/gitlab/v1/api-ref/Instance/update.md
---

# Managed Service for Gitlab API, REST: Instance.Update

Updates GitLab instance.

## HTTP request

```
PATCH https://gitlab.{{ api-host }}/gitlab/v1/instances/{instanceId}
```

## Path parameters

Request message for InstanceService.Update.

#|
||Field | Description ||
|| instanceId | **string**

Required field. ID of the GitLab instance to update. ||
|#

## Body parameters {#yandex.cloud.gitlab.v1.UpdateInstanceRequest}

```json
{
  "name": "string",
  "description": "string",
  "labels": "object",
  "backupRetainPeriodDays": "string",
  "resourcePresetId": "string",
  "maintenanceDeleteUntagged": "boolean",
  "deletionProtection": "boolean",
  "approvalRulesId": "string",
  "approvalRulesToken": "string",
  "diskSize": "string",
  "updateMask": "string"
}
```

Request message for InstanceService.Update.

#|
||Field | Description ||
|| name | **string**

Name of the instance (must be unique within the folder). ||
|| description | **string**

Description of the instance. ||
|| labels | **object** (map<**string**, **string**>)

Custom labels for the instance as `` key:value `` pairs. For example, "env": "prod" ||
|| backupRetainPeriodDays | **string** (int64)

Number of days to retain backups. ||
|| resourcePresetId | **string**

ID of the resource preset for computational resources. ||
|| maintenanceDeleteUntagged | **boolean**

Whether to delete untagged resources during maintenance. ||
|| deletionProtection | **boolean**

Whether deletion protection is enabled. ||
|| approvalRulesId | **string**

ID of approval rules for the instance. ||
|| approvalRulesToken | **string**

Token of approval rules for the instance. ||
|| diskSize | **string** (int64)

Disk size in bytes. ||
|| updateMask | **string** (field-mask)

A comma-separated names off ALL fields to be updated.
Only the specified fields will be changed. The others will be left untouched.
If the field is specified in `` updateMask `` and no value for that field was sent in the request,
the field's value will be reset to the default. The default value for most fields is null or 0.

If `` updateMask `` is not sent in the request, all fields' values will be updated.
Fields specified in the request will be updated to provided values.
The rest of the fields will be reset to the default. ||
|#

## Response {#yandex.cloud.operation.Operation}

**HTTP Code: 200 - OK**

```json
{
  "id": "string",
  "description": "string",
  "createdAt": "string",
  "createdBy": "string",
  "modifiedAt": "string",
  "done": "boolean",
  "metadata": {
    "instanceId": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": {
    "code": "integer",
    "message": "string",
    "details": [
      "object"
    ]
  },
  "response": {
    "id": "string",
    "folderId": "string",
    "createdAt": "string",
    "updatedAt": "string",
    "name": "string",
    "description": "string",
    "labels": "object",
    "resourcePresetId": "string",
    "diskSize": "string",
    "status": "string",
    "adminLogin": "string",
    "adminEmail": "string",
    "domain": "string",
    "subnetId": "string",
    "plannedOperation": {
      "info": "string",
      "delayedUntil": "string",
      "latestMaintenanceTime": "string",
      "nextMaintenanceWindowTime": "string"
    },
    "backupRetainPeriodDays": "string",
    "maintenanceDeleteUntagged": "boolean",
    "deletionProtection": "boolean",
    "approvalRulesId": "string",
    "gitlabVersion": "string"
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
|| createdAt | **string** (date-time)

Creation timestamp.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| createdBy | **string**

ID of the user or service account who initiated the operation. ||
|| modifiedAt | **string** (date-time)

The time when the Operation resource was last modified.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| done | **boolean**

If the value is `false`, it means the operation is still in progress.
If `true`, the operation is completed, and either `error` or `response` is available. ||
|| metadata | **[UpdateInstanceMetadata](#yandex.cloud.gitlab.v1.UpdateInstanceMetadata)**

Service-specific metadata associated with the operation.
It typically contains the ID of the target resource that the operation is performed on.
Any method that returns a long-running operation should document the metadata type, if any. ||
|| error | **[Status](#google.rpc.Status)**

The error result of the operation in case of failure or cancellation.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|| response | **[Instance](#yandex.cloud.gitlab.v1.Instance)**

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

## UpdateInstanceMetadata {#yandex.cloud.gitlab.v1.UpdateInstanceMetadata}

Metadata message for InstanceService.Update.

#|
||Field | Description ||
|| instanceId | **string**

ID of the GitLab instance to update. ||
|#

## Status {#google.rpc.Status}

The error result of the operation in case of failure or cancellation.

#|
||Field | Description ||
|| code | **integer** (int32)

Error code. An enum value of [google.rpc.Code](https://github.com/googleapis/googleapis/blob/master/google/rpc/code.proto). ||
|| message | **string**

An error message. ||
|| details[] | **object**

A list of messages that carry the error details. ||
|#

## Instance {#yandex.cloud.gitlab.v1.Instance}

Instance represents a GitLab instance with its configuration and state.

#|
||Field | Description ||
|| id | **string**

Unique instance ID. ||
|| folderId | **string**

Folder ID where instance resides. ||
|| createdAt | **string** (date-time)

Creation timestamp.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| updatedAt | **string** (date-time)

Last update timestamp.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| name | **string**

Human-readable name. ||
|| description | **string**

Instance description. ||
|| labels | **object** (map<**string**, **string**>)

Resource labels as key-value pairs. ||
|| resourcePresetId | **string**

Resource preset ID. ||
|| diskSize | **string** (int64)

Disk size in bytes. ||
|| status | **enum** (Status)

Current instance status.

- `STATUS_UNSPECIFIED`: Default state.
- `CREATING`: Instance is being created.
- `RUNNING`: Instance is running normally.
- `UPDATING`: Instance is being updated.
- `ERROR`: Instance is in error state.
- `DELETING`: Instance is being deleted.
- `BACKUP_CREATING`: Backup is being created.
- `BACKUP_RESTORING`: Backup is being restored.
- `STARTING`: Instance is starting.
- `STOPPING`: Instance is stopping.
- `STOPPED`: Instance is stopped.
- `BACKGROUND_MIGRATIONS`: Background migrations in progress.
- `OBJECT_STORAGE_MIGRATIONS`: Object storage migrations in progress.
- `SNAPSHOT_RESTORING`: Snapshot is being restored. ||
|| adminLogin | **string**

Admin username. ||
|| adminEmail | **string**

Admin email. ||
|| domain | **string**

Instance domain. ||
|| subnetId | **string**

Subnet ID. ||
|| plannedOperation | **[MaintenanceOperation](#yandex.cloud.gitlab.v1.MaintenanceOperation)**

Planned maintenance operation. ||
|| backupRetainPeriodDays | **string** (int64)

How long to keep backups (days). ||
|| maintenanceDeleteUntagged | **boolean**

Delete untagged resources during maintenance. ||
|| deletionProtection | **boolean**

Protect from accidental deletion. ||
|| approvalRulesId | **string**

Approval rules ID. ||
|| gitlabVersion | **string**

GitLab version of the instance. ||
|#

## MaintenanceOperation {#yandex.cloud.gitlab.v1.MaintenanceOperation}

#|
||Field | Description ||
|| info | **string**

The description of the operation. ||
|| delayedUntil | **string** (date-time)

Delay time for the maintenance operation.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| latestMaintenanceTime | **string** (date-time)

Time of the last maintenance window.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| nextMaintenanceWindowTime | **string** (date-time)

Time of the next maintenance window.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|#