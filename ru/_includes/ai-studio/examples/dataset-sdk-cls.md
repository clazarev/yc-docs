```python
#!/usr/bin/env python3

from __future__ import annotations

import pathlib
from yandex_cloud_ml_sdk import YCloudML
from yandex_cloud_ml_sdk.auth import YandexCloudCLIAuth


def main() -> None:

    sdk = YCloudML(
        folder_id="<идентификатор_каталога>",
        auth="<API-ключ>",
    )

    # Так можно посмотреть список всех загруженных датасетов
    for dataset in sdk.datasets.list():
        print(f"List of existing datasets {dataset=}")

    # Так можно удалить все ранее загруженные датасеты
    for dataset in sdk.datasets.list():
        dataset.delete()

    # Создаем датасет для дообучения базовой модели {{ gpt-lite }}
    dataset_draft = sdk.datasets.draft_from_path(
        task_type="<тип_классификации>",
        path="<путь_к_файлу>",
        upload_format="jsonlines",
        name="multiclass",
    )

    # Дождемся окончания загрузки данных и создания датасета
    operation = dataset_draft.upload_deferred()
    tuning_dataset = operation.wait()
    print(f"new {tuning_dataset=}")


if __name__ == "__main__":
    main()
```

Где:

* `<идентификатор_каталога>` — [идентификатор каталога](../../../resource-manager/operations/folder/get-id.md), в котором создан [сервисный аккаунт](../../../iam/concepts/users/service-accounts.md).
* `<API-ключ>` — [API-ключ](../../../iam/concepts/authorization/api-key.md) сервисного аккаунта, полученный ранее и необходимый для [аутентификации в API](../../../ai-studio/api-ref/authentication.md).

    {% include [sdk-auth-details-paragraph](../sdk-auth-details-paragraph.md) %}
* `<тип_классификации>` — [тип классификации](../../../ai-studio/concepts/classifier/index.md), для которой с помощью создаваемого датасета будет дообучаться модель. Возможные значения:

    * `TextClassificationMultilabel` — бинарная классификация или классификация с несколькими метками.
    * `TextClassificationMulticlass` — многоклассовая классификация.
* `<путь_к_файлу>` — путь к файлу, содержащему заранее подготовленные примеры для дообучения модели.