---
title: Как удалить пользователя из пула в {{ org-full-name }}
description: Следуя данной инструкции, вы сможете удалить пользователя из пула в {{ org-name }}.
---

# Удалить пользователя


{% include [note-preview](../../../_includes/note-preview.md) %}

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}) с учетной записью администратора или владельца организации.
  1. На панели слева нажмите ![userpool](../../../_assets/organization/userpool.svg) **{{ ui-key.yacloud_org.pages.userpools }}** и выберите нужный [пул пользователей](../../concepts/user-pools.md).
  1. Перейдите на вкладку **{{ ui-key.yacloud_org.organization.userpools.title_userpool_members }}**.
  1. В строке с нужным пользователем нажмите ![image](../../../_assets/console-icons/ellipsis.svg) и выберите ![image](../../../_assets/console-icons/trash-bin.svg) **Удалить пользователя**.
  1. В открывшемся окне подтвердите удаление пользователя из пула.

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для удаления пользователя из [пула](../../concepts/user-pools.md):

     ```bash
     yc organization-manager idp user delete --help
     ```

  1. Выполните команду:

     ```bash
     yc organization-manager idp user delete <идентификатор_пользователя>
     ```

{% endlist %}