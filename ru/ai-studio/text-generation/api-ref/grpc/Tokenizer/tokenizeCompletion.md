---
editable: false
sourcePath: en/_api-ref-grpc/ai/foundation_models/v1/text_generation/text-generation/api-ref/grpc/Tokenizer/tokenizeCompletion.md
---

# Foundation Models Text Generation API, gRPC: TokenizerService.TokenizeCompletion

RPC method for tokenizing content of CompletionRequest

## gRPC request

**rpc TokenizeCompletion ([CompletionRequest](#yandex.cloud.ai.foundation_models.v1.CompletionRequest)) returns ([TokenizeResponse](#yandex.cloud.ai.foundation_models.v1.TokenizeResponse))**

## CompletionRequest {#yandex.cloud.ai.foundation_models.v1.CompletionRequest}

```json
{
  "model_uri": "string",
  "completion_options": {
    "stream": "bool",
    "temperature": "google.protobuf.DoubleValue",
    "max_tokens": "google.protobuf.Int64Value",
    "reasoning_options": {
      "mode": "ReasoningMode"
    }
  },
  "messages": [
    {
      "role": "string",
      // Includes only one of the fields `text`, `tool_call_list`, `tool_result_list`
      "text": "string",
      "tool_call_list": {
        "tool_calls": [
          {
            // Includes only one of the fields `function_call`
            "function_call": {
              "name": "string",
              "arguments": "google.protobuf.Struct"
            }
            // end of the list of possible fields
          }
        ]
      },
      "tool_result_list": {
        "tool_results": [
          {
            // Includes only one of the fields `function_result`
            "function_result": {
              "name": "string",
              // Includes only one of the fields `content`
              "content": "string"
              // end of the list of possible fields
            }
            // end of the list of possible fields
          }
        ]
      }
      // end of the list of possible fields
    }
  ],
  "tools": [
    {
      // Includes only one of the fields `function`
      "function": {
        "name": "string",
        "description": "string",
        "parameters": "google.protobuf.Struct",
        "strict": "bool"
      }
      // end of the list of possible fields
    }
  ],
  // Includes only one of the fields `json_object`, `json_schema`
  "json_object": "bool",
  "json_schema": {
    "schema": "google.protobuf.Struct"
  },
  // end of the list of possible fields
  "parallel_tool_calls": "google.protobuf.BoolValue",
  "tool_choice": {
    // Includes only one of the fields `mode`, `function_name`
    "mode": "ToolChoiceMode",
    "function_name": "string"
    // end of the list of possible fields
  }
}
```

Request for the service to generate text completion.

#|
||Field | Description ||
|| model_uri | **string**

The [ID of the model](/docs/foundation-models/concepts/yandexgpt/models) to be used for completion generation. ||
|| completion_options | **[CompletionOptions](#yandex.cloud.ai.foundation_models.v1.CompletionOptions)**

Configuration options for completion generation. ||
|| messages[] | **[Message](#yandex.cloud.ai.foundation_models.v1.Message)**

A list of messages representing the context for the completion model. ||
|| tools[] | **[Tool](#yandex.cloud.ai.foundation_models.v1.Tool)**

List of tools that are available for the model to invoke during the completion generation.
Note: This parameter is not yet supported and will be ignored if provided. ||
|| json_object | **bool**

When set to true, the model will respond with a valid JSON object.
Be sure to explicitly ask the model for JSON.
Otherwise, it may generate excessive whitespace and run indefinitely until it reaches the token limit.

Includes only one of the fields `json_object`, `json_schema`.

Specifies the format of the model's response. ||
|| json_schema | **[JsonSchema](#yandex.cloud.ai.foundation_models.v1.JsonSchema)**

Enforces a specific JSON structure for the model's response based on a provided schema.

Includes only one of the fields `json_object`, `json_schema`.

Specifies the format of the model's response. ||
|| parallel_tool_calls | **[google.protobuf.BoolValue](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/bool-value)**

Controls whether the model can generate multiple tool calls in a single response. Defaults to true. ||
|| tool_choice | **[ToolChoice](#yandex.cloud.ai.foundation_models.v1.ToolChoice)**

Specifies how the model should select which tool (or tools) to use when generating a response. ||
|#

## CompletionOptions {#yandex.cloud.ai.foundation_models.v1.CompletionOptions}

Defines the options for completion generation.

#|
||Field | Description ||
|| stream | **bool**

Enables streaming of partially generated text. ||
|| temperature | **[google.protobuf.DoubleValue](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/double-value)**

Affects creativity and randomness of responses. Should be a double number between 0 (inclusive) and 1 (inclusive).
Lower values produce more straightforward responses while higher values lead to increased creativity and randomness.
Default temperature: 0.3 ||
|| max_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The limit on the number of tokens used for single completion generation.
Must be greater than zero. This maximum allowed parameter value may depend on the model being used. ||
|| reasoning_options | **[ReasoningOptions](#yandex.cloud.ai.foundation_models.v1.ReasoningOptions)**

Configures reasoning capabilities for the model, allowing it to perform internal reasoning before responding. ||
|#

## ReasoningOptions {#yandex.cloud.ai.foundation_models.v1.ReasoningOptions}

Represents reasoning options that enable the model's ability to perform internal reasoning before generating a response.

#|
||Field | Description ||
|| mode | enum **ReasoningMode**

Specifies the reasoning mode to be used.

- `REASONING_MODE_UNSPECIFIED`: Unspecified reasoning mode.
- `DISABLED`: Disables reasoning. The model will generate a response without performing any internal reasoning.
- `ENABLED_HIDDEN`: Enables reasoning in a hidden manner without exposing the reasoning steps to the user. ||
|#

## Message {#yandex.cloud.ai.foundation_models.v1.Message}

A message object representing a wrapper over the inputs and outputs of the completion model.

#|
||Field | Description ||
|| role | **string**

The ID of the message sender. Supported roles:
* `system`: Special role used to define the behaviour of the completion model.
* `assistant`: A role used by the model to generate responses.
* `user`: A role used by the user to describe requests to the model. ||
|| text | **string**

Textual content of the message.

Includes only one of the fields `text`, `tool_call_list`, `tool_result_list`.

Message content. ||
|| tool_call_list | **[ToolCallList](#yandex.cloud.ai.foundation_models.v1.ToolCallList)**

List of tool calls made by the model as part of the response generation.

Includes only one of the fields `text`, `tool_call_list`, `tool_result_list`.

Message content. ||
|| tool_result_list | **[ToolResultList](#yandex.cloud.ai.foundation_models.v1.ToolResultList)**

List of tool results returned from external tools that were invoked by the model.

Includes only one of the fields `text`, `tool_call_list`, `tool_result_list`.

Message content. ||
|#

## ToolCallList {#yandex.cloud.ai.foundation_models.v1.ToolCallList}

Represents a list of tool calls.

#|
||Field | Description ||
|| tool_calls[] | **[ToolCall](#yandex.cloud.ai.foundation_models.v1.ToolCall)**

A list of tool calls to be executed. ||
|#

## ToolCall {#yandex.cloud.ai.foundation_models.v1.ToolCall}

Represents a call to a tool.

#|
||Field | Description ||
|| function_call | **[FunctionCall](#yandex.cloud.ai.foundation_models.v1.FunctionCall)**

Represents a call to a function.

Includes only one of the fields `function_call`. ||
|#

## FunctionCall {#yandex.cloud.ai.foundation_models.v1.FunctionCall}

Represents the invocation of a function with specific arguments.

#|
||Field | Description ||
|| name | **string**

The name of the function being called. ||
|| arguments | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

The structured arguments passed to the function.
These arguments must adhere to the JSON Schema defined in the corresponding function's parameters. ||
|#

## ToolResultList {#yandex.cloud.ai.foundation_models.v1.ToolResultList}

Represents a list of tool results.

#|
||Field | Description ||
|| tool_results[] | **[ToolResult](#yandex.cloud.ai.foundation_models.v1.ToolResult)**

A list of tool results. ||
|#

## ToolResult {#yandex.cloud.ai.foundation_models.v1.ToolResult}

Represents the result of a tool call.

#|
||Field | Description ||
|| function_result | **[FunctionResult](#yandex.cloud.ai.foundation_models.v1.FunctionResult)**

Represents the result of a function call.

Includes only one of the fields `function_result`. ||
|#

## FunctionResult {#yandex.cloud.ai.foundation_models.v1.FunctionResult}

Represents the result of a function call.

#|
||Field | Description ||
|| name | **string**

The name of the function that was executed. ||
|| content | **string**

The result of the function call, represented as a string.
This field can be used to store the output of the function.

Includes only one of the fields `content`. ||
|#

## Tool {#yandex.cloud.ai.foundation_models.v1.Tool}

Represents a tool that can be invoked during completion generation.

#|
||Field | Description ||
|| function | **[FunctionTool](#yandex.cloud.ai.foundation_models.v1.FunctionTool)**

Represents a function that can be called.

Includes only one of the fields `function`. ||
|#

## FunctionTool {#yandex.cloud.ai.foundation_models.v1.FunctionTool}

Represents a function tool that can be invoked during completion generation.

#|
||Field | Description ||
|| name | **string**

The name of the function. ||
|| description | **string**

A description of the function's purpose or behavior. ||
|| parameters | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

A JSON Schema that defines the expected parameters for the function.
The schema should describe the required fields, their types, and any constraints or default values. ||
|| strict | **bool**

Enforces strict adherence to the function schema, ensuring only defined parameters are used. ||
|#

## JsonSchema {#yandex.cloud.ai.foundation_models.v1.JsonSchema}

Represents the expected structure of the model's response using a JSON Schema.

#|
||Field | Description ||
|| schema | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

The JSON Schema that the model's output must conform to. ||
|#

## ToolChoice {#yandex.cloud.ai.foundation_models.v1.ToolChoice}

Specifies how the model should select which tool (or tools) to use when generating a response.

#|
||Field | Description ||
|| mode | enum **ToolChoiceMode**

Specifies the overall tool-calling mode.

Includes only one of the fields `mode`, `function_name`.

- `TOOL_CHOICE_MODE_UNSPECIFIED`: The server will choose the default behavior, which is AUTO.
- `NONE`: The model will not call any tool and will generate a standard text response.
- `AUTO`: The model can choose between generating a text response or calling one or more tools.
This is the default behavior.
- `REQUIRED`: The model is required to call one or more tools. ||
|| function_name | **string**

Forces the model to call a specific function.
The provided string must match the name of a function in the API request.

Includes only one of the fields `mode`, `function_name`. ||
|#

## TokenizeResponse {#yandex.cloud.ai.foundation_models.v1.TokenizeResponse}

```json
{
  "tokens": [
    {
      "id": "int64",
      "text": "string",
      "special": "bool"
    }
  ],
  "model_version": "string"
}
```

Response containing tokenized content from request.

#|
||Field | Description ||
|| tokens[] | **[Token](#yandex.cloud.ai.foundation_models.v1.Token)**

A list of tokens obtained from tokenization. ||
|| model_version | **string**

Model version (changes with model releases). ||
|#

## Token {#yandex.cloud.ai.foundation_models.v1.Token}

Represents a token, the basic unit of content, used by the foundation model.

#|
||Field | Description ||
|| id | **int64**

An internal token identifier. ||
|| text | **string**

The textual representation of the token. ||
|| special | **bool**

Indicates whether the token is special or not. Special tokens may define the model's behavior and are not visible to users. ||
|#