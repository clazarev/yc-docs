---
editable: false
apiPlayground:
  - url: https://rest-assistant.{{ api-host }}/assistants/v1/assistants:listVersions
    method: get
    path: null
    query:
      type: object
      properties:
        assistantId:
          description: |-
            **string**
            Required field. ID of the assistant whose versions are to be listed.
          type: string
        pageSize:
          description: |-
            **string** (int64)
            Maximum number of versions to return per page.
          type: string
          format: int64
        pageToken:
          description: |-
            **string**
            Token to retrieve the next page of results.
          type: string
      required:
        - assistantId
      additionalProperties: false
    body: null
    definitions: null
sourcePath: en/_api-ref/ai/assistants/v1/assistants/api-ref/Assistant/listVersions.md
---

# AI Assistants API, REST: Assistant.ListVersions

Lists all versions of a specific assistant.

## HTTP request

```
GET https://rest-assistant.{{ api-host }}/assistants/v1/assistants:listVersions
```

## Query parameters {#yandex.cloud.ai.assistants.v1.ListAssistantVersionsRequest}

Request to list all versions of a specific assistant.

#|
||Field | Description ||
|| assistantId | **string**

Required field. ID of the assistant whose versions are to be listed. ||
|| pageSize | **string** (int64)

Maximum number of versions to return per page. ||
|| pageToken | **string**

Token to retrieve the next page of results. ||
|#

## Response {#yandex.cloud.ai.assistants.v1.ListAssistantVersionsResponse}

**HTTP Code: 200 - OK**

```json
{
  "versions": [
    {
      "id": "string",
      "updateMask": "string",
      "assistant": {
        "id": "string",
        "folderId": "string",
        "name": "string",
        "description": "string",
        "createdBy": "string",
        "createdAt": "string",
        "updatedBy": "string",
        "updatedAt": "string",
        "expirationConfig": {
          "expirationPolicy": "string",
          "ttlDays": "string"
        },
        "expiresAt": "string",
        "labels": "object",
        "modelUri": "string",
        "instruction": "string",
        "promptTruncationOptions": {
          "maxPromptTokens": "string",
          // Includes only one of the fields `autoStrategy`, `lastMessagesStrategy`
          "autoStrategy": "object",
          "lastMessagesStrategy": {
            "numMessages": "string"
          }
          // end of the list of possible fields
        },
        "completionOptions": {
          "maxTokens": "string",
          "temperature": "number"
        },
        "tools": [
          {
            // Includes only one of the fields `searchIndex`, `function`, `genSearch`
            "searchIndex": {
              "searchIndexIds": [
                "string"
              ],
              "maxNumResults": "string",
              "rephraserOptions": {
                "rephraserUri": "string"
              },
              "callStrategy": {
                // Includes only one of the fields `alwaysCall`, `autoCall`
                "alwaysCall": "object",
                "autoCall": {
                  "name": "string",
                  "instruction": "string"
                }
                // end of the list of possible fields
              }
            },
            "function": {
              "name": "string",
              "description": "string",
              "parameters": "object"
            },
            "genSearch": {
              "options": {
                // Includes only one of the fields `site`, `host`, `url`
                "site": {
                  "site": [
                    "string"
                  ]
                },
                "host": {
                  "host": [
                    "string"
                  ]
                },
                "url": {
                  "url": [
                    "string"
                  ]
                },
                // end of the list of possible fields
                "enableNrfmDocs": "boolean",
                "searchFilters": [
                  {
                    // Includes only one of the fields `date`, `lang`, `format`
                    "date": "string",
                    "lang": "string",
                    "format": "string"
                    // end of the list of possible fields
                  }
                ]
              },
              "description": "string"
            }
            // end of the list of possible fields
          }
        ],
        "responseFormat": {
          // Includes only one of the fields `jsonObject`, `jsonSchema`
          "jsonObject": "boolean",
          "jsonSchema": {
            "schema": "object"
          }
          // end of the list of possible fields
        }
      }
    }
  ],
  "nextPageToken": "string"
}
```

Response message containing the list versions operation.

#|
||Field | Description ||
|| versions[] | **[AssistantVersion](#yandex.cloud.ai.assistants.v1.AssistantVersion)**

List of assistant versions. ||
|| nextPageToken | **string**

Token to retrieve the next page of results. ||
|#

## AssistantVersion {#yandex.cloud.ai.assistants.v1.AssistantVersion}

Represents a specific version of an assistant.

#|
||Field | Description ||
|| id | **string**

ID of the assistant version. ||
|| updateMask | **string** (field-mask)

A comma-separated names off ALL fields to be updated.
Only the specified fields will be changed. The others will be left untouched.
If the field is specified in `` updateMask `` and no value for that field was sent in the request,
the field's value will be reset to the default. The default value for most fields is null or 0.

If `` updateMask `` is not sent in the request, all fields' values will be updated.
Fields specified in the request will be updated to provided values.
The rest of the fields will be reset to the default. ||
|| assistant | **[Assistant](#yandex.cloud.ai.assistants.v1.Assistant)**

Assistant configuration for this version. ||
|#

## Assistant {#yandex.cloud.ai.assistants.v1.Assistant}

Assistant represents an AI assistant configuration with various settings and metadata.

#|
||Field | Description ||
|| id | **string**

Unique identifier of the assistant. ||
|| folderId | **string**

ID of the folder that the assistant belongs to. ||
|| name | **string**

Name of the assistant. ||
|| description | **string**

Description of the assistant. ||
|| createdBy | **string**

Identifier of the subject who created this assistant. ||
|| createdAt | **string** (date-time)

Timestamp representing when the assistant was created.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| updatedBy | **string**

Identifier of the subject who last updated this assistant. ||
|| updatedAt | **string** (date-time)

Timestamp representing the last time this assistant was updated.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| expirationConfig | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig)**

Configuration for the expiration of the assistant, defining when and how the assistant will expire. ||
|| expiresAt | **string** (date-time)

Timestamp representing when the assistant will expire.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs that can be used to organize and categorize the assistant. ||
|| modelUri | **string**

The [ID of the model](/docs/foundation-models/concepts/yandexgpt/models) to be used for completion generation. ||
|| instruction | **string**

Instructions or guidelines that the assistant should follow when generating responses or performing tasks.
These instructions can help guide the assistant's behavior and responses. ||
|| promptTruncationOptions | **[PromptTruncationOptions](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions)**

Configuration options for truncating the prompt when the token count exceeds a specified limit. ||
|| completionOptions | **[CompletionOptions](#yandex.cloud.ai.assistants.v1.CompletionOptions)**

Configuration options for completion generation. ||
|| tools[] | **[Tool](#yandex.cloud.ai.assistants.v1.Tool)**

List of tools that the assistant can use to perform additional tasks.
One example is the SearchIndexTool, which is used for Retrieval-Augmented Generation (RAG). ||
|| responseFormat | **[ResponseFormat](#yandex.cloud.ai.assistants.v1.ResponseFormat)**

Specifies the format of the model's response. ||
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

## PromptTruncationOptions {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions}

Defines the options for truncating thread messages within a prompt.

#|
||Field | Description ||
|| maxPromptTokens | **string** (int64)

The maximum number of tokens allowed in the prompt.
If the prompt exceeds this limit, the thread messages will be truncated.
Default max_prompt_tokens: 7000 ||
|| autoStrategy | **object**

Includes only one of the fields `autoStrategy`, `lastMessagesStrategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|| lastMessagesStrategy | **[LastMessagesStrategy](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy)**

Retains only the last `num_messages` messages in the thread.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit.

Includes only one of the fields `autoStrategy`, `lastMessagesStrategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|#

## LastMessagesStrategy {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy}

Truncates the prompt by retaining only the last `num_messages` messages in the thread.

#|
||Field | Description ||
|| numMessages | **string** (int64)

The number of most recent messages to retain in the prompt.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit. ||
|#

## CompletionOptions {#yandex.cloud.ai.assistants.v1.CompletionOptions}

Defines the options for completion generation.

#|
||Field | Description ||
|| maxTokens | **string** (int64)

The limit on the number of tokens used for single completion generation.
Must be greater than zero. This maximum allowed parameter value may depend on the model being used. ||
|| temperature | **number** (double)

Affects creativity and randomness of responses. Should be a double number between 0 (inclusive) and 1 (inclusive).
Lower values produce more straightforward responses while higher values lead to increased creativity and randomness.
Default temperature: 0.3 ||
|#

## Tool {#yandex.cloud.ai.assistants.v1.Tool}

Represents a general tool that can be one of several types.

#|
||Field | Description ||
|| searchIndex | **[SearchIndexTool](#yandex.cloud.ai.assistants.v1.SearchIndexTool)**

SearchIndexTool tool that performs search across specified indexes.

Includes only one of the fields `searchIndex`, `function`, `genSearch`. ||
|| function | **[FunctionTool](#yandex.cloud.ai.assistants.v1.FunctionTool)**

Function tool that can be invoked by the assistant.

Includes only one of the fields `searchIndex`, `function`, `genSearch`. ||
|| genSearch | **[GenSearchTool](#yandex.cloud.ai.assistants.v1.GenSearchTool)**

Performs web retrieval and generative synthesis.

Includes only one of the fields `searchIndex`, `function`, `genSearch`. ||
|#

## SearchIndexTool {#yandex.cloud.ai.assistants.v1.SearchIndexTool}

Configures a tool that enables Retrieval-Augmented Generation (RAG) by allowing the assistant to search across a specified search index.

#|
||Field | Description ||
|| searchIndexIds[] | **string**

A list of search index IDs that this tool will query. Currently, only a single index ID is supported. ||
|| maxNumResults | **string** (int64)

The maximum number of results to return from the search.
Fewer results may be returned if necessary to fit within the prompt's token limit.
This ensures that the combined prompt and search results do not exceed the token constraints. ||
|| rephraserOptions | **[RephraserOptions](#yandex.cloud.ai.assistants.v1.RephraserOptions)**

Options for rephrasing user queries.
Used to rewrite the last user message for search,
incorporating context from the previous conversation. ||
|| callStrategy | **[CallStrategy](#yandex.cloud.ai.assistants.v1.CallStrategy)**

Defines the strategy for triggering search.
Controls whether search results are always included or returned only when
the model explicitly calls the tool. ||
|#

## RephraserOptions {#yandex.cloud.ai.assistants.v1.RephraserOptions}

Options for configuring the rephrasing the last user message for search using context from previous conversation.

#|
||Field | Description ||
|| rephraserUri | **string**

Required field. The ID of the model used to rephrase the last user message for search. ||
|#

## CallStrategy {#yandex.cloud.ai.assistants.v1.CallStrategy}

Defines when the assistant uses the search tool.

#|
||Field | Description ||
|| alwaysCall | **object**

Includes only one of the fields `alwaysCall`, `autoCall`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|| autoCall | **[AutoCall](#yandex.cloud.ai.assistants.v1.CallStrategy.AutoCall)**

Includes only one of the fields `alwaysCall`, `autoCall`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|#

## AutoCall {#yandex.cloud.ai.assistants.v1.CallStrategy.AutoCall}

Exposes the tool as a callable function.
The model decides when to trigger search based on the instruction.

#|
||Field | Description ||
|| name | **string**

The name of the tool as exposed to the model. ||
|| instruction | **string**

Required field. Required instruction that helps the model decide when to call the tool. ||
|#

## FunctionTool {#yandex.cloud.ai.assistants.v1.FunctionTool}

Represents a function tool that can be invoked by the assistant.

#|
||Field | Description ||
|| name | **string**

The name of the function. ||
|| description | **string**

A description of the function's purpose or behavior. ||
|| parameters | **object**

A JSON Schema that defines the expected parameters for the function.
The schema should describe the required fields, their types, and any constraints or default values. ||
|#

## GenSearchTool {#yandex.cloud.ai.assistants.v1.GenSearchTool}

#|
||Field | Description ||
|| options | **[GenSearchOptions](#yandex.cloud.ai.assistants.v1.GenSearchOptions)**

Scoping and filtering rules for the search query ||
|| description | **string**

Required field. description of the purpose ||
|#

## GenSearchOptions {#yandex.cloud.ai.assistants.v1.GenSearchOptions}

#|
||Field | Description ||
|| site | **[SiteOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.SiteOption)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| host | **[HostOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.HostOption)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| url | **[UrlOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.UrlOption)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| enableNrfmDocs | **boolean**

Use the documents inaccessible from the front page. ||
|| searchFilters[] | **[SearchFilter](#yandex.cloud.ai.assistants.v1.GenSearchOptions.SearchFilter)**

Restricts the search by date, document formats or language. ||
|#

## SiteOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.SiteOption}

Restricts the search to the specific websites.

#|
||Field | Description ||
|| site[] | **string** ||
|#

## HostOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.HostOption}

Restricts the search to the specific hosts.

#|
||Field | Description ||
|| host[] | **string** ||
|#

## UrlOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.UrlOption}

Restricts the search to the specific pages.

#|
||Field | Description ||
|| url[] | **string** ||
|#

## SearchFilter {#yandex.cloud.ai.assistants.v1.GenSearchOptions.SearchFilter}

#|
||Field | Description ||
|| date | **string**

Restrict by document date

Includes only one of the fields `date`, `lang`, `format`.

Includes only one of the fields date, lang, format. ||
|| lang | **string**

Restrict by document language. Use ISO 639-1 language codes.

Includes only one of the fields `date`, `lang`, `format`.

Includes only one of the fields date, lang, format. ||
|| format | **enum** (DocFormat)

Restrict by document format.

Includes only one of the fields `date`, `lang`, `format`.

Includes only one of the fields date, lang, format.

- `DOC_FORMAT_UNSPECIFIED`
- `DOC_FORMAT_PDF`
- `DOC_FORMAT_XLS`
- `DOC_FORMAT_ODS`
- `DOC_FORMAT_RTF`
- `DOC_FORMAT_PPT`
- `DOC_FORMAT_ODP`
- `DOC_FORMAT_SWF`
- `DOC_FORMAT_ODT`
- `DOC_FORMAT_ODG`
- `DOC_FORMAT_DOC` ||
|#

## ResponseFormat {#yandex.cloud.ai.assistants.v1.ResponseFormat}

Specifies the format of the model's response.

#|
||Field | Description ||
|| jsonObject | **boolean**

When set to true, the model will respond with a valid JSON object.
Be sure to explicitly ask the model for JSON.
Otherwise, it may generate excessive whitespace and run indefinitely until it reaches the token limit.

Includes only one of the fields `jsonObject`, `jsonSchema`. ||
|| jsonSchema | **[JsonSchema](#yandex.cloud.ai.assistants.v1.JsonSchema)**

Enforces a specific JSON structure for the model's response based on a provided schema.

Includes only one of the fields `jsonObject`, `jsonSchema`. ||
|#

## JsonSchema {#yandex.cloud.ai.assistants.v1.JsonSchema}

Represents the expected structure of the model's response using a JSON Schema.

#|
||Field | Description ||
|| schema | **object**

The JSON Schema that the model's output must conform to. ||
|#