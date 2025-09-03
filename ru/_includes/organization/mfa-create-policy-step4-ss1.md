* `{{ ui-key.yacloud_org.organization.security-settings.acr_any-mfa_j6PdY }}`. В этом варианте пользователи должны будут выбрать один из следующих стандартов дополнительного фактора аутентификации:

    * [WebAuthn](https://en.wikipedia.org/wiki/WebAuthn) ([FIDO2](https://en.wikipedia.org/wiki/FIDO_Alliance#FIDO2)). Дополнительным фактором аутентификации могут выступать, например, аппаратные ключи, такие как [Рутокен](https://www.rutoken.ru/) или [YubiKey](https://developers.yubico.com/Passkeys/), аутентификаторы [Passkeys](https://www.passkeys.com/), платформенные аутентификаторы, такие как [Windows Hello](https://www.microsoft.com/en-us/windows/tips/windows-hello), и т.п.

        {% note warning %}

        Расширения браузера, у которых есть возможность управлять вводом паролей, могут вызывать ошибки при вводе дополнительных факторов. При возникновении ошибок рекомендуется отключать такие расширения.

        {% endnote %}

    * [TOTP](https://ru.wikipedia.org/wiki/TOTP). Дополнительным фактором аутентификации будут выступать одноразовые коды, генерируемые специальными приложениями-аутентификаторами.
* `{{ ui-key.yacloud_org.organization.security-settings.acr_phr_7hMqk }}`. В этом варианте пользователь должен будет использовать только факторы аутентификации по стандарту [WebAuthn](https://en.wikipedia.org/wiki/WebAuthn) как наиболее безопасные.