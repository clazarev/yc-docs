---
title: Корпоративный мерч
description: В статье вы ознакомитесь с промтом для создания корпоративного мерча.
---

# Корпоративный мерч

## Параметры запроса {#params}

* **Промт**: Худи свободного кроя, жёлтого цвета, в центре иллюстрация с логотипом в виде рисунка кометы чёрного цвета. Минималистичная съемка товара, на светлом однотонном фоне, на модели со светлыми волосами.
* **Зерно**: `11`
* **Результат**:

![brand-design-corporate-merch](../../../_assets/yandexgpt/brand-design-corporate-merch.jpg)

## Структура запроса {#structure}

```json
{
  "modelUri": "art://<идентификатор_каталога>/yandex-art/latest",
  "generationOptions": {
    "seed": 11
  },
  "messages": [
    {
      "text": "Худи свободного кроя, жёлтого цвета, в центре иллюстрация с логотипом в виде рисунка кометы чёрного цвета. Минималистичная съемка товара, на светлом однотонном фоне, на модели со светлыми волосами"
    }
  ]
}
```

{% include [prompt-structure](../../../_includes/ai-studio/yandexart/api-parameters.md) %}

{% list tabs group=programming_language %}

- cURL {#curl}

  {% include [prompt-structure](../../../_includes/ai-studio/yandexart/prompt-request.md) %}

{% endlist %}

## Получение результата {#result}

{% include [prompt-result](../../../_includes/ai-studio/yandexart/prompt-result.md) %}
