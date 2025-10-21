# Exporting extended usage details

You can get a CSV file with your general or per-resource spending details. You can download the file once or set up regular export.

## One-time export {#single-time-download}

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}

  1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
  1. Select the account you want to get details for.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/square-chart-column.svg) **{{ ui-key.yacloud_billing.billing.account.switch_detail }}**.
  1. At the top right, click **More** and select **{{ ui-key.yacloud_billing.billing.account.detail.button_download-csv }}**.
  1. Select the time period to get the details for.
  1. Click **{{ ui-key.yacloud_billing.billing.account.detail.popup-csv_button_download }}**.

{% endlist %}

## Setting up regular export {#set-up-regular-download}

### Getting started {#before-you-begin}

1. If you do not have any [folders](../../resource-manager/concepts/resources-hierarchy.md#folder) yet, [create one](../../resource-manager/operations/folder/create.md). In the folder name, specify the project or client to use the folder.

1. [Create a bucket](../../storage/operations/buckets/create.md) in {{ objstorage-full-name }}. {{ yandex-cloud }} will save detail files to this bucket. You can save details in multiple buckets.

   {% note alert %}

   Bucket requirements:
 
   * Do not make your bucket public; otherwise, anyone will be able to download your expense details using the bucket link.
   * Do not add [encryption](../../storage/concepts/encryption.md) to the bucket.
   * Do not configure an [access policy](../../storage/concepts/policy.md) for the bucket.

   {% endnote %}

1. Make sure you have one of the following roles: `billing.accounts.owner`, `billing.accounts.admin`, or `billing.accounts.editor`.

### Get the expense details {#download-detail}

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}

  1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
  1. Select the account you want to get details for.
  1. In the left-hand panel, select **{{ ui-key.yacloud_billing.billing.account.switch_exports }}**.
  1. Click **{{ ui-key.yacloud_billing.billing.account.exports.button_create-periodic-export }}**.
  1. In the **{{ ui-key.yacloud_billing.billing.account.exports.label_create-export-title }}** window that opens:
     * In the **{{ ui-key.yacloud_org.billing.account.exports.field_bucket }}** field, enter the name of the bucket to store the CSV file with details.
     * In the **{{ ui-key.yacloud_org.billing.account.exports.field_prefix }}** field, enter the directory name for the file. It must end with the `/` character.
     * In the **{{ ui-key.yacloud_org.billing.account.exports.field_locale }}** field, select the display language for product names: `{{ ui-key.yacloud_org.billing.account.exports.locale_value_ru-lang }}` or `{{ ui-key.yacloud_org.billing.account.exports.locale_value_en-lang }}`.
     * In the **{{ ui-key.yacloud_org.billing.account.exports.field_detail-type }}** field, select the type of details: `{{ ui-key.yacloud_billing.billing.account.exports.label_not-include-resources }}` or `{{ ui-key.yacloud_billing.billing.account.exports.label_include-resources }}`.

         {% note tip %}

         If you select `{{ ui-key.yacloud_billing.billing.account.exports.label_include-resources }}` for regular export of details, you can also view the {{ datalens-full-name }}, {{ tracker-full-name }}, and {{ ml-platform-name }} resources in the [`resource_id`](#format) field (e.g., [{{ ml-platform-name }} community IDs](../../datasphere/concepts/community.md)).

         {% endnote %}

  1. Optionally, to export data for the previous period, enable the relevant option. In the **Export reports starting from** field that appears, select the start date for exporting reports.
  1. Click **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

A new file with details is created in the bucket daily. The file name contains the prefix and date that details are collected for. The file is overwritten and updated with new data every hour.

The information can be updated in any daily file within a month.

{% note tip %}

Files with details from buckets are not deleted automatically, but you can configure scheduled deletion. For more information, see [Object lifecycle](../../storage/concepts/lifecycles.md) ({{ objstorage-name }} refers to files in a bucket as _objects_).

{% endnote %}

## Expense details file format {#format}

An uploaded file with details is a CSV table with comma-separated columns. Each row shows the product consumption level per day for the specified folder.

The table contains the following columns:

* `billing_account_id`: [Billing account](../concepts/billing-account.md) ID.
* `billing_account_name`: Billing account name.
* `cloud_id`: ID of the [cloud](../../resource-manager/concepts/resources-hierarchy.md#cloud) covered by the details.
* `cloud_name`: Cloud name.
* `folder_id`: Folder ID.
* `folder_name`: Name of the folder at the time of exporting the details. This field may be empty if you deleted the folder before exporting the details.
* `resource_id`: Resource ID, resource name, or subscription ID. The value of this field may be empty if the use of the service refers to the entire folder or it does not contain any resources. The services and `resource_id` column values are mapped as follows:
   * {{ compute-name }} and {{ marketplace-name }}: ID of the VM, disk snapshot, disk image, or other resource installed using {{ marketplace-name }}.
   * {{ objstorage-name }}: Bucket name.
   * {{ vpc-name }}: ID of the resource that processes incoming or outgoing traffic.
   * {{ kms-short-name }}: Key ID.
   * {{ network-load-balancer-name }}: Load balancer ID.
   * {{ container-registry-short-name }}: Container ID.
   * {{ k8s }} and {{ dataproc-name }}: Cluster ID.
   * {{ mpg-short-name }}, {{ mch-short-name }}, {{ mmg-short-name }}, {{ mmy-short-name }}, {{ mrd-short-name }}, and {{ mkf-name }}: Host ID in the cluster.
   * {{ message-queue-name }}: Request ID.
   * {{ sf-name }}: Function ID.
   * {{ monitoring-short-name }}, {{ datalens-short-name }}, {{ iot-name }}, {{ speechkit-short-name }}, {{ translate-name }}, and {{ vision-short-name }}: Empty value.
   * Technical support: Subscription ID.
* `service_id`: ID of the service the consumed product belongs to.
* `service_name`: Name of the service the consumed product belongs to.
* `sku_id`: Consumed product ID.
* `sku_name`: Product name.
* `date`: Consumption charge date. A date is set as an interval from 0:00 to 23:59 (UTC+3).
* `currency`: Billing account currency. Possible values: `RUB`, `USD`, and `KZT`.
* `pricing_quantity`: Number of product units consumed. The decimal separator is a period.
* `pricing_unit`: Product consumption unit.
* `cost`: Total cost of consumption. The decimal separator is a period.
* `credit`: Total discounts. Always negative. The decimal separator is a period.
* `monetary_grant_credit`: Grant discount, including the platform introduction grant. The decimal separator is a period.
* `volume_incentive_credit`: Consumption volume discount. The decimal separator is a period.
* `cud_credit`: Discount for the [committed](../concepts/cvos.md) resource volume. The cost of the consumption volume above the committed one equals the sum of the `cost` and `credit` column values. The decimal separator is a period.
* `misc_credit`: Other types of discounts, including discounts for consumption after the platform introduction grant expires but before switching to the paid version. The decimal separator is a period.
* `label.user_labels.<label_name>`: Labels added to the resources. For information about how to manage labels, see [{#T}](../../resource-manager/operations/manage-labels.md).
* `locale`: Language of each exported line. This field's value determines the `sku_name` column language. The possible values are `en` and `ru`.
* `updated_at`: Date and time of the last line update in [Unix Timestamp](https://www.unixtimestamp.com) format.
* `exported_at`: Date and time when the line was added to the details file.

{% cut "Deprecated parameters" %}

* `created_at`: Date and time when the specified consumption charge was made, provided in [Unix Timestamp](https://www.unixtimestamp.com) format.
* `label.system_labels.folder_id`: Folder ID. The value is now displayed in the `folder_id` column.

{% endcut %}


##### See also

* [View usage details for the cloud](check-charges.md)

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}