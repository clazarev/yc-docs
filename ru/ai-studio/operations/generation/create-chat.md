---
title: Как создать чат с моделью {{ gpt-lite }} в {{ foundation-models-full-name }}
description: Следуя данной инструкции, вы создадите чат с {{ gpt-lite }} с помощью {{ responses-api }}.
---

# Как реализовать чат с помощью {{ responses-api }}

## Перед началом работы {#before-begin}

{% list tabs group=programming_language %}

- Python {#python}

  {% include notitle [ai-before-beginning](../../../_includes/ai-studio/yandexgpt/ai-before-beginning.md) %}

{% endlist %}

## Реализуйте чат {#develop-chat}

{% list tabs group=programming_language %}

- Python {#python}

  1. Создайте файл `index.py` и добавьте в него код:

      ```python
      import openai

      YANDEX_CLOUD_MODEL = "yandexgpt-lite"

      client = openai.OpenAI(
          api_key=YANDEX_CLOUD_API_KEY,
          base_url="https://rest-assistant.{{ api-host }}/v1",
          project=YANDEX_CLOUD_FOLDER
      )

      previous_id = None # Сохранение идентификатора последнего ответа

      print("💬 Чат с GPT (введите 'выход' для выхода)\n")

      while True:
          user_input = input("Вы: ")
          if user_input.lower() in ("exit", "quit", "выход"):
              print("Чат завершен.")
              break

          response = client.responses.create(
              model=f"gpt://{YANDEX_CLOUD_FOLDER}/{YANDEX_CLOUD_MODEL}",
              input=[{"role": "user", "content": user_input}],
              previous_response_id=previous_id  # Передача контекста, если он есть
          )

          # Сохранение идентификатора для следующего шага
          previous_id = response.id

          # Вывод ответа агента
          print("Агент:", response.output_text)
      ```

  1. Сохраните данные для аутентификации в переменные окружения:

      ```bash
      export YANDEX_CLOUD_FOLDER=<идентификатор_каталога>
      export YANDEX_CLOUD_API_KEY=<API-ключ>
      ```

  1. Выполните созданный файл:

      ```bash
      python index.py
      ```

      Пример ответа:

      ```text
      💬 Чат с GPT (введите 'выход' для выхода)

      Вы: привет!
      Агент: Здравствуйте! Чем я могу вам помочь?
      Вы: что ты умеешь?
      Агент: Я умею отвечать на вопросы, помогать в решении различных задач, предоставлять информацию по разным темам. Например, могу рассказать о погоде, помочь с переводом текста, предложить идеи или просто поддержать разговор. Что вас интересует?
      Вы: Шутку расскажи
      Агент: Почему программисты всегда носят с собой чашечку кофе? Потому что они работают над сложными задачами, и иногда кажется, что кофе помогает им «разогнать» процессор!
      Вы: выход
      Чат завершён.
      ```

{% endlist %}

#### См. также {#see-also}

* [{#T}](../../concepts/generation/models.md)
* Примеры работы с {{ ml-sdk-name }} на [GitHub](https://github.com/yandex-cloud/yandex-cloud-ml-sdk/tree/master/examples/sync/completions)