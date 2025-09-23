{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}) с учетной записью администратора или владельца организации.
  1. На панели слева нажмите ![userpool](../../_assets/organization/userpool.svg) **{{ ui-key.yacloud_org.pages.userpools }}** и выберите нужный пул пользователей.
  1. В верхнем правом углу нажмите **{{ ui-key.yacloud_org.organization.userpools.action.add_domain }}**.
  1. Введите имя домена. Домен не должен быть привязан к другому пулу пользователей или федерации.
  1. Нажмите **{{ ui-key.yacloud.common.add }}**.

  Чтобы использовать домен для добавления новых пользователей, [подтвердите](../../organization/operations/user-pools/validate-domain.md) его.

{% endlist %}