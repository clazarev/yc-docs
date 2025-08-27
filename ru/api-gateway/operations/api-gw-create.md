---
title: Создание API-шлюза
description: Следуя данной инструкции, вы сможете создать API-шлюз.
---

# Создание API-шлюза

Перед началом работы подготовьте файл спецификации API по стандарту [OpenAPI 3.0](https://github.com/OAI/OpenAPI-Specification).

{% cut "Пример файла спецификации `hello-world.yaml`" %}

```yaml
openapi: "3.0.0"
info:
  version: 1.0.0
  title: Test API
paths:
  /hello:
    get:
      summary: Say hello
      operationId: hello
      parameters:
        - name: user
          in: query
          description: User name to appear in greetings.
          required: false
          schema:
            type: string
            default: 'world'
      responses:
        '200':
          description: Greeting.
          content:
            'text/plain':
                schema:
                  type: "string"
      x-yc-apigateway-integration:
        type: dummy
        http_code: 200
        http_headers:
          'Content-Type': "text/plain"
        content:
          'text/plain': "Hello, {user}!\n"
```

{% endcut %}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) перейдите в [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором необходимо создать [API-шлюз](../concepts/index.md).
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_api-gateway }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.serverless-functions.gateways.list.button_create }}**.
  1. В поле **{{ ui-key.yacloud.common.name }}** введите название API-шлюза.
  1. (Опционально) В поле **{{ ui-key.yacloud.common.description }}** введите описание API-шлюза.
  1. В поле **{{ ui-key.yacloud.serverless-functions.gateways.form.label_execution-timeout }}** задайте таймаут обработки запроса. Значение не должно превышать установленный [лимит](../concepts/limits.md#api-gw-limits).
  1. В блок **{{ ui-key.yacloud.serverless-functions.gateways.form.field_spec }}** добавьте текст [спецификаций OpenAPI](https://ru.wikipedia.org/wiki/OpenAPI_(спецификация)).

      {% include [add-extentions-constructor](../../_includes/api-gateway/add-extentions-constructor.md) %}

  1. При необходимости задайте дополнительные настройки API-шлюза.
  1. Нажмите кнопку **{{ ui-key.yacloud.serverless-functions.gateways.form.button_create-gateway }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы создать [API-шлюз](../concepts/index.md):
  1. Посмотрите описание команды CLI для создания API-шлюза:

     ```bash
     {{ yc-serverless }} api-gateway create --help
     ```

  1. Укажите параметры API-шлюза в команде создания (в примере приведены не все доступные параметры):

     ```bash
     {{ yc-serverless }} api-gateway create \
       --name <имя_API-шлюза> \
       --execution-timeout <таймаут_обработки_запроса> \
       --spec=<путь_к_файлу_спецификации>
     ```

     Где:
     * `--name` — имя API-шлюза:

         {% include [name-format](../../_includes/name-format.md) %}
     * `--execution-timeout` — таймаут обработки запроса. Значение задается в секундах и не должно превышать установленный [лимит](../concepts/limits.md#api-gw-limits). Необязательный параметр. Значение по умолчанию – `300` сек.
     * `--spec` — путь к созданному ранее файлу со спецификацией.

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  {% include [terraform-create](../../_includes/api-gateway/terraform-create.md) %}

- API {#api}

  Чтобы создать [API-шлюз](../concepts/index.md), воспользуйтесь методом REST API [create](../apigateway/api-ref/ApiGateway/create.md) для ресурса [ApiGateway](../apigateway/api-ref/ApiGateway/index.md) или вызовом gRPC API [ApiGatewayService/Create](../apigateway/api-ref/grpc/ApiGateway/create.md).


{% endlist %}