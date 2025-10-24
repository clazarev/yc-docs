---
title: Reasoning mode in generative {{ foundation-models-name }}
description: In this tutorial, you will learn how to use the reasoning mode to improve the accuracy of generative models in {{ foundation-models-full-name }}.
---

# Reasoning mode in generative models

Generative models do not always cope equally well with tasks that require reasoning, i.e., breaking the task into steps and performing a chain of successive computations, where the results of the previous computation provide the input data for the next one.

You can improve the accuracy of the model's responses by forcing the model to reason and generate based on such chains of intermediate computations. You can do this using a prompt or a special [generation parameter](../../text-generation/api-ref/TextGeneration/completion.md#yandex.cloud.ai.foundation_models.v1.ReasoningOptions).


## reasoning_option parameter {#reasoning-option}

You can configure the reasoning mode using the `reasoning_options` parameter when you [access](./models.md#addressing-models) the models supporting this parameter through the API or SDK. The `reasoning_options` parameter can take the following values:
* `DISABLED`: Reasoning mode is disabled. This is a default value. If the `reasoning_options` parameter is not specified in the request, the reasoning mode is disabled.
* `ENABLED_HIDDEN`: Reasoning mode is enabled. Different models decide differently whether to use this mode for each particular request. Even if the model uses reasoning when generating a response, the response will not contain the model's actual chain of reasoning.

Example of a request configuration in the reasoning mode:

{% list tabs group=programming_language %}

- SDK {#sdk}

  ```python
  model = sdk.models.completions('yandexgpt')
  modelRequest = model.configure(
          reasoning_mode='enabled_hidden',
      ).run("Request text")
  ```

- API {#api}

  ```json
  {
    "modelUri": "gpt://<folder_ID>/yandexgpt",
    "completionOptions": {
      "stream": false,
      "temperature": 0.1,
      "maxTokens": "1000",
      "reasoningOptions": {
        "mode": "ENABLED_HIDDEN"
      }
    },
    "messages": [...]
  }
  ```

{% endlist %}

The reasoning mode may increase the amount of computations and the total number of resulting request [tokens](./tokens.md): if reasoning was used, the model's response will contain the `reasoningTokens` field with a non-zero value.

The reasoning mode is available in the {{ gpt-pro }} model via the `reasoning_options` parameter.


## reasoning_effort parameter {#reasoning-effort}

The `reasoning_effort` parameter determines how many reasoning tokens the model should generate before verbalizing a response to a query.

Supported values:

* `low`: Prioritizes speed and saves tokens.
* `medium`: Balance between reasoning speed and accuracy.
* `high`: Prioritizes complete and thorough reasoning.

Example of using the `reasoning_effort` parameter:

{% list tabs group=programming_language %}

- Python {#python}

  ```python
  # Install OpenAI SDK using pip
  # pip install openai 
  import openai
  from openai import OpenAI

  YANDEX_CLOUD_FOLDER = "<folder_ID>"
  YANDEX_CLOUD_API_KEY = "<API_key_value>"

  def run():
      client = OpenAI(
          api_key=YANDEX_CLOUD_API_KEY,
          base_url="https://{{ api-host-llm }}/v1",
          project=YANDEX_CLOUD_FOLDER
      )

      response = client.chat.completions.create(
          model=f"gpt://{YANDEX_CLOUD_FOLDER}/gpt-oss-120b",
          # or
          # model=f"gpt://{YANDEX_CLOUD_FOLDER}/gpt-oss-20b",
          messages=[
              {
                  "role": "developer",
                  "content": "You are a very smart assistant."},
              {
                  "role": "user",
                  "content": "What is under the hood of LLM?",
              },
          ],
          reasoning_effort="low",
      )

      print(response.choices[0].message.content)

  if __name__ == "__main__":
      run()
  ```

{% endlist %}

