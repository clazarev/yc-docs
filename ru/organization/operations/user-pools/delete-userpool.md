---
title: Как удалить пул пользователей в {{ org-full-name }}
description: Следуя данной инструкции, вы сможете удалить пул пользователей в {{ org-name }}.
---

# Удалить пул пользователей


{% include [note-preview](../../../_includes/note-preview.md) %}

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}) с учетной записью администратора или владельца организации.
  1. На панели слева выберите ![userpool](../../../_assets/organization/userpool.svg) **{{ ui-key.yacloud_org.pages.userpools }}**.
  1. В строке с нужным [пулом пользователей](../../concepts/user-pools.md) нажмите ![image](../../../_assets/console-icons/ellipsis.svg) и выберите ![image](../../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.common.delete }}**.
  1. В открывшемся окне подтвердите удаление пула пользователей.

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для удаления [пула пользователей](../../concepts/user-pools.md):

     ```bash
     yc organization-manager idp userpool delete --help
     ```

  1. Выполните команду:

     ```bash
     yc organization-manager idp userpool delete <идентификатор_пула>
     ```

{% endlist %}