---
editable: false
apiPlayground:
  - url: https://rest-assistant.{{ api-host }}/files/v1/files/{fileId}
    method: get
    path:
      type: object
      properties:
        fileId:
          description: |-
            **string**
            Required field. ID of the file to retrieve.
          type: string
      required:
        - fileId
      additionalProperties: false
    query: null
    body: null
    definitions: null
sourcePath: en/_api-ref/ai/files/v1/files/api-ref/File/get.md
---

# Files API, REST: File.Get

Retrieve details of a specific file by its ID.

## HTTP request

```
GET https://rest-assistant.{{ api-host }}/files/v1/files/{fileId}
```

## Path parameters

Request message for retrieving a file by ID.

#|
||Field | Description ||
|| fileId | **string**

Required field. ID of the file to retrieve. ||
|#

## Response {#yandex.cloud.ai.files.v1.File}

**HTTP Code: 200 - OK**

```json
{
  "id": "string",
  "folderId": "string",
  "name": "string",
  "description": "string",
  "mimeType": "string",
  "createdBy": "string",
  "createdAt": "string",
  "updatedBy": "string",
  "updatedAt": "string",
  "expirationConfig": {
    "expirationPolicy": "string",
    "ttlDays": "string"
  },
  "expiresAt": "string",
  "labels": "object"
}
```

#|
||Field | Description ||
|| id | **string**

Unique identifier of the file. ||
|| folderId | **string**

ID of the folder that the file belongs to. ||
|| name | **string**

Name of the file. ||
|| description | **string**

Description of the file. ||
|| mimeType | **string**

MIME type of the file, indicating the file's format (e.g., "application/pdf") ||
|| createdBy | **string**

Identifier of the subject who created this file. ||
|| createdAt | **string** (date-time)

Timestamp representing when the file was created.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| updatedBy | **string**

Identifier of the subject who last updated this file. ||
|| updatedAt | **string** (date-time)

Timestamp representing the last time this file was updated.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| expirationConfig | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig)**

Configuration for the expiration of the file, defining when and how the file will expire. ||
|| expiresAt | **string** (date-time)

Timestamp representing when the file will expire.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs that can be used to organize and categorize the file. ||
|#

## ExpirationConfig {#yandex.cloud.ai.common.ExpirationConfig}

#|
||Field | Description ||
|| expirationPolicy | **enum** (ExpirationPolicy)

- `EXPIRATION_POLICY_UNSPECIFIED`
- `STATIC`
- `SINCE_LAST_ACTIVE` ||
|| ttlDays | **string** (int64) ||
|#