1. In the [management console]({{ link-console-main }}), select the folder where you want to create an encrypted disk.
1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
1. In the left-hand panel, select ![image](../../_assets/console-icons/hard-drive.svg) **{{ ui-key.yacloud.compute.disks.label_title }}**.
1. Click **{{ ui-key.yacloud.compute.storage.button_create-disk }}**.
1. Enter a name for the disk.

    {% include [name-format](../name-format.md) %}

1. Select the same [availability zone](../../overview/concepts/geo-scope.md) that contained the source disk.
1. Set the disk parameters, such as [disk type](../../compute/concepts/disk.md#disks_types), [block size](../../compute/concepts/disk.md#maximum-disk-size), and [disk size](../../compute/concepts/disk.md#maximum-disk-size).
1. In the **{{ ui-key.yacloud.compute.instances.create-disk.field_source }}** field, select `{{ ui-key.yacloud.compute.instances.create-disk.value_source-image }}` and then select the image you created earlier from the list below. Use the filter to find the image.
1. Under **{{ ui-key.yacloud.compute.disk-form.section_encryption }}**, enable **{{ ui-key.yacloud.compute.disk-form.label_disk-encryption }}** and select the [key](../../kms/concepts/key.md) you created earlier in the **{{ ui-key.yacloud.compute.disk-form.label_disk-kms-key }}** field.

1. Click **{{ ui-key.yacloud.compute.storage.button_create-disk }}**.
