---
editable: false
apiPlayground:
  - url: https://rest-assistant.{{ api-host }}/assistants/v1/threads/{threadId}
    method: delete
    path:
      type: object
      properties:
        threadId:
          description: |-
            **string**
            Required field. ID of the thread to delete.
          type: string
      required:
        - threadId
      additionalProperties: false
    query: null
    body: null
    definitions: null
sourcePath: en/_api-ref/ai/assistants/v1/threads/api-ref/Thread/delete.md
---

# Threads, REST: Thread.Delete

Delete a thread by its ID.

## HTTP request

```
DELETE https://rest-assistant.{{ api-host }}/assistants/v1/threads/{threadId}
```

## Path parameters

Request message for deleting a thread by ID.

#|
||Field | Description ||
|| threadId | **string**

Required field. ID of the thread to delete. ||
|#

## Response {#yandex.cloud.ai.assistants.v1.threads.DeleteThreadResponse}

**HTTP Code: 200 - OK**