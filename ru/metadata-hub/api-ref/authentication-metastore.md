---
title: Аутентификация в API {{ metastore-name }}
description: Для выполнения операций через API в сервисе {{ metastore-name }}, необходимо получить IAM-токен для своего аккаунта.
---

# Аутентификация в API {{ metastore-full-name }}

Для выполнения операций через [API](../../glossary/rest-api.md) необходимо получить [IAM-токен](../../iam/concepts/authorization/iam-token.md):

* [Инструкция](../../iam/operations/iam-token/create.md) для аккаунта на Яндексе.
* [Инструкция](../../iam/operations/iam-token/create-for-sa.md) для сервисного аккаунта.
* [Инструкция](../../iam/operations/iam-token/create-for-federation.md) для федеративного аккаунта.

{% include [iam-token-usage](../../_includes/iam-token-usage.md) %}

#### См. также {#see-also}

[{#T}](../../iam/concepts/users/accounts.md)

{% include [metastore-trademark](../../_includes/metadata-hub/metastore-trademark.md) %}
