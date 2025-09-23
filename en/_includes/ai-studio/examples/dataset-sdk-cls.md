```python
#!/usr/bin/env python3

from __future__ import annotations

import pathlib
from yandex_cloud_ml_sdk import YCloudML
from yandex_cloud_ml_sdk.auth import YandexCloudCLIAuth


def main() -> None:

    sdk = YCloudML(
        folder_id="<folder_ID>",
        auth="<API_key>",
    )

    # Viewing the list of all uploaded datasets
    for dataset in sdk.datasets.list():
        print(f"List of existing datasets {dataset=}")

    # Deleting all previously uploaded datasets
    for dataset in sdk.datasets.list():
        dataset.delete()

    # Creating a tuning dataset for the {{ gpt-lite }} base model
    dataset_draft = sdk.datasets.draft_from_path(
        task_type="<classification_type>",
        path="<file_path>",
        upload_format="jsonlines",
        name="multiclass",
    )

    # Waiting for the data to be uploaded and the dataset to be created
    operation = dataset_draft.upload_deferred()
    tuning_dataset = operation.wait()
    print(f"new {tuning_dataset=}")


if __name__ == "__main__":
    main()
```

Where:

* `<folder_ID>`: [ID of the folder](../../../resource-manager/operations/folder/get-id.md) the [service account](../../../iam/concepts/users/service-accounts.md) was created in.
* `<API_key>`: Service account [API key](../../../iam/concepts/authorization/api-key.md) you got earlier required for [authentication in the API](../../../ai-studio/api-ref/authentication.md).

    {% include [sdk-auth-details-paragraph](../sdk-auth-details-paragraph.md) %}
* `<classification_type>`: [Classification type](../../../ai-studio/concepts/classifier/index.md) the model will be tuned for using the new dataset. The possible values are:

    * `TextClassificationMultilabel`: Binary classification or multi-label classification.
    * `TextClassificationMulticlass`: Multi-class classification.
* `<file_path>`: Path to the file containing the ready-made examples for model tuning.