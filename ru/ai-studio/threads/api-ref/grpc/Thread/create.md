---
editable: false
sourcePath: en/_api-ref-grpc/ai/assistants/v1/threads/api-ref/grpc/Thread/create.md
---

# Threads, gRPC: ThreadService.Create

Create a new thread.

## gRPC request

**rpc Create ([CreateThreadRequest](#yandex.cloud.ai.assistants.v1.threads.CreateThreadRequest)) returns ([Thread](#yandex.cloud.ai.assistants.v1.threads.Thread))**

## CreateThreadRequest {#yandex.cloud.ai.assistants.v1.threads.CreateThreadRequest}

```json
{
  "folder_id": "string",
  "messages": [
    {
      "author": {
        "id": "string",
        "role": "string"
      },
      "labels": "map<string, string>",
      "content": {
        "content": [
          {
            // Includes only one of the fields `text`
            "text": {
              "content": "string"
            }
            // end of the list of possible fields
          }
        ]
      }
    }
  ],
  "name": "string",
  "description": "string",
  "default_message_author_id": "string",
  "expiration_config": {
    "expiration_policy": "ExpirationPolicy",
    "ttl_days": "int64"
  },
  "labels": "map<string, string>",
  "tools": [
    {
      // Includes only one of the fields `search_index`, `function`, `gen_search`
      "search_index": {
        "search_index_ids": [
          "string"
        ],
        "max_num_results": "google.protobuf.Int64Value",
        "rephraser_options": {
          "rephraser_uri": "string"
        },
        "call_strategy": {
          // Includes only one of the fields `always_call`, `auto_call`
          "always_call": "AlwaysCall",
          "auto_call": {
            "name": "string",
            "instruction": "string"
          }
          // end of the list of possible fields
        }
      },
      "function": {
        "name": "string",
        "description": "string",
        "parameters": "google.protobuf.Struct"
      },
      "gen_search": {
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
          "enable_nrfm_docs": "bool",
          "search_filters": [
            {
              // Includes only one of the fields `date`, `lang`, `format`
              "date": "string",
              "lang": "string",
              "format": "DocFormat"
              // end of the list of possible fields
            }
          ]
        },
        "description": "string"
      }
      // end of the list of possible fields
    }
  ]
}
```

Request message for creating a new thread.

#|
||Field | Description ||
|| folder_id | **string**

Required field.  ||
|| messages[] | **[MessageData](#yandex.cloud.ai.assistants.v1.threads.MessageData)**

List of messages to initialize the thread. ||
|| name | **string**

Name of the thread. ||
|| description | **string**

Description of the thread. ||
|| default_message_author_id | **string**

Default user ID that will be used as the author for thread messages if no other author is specified. ||
|| expiration_config | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig)**

Expiration configuration for the thread. ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs to label the thread. ||
|| tools[] | **[Tool](#yandex.cloud.ai.assistants.v1.Tool)**

List of tools that are available for assistants to use in this thread. ||
|#

## MessageData {#yandex.cloud.ai.assistants.v1.threads.MessageData}

Represents the data required to create or initialize a message in a thread.
This message is used, for example, to initialize a thread with some messages upon its creation.

#|
||Field | Description ||
|| author | **[Author](#yandex.cloud.ai.assistants.v1.threads.Author)**

Author of the message, containing details about the message's creator. ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs that can be used to organize and categorize the message. ||
|| content | **[MessageContent](#yandex.cloud.ai.assistants.v1.threads.MessageContent)**

Content of the message. ||
|#

## Author {#yandex.cloud.ai.assistants.v1.threads.Author}

Author of the message, containing details about the message's creator.

#|
||Field | Description ||
|| id | **string**

Unique identifier of the author. This could be either the user's ID or the assistant's ID, depending on the role. ||
|| role | **string**

Role of the author, indicating whether the message was created by a "user" or an "assistant". ||
|#

## MessageContent {#yandex.cloud.ai.assistants.v1.threads.MessageContent}

Represents the content of a message, which can consist of multiple parts.

#|
||Field | Description ||
|| content[] | **[ContentPart](#yandex.cloud.ai.assistants.v1.threads.ContentPart)**

A list of content parts that make up the message. ||
|#

## ContentPart {#yandex.cloud.ai.assistants.v1.threads.ContentPart}

ContentPart represents an individual part of the message content, which can be of various types.

#|
||Field | Description ||
|| text | **[Text](#yandex.cloud.ai.assistants.v1.threads.Text)**

Text content of the message part.

Includes only one of the fields `text`.

Specifies the type of content that the part contains. ||
|#

## Text {#yandex.cloud.ai.assistants.v1.threads.Text}

Text represents a textual content part of a message.

#|
||Field | Description ||
|| content | **string**

Text content of the message. ||
|#

## ExpirationConfig {#yandex.cloud.ai.common.ExpirationConfig}

#|
||Field | Description ||
|| expiration_policy | enum **ExpirationPolicy**

- `EXPIRATION_POLICY_UNSPECIFIED`
- `STATIC`
- `SINCE_LAST_ACTIVE` ||
|| ttl_days | **int64** ||
|#

## Tool {#yandex.cloud.ai.assistants.v1.Tool}

Represents a general tool that can be one of several types.

#|
||Field | Description ||
|| search_index | **[SearchIndexTool](#yandex.cloud.ai.assistants.v1.SearchIndexTool)**

SearchIndexTool tool that performs search across specified indexes.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|| function | **[FunctionTool](#yandex.cloud.ai.assistants.v1.FunctionTool)**

Function tool that can be invoked by the assistant.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|| gen_search | **[GenSearchTool](#yandex.cloud.ai.assistants.v1.GenSearchTool)**

Performs web retrieval and generative synthesis.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|#

## SearchIndexTool {#yandex.cloud.ai.assistants.v1.SearchIndexTool}

Configures a tool that enables Retrieval-Augmented Generation (RAG) by allowing the assistant to search across a specified search index.

#|
||Field | Description ||
|| search_index_ids[] | **string**

A list of search index IDs that this tool will query. Currently, only a single index ID is supported. ||
|| max_num_results | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of results to return from the search.
Fewer results may be returned if necessary to fit within the prompt's token limit.
This ensures that the combined prompt and search results do not exceed the token constraints. ||
|| rephraser_options | **[RephraserOptions](#yandex.cloud.ai.assistants.v1.RephraserOptions)**

Options for rephrasing user queries.
Used to rewrite the last user message for search,
incorporating context from the previous conversation. ||
|| call_strategy | **[CallStrategy](#yandex.cloud.ai.assistants.v1.CallStrategy)**

Defines the strategy for triggering search.
Controls whether search results are always included or returned only when
the model explicitly calls the tool. ||
|#

## RephraserOptions {#yandex.cloud.ai.assistants.v1.RephraserOptions}

Options for configuring the rephrasing the last user message for search using context from previous conversation.

#|
||Field | Description ||
|| rephraser_uri | **string**

Required field. The ID of the model used to rephrase the last user message for search. ||
|#

## CallStrategy {#yandex.cloud.ai.assistants.v1.CallStrategy}

Defines when the assistant uses the search tool.

#|
||Field | Description ||
|| always_call | **[AlwaysCall](#yandex.cloud.ai.assistants.v1.CallStrategy.AlwaysCall)**

Includes only one of the fields `always_call`, `auto_call`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|| auto_call | **[AutoCall](#yandex.cloud.ai.assistants.v1.CallStrategy.AutoCall)**

Includes only one of the fields `always_call`, `auto_call`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|#

## AlwaysCall {#yandex.cloud.ai.assistants.v1.CallStrategy.AlwaysCall}

Always includes retrieved search results in the prompt.

#|
||Field | Description ||
|| Empty | > ||
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
|| parameters | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

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
|| enable_nrfm_docs | **bool**

Use the documents inaccessible from the front page. ||
|| search_filters[] | **[SearchFilter](#yandex.cloud.ai.assistants.v1.GenSearchOptions.SearchFilter)**

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
|| format | enum **DocFormat**

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

## Thread {#yandex.cloud.ai.assistants.v1.threads.Thread}

```json
{
  "id": "string",
  "folder_id": "string",
  "name": "string",
  "description": "string",
  "default_message_author_id": "string",
  "created_by": "string",
  "created_at": "google.protobuf.Timestamp",
  "updated_by": "string",
  "updated_at": "google.protobuf.Timestamp",
  "expiration_config": {
    "expiration_policy": "ExpirationPolicy",
    "ttl_days": "int64"
  },
  "expires_at": "google.protobuf.Timestamp",
  "labels": "map<string, string>",
  "tools": [
    {
      // Includes only one of the fields `search_index`, `function`, `gen_search`
      "search_index": {
        "search_index_ids": [
          "string"
        ],
        "max_num_results": "google.protobuf.Int64Value",
        "rephraser_options": {
          "rephraser_uri": "string"
        },
        "call_strategy": {
          // Includes only one of the fields `always_call`, `auto_call`
          "always_call": "AlwaysCall",
          "auto_call": {
            "name": "string",
            "instruction": "string"
          }
          // end of the list of possible fields
        }
      },
      "function": {
        "name": "string",
        "description": "string",
        "parameters": "google.protobuf.Struct"
      },
      "gen_search": {
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
          "enable_nrfm_docs": "bool",
          "search_filters": [
            {
              // Includes only one of the fields `date`, `lang`, `format`
              "date": "string",
              "lang": "string",
              "format": "DocFormat"
              // end of the list of possible fields
            }
          ]
        },
        "description": "string"
      }
      // end of the list of possible fields
    }
  ]
}
```

#|
||Field | Description ||
|| id | **string**

Unique identifier of the thread. ||
|| folder_id | **string**

ID of the folder that the thread belongs to. ||
|| name | **string**

Name of the thread. ||
|| description | **string**

Description of the thread. ||
|| default_message_author_id | **string**

Default user ID that will be used as the author for thread messages if no other author is specified. ||
|| created_by | **string**

Identifier of the subject who created this thread. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing when the thread was created. ||
|| updated_by | **string**

Identifier of the subject who last updated this thread. ||
|| updated_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing the last time this thread was updated. ||
|| expiration_config | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig2)**

Configuration for the expiration of the thread, defining when and how the thread will expire. ||
|| expires_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing when the thread will expire. ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs that can be used to organize and categorize the thread. ||
|| tools[] | **[Tool](#yandex.cloud.ai.assistants.v1.Tool2)**

List of tools that are available for assistants to use in this thread. ||
|#

## ExpirationConfig {#yandex.cloud.ai.common.ExpirationConfig2}

#|
||Field | Description ||
|| expiration_policy | enum **ExpirationPolicy**

- `EXPIRATION_POLICY_UNSPECIFIED`
- `STATIC`
- `SINCE_LAST_ACTIVE` ||
|| ttl_days | **int64** ||
|#

## Tool {#yandex.cloud.ai.assistants.v1.Tool2}

Represents a general tool that can be one of several types.

#|
||Field | Description ||
|| search_index | **[SearchIndexTool](#yandex.cloud.ai.assistants.v1.SearchIndexTool2)**

SearchIndexTool tool that performs search across specified indexes.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|| function | **[FunctionTool](#yandex.cloud.ai.assistants.v1.FunctionTool2)**

Function tool that can be invoked by the assistant.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|| gen_search | **[GenSearchTool](#yandex.cloud.ai.assistants.v1.GenSearchTool2)**

Performs web retrieval and generative synthesis.

Includes only one of the fields `search_index`, `function`, `gen_search`. ||
|#

## SearchIndexTool {#yandex.cloud.ai.assistants.v1.SearchIndexTool2}

Configures a tool that enables Retrieval-Augmented Generation (RAG) by allowing the assistant to search across a specified search index.

#|
||Field | Description ||
|| search_index_ids[] | **string**

A list of search index IDs that this tool will query. Currently, only a single index ID is supported. ||
|| max_num_results | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of results to return from the search.
Fewer results may be returned if necessary to fit within the prompt's token limit.
This ensures that the combined prompt and search results do not exceed the token constraints. ||
|| rephraser_options | **[RephraserOptions](#yandex.cloud.ai.assistants.v1.RephraserOptions2)**

Options for rephrasing user queries.
Used to rewrite the last user message for search,
incorporating context from the previous conversation. ||
|| call_strategy | **[CallStrategy](#yandex.cloud.ai.assistants.v1.CallStrategy2)**

Defines the strategy for triggering search.
Controls whether search results are always included or returned only when
the model explicitly calls the tool. ||
|#

## RephraserOptions {#yandex.cloud.ai.assistants.v1.RephraserOptions2}

Options for configuring the rephrasing the last user message for search using context from previous conversation.

#|
||Field | Description ||
|| rephraser_uri | **string**

Required field. The ID of the model used to rephrase the last user message for search. ||
|#

## CallStrategy {#yandex.cloud.ai.assistants.v1.CallStrategy2}

Defines when the assistant uses the search tool.

#|
||Field | Description ||
|| always_call | **[AlwaysCall](#yandex.cloud.ai.assistants.v1.CallStrategy.AlwaysCall2)**

Includes only one of the fields `always_call`, `auto_call`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|| auto_call | **[AutoCall](#yandex.cloud.ai.assistants.v1.CallStrategy.AutoCall2)**

Includes only one of the fields `always_call`, `auto_call`.

One of `always_call` or `auto_call`.
always_call is used if no strategy is explicitly set ||
|#

## AlwaysCall {#yandex.cloud.ai.assistants.v1.CallStrategy.AlwaysCall2}

Always includes retrieved search results in the prompt.

#|
||Field | Description ||
|| Empty | > ||
|#

## AutoCall {#yandex.cloud.ai.assistants.v1.CallStrategy.AutoCall2}

Exposes the tool as a callable function.
The model decides when to trigger search based on the instruction.

#|
||Field | Description ||
|| name | **string**

The name of the tool as exposed to the model. ||
|| instruction | **string**

Required field. Required instruction that helps the model decide when to call the tool. ||
|#

## FunctionTool {#yandex.cloud.ai.assistants.v1.FunctionTool2}

Represents a function tool that can be invoked by the assistant.

#|
||Field | Description ||
|| name | **string**

The name of the function. ||
|| description | **string**

A description of the function's purpose or behavior. ||
|| parameters | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

A JSON Schema that defines the expected parameters for the function.
The schema should describe the required fields, their types, and any constraints or default values. ||
|#

## GenSearchTool {#yandex.cloud.ai.assistants.v1.GenSearchTool2}

#|
||Field | Description ||
|| options | **[GenSearchOptions](#yandex.cloud.ai.assistants.v1.GenSearchOptions2)**

Scoping and filtering rules for the search query ||
|| description | **string**

Required field. description of the purpose ||
|#

## GenSearchOptions {#yandex.cloud.ai.assistants.v1.GenSearchOptions2}

#|
||Field | Description ||
|| site | **[SiteOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.SiteOption2)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| host | **[HostOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.HostOption2)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| url | **[UrlOption](#yandex.cloud.ai.assistants.v1.GenSearchOptions.UrlOption2)**

Includes only one of the fields `site`, `host`, `url`.

Restricts the search to the specific websites, hosts or pages.
Includes only one of the fields site, host, url. ||
|| enable_nrfm_docs | **bool**

Use the documents inaccessible from the front page. ||
|| search_filters[] | **[SearchFilter](#yandex.cloud.ai.assistants.v1.GenSearchOptions.SearchFilter2)**

Restricts the search by date, document formats or language. ||
|#

## SiteOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.SiteOption2}

Restricts the search to the specific websites.

#|
||Field | Description ||
|| site[] | **string** ||
|#

## HostOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.HostOption2}

Restricts the search to the specific hosts.

#|
||Field | Description ||
|| host[] | **string** ||
|#

## UrlOption {#yandex.cloud.ai.assistants.v1.GenSearchOptions.UrlOption2}

Restricts the search to the specific pages.

#|
||Field | Description ||
|| url[] | **string** ||
|#

## SearchFilter {#yandex.cloud.ai.assistants.v1.GenSearchOptions.SearchFilter2}

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
|| format | enum **DocFormat**

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