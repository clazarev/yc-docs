# Уведомления об операциях с письмами

Чтобы получать уведомления об операциях с письмами, необходимо [создать конфигурацию](../operations/create-configuration.md) и [привязать ее к адресу](../operations/bind-configuration.md).

## Типы уведомлений {#types}

### Уведомление о приеме письма сервисом {#send}

Приходит, когда {{ postbox-name }} принял письмо в обработку.

Пример уведомления:

```json
{
    "eventType": "Send",
    "mail": {
        "timestamp": "2024-04-25T18:05:04.84108+03:00",
        "messageId": "vgAyRUls8591ybPKeH-Ov",
        "identityId": "nWh0ZpVEgnKO1bghxydXn",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"vgAyRUls8591ybPKeH-Ov",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "send": {  },
    "eventId": "vgAyRUls8591ybPKeH-Ov:0"
}
```

### Уведомление о доставке письма {#delivery}

Приходит, когда получателю отправили письмо и его почтовый клиент подтвердил прием письма.

Пример уведомления:

```json
{
    "eventType": "Delivery",
    "mail": {
        "timestamp": "2024-04-25T18:05:04.84108+03:00",
        "messageId": "vgAyRUls8591ybPKeH-Ov",
        "identityId": "nWh0ZpVEgnKO1bghxydXn",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"vgAyRUls8591ybPKeH-Ov",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "bounce": null,
    "delivery": {
        "timestamp": "2024-04-25T18:05:14.84107+03:00",
        "processingTimeMillis": 9999,
        "recipients": [
            "abc@example.com"
        ]
    },
    "eventId": "ce3uqnS9pzQBMsnaAbrT_:0"
}
```

### Уведомление о том, что письмо не доставлено {#bounce}

Приходит, когда почтовый клиент получателя на попытку доставки отвечает ошибкой, которую {{ postbox-name }} считает не требующей повторной попытки доставки, или адрес получателя находится в стоп-листе.

Пример уведомления:

```json
{
    "eventType": "Bounce",
    "mail": {
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "messageId": "QA_JPkU2fkpIWdkxAOASH",
        "identityId": "ZtYk0rrjN87m-Ovxjte1G",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"QA_JPkU2fkpIWdkxAOASH",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "bounce": {
        "bounceType": "Permanent",
        "bounceSubType": "Undetermined",
        "bouncedRecipients": [
            {
                "emailAddress": "abc@example.com",
                "action": "failed",
                "status": "5.7.1",
                "diagnosticCode": "Other"
            }
        ],
        "timestamp": "2024-04-25T18:08:04.973666+03:00",
    },
    "delivery": null,
    "eventId": "jdMtnVniDeHqlQX8ygwEX:0"
}
```

### Уведомление о том, что письмо открыто {#open}

Приходит, когда получатель открыл письмо.

Пример уведомления:

```json
{
    "eventType": "Open",
    "mail": {
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "messageId": "QA_JPkU2fkpIWdkxAOASH",
        "identityId": "ZtYk0rrjN87m-Ovxjte1G",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"QA_JPkU2fkpIWdkxAOASH",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "open": {
        "ipAddress": "192.0.2.1",
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_3 like Mac OS X) AppleWebKit/603.3.8 (KHTML, like Gecko) Mobile/14G60"
    },
    "eventId": "jdMtnVniDeHqlQX8ygwEX:0"
}
```

### Уведомление о том, что перешли по ссылке в письме {#click}

Приходит, когда получатель перешел по ссылке в письме.

Пример уведомления:

```json
{
    "eventType": "Click",
    "mail": {
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "messageId": "QA_JPkU2fkpIWdkxAOASH",
        "identityId": "ZtYk0rrjN87m-Ovxjte1G",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"QA_JPkU2fkpIWdkxAOASH",
            "subject":"Message sent using {{ postbox-full-name }}"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "click": {
        "ipAddress": "192.0.2.1",
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_3 like Mac OS X) AppleWebKit/603.3.8 (KHTML, like Gecko) Mobile/14G60",
        "url": "https://example.com/some-link",
        "linkTags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "eventId": "jdMtnVniDeHqlQX8ygwEX:0"
}
```

### Уведомление о том, что доставка письма задерживается {#delayed-delivery}

После того как {{ postbox-name }} успешно принял письмо, обычно оно отправляется немедленно. Однако иногда может возникнуть небольшая задержка доставки. В таком случае приходит данное уведомление.

Пример уведомления:

```json
{
    "eventType": "DeliveryDelay",
    "mail": {
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "messageId": "QA_JPkU2fkpIWdkxAOASH",
        "identityId": "ZtYk0rrjN87m-Ovxjte1G",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"QA_JPkU2fkpIWdkxAOASH",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "deliveryDelay": {
        "delayType": "General",
        "delayedRecipients": [
            {
                "emailAddress": "recipient@example.com"
            }
        ],
        "timestamp": "2024-04-25T18:10:04.973666+03:00"
    },
    "eventId": "jdMtnVniDeHqlQX8ygwEX:0"
}
```

### Уведомление о том, что получатель отписался от рассылки {#subscription}

Приходит, когда получатель отписался от рассылки через механизм «отказ от подписки в один клик» (`one-click unsubscribe`), добавленный {{ postbox-name }} в письмо.

Пример уведомления:

```json
{
    "eventType": "Unsubscribe",
    "mail": {
        "timestamp": "2024-04-25T18:08:04.933666+03:00",
        "messageId": "QA_JPkU2fkpIWdkxAOASH",
        "identityId": "ZtYk0rrjN87m-Ovxjte1G",
        "commonHeaders": {
            "from":[ "User <user@example.com>" ],
            "date":"Thu, 27 Jun 2024 14:05:45 +0000",
            "to":[ "Recipient Name <recipient@example.com>" ],
            "messageId":"QA_JPkU2fkpIWdkxAOASH",
            "subject":"Message sent using Yandex Cloud Postbox"
        },
        "tags": {
            "key1": [
                "value1"
            ],
            "key2": [
                "value2"
            ]
        }
    },
    "subscription": {
        "contactList": "my-list",
        "timestamp": "2024-04-25T18:08:04.973666+03:00",
        "source": "UnsubscribeHeader"
    }
}
```

## Формат уведомлений {#format}

Уведомление записывается в [поток данных](../../data-streams/concepts/glossary.md#stream-concepts) {{ yds-full-name }} в формате JSON. Последовательность и набор полей могут отличаться от описанных ниже.

### Основной объект {#main-object}

Название | Тип | Описание
--- | --- | ---
`notificationType` | Строка | [Тип уведомления](#types). Возможные значения: `Bounce`, `Delivery`, `Send`.
`mail` | Объект [Mail](#mail-object) | Объект, который содержит общую информацию об отправленном письме.
`bounce` | Объект [Bounce](#bounce-object) | Объект, который содержит информацию о том, что письмо не доставлено. Обязателен, если `notificationType` — `Bounce`, иначе отсутствует.
`delivery` | Объект [Delivery](#delivery-object) | Объект, который содержит информацию о доставке письма отдельному получателю. Обязателен, если `notificationType` — `Delivery`, иначе отсутствует.
`subscription` | Объект [Subscription](#subscription-object) | Объект, который содержит информацию о том, что получатель отписался от рассылки. Обязателен, если `notificationType` — `Subscription`, иначе отсутствует.
`open` | Объект [Open](#open-object) | Объект, который содержит информацию о том, что письмо было открыто. Обязателен, если `notificationType` — `Open`, иначе отсутствует.
`eventId` | Строка | Уникальный идентификатор события.

### Объект Mail {#mail-object}

Название | Тип | Описание
--- | --- | ---
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда письмо было принято {{ postbox-name }}.
`messageId` | Строка | Уникальный идентификатор письма. У одного письма может быть несколько получателей. Выдается {{ postbox-name }} при приеме письма в обработку.
`identityId` | Строка | Идентификатор адреса {{ postbox-name }}, который используется при отправке письма.
`commonHeaders` | Объект [CommonHeaders](#common-headers-object) | Объект, который содержит основные заголовки письма.
`tags` | Объект | Объект, который содержит теги, добавленные к письму.

### Объект CommonHeaders {#common-headers-object}

Название | Тип | Описание
--- | --- | ---
`from` | Массив строк | Содержимое заголовка `From`, разбитое по адресам.
`to` | Массив строк | Содержимое заголовка `To`, разбитое по адресам.
`subject` | Строка | Содержимое заголовка `Subject`.
`date` | Строка | Содержимое заголовка `Date`.
`messageId` | Строка | Уникальный идентификатор письма. Выдается {{ postbox-name }} при приеме письма.

### Объект Send {#send-object}

Пустой объект.

### Объект Bounce {#bounce-object}

Название | Тип | Описание
--- | --- | ---
`bounceType` | Строка | Тип ошибки. Возможные значения:<ul><li>`Permenent` — письмо не доставлено.</li></ul>
`bounceSubType` | Строка | Подтип ошибки. Возможные значения:<ul><li>`Undetermined` — неизвестная ошибка;</li><li>`Suppressed` — письмо не доставлено из-за того, что получатель находится в стоп-лист.</li></ul>
`bouncedRecipients` | Массив объектов [BounceRecipient](#bounce-recipent-object) | Массив, который содержит информацию о получателе письма и связанной с ним ошибке доставки, если она была.
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда получена ошибка от почтового клиента получателя.

### Объект BounceRecipent {#bounce-recipent-object}

Название | Тип | Описание
--- | --- | ---
`emailAddress` | Строка | Электронный адрес получателя.
`action` | Строка | Необязательное поле. Результат отправки. Возможные значения: `failed`.
`status` | Строка | Необязательное поле. SMTP-код ответа.
`diagnosticCode` | Строка | Необязательное поле. Расширенный текст ошибки. Может содержать текст ошибки от почтового клиента получателя.

### Объект Click {#click-object}

Название | Тип | Описание
--- | --- | ---
`ipAddress` | Строка | IP-адрес устройства получателя, с которого перешли по ссылке.
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда получатель перешел по ссылке.
`userAgent` | Строка | Идентификационная строка (`User-Agent`) устройства или почтового клиента, с которого перешли по ссылке.
`url` | Строка | Оригинальный URL, по которому перешел получатель.
`linkTags` | Объект | Объект, который содержит теги, добавленные к ссылке.

### Объект Delivery {#delivery-object}

Название | Тип | Описание
--- | --- | ---
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда {{ postbox-name }} отправил письмо и получил успешный ответ от почтового клиента получателя.
`processingTimeMillis` | Целое число | Время, которое потребовалось на обработку письма в миллисекундах.
`recipients` | Массив строк | Адреса получателей.

### Объект DeliveryDelay {#delivery-delay-object}

Название | Тип | Описание
--- | --- | ---
`delayType` | Строка | Тип задержки. Возможные значения: `General`.
`delayedRecipients` | Массив объектов [DelayedRecipient](#delayed-recipient-object) | Массив, который содержит информацию о получателе письма и связанной с ним задержке доставки.
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда случилась задержка доставки.

### Объект DelayedRecipient {#delayed-recipient-object}

Название | Тип | Описание
--- | --- | ---
`emailAddress` | Строка | Электронный адрес получателя.

### Объект Subscription {#subscription-object}

Название | Тип | Описание
--- | --- | ---
`contactList` | Строка | Имя списка контактов, с которым связано письмо.
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда получатель отписался от рассылки.
`source` | Строка | Источник отписки. Возможные значения: `UnsubscribeHeader`.

### Объект Open {#open-object}

Название | Тип | Описание
--- | --- | ---
`ipAddress` | Строка | IP-адрес устройства получателя, с которого было открыто письмо.
`timestamp` | Строка | Дата в формате [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) (`2006-01-02T15:04:05Z07:00`). Время, когда письмо было открыто.
`userAgent` | Строка | Идентификационная строка (`User-Agent`) устройства или почтового клиента, с которого было открыто письмо.

## Уровень качества обслуживания (QoS) {#qos}

При доставке уведомлений сервис поддерживает [уровень качества обслуживания](../../glossary/qos.md) `QoS 1: At least once`, поэтому возможна повторная отправка уведомлений. У одинаковых уведомлений совпадает `eventId`.

Если к адресу привязана [конфигурация](glossary.md#configuration), в которую добавлены несколько [подписок](glossary.md#subscription.md), по каждому назначению будет приходить отдельное уведомление.

## Пример работы {#example}

Вы отправили письмо двум получателям — `user1@example.com` и `user2@other.example.com`. {{ postbox-name }} отправил письмо обоим получателям отдельно.

Почтовый клиент получателя `user1@example.com` принял письмо. Почтовый клиент получателя `user2@other.example.com` после первой попытки отправить письмо вернул ошибку, а после второй — не принял письмо и ответил, что данный пользователь не найден.

В таком случае вы получите три уведомления:
* уведомление о том, что {{ postbox-name }} принял письмо в обработку;
* уведомление о доставке письма получателю `user1@example.com`;
* уведомление о том, что письмо не доставлено получателю `user2@other.example.com`, с информацией об ошибке. Уведомление придет после второй попытки отправить письмо.

Так как почтовый клиент ответил, что получатель `user2@other.example.com` не найден, его адрес временно попадет в стоп-лист. Прежде чем пытаться отправлять письмо на такой адрес повторно, необходимо подождать некоторое время, иначе придет уведомление о том, что письмо не доставлено, потому что получатель находится в стоп-листе. 
