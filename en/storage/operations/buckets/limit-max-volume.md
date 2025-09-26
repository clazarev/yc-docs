---
title: Limiting the maximum size of a bucket in {{ objstorage-full-name }}
description: Follow this guide to set the maximum size of a bucket in {{ objstorage-name }}.
---

# Limiting the maximum size of a bucket

{{ objstorage-name }} enables limiting the maximum bucket size.

For example, if your service enables users to upload objects to {{ objstorage-name }}, then, by limiting the maximum size, you can better manage user actions and avoid unnecessary costs.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services and go to the bucket whose maximum size you want to limit.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/wrench.svg) **{{ ui-key.yacloud.storage.bucket.switch_settings }}**.
  1. Select the **{{ ui-key.yacloud.storage.bucket.switch_general-settings }}** tab.
  1. Set the value of the **{{ ui-key.yacloud.storage.bucket.settings.field_size-limit }}** field.

      {% include [storage-no-max-limit](../../_includes_service/storage-no-max-limit.md) %}
      
  1. Click **{{ ui-key.yacloud.storage.bucket.settings.button_save }}**.

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for updating a bucket:

      ```bash
      yc storage bucket update --help
      ```

  1. {% include [bucket-list-cli](../../../_includes/storage/bucket-list-cli.md) %}
  1. Save the name (from the `NAME` column) of the bucket whose size you want to limit.
  1. Limit the maximum size of the bucket:

      ```bash
      yc storage bucket update \
        --name <bucket_name> \
        --max-size <maximum_bucket_size>
      ```

      Where:
      * `--name`: Name of the bucket whose size you want to limit.
      * `--max-size`: Maximum bucket size, in bytes (`0` for unlimited).

      Result:

      ```text
      name: first-bucket
      older_id: b1gmit33ngp6********
      default_storage_class: STANDARD
      versioning: VERSIONING_DISABLED
      max_size: "26843545600"
      acl: {}
      created_at: "2022-12-16T13:58:18.933814Z"
      ```

- {{ TF }} {#tf}

  {% include [terraform-role](../../../_includes/storage/terraform-role.md) %}

  {% include [terraform-definition](../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  
  {% include [terraform-install](../../../_includes/terraform-install.md) %}


  To limit the maximum bucket size:

  1. Open the {{ TF }} configuration file and add the `max_size` parameter to the bucket description:

     ```hcl
     ...
     resource "yandex_storage_bucket" "test" {
       access_key = "<static_key_ID>"
       secret_key = "<secret_key>"
       bucket     = "tf-test-bucket"
       max_size   = 1048576
     }
     ...
     ```

     Where:
     * `access_key`: Static access key ID.

        {% include [terraform-iamtoken-note](../../../_includes/storage/terraform-iamtoken-note.md) %}

     * `secret_key`: Secret access key value.
     * `max_size`: Maximum bucket size, in bytes.

     For more information about `yandex_storage_bucket` properties, see [this {{ TF }} article]({{ tf-provider-resources-link }}/storage_bucket#bucket-max-size).

  1. Check the configuration using this command:

     ```
     terraform validate
     ```

     If the configuration is correct, you will get this message:

     ```
     Success! The configuration is valid.
     ```

  1. Run this command:

     ```
     terraform plan
     ```

     You will see a detailed list of resources. No changes will be made at this step. If the configuration contains any errors, {{ TF }} will show them.

  1. Apply the changes:

     ```
     terraform apply
     ```

  1. Confirm the changes: type `yes` into the terminal and press **Enter**.

     You can check the bucket update using the [management console]({{ link-console-main }}).

- API {#api}

  To limit the maximum bucket size, use the [update](../../api-ref/Bucket/update.md) REST API method for the [Bucket](../../api-ref/Bucket/index.md) resource or the [BucketService/Update](../../api-ref/grpc/Bucket/update.md) gRPC API call.

{% endlist %}
