---
title: Создать ассистента с инструментом {{ vector-store-name }}
description: Следуя этой инструкции, с помощью {{ assistant-api }} вы сможете создать персонализированного ассистента, реализующего сценарий генеративного ответа с учетом информации из поискового индекса с использованием инструмента {{ vector-store-name }}.
noIndex: true
---

# Создать RAG-ассистента с инструментом {{ vector-store-name }}

{% include [assistants-do-not-use](../../../_includes/ai-studio/ai-assistant-disclaimer.md) %}

{{ assistant-api }} — это функциональность {{ foundation-models-name }} для создания [AI-ассистентов](../../concepts/assistant/index.md). С его помощью можно создать персонализированных помощников, реализовывать сценарий генеративного ответа с учетом информации из внешних источников ([RAG](https://ru.wikipedia.org/wiki/Генерация,_дополненная_поиском), Retrieval Augmented Generation), а также сохранять контекст запросов к модели.

Получать информацию из базы знаний AI-ассистентам позволяет [инструмент](../../concepts/assistant/tools/vector-store.md) {{ retrieval-tool-name }}.

## Перед началом работы {#before-begin}

Чтобы воспользоваться примерами:

{% list tabs group=programming_language %}

- SDK {#sdk}

  {% include [sdk-before-begin-assistants](../../../_includes/ai-studio/sdk-before-begin-assistants.md) %}

- cURL {#curl}

  {% include notitle [ai-before-beginning](../../../_includes/ai-studio/yandexgpt/ai-before-beginning.md) %}

  Чтобы воспользоваться примерами, установите утилиты [cURL](https://curl.haxx.se) и [jq](https://stedolan.github.io/jq).

{% endlist %}

## Создайте ассистента {#create-assistant}

{% list tabs group=programming_language %}

- SDK {#sdk}

  Этот пример показывает, как создать [ассистента](../../concepts/assistant/index.md), который для ответов использует информацию из файлов. В примере будет создан индекс для полнотекстового поиска и реализован простейший вариант чата.

  {% include [rag-default-strategy-paragraph](../../../_includes/ai-studio/assistants/rag-default-strategy-paragraph.md) %}

  Чтобы скорректировать такое поведение, в данном примере для ассистента будет настроена [стратегия](../../concepts/assistant/tools/vector-store.md) обращения к индексу, в соответствии с которой модель будет обращаться к дополнительным источникам информации только в тех случаях, которые определены в инструкции, заданной в переменной `instruction`.

  1. {% include [download-context-file-step](../../../_includes/ai-studio/assistants/download-context-file-step.md) %}
  1. Создайте файл `search-assistant.py` и добавьте в него следующий код:

      {% include [searchindex-assistant](../../../_includes/ai-studio/examples/searchindex-assistant.md) %}

      Где:

      * `<путь_к_файлам_с_примерами>` — путь к директории, в которой сохранены скачанные ранее файлы. Например: `/Users/myuser/tours-example/`.

      {% include [sdk-code-legend](../../../_includes/ai-studio/examples/sdk-code-legend.md) %}

      * `<инструкция_для_стратегии_поиска>` — промт с инструкцией для модели по обращению к поисковому индексу. Например: `Выполняй поиск по базе знаний только в тех случаях, когда пользователь тебя специально об этом просит`.

  1. Выполните созданный файл:

      ```bash
      python3 search-assistant.py
      ```

      В примере реализован простейший вариант чата: вводите с клавиатуры запросы ассистенту и получайте ответы на них. Чтобы искать информацию в базе знаний, явно укажите это в запросе. Чтобы прекратить диалог, введите `exit`.

      {% cut "Примерный результат выполнения" %}

      ```text
      Введите ваш вопрос ассистенту ("exit" - чтобы завершить диалог): Сколько стоит виза на Бали?

      Ответ:  У меня нет информации о стоимости визы на Бали. Рекомендую обратиться к официальным источникам или консульству Индонезии для получения актуальной информации.

      Введите ваш вопрос ассистенту ("exit" - чтобы завершить диалог): Сколько стоит виза на Бали? Поищи в базе знаний, пожалуйста.

      Ответ:  Согласно найденной информации, стоимость визы на Бали составляет 300 рублей. Однако обратите внимание, что требования могут меняться, поэтому перед поездкой рекомендуется проверить актуальную информацию на сайте консульства или визового центра.

      * Содержимое фрагмента №1: ('**Бали — райский уголок, где вас ждут незабываемые впечатления!**\n\nПриглашаем вас провести незабываемый отпуск на Бали! Этот волшебный остров в Индонезии славится своими прекрасными пляжами, уникальной культурой и гостеприимными жителями. Здесь вы сможете насладиться красотой природы, попробовать местную кухню и познакомиться с новыми людьми. **Что нужно для поездки?** Для въезда на территорию Индонезии вам потребуется виза. Вот список документов, которые необходимы для её оформления:\n* Загранпаспорт, срок действия которого составляет не менее 6 месяцев на момент въезда в страну. * Две фотографии, соответствующие требованиям консульства.',)
      * Идентификатор поискового индекса в фрагменте №1: fvtacpbi0cg3********
      * Настройки типа поискового индекса в фрагменте №1: TextSearchIndexType(chunking_strategy=StaticIndexChunkingStrategy(max_chunk_size_tokens=700, chunk_overlap_tokens=300))
      * Идентификатор файла-источника для фрагмента №1: fvtg6bmrdvb3********
      * MIME-тип файла-источника для фрагмента №1: text/plain

      * Содержимое фрагмента №2: ('* Бронь или билеты туда и обратно. * Анкета, заполненная на английском языке. Обратите внимание, что требования могут меняться, поэтому перед поездкой рекомендуется проверить актуальную информацию на сайте консульства или визового центра. Стоимость визы 300 рублей. Не упустите возможность посетить этот прекрасный остров и получить массу положительных эмоций! Бронируйте свой отдых на Бали уже сегодня! **Мы ждём вас!**',)
      * Идентификатор поискового индекса в фрагменте №2: fvtacpbi0cg3********
      * Настройки типа поискового индекса в фрагменте №2: TextSearchIndexType(chunking_strategy=StaticIndexChunkingStrategy(max_chunk_size_tokens=700, chunk_overlap_tokens=300))
      * Идентификатор файла-источника для фрагмента №2: fvtg6bmrdvb3********
      * MIME-тип файла-источника для фрагмента №2: text/plain

      * Содержимое фрагмента №3: ('**Что нужно для поездки?** Для въезда на территорию Индонезии вам потребуется виза. Вот список документов, которые необходимы для её оформления:\n* Загранпаспорт, срок действия которого составляет не менее 6 месяцев на момент въезда в страну. * Две фотографии, соответствующие требованиям консульства. * Подтверждение бронирования отеля или письмо другого жилья. * Бронь или билеты туда и обратно. * Анкета, заполненная на английском языке. Обратите внимание, что требования могут меняться, поэтому перед поездкой рекомендуется проверить актуальную информацию на сайте консульства или визового центра. Стоимость визы 300 рублей.',)
      * Идентификатор поискового индекса в фрагменте №3: fvtacpbi0cg3********
      * Настройки типа поискового индекса в фрагменте №3: TextSearchIndexType(chunking_strategy=StaticIndexChunkingStrategy(max_chunk_size_tokens=700, chunk_overlap_tokens=300))
      * Идентификатор файла-источника для фрагмента №3: fvtg6bmrdvb3********
      * MIME-тип файла-источника для фрагмента №3: text/plain

      * Содержимое фрагмента №4: ('**Казахстан: путешествие в сердце Евразии**\n\nОткройте для себя Казахстан — удивительную страну, где встречаются Восток и Запад. Здесь вы сможете насладиться бескрайними степями, величественными горами, историческими памятниками и гостеприимством местных жителей. **Что нужно для поездки?** Чтобы попасть в Казахстан из России, вам потребуются следующие документы:\n* Загранпаспорт, срок действия которого составляет не менее 3 месяцев на момент окончания поездки. * Миграционная карта (выдаётся в самолете или на границе). * Медицинская страховка (не обязательна, но рекомендуется). Не упустите возможность посетить эту прекрасную страну и получить массу положительных эмоций!',)
      * Идентификатор поискового индекса в фрагменте №4: fvtacpbi0cg3********
      * Настройки типа поискового индекса в фрагменте №4: TextSearchIndexType(chunking_strategy=StaticIndexChunkingStrategy(max_chunk_size_tokens=700, chunk_overlap_tokens=300))
      * Идентификатор файла-источника для фрагмента №4: fvttf5sq7u0j********
      * MIME-тип файла-источника для фрагмента №4: text/plain

      * Содержимое фрагмента №5: ('* Миграционная карта (выдаётся в самолете или на границе). * Медицинская страховка (не обязательна, но рекомендуется). Не упустите возможность посетить эту прекрасную страну и получить массу положительных эмоций! Бронируйте свой отдых в Казахстане уже сегодня! **Мы ждём вас!**',)
      * Идентификатор поискового индекса в фрагменте №5: fvtacpbi0cg3********
      * Настройки типа поискового индекса в фрагменте №5: TextSearchIndexType(chunking_strategy=StaticIndexChunkingStrategy(max_chunk_size_tokens=700, chunk_overlap_tokens=300))
      * Идентификатор файла-источника для фрагмента №5: fvttf5sq7u0j********
      * MIME-тип файла-источника для фрагмента №5: text/plain

      Введите ваш вопрос ассистенту ("exit" - чтобы завершить диалог): exit
      ```

      {% endcut %}

      В свойстве `run.text` AI-ассистент вернул ответ модели, который был сгенерирован с использованием загруженной базы знаний. Свойство `run.citations` содержит [ссылки на источники](../../concepts/assistant/index.md#citations) — информацию об использованных в ответе файлах базы знаний и их фрагментах.

- cURL {#curl}

  Этот пример показывает, как создать [ассистента](../../concepts/assistant/index.md), который для ответов использует информацию из файлов. В примере будет показан базовый алгоритм работы с {{ assistant-api }} через интерфейс [REST API](../../assistants/api-ref/index.md) — создание индекса для гибридного поиска, создание ассистента и треда, а также последующее обращение к ассистенту с запросом.

  {% include [rag-default-strategy-paragraph](../../../_includes/ai-studio/assistants/rag-default-strategy-paragraph.md) %}

  Чтобы скорректировать такое поведение, в данном примере для ассистента будет настроена [стратегия](../../concepts/assistant/tools/vector-store.md) обращения к индексу, в соответствии с которой модель будет обращаться к дополнительным источникам информации только в тех случаях, которые определены в инструкции, заданной в секции `callStrategy`.

  1. Загрузите файл с контекстом для базы знаний с помощью [Files API](../../files/api-ref/File/index.md):
      1. {% include [download-context-file-step](../../../_includes/ai-studio/assistants/download-context-file-step.md) %}

          В качестве базы знаний в этом примере вы будете использовать файл `bali.md`, содержащийся в скачанном архиве.
      1. Кодируйте файл `bali.md` в формат [Base64]({{ link-base64 }}):

          ```bash
          base64 -i bali.md -o bali-b64coded.txt
          ```
      1. Создайте файл `file.json` с телом запроса на загрузку файла:

          **file.json**

          ```json
          {
            "folderId": "<идентификатор_каталога>",
            "mimeType": "text/markdown",
            "content": "<содержимое_файла>"
          }
          ```

          Где:
          * `folderId` — [идентификатор](../../../resource-manager/operations/folder/get-id.md) каталога, на который у вашего аккаунта есть [роли](../../../iam/concepts/access-control/roles.md) [`ai.assistants.editor`](../../security/index.md#ai-assistants-editor) и [`{{ roles-yagpt-user }}`](../../security/index.md#languageModels-user) или выше.
          * `mimeType` — [MIME-тип](../../concepts/assistant/index.md#files) загружаемого контента. В приведенном примере файл `bali.md` имеет тип `text/markdown`.
          * `content` — содержимое полученного на предыдущем шаге файла `bali-b64coded.txt` в кодировке Base64.
      1. Отправьте запрос на создание файла:

          ```bash
          export IAM_TOKEN=<IAM-токен>
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/files/v1/files" | \
            jq
          ```

          Где:
          * `<IAM-токен>` — IAM-токен, полученный [перед началом работы](#before-begin).
          * `<путь_к_телу_запроса>` — путь к созданному ранее файлу с телом запроса `file.json`.

          Результат:

          ```json
          {
            "id": "fvtjbljvmc0a********",
            "folder_id": "b1gt6g8ht345********",
            "name": "",
            "description": "",
            "mime_type": "text/markdown",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T08:25:45.544260Z",
            "updated_by": "ajeol2afu1js********",
            "updated_at": "2025-08-27T08:25:45.544260Z",
            "expiration_config": {
              "expiration_policy": "SINCE_LAST_ACTIVE",
              "ttl_days": "7"
            },
            "expires_at": "2025-09-03T08:25:45.544260Z",
            "labels": {}
          }
          ```

          В ответ сервис вернет идентификатор загруженного файла. Сохраните полученный идентификатор файла (значение поля `id`) — он понадобится на следующем шаге.
  1. Создайте [поисковый индекс](../../concepts/assistant/index.md#files):

      1. Создайте файл `index.json` с телом запроса на создание индекса, указав полученный ранее идентификатор файла:

          **index.json**

          ```json
          {
            "folderId": "<идентификатор_каталога>",
            "fileIds": [
              "<идентификатор_файла>"
            ],
            "hybridSearchIndex": {}
          }
          ```
      1. Отправьте запрос на создание поискового индекса, указав путь к созданному файлу `index.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/searchIndex" | \
            jq
          ```

          Результат:

          ```json
          {
            "id": "fvtcckldp96f********",
            "description": "search index creation",
            "created_at": "2025-08-27T08:27:25.755559Z",
            "created_by": "ajeol2afu1js********",
            "modified_at": "2025-08-27T08:27:25.755559Z",
            "done": false,
            "metadata": null
          }
          ```

          В ответе сервис вернет идентификатор объекта [Operation](../../../api-design-guide/concepts/operation.md). Сохраните этот идентификатор (значение поля `id`) — он понадобится на следующем шаге.
      1. Чтобы проверить готовность создания поискового индекса, запросите статус операции, указав сохраненный ранее идентификатор:

          ```bash
          curl \
            --request GET \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            "https://operation.{{ api-host }}/operations/<идентификатор_операции>" | \
            jq
          ```

          {% cut "Результат" %}

          ```json
          {
            "done": true,
            "response": {
              "@type": "type.googleapis.com/yandex.cloud.ai.assistants.v1.searchindex.SearchIndex",
              "expirationConfig": {
                "expirationPolicy": "SINCE_LAST_ACTIVE",
                "ttlDays": "7"
              },
              "hybridSearchIndex": {
                "textSearchIndex": {
                  "standardTokenizer": {},
                  "yandexLemmerAnalyzer": {}
                },
                "vectorSearchIndex": {
                  "docEmbedderUri": "emb://yc.ml.rag-prod.common/text-search-doc/latest",
                  "queryEmbedderUri": "emb://yc.ml.rag-prod.common/text-search-query/latest"
                },
                "chunkingStrategy": {
                  "staticStrategy": {
                    "maxChunkSizeTokens": "800",
                    "chunkOverlapTokens": "400"
                  }
                },
                "combinationStrategy": {
                  "meanCombination": {
                    "weights": [
                      0.5,
                      0.5
                    ],
                    "meanEvaluationTechnique": "ARITHMETIC"
                  }
                },
                "normalizationStrategy": "MIN_MAX"
              },
              "id": "fvti5snpooha********",
              "folderId": "b1gt6g8ht345********",
              "createdBy": "ajeol2afu1js********",
              "createdAt": "2025-08-27T08:27:25.791066Z",
              "updatedBy": "ajeol2afu1js********",
              "updatedAt": "2025-08-27T08:27:25.791066Z",
              "expiresAt": "2025-09-03T08:27:25.791066Z"
            },
            "id": "fvtcckldp96f********",
            "description": "search index creation",
            "createdAt": "2025-08-27T08:27:25.755559Z",
            "createdBy": "ajeol2afu1js********",
            "modifiedAt": "2025-08-27T08:27:31.930432Z"
          }
          ```

          {% endcut %}

          Значение `true` поля `done` говорит о том, что операция создания индекса завершена. Сохраните полученный идентификатор поискового индекса (значение поля `response.id`) — он понадобится при создании ассистента и [треда](../../concepts/assistant/index.md#content).
  1. Создайте AI-ассистента:

      1. Создайте файл `assistant.json` с телом запроса на создание ассистента:

          **assistant.json**

          ```json
          {
            "folderId": "<идентификатор_каталога>",
            "modelUri": "gpt://<идентификатор_каталога>/yandexgpt-lite/latest",
            "instruction": "Ты — помощник по внутренней документации компании. Отвечай вежливо. Если информация не содержится в документах ниже, не придумывай ответ.",
            "tools": [
              {
                "searchIndex": {
                  "searchIndexIds": [
                    "<идентификатор_индекса>"
                  ],
                  "callStrategy": {
                    "auto_call": {
                      "name": "knowledge_base_search",
                      "instruction": "<инструкция_для_стратегии_поиска>"
                    }
                  }
                }
              }
            ]
          }
          ```

          Где:
          * `modelUri` — [URI](../../concepts/generation/models.md#generation) используемой модели генерации текста.
          * `searchIndexIds` — полученный на предыдущем шаге идентификатор поискового индекса.
          * `instruction` — промт с инструкцией для модели по обращению к поисковому индексу. Например: `Выполняй поиск по базе знаний только в тех случаях, когда пользователь тебя специально об этом просит`.
      1. Отправьте запрос на создание AI-ассистента, указав путь к созданному файлу `assistant.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/assistants" | \
            jq
          ```

          {% cut "Результат" %}

          ```json
          {
            "id": "fvted8p4d1k1********",
            "folder_id": "b1gt6g8ht345********",
            "name": "",
            "description": "",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T08:42:09.842579Z",
            "updated_by": "ajeol2afu1js********",
            "updated_at": "2025-08-27T08:42:09.842579Z",
            "expiration_config": {
              "expiration_policy": "SINCE_LAST_ACTIVE",
              "ttl_days": "7"
            },
            "expires_at": "2025-09-03T08:42:09.842579Z",
            "labels": {},
            "model_uri": "gpt://b1gt6g8ht345********/yandexgpt-lite/latest",
            "instruction": "Ты — помощник по внутренней документации компании. Отвечай вежливо. Если информация не содержится в документах ниже, не придумывай ответ.",
            "prompt_truncation_options": {
              "max_prompt_tokens": null,
              "auto_strategy": {}
            },
            "completion_options": {
              "max_tokens": null,
              "temperature": null
            },
            "tools": [
              {
                "search_index": {
                  "search_index_ids": [
                    "fvti5snpooha********"
                  ],
                  "max_num_results": null,
                  "rephraser_options": null,
                  "call_strategy": {
                    "auto_call": {
                      "name": "knowledge_base_search",
                      "instruction": "Выполняй поиск по базе знаний только в тех случаях, когда пользователь тебя специально об этом просит."
                    }
                  }
                }
              }
            ],
            "response_format": null
          }
          ```

          {% endcut %}

          В ответ сервис вернет идентификатор созданного AI-ассистента. Сохраните полученный идентификатор (значение поля `id`), он понадобится при обращении к ассистенту.
  1. Создайте тред:

      1. Создайте файл `thread.json` с телом запроса на создание треда:

          **thread.json**

          ```json
          {
            "folderId": "<идентификатор_каталога>"
          }
          ```
      1. Отправьте запрос на создание треда, указав путь к созданному файлу `thread.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/threads" | \
            jq
          ```

          Результат:

          ```json
          {
            "id": "fvt8rn2ip0fg********",
            "folder_id": "b1gt6g8ht345********",
            "name": "",
            "description": "",
            "default_message_author_id": "fvtgsemis1k5********",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T08:51:05.255025Z",
            "updated_by": "ajeol2afu1js********",
            "updated_at": "2025-08-27T08:51:05.255025Z",
            "expiration_config": {
              "expiration_policy": "SINCE_LAST_ACTIVE",
              "ttl_days": "7"
            },
            "expires_at": "2025-09-03T08:51:05.255025Z",
            "labels": {},
            "tools": []
          }
          ```

          Сохраните полученный идентификатор треда (значение поля `id`) — он понадобится позднее.
  1. Создайте в треде сообщение без просьбы обратиться к базе знаний:

      1. Создайте файл `message.json` с телом запроса на создание сообщения, указав полученный ранее идентификатор треда и текст запроса в контексте загруженного файла с базой знаний:

          **message.json**

          ```json
          {
            "threadId": "<идентификатор_треда>",
            "content": {
              "content": [
                {
                  "text": {
                    "content": "Сколько стоит виза на Бали?"
                  }
                }
              ]
            }
          }
          ```
      1. Отправьте запрос на создание сообщения, указав путь к созданному файлу `message.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/messages" | \
            jq
          ```

          Результат:

          ```json
          {
            "id": "fvtel9i871ra********",
            "thread_id": "fvt8rn2ip0fg********",
            "created_by": "ajeol2afu1jsk89tn159",
            "created_at": "2025-08-27T08:58:47.758337Z",
            "author": {
              "id": "fvtgsemis1k5********",
              "role": "USER"
            },
            "labels": {},
            "content": {
              "content": [
                {
                  "text": {
                    "content": "Сколько стоит виза на Бали?"
                  }
                }
              ]
            },
            "status": "COMPLETED",
            "citations": []
          }
          ```
  1. Выполните запуск ассистента с созданным ранее сообщением:

      1. Создайте файл `run.json` с телом запроса на запуск ассистента, указав полученные ранее идентификаторы ассистента и треда:

          **run.json**

          ```json
          {
            "assistantId": "<идентификатор_ассистента>",
            "threadId": "<идентификатор_треда>"
          }
          ```

      1. Отправьте запрос на запуск ассистента, указав путь к созданному файлу `run.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/runs" | \
            jq
          ```

          Результат:

          ```json
          {
            "id": "fvtg5cv4pmc6********",
            "assistant_id": "fvted8p4d1k1********",
            "thread_id": "fvt8rn2ip0fg********",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T09:12:32.398612020Z",
            "labels": {},
            "state": {
              "status": "PENDING"
            },
            "usage": null,
            "custom_prompt_truncation_options": null,
            "custom_completion_options": null,
            "tools": [],
            "custom_response_format": null
          }
          ```

          Сервис вернул информацию о запуске: запуск находится в ожидании выполнения (статус `PENDING`). Сохраните идентификатор запуска (значение поля `id`) — он понадобится на следующем шаге.

      1. Получите результат запуска с ответом ассистента. Для этого выполните запрос, указав полученный ранее идентификатор запуска:

          ```bash
          curl \
            --request GET \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            "https://rest-assistant.{{ api-host }}/assistants/v1/runs/<идентификатор_запуска>" | \
            jq
          ```

          {% cut "Результат" %}

          ```json
          {
            "id": "fvtg5cv4pmc6********",
            "assistant_id": "fvted8p4d1k1********",
            "thread_id": "fvt8rn2ip0fg********",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T09:12:32.398612020Z",
            "labels": {},
            "state": {
              "status": "COMPLETED",
              "completed_message": {
                "id": "fvt096hn7kf5********",
                "thread_id": "fvt8rn2ip0fg********",
                "created_by": "ajeol2afu1js********",
                "created_at": "2025-08-27T09:12:32.855568027Z",
                "author": {
                  "id": "fvted8p4d1k1********",
                  "role": "ASSISTANT"
                },
                "labels": {},
                "content": {
                  "content": [
                    {
                      "text": {
                        "content": "К сожалению, у меня нет информации по стоимости визы на Бали в базе знаний."
                      }
                    }
                  ]
                },
                "status": "COMPLETED",
                "citations": []
              }
            },
            "usage": {
              "prompt_tokens": "114",
              "completion_tokens": "16",
              "total_tokens": "130"
            },
            "custom_prompt_truncation_options": null,
            "custom_completion_options": null,
            "tools": [],
            "custom_response_format": null
          }
          ```

          {% endcut %}

          Как видно из результата, в поле `content` AI-ассистент вернул ответ модели, который был сгенерирован без использования загруженной базы знаний. Это связано с тем, что в настройках [стратегии](../../concepts/assistant/tools/vector-store.md) обращения к индексу есть инструкция использовать базу знаний только в том случае, если пользователь специально попросит об этом.
  1. Теперь создайте в треде новое сообщение, содержащее просьбу обратиться к базе знаний:

      1. Измените файл `message.json` с телом запроса на создание сообщения, указав обновленный текст запроса в контексте загруженного файла с базой знаний:

          **message.json**

          ```json
          {
            "threadId": "<идентификатор_треда>",
            "content": {
              "content": [
                {
                  "text": {
                    "content": "Сколько стоит виза на Бали? Поищи в базе знаний, пожалуйста."
                  }
                }
              ]
            }
          }
          ```
      1. Отправьте запрос на создание сообщения, указав путь к созданному файлу `message.json` с телом запроса:

          ```bash
          curl \
            --request POST \
            --header "Authorization: Bearer ${IAM_TOKEN}" \
            --silent \
            --data "@<путь_к_телу_запроса>" \
            "https://rest-assistant.{{ api-host }}/assistants/v1/messages" | \
            jq
          ```

          Результат:

          ```json
          {
            "id": "fvtpcs3ef1ve********",
            "thread_id": "fvt8rn2ip0fg********",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T09:35:00.209975Z",
            "author": {
              "id": "fvtgsemis1k5********",
              "role": "USER"
            },
            "labels": {},
            "content": {
              "content": [
                {
                  "text": {
                    "content": "Сколько стоит виза на Бали? Поищи в базе знаний, пожалуйста."
                  }
                }
              ]
            },
            "status": "COMPLETED",
            "citations": []
          }
          ```
  1. Повторно выполните запуск ассистента, указав путь к созданному ранее файлу `run.json` с телом запроса:

      ```bash
      curl \
        --request POST \
        --header "Authorization: Bearer ${IAM_TOKEN}" \
        --silent \
        --data "@<путь_к_телу_запроса>" \
        "https://rest-assistant.{{ api-host }}/assistants/v1/runs" | \
        jq
      ```

      Результат:

      ```json
      {
        "id": "fvtfn4h45gpj********",
        "assistant_id": "fvted8p4d1k1********",
        "thread_id": "fvt8rn2ip0fg********",
        "created_by": "ajeol2afu1js********",
        "created_at": "2025-08-27T09:37:48.807600598Z",
        "labels": {},
        "state": {
          "status": "PENDING"
        },
        "usage": null,
        "custom_prompt_truncation_options": null,
        "custom_completion_options": null,
        "tools": [],
        "custom_response_format": null
      }
      ```

      Сервис вернул информацию о запуске: запуск находится в ожидании выполнения (статус `PENDING`). Сохраните идентификатор запуска (значение поля `id`) — он понадобится на следующем шаге.
  1. Получите результат запуска со вторым ответом ассистента. Для этого выполните запрос, указав полученный ранее идентификатор запуска:

      ```bash
      curl \
        --request GET \
        --header "Authorization: Bearer ${IAM_TOKEN}" \
        --silent \
        "https://rest-assistant.{{ api-host }}/assistants/v1/runs/<идентификатор_запуска>" | \
        jq
      ```

      {% cut "Результат" %}

      ```json
      {
        "id": "fvtfn4h45gpj********",
        "assistant_id": "fvted8p4d1k1********",
        "thread_id": "fvt8rn2ip0fg********",
        "created_by": "ajeol2afu1js********",
        "created_at": "2025-08-27T09:37:48.807600598Z",
        "labels": {},
        "state": {
          "status": "COMPLETED",
          "completed_message": {
            "id": "fvtusjjsbbv9********",
            "thread_id": "fvt8rn2ip0fg********",
            "created_by": "ajeol2afu1js********",
            "created_at": "2025-08-27T09:37:49.837330438Z",
            "author": {
              "id": "fvted8p4d1k1********",
              "role": "ASSISTANT"
            },
            "labels": {},
            "content": {
              "content": [
                {
                  "text": {
                    "content": "Виза на Бали стоит 300 рублей."
                  }
                }
              ]
            },
            "status": "COMPLETED",
            "citations": [
              {
                "sources": [
                  {
                    "chunk": {
                      "search_index": {
                        "id": "fvti5snpooha********",
                        "folder_id": "b1gt6g8ht345********",
                        "name": "",
                        "description": "",
                        "created_by": "ajeol2afu1js********",
                        "created_at": "2025-08-27T08:27:25.791066Z",
                        "updated_by": "ajeol2afu1js********",
                        "updated_at": "2025-08-27T08:27:31.925785Z",
                        "expiration_config": {
                          "expiration_policy": "SINCE_LAST_ACTIVE",
                          "ttl_days": "7"
                        },
                        "expires_at": "2025-09-03T09:12:32.856123Z",
                        "labels": {},
                        "hybrid_search_index": {
                          "text_search_index": {
                            "chunking_strategy": null,
                            "standard_tokenizer": {},
                            "yandex_lemmer_analyzer": {}
                          },
                          "vector_search_index": {
                            "doc_embedder_uri": "emb://yc.ml.rag-prod.common/text-search-doc/latest",
                            "query_embedder_uri": "emb://yc.ml.rag-prod.common/text-search-query/latest",
                            "chunking_strategy": null
                          },
                          "chunking_strategy": {
                            "static_strategy": {
                              "max_chunk_size_tokens": "800",
                              "chunk_overlap_tokens": "400"
                            }
                          },
                          "normalization_strategy": "MIN_MAX",
                          "combination_strategy": {
                            "mean_combination": {
                              "mean_evaluation_technique": "ARITHMETIC",
                              "weights": [
                                0.5,
                                0.5
                              ]
                            }
                          }
                        }
                      },
                      "source_file": {
                        "id": "fvtjbljvmc0a********",
                        "folder_id": "b1gt6g8ht345********",
                        "name": "",
                        "description": "",
                        "mime_type": "text/markdown",
                        "created_by": "ajeol2afu1js********",
                        "created_at": "2025-08-27T08:25:45.544260Z",
                        "updated_by": "ajeol2afu1js********",
                        "updated_at": "2025-08-27T08:25:45.544260Z",
                        "expiration_config": {
                          "expiration_policy": "SINCE_LAST_ACTIVE",
                          "ttl_days": "7"
                        },
                        "expires_at": "2025-09-03T08:27:25.817732Z",
                        "labels": {}
                      },
                      "content": {
                        "content": [
                          {
                            "text": {
                              "content": "**Бали — райский уголок, где вас ждут незабываемые впечатления!**\n\nПриглашаем вас провести незабываемый отпуск на Бали! Этот волшебный остров в Индонезии славится своими прекрасными пляжами, уникальной культурой и гостеприимными жителями. Здесь вы сможете насладиться красотой природы, попробовать местную кухню и познакомиться с новыми людьми. **Что нужно для поездки?** Для въезда на территорию Индонезии вам потребуется виза. Вот список документов, которые необходимы для её оформления:\n* Загранпаспорт, срок действия которого составляет не менее 6 месяцев на момент въезда в страну. * Две фотографии, соответствующие требованиям консульства. * Подтверждение бронирования отеля или письмо другого жилья. * Бронь или билеты туда и обратно. * Анкета, заполненная на английском языке. Обратите внимание, что требования могут меняться, поэтому перед поездкой рекомендуется проверить актуальную информацию на сайте консульства или визового центра. Стоимость визы 300 рублей. Не упустите возможность посетить этот прекрасный остров и получить массу положительных эмоций! Бронируйте свой отдых на Бали уже сегодня! **Мы ждём вас!**"
                            }
                          }
                        ]
                      }
                    }
                  }
                ]
              }
            ]
          }
        },
        "usage": {
          "prompt_tokens": "542",
          "completion_tokens": "29",
          "total_tokens": "571"
        },
        "custom_prompt_truncation_options": null,
        "custom_completion_options": null,
        "tools": [],
        "custom_response_format": null
      }
      ```

      {% endcut %}

      Как видно из результата, на этот раз в поле `content` AI-ассистент вернул ответ модели, который был сгенерирован с использованием загруженной базы знаний, поскольку вы специально его об этом попросили. Кроме того, секция `citations` содержит [ссылки на источники](../../concepts/assistant/index.md#citations) — информацию об использованных в ответе файлах базы знаний и их фрагментах.

{% endlist %}

#### См. также {#see-also}

* [{#T}](./create.md)
* [{#T}](./create-with-labels.md)
* [{#T}](./create-with-websearch.md)
* [{#T}](../../tutorials/pdf-searchindex-ai-assistant.md)
* [{#T}](../../concepts/assistant/tools/index.md)
* Примеры работы с {{ ml-sdk-name }} на [GitHub](https://github.com/yandex-cloud/yandex-cloud-ml-sdk/tree/master/examples/sync/assistants)
