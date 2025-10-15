---
title: Как изменить данные пользователя в пуле в {{ org-full-name }}
description: Следуя данной инструкции, вы сможете изменить данные пользователя в пуле в {{ org-full-name }}.
---

# Изменить данные пользователя


{% include [note-preview](../../../_includes/note-preview.md) %}

Изменять пользовательские данные можно только у [локальных](../../../iam/concepts/users/accounts.md#local) пользователей.

Чтобы изменить данные локального пользователя:

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}) с учетной записью администратора или владельца организации.
  1. На панели слева выберите ![icon-users](../../../_assets/console-icons/person.svg) **{{ ui-key.yacloud_org.pages.users }}** и в открывшемся списке найдите нужного локального пользователя.

        При необходимости воспользуйтесь строкой поиска и фильтрами в верхней части экрана.
  1. В строке с нужным пользователем нажмите значок ![image](../../../_assets/console-icons/ellipsis.svg) и выберите ![person-pencil](../../../_assets/console-icons/person-pencil.svg) **{{ ui-key.yacloud_org.page.user.action_edit-user }}**. В открывшемся окне:

      1. Измените имя пользователя, [домен](../../concepts/domains.md) и полное имя.
      1. Добавьте или измените адрес электронной почты.
      1. Разверните блок **{{ ui-key.yacloud_org.organization.userpools.title_personal-info }}** и измените имя, фамилию и номер телефона пользователя.
      1. Нажмите кнопку **{{ ui-key.yacloud_org.organization.userpools.action_update-user }}**.

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для изменения данных пользователя в [пуле](../../concepts/user-pools.md):

     ```bash
     yc organization-manager idp user update --help
     ```
  1. {% include [cli-list-userpools](../../../_includes/organization/cli-list-userpools.md) %}
  1. {% include [cli-userpool-list-users](../../../_includes/organization/cli-userpool-list-users.md) %}
  1. Чтобы изменить пользователя, передайте его идентификатор и другие параметры в команде:

     ```bash
     yc organization-manager idp user update <идентификатор_пользователя> \
       --username <логин_и_домен_пользователя> \
       --full-name <имя_и_фамилия_пользователя> \
       --given-name <имя_пользователя> \
       --family-name <фамилия_пользователя> \
       --email <электронная_почта_пользователя> \
       --phone-number <номер_телефона_пользователя>
     ```

     Где:     

     * `--username` — новые логин и домен пользователя в формате `логин@домен`. Логин должен быть уникальным для данного пула пользователей.
     * `--full-name` — новые имя и фамилия пользователя.
     * `--given-name` — новое имя пользователя.
     * `--family-name` — новая фамилия пользователя.
     * `--email` — новая электронная почта пользователя.
     * `--phone-number` — новый номер телефона пользователя.

{% endlist %}