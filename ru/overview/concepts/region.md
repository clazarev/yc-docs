# Регионы

Регион — географическая область, которая объединяет [зоны доступности](geo-scope.md). Каждый регион имеет свою инфраструктуру и [набор сервисов](services.md).

Регион пользователя зависит от консоли управления, в которой он регистрировался. Пользователь видит в консоли управления только сервисы и ресурсы выбранного региона.

В {{ yandex-cloud }} доступны следующие регионы:

Регион | Россия | Казахстан
--- | --- | ---
Зоны доступности | `ru-central1-a`<br/>`ru-central1-b`<br/>`ru-central1-d` | `kz1-a`
Консоль управления | [https://console.yandex.cloud](https://console.yandex.cloud) | [https://kz.console.yandex.cloud](https://kz.console.yandex.cloud)
Оплата ресурсов | [Тарифы для региона Россия](https://yandex.cloud/ru/prices) | [Тарифы для региона Казахстан](https://yandex.cloud/ru-kz/prices)
Сервис {{ billing-name }} | [https://center.yandex.cloud/billing](https://center.yandex.cloud/billing) | [https://kz.center.yandex.cloud/billing](https://kz.center.yandex.cloud/billing)
Эндпоинт {{ objstorage-full-name }} | https://storage.yandexcloud.net | https://storage.yandexcloud.kz

В разных регионах разные:

* адреса эндпоинтов {{ yandex-cloud }} API:
    * [адреса эндпоинтов API в регионе Россия](https://yandex.cloud/ru/docs/api-design-guide/concepts/endpoints)
    * [адреса эндпоинтов API в регионе Казахстан](https://yandex.cloud/ru-kz/docs/api-design-guide/concepts/endpoints)
* настройки профиля {{ yandex-cloud }} CLI:
    * [Инструкция по созданию профиля CLI в регионе Россия](https://yandex.cloud/ru/docs/cli/operations/profile/profile-create)
    * [Инструкция по созданию профиля CLI в регионе Казахстан](https://yandex.cloud/ru-kz/docs/cli/operations/profile/profile-create)

Пользовательские данные хранятся внутри региона и доступны только внутри него. Например, бакеты {{ objstorage-full-name }}, созданные в регионе Россия, недоступны в регионе Казахстан.

Пользователь может войти в консоль управления каждого региона. Чтобы начать работу в новом регионе, нужно зарегистрироваться и создать платежный аккаунт в регионе:
* [Инструкция для региона Россия](https://yandex.cloud/ru/docs/overview/quickstart)
* [Инструкция для региона Казахстан](https://yandex.cloud/ru-kz/docs/overview/quickstart)

## Управляемая организация в другом регионе {#controlled-org}

{% include [note-preview-by-request](../../_includes/note-preview-by-request.md) %}

[Управляемая организация](../../organization/concepts/controlled-org.md) позволяет разместить ресурсы в другом регионе, не регистрируя для этого отдельную организацию. Вместо этого вы можете [подключить](../../organization/operations/add-region.md) регион к основной организации, чтобы управлять ресурсами в разных регионах через единый интерфейс.

Ресурсы в разных регионах изолированы друг от друга. Данные хранятся в тех регионах, в которых созданы использующие их ресурсы.
