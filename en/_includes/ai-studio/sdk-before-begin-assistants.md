1. [Create](../../iam/operations/sa/create.md) a service account and [assign](../../iam/operations/sa/assign-role-for-sa.md) the `ai.assistants.editor` and `ai.languageModels.user` [roles](../../ai-studio/security/index.md#service-roles) to it.
1. [Get](../../iam/operations/authentication/manage-api-keys.md#create-api-key) the service account API key and save it.

    {% include [sdk-auth-details-paragraph](./sdk-auth-details-paragraph.md) %}
1. Use the [pip](https://pip.pypa.io/en/stable/) package manager to install the {{ ml-sdk-name }} library:

    ```bash
    pip install yandex-cloud-ml-sdk
    ```