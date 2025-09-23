---
title: Как реализовать поиск с использованием эмбеддингов
description: Следуя данной инструкции, вы научитесь использовать модели векторного представления текста {{ foundation-models-full-name }} для реализации поиска по базе знаний с помощью эмбеддингов.
---

# Использовать эмбеддинги в поиске по базе знаний

С помощью [эмбеддингов](../../concepts/embeddings.md) вы можете найти в базе знаний наиболее близкий ответ на заданный вопрос.

Если базовая модель эмбеддингов вам не подходит, вы можете [дообучить](../tuning/create-embeddings.md) ее.

## Перед началом работы {#before-begin}

Чтобы воспользоваться примерами:

{% list tabs group=programming_language %}

- SDK {#sdk}

  {% include [sdk-before-begin-ai-langmodel-user](../../../_includes/ai-studio/sdk-before-begin-ai-langmodel-user.md) %}

- Python 3 {#python}

  1. [Создайте](../../../iam/operations/sa/create.md) сервисный аккаунт и [назначьте](../../../iam/operations/sa/assign-role-for-sa.md) ему [роль](../../security/index.md#languageModels-user) `ai.languageModels.user`.
  1. [Получите](../../../iam/operations/iam-token/create-for-sa.md) IAM-токен для сервисного аккаунта.

{% endlist %}

## Выполните поиск {#search}

В приведенном примере в массиве `doc_texts` собраны исходные данные для векторизации (база знаний), переменная `query_text` содержит текст поискового запроса. После получения эмбеддингов вычисляется расстояние между каждым вектором в базе знаний и вектором запроса и определяется наиболее близкий текст в базе знаний.

{% note info %}

{% include [sdk-input-format](../../../_includes/ai-studio/sdk-input-format.md) %}

{% endnote %}

{% list tabs group=programming_language %}

- SDK {#sdk}

  1. Создайте файл `search-knowledge-base.py` и добавьте в него следующий код:

      {% include [search-embedding-sdk](../../../_includes/ai-studio/examples/search-embedding-sdk.md) %}

      Где:

      {% include [sdk-code-legend](../../../_includes/ai-studio/examples/sdk-code-legend.md) %}

      Подробнее о том, как обращаться к моделям векторного представления текста, см. в разделе [{#T}](../../concepts/embeddings.md#addressing-models).

  1. Выполните созданный файл:

      ```bash
      python3 search-knowledge-base.py
      ```

      Результат:

      ```text
      Александр Сергеевич Пушкин (26 мая [6 июня] 1799, Москва — 29 января [10 февраля] 1837, Санкт-Петербург)
          — русский поэт, драматург и прозаик, заложивший основы русского реалистического направления,
          литературный критик и теоретик литературы, историк, публицист, журналист.
      ```

- Python 3 {#python}

  1. Создайте файл `search-knowledge-base.py` и добавьте в него следующий код:

      {% include [search-embedding-py](../../../_includes/ai-studio/examples/search-embedding-py.md) %}

      Где:

      * `<идентификатор_каталога>` — идентификатор [каталога](../../../resource-manager/concepts/resources-hierarchy.md#folder) {{ yandex-cloud }}.
      * `<IAM-токен>` — [IAM-токен](../../../iam/concepts/authorization/iam-token.md) сервисного аккаунта, полученный ранее и необходимый для [аутентификации в API](../../api-ref/authentication.md).

  1. Выполните созданный файл:

      ```bash
      python3 search-knowledge-base.py
      ```

      Результат:

      ```text
      Александр Сергеевич Пушкин (26 мая [6 июня] 1799, Москва — 29 января [10 февраля] 1837, Санкт-Петербург) — русский поэт, драматург и прозаик, заложивший основы русского реалистического направления, литературный критик и теоретик литературы, историк, публицист, журналист.
      ```

{% endlist %}

#### См. также {#see-also}

* [{#T}](../../concepts/embeddings.md)
* Примеры работы с {{ ml-sdk-name }} на [GitHub](https://github.com/yandex-cloud/yandex-cloud-ml-sdk/tree/master/examples/sync/text_embeddings)
* [{#T}](../tuning/create-embeddings.md)