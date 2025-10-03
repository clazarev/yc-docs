---
editable: false
apiPlayground:
  - url: https://{{ api-host-sk-stt }}/stt/v3/deleteRecognition
    method: delete
    path: null
    query:
      type: object
      properties:
        operationId:
          description: '**string**'
          type: string
      additionalProperties: false
    body: null
    definitions: null
sourcePath: en/_api-ref/ai/stt/v3/stt-v3/api-ref/AsyncRecognizer/deleteRecognition.md
---

# SpeechKit Recognition API v3, REST: AsyncRecognizer.DeleteRecognition

Deletes results of asynchronous recognition by operation ID.

## HTTP request

```
DELETE https://{{ api-host-sk-stt }}/stt/v3/deleteRecognition
```

## Query parameters {#speechkit.stt.v3.DeleteRecognitionRequest}

#|
||Field | Description ||
|| operationId | **string** ||
|#

## Response {#google.protobuf.Empty}

**HTTP Code: 200 - OK**