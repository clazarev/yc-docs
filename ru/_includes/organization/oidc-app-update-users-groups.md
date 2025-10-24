 {% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}).
  1. На панели слева выберите ![shapes-4](../../_assets/console-icons/shapes-4.svg) **{{ ui-key.yacloud_org.pages.apps }}** и выберите нужное OIDC-приложение.
  1. Перейдите на вкладку **{{ ui-key.yacloud_org.organization.apps.AppPageLayout.assignments_kKzJS }}**.
  1. Чтобы добавить в приложение пользователя или группу пользователей:

      1. Нажмите кнопку ![person-plus](../../_assets/console-icons/person-plus.svg) **{{ ui-key.yacloud_org.organization.apps.AppAssignmentsPage.action_add-assignments }}**.
      1. В открывшемся окне выберите нужного пользователя или группу пользователей.
      1. Нажмите кнопку **{{ ui-key.yacloud.common.add }}**.
  1. Чтобы удалить пользователя или группу пользователей из приложения:

      1. В списке пользователей и групп в строке с нужным пользователем или группой нажмите значок ![ellipsis](../../_assets/console-icons/ellipsis.svg) и выберите ![trash-bin](../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.common.delete }}**.
      1. Подтвердите удаление.

- CLI {#cli}

  {% include [cli-install](../cli-install.md) %}

  {% include [default-catalogue](../default-catalogue.md) %}

  1. Получите [идентификатор пользователя](../../organization/operations/users-get.md) или [группы пользователей](../../organization/operations/group-get-id.md).

  1. Чтобы добавить в приложение пользователя или группу пользователей:
  
     1. Посмотрите описание команды CLI для добавления пользователей в приложение:
   
        ```bash
        yc organization-manager idp application oauth application add-assignments --help
        ```
   
     1. Выполните команду:
   
        ```bash
        yc organization-manager idp application oauth application add-assignments \
          --id <идентификатор_приложения> \
          --subject-id ek0omvvcb9vv********
        ```
   
        Где:
   
        * `--id` — идентификатор приложения.
        * `--subject-id` — идентификатор нужного пользователя или группы пользователей.
   
        Результат:
   
        ```text
        assignment_deltas:
          - action: ADD
            assignment:
              subject_id: ajetvnq2mil8********
        ```

  1. Чтобы удалить пользователя или группу пользователей из приложения:

     1. Посмотрите описание команды CLI для удаления пользователей из приложения:
   
        ```bash
        yc organization-manager idp application oauth application remove-assignments --help
        ```
   
     1. Выполните команду:
   
        ```bash
        yc organization-manager idp application oauth application remove-assignments \
          --id <идентификатор_приложения> \
          --subject-id <идентификатор_пользователя>
        ```
   
        Где:
   
        * `--id` — идентификатор OIDC-приложения.
        * `--subject-id` — идентификатор нужного пользователя или группы пользователей.
   
        Результат:
   
        ```text
        assignment_deltas:
          - action: REMOVE
            assignment:
              subject_id: ajetvnq2mil8********
        ```

{% endlist %}