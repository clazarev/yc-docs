## Федеративные пользователи {#user-sso}

Если при [настройке федерации](../../organization/concepts/add-federation.md#federation-usage) вы не включили опцию **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}**, федеративных пользователей нужно добавить в организацию вручную.

Для этого необходимо знать Name ID пользователей, который вместе с ответом об успешной аутентификации возвращает сервер поставщика удостоверений (IdP). Обычно это электронная почта пользователя. Чтобы узнать, что возвращается в качестве Name ID, обратитесь к администратору, который настраивал аутентификацию в вашей федерации.

{% include [auto-create-users](../../_includes/organization/auto-create-users.md) %}

Федеративных пользователей не нужно отдельно приглашать в организацию, они добавляются автоматически после первой аутентификации.

Добавлять в организацию федеративных пользователей и удалять их могут пользователи с любой из следующих [ролей](../../organization/security/index.md): `organization-manager.federations.userAdmin`, `organization-manager.federations.admin`, `organization-manager.admin` или `organization-manager.organizations.owner`.

### Добавьте федеративных пользователей {#add-user-sso}

{% include notitle [user-sso](add-user-sso.md) %}
