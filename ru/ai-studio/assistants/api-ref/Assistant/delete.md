---
editable: false
apiPlayground:
  - url: https://rest-assistant.{{ api-host }}/assistants/v1/assistants/{assistantId}
    method: delete
    path:
      type: object
      properties:
        assistantId:
          description: |-
            **string**
            Required field. ID of the assistant to delete.
          type: string
      required:
        - assistantId
      additionalProperties: false
    query: null
    body: null
    definitions: null
sourcePath: en/_api-ref/ai/assistants/v1/assistants/api-ref/Assistant/delete.md
---

# AI Assistants API, REST: Assistant.Delete

Delete an assistant by its ID.

## HTTP request

```
DELETE https://rest-assistant.{{ api-host }}/assistants/v1/assistants/{assistantId}
```

## Path parameters

Request message for deleting an assistant by ID.

#|
||Field | Description ||
|| assistantId | **string**

Required field. ID of the assistant to delete. ||
|#

## Response {#yandex.cloud.ai.assistants.v1.DeleteAssistantResponse}

**HTTP Code: 200 - OK**