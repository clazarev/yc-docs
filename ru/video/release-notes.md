---
title: История изменений в {{ video-full-name }}
description: В разделе представлена история изменений сервиса {{ video-name }}.
---

# История изменений в {{ video-full-name }}

## Июль-сентябрь 2025 {#jul-sep-2025}

* Для повышения доступности видео и расширения аудитории добавлены [нейросетевые функции](concepts/videos.md#ai-capabilities): суммаризация, нейроперевод и автоматическая генерация субтитров. Функции работают на ML-моделях [Яндекса](https://habr.com/ru/companies/yandex/articles/792608/) и регулярно обновляются.

* Поддержана возможность монетизировать видеоконтент с помощью [рекламной сети Яндекса](https://yandex.ru/support2/partner/ru/yan-rules/video). [Настройка](operations/channels/settings.md#ad-settings) рекламы выполняется на уровне [канала](concepts/index.md#channels), вы можете выбрать категорию рекламных роликов, которые соответствуют тематике ваших видео.

* Чтобы разрешить показ видео только на определенных сайтах, добавлена возможность [указывать домены](operations/channels/settings.md#channel-settings) для показа видео. На других доменах видео отображаться не будут.

* 1 августа 2025 {{ video-name }} перешел на стадию [General Availability](../overview/concepts/launch-stages.md), и за его использование начала взиматься плата в соответствии с [правилами тарификации](pricing.md).

## Ноябрь-декабрь 2024 {#nov-dec-2024}

* Реализован поиск [канала](concepts/index.md#channels) по имени на [главной странице]({{ link-video-main }}) {{ video-name }}.
* Добавлена возможность выдать доступ к видео по [временной ссылке](concepts/videos.md#temporary-link).

## Октябрь 2024 {#oct-2024}

* Опубликованы SDK видеоплеера для [iOS](./sdk/ios-sdk.md).

## Сентябрь 2024 {#sep-2024}

* Поддержана загрузка [субтитров](./concepts/videos.md#subtitles) для [видео](./concepts/videos.md).
* Опубликованы SDK видеоплеера для [JavaScript](./sdk/javascript/index.md).
* Реализована поддержка [методов REST API](https://github.com/yandex-cloud/cloudapi/tree/master/yandex/cloud/video) для взаимодействия с сервисом, опубликован [справочник](./api-ref/).

## Август 2024 {#aug-2024}

* Реализована функциональность [плейлистов](./concepts/playlists.md).
* Сервис доступен публично на [стадии Preview](../overview/concepts/launch-stages.md).

## Июль 2024 {#jul-2024}

* Добавлена возможность [одновременно загружать](./operations/video/upload.md#multiple) несколько видео.

## Апрель 2024 {#apr-2024}

* Опубликован [справочник вызовов gRPC API](./api-ref/grpc/).

## Март 2024 {#mar-2024}

* Добавлена возможность получения [статистики просмотров](./operations/video/get-statistics.md) видео.
* Опубликован [справочник Cloud Video Player SDK для IFrame](./iframe-sdk.md).
* Добавлена возможность [настройки публичного доступа](./operations/video/publish.md) к видео.

## Ноябрь 2023 {#nov-2023}

Опубликованы [спецификации gRPC API {{ video-name }}](https://github.com/yandex-cloud/cloudapi/tree/master/yandex/cloud/video).

## Октябрь 2023 {#oct-2023}

Сервис доступен по запросу на [стадии Preview](../overview/concepts/launch-stages.md) на [отдельной странице]({{ link-video-main }}) {{ video-name }}.
