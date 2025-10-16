Роль `organization-manager.passportUserAdmin` позволяет просматривать информацию о пользователях организации, а также приглашать в организацию и исключать из нее пользователей с аккаунтами на Яндексе.

Пользователи с этой ролью могут:
* [приглашать](../../organization/operations/add-account.md#send-invitation), в том числе [повторно](../../organization/operations/add-account.md#resend-invitation), в организацию новых [пользователей](../../organization/concepts/membership.md) с аккаунтами на Яндексе, а также просматривать и [удалять](../../organization/operations/add-account.md#delete-invitation) отправленные приглашения;
* [удалять](../../organization/operations/edit-account.md) аккаунты пользователей из [организации](../../organization/concepts/organization.md);
* просматривать список пользователей организации и сведения в профилях пользователей (кроме номера телефона), дату последней аутентификации, а также дату последней верификации федеративных и локальных аккаунтов с помощью [двухфакторной аутентификации](../../organization/concepts/mfa.md);
* просматривать [атрибуты](../../organization/operations/setup-federation.md#claims-mapping) [федеративных](../../iam/concepts/users/accounts.md#saml-federation) и [локальных](../../iam/concepts/users/accounts.md#local) пользователей организации.
