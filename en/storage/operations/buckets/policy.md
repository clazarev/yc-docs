---
title: Managing a {{ objstorage-full-name }} bucket policy
description: Follow this guide to learn how to manage an {{ objstorage-name }} bucket policy.
---

# Managing a bucket policy

{% include [full-overview](../../../_includes/storage/security/full-overview.md) %}

[Bucket policies](../../concepts/policy.md) set permissions for operations with [buckets](../../concepts/bucket.md), [objects](../../concepts/object.md), and object groups.

For examples of bucket policies for specific use cases, see [Configuration examples](../../concepts/policy.md#config-examples).


{% note warning %}

{% include [s3-with-policy-access](../../../_includes/storage/s3-with-policy-access.md) %}

{% endnote %}


## Applying or updating a policy {#apply-policy}

The minimum role required to apply or update a bucket policy is `storage.configurer`. For more information, see the [role description](../../../storage/security/index.md#storage-configurer).

{% note info %}

If the bucket already had a bucket policy configured, it will be completely overwritten once you apply the changes.

{% endnote %}

To apply or update a bucket policy:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services and go to the bucket whose policy you need to configure.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}** and go to the **{{ ui-key.yacloud.storage.bucket.switch_policy }}** tab.
  1. Click **{{ ui-key.yacloud.storage.bucket.policy.button_policy-edit }}**.
  1. Enter a bucket policy ID.
  1. Set up a rule:
     1. Enter a rule ID.
     1. Configure rule settings:
        * **{{ ui-key.yacloud.storage.bucket.policy.field_effect }}**: Allow or deny.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_principal-type }}**: Include or exclude users.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_user }}**: Select all users or list specific subjects.

            To list specific subjects:
            
            * Select `{{ ui-key.yacloud.storage.bucket.policy.label_select-users }}`.

            * {% include [select-subject-console](../../../_includes/storage/select-subject-console.md) %}

                You can select multiple subjects; to do this, select them one by one.

        * **{{ ui-key.yacloud.storage.bucket.policy.field_action }}** for which you are creating the rule. You can also enable **All actions**.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_resource }}**: Defaults to the selected bucket. To add other resources to the rule, click **{{ ui-key.yacloud.storage.bucket.policy.button_add-resource }}**.

            {% note info %}

            {% include [policy-bucket-objects](../../../_includes/storage/policy-bucket-objects.md) %}

            {% endnote %}

     1. If required, add a [condition](../../s3/api-ref/policy/conditions.md) for the rule:
        * Select **{{ ui-key.yacloud.storage.bucket.policy.field_key }}** from the list.
        * Select **{{ ui-key.yacloud.storage.bucket.policy.field_operator }}** from the list. If you want the operator to apply solely to the existing fields, select **{{ ui-key.yacloud.storage.bucket.policy.label_if-exists }}**. This way, if the field is missing, the condition will be considered met.
        * Specify **{{ ui-key.yacloud.storage.bucket.policy.field_value }}**.
        * Click **{{ ui-key.yacloud.storage.bucket.policy.button_add-value }}** to add another value to the condition.

        {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

        {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

  1. Add and configure rules as required.
  1. Click **{{ ui-key.yacloud.common.save }}**.

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for editing a bucket ACL:

     ```bash
     yc storage bucket update --help
     ```

  1. Describe your bucket policy configuration as a JSON [data schema](../../s3/api-ref/policy/scheme.md):

     ```json
     {
       "Version": "2012-10-17",
       "Statement": {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::<bucket_name>/*",
         "Condition": {
           "Bool": {
             "aws:SecureTransport": "true"
           }
         }
       }
     }
     ```

     Where:

     * `Version`: Version of the bucket policy description. This is an optional parameter.
     * `Statement`: Bucket policy rules:
       * `Effect`: Deny or allow the requested action. The possible values are `Allow` and `Deny`.
       * `Principal`: Requested permission subject ID. You can request permissions for a [user](../../../iam/operations/users/get.md), [service account](../../../iam/operations/sa/get-id.md), or [user group](../../../organization/operations/manage-groups.md). Possible values: `*` and `<subject_ID>`. This is an optional setting.

         You can get the IDs in the following ways:

         {% include [acl-grantee](../../../_includes/storage/acl-grantee.md) %}

       * `Action`: [Action](../../s3/api-ref/policy/actions.md) to allow when the policy is triggered. The possible values are `s3:GetObject`, `s3:PutObject`, and `*` (if you need to apply the policy to all actions).
       * `Resource`: Resource to apply the rule to.
       * `Condition`: [Condition](../../s3/api-ref/policy/conditions.md) to check. This is an optional setting.

         {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

         {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

  1. Run this command:

     ```bash
     yc storage bucket update \
       --name <bucket_name> \
       --policy-from-file <policy_file_path>
     ```

     Result:

     ```text
     name: my-bucket
     folder_id: csgeoelk7fl1********
     default_storage_class: STANDARD
     versioning: VERSIONING_SUSPENDED
     max_size: "10737418240"
     policy:
         Statement:
           Action: s3:GetObject
           Condition:
             Bool:
               aws:SecureTransport: "true"
             Effect: Allow
             Principal: '*'
             Resource: arn:aws:s3:::my-bucket
           Version: "2012-10-17"
     acl: {}
     created_at: "2022-12-14T08:42:16.273717Z"
     ```

- AWS CLI {#aws-cli}

  {% note info %}

  To manage a policy using the AWS CLI, a service account needs the `storage.admin` [role](../../security/index.md#storage-admin).

  {% endnote %}

  If you do not have the AWS CLI yet, [install and configure it](../../tools/aws-cli.md).

  1. Describe your bucket policy configuration as a JSON [data schema](../../s3/api-ref/policy/scheme.md):

     ```json
     {
       "Version": "2012-10-17",
       "Statement": {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::<bucket_name>/*",
         "Condition": {
           "Bool": {
             "aws:SecureTransport": "true"
           }
         }
       }
     }
     ```

     Where:

     * `Version`: Version of the bucket policy description. This is an optional parameter.
     * `Statement`: Bucket policy rules:
       * `Effect`: Deny or allow the requested action. The possible values are `Allow` and `Deny`.
       * `Principal`: Requested permission subject ID. You can request permissions for a [user](../../../iam/operations/users/get.md), [service account](../../../iam/operations/sa/get-id.md), or [user group](../../../organization/operations/manage-groups.md). Possible values: `*` and `<subject_ID>`. This is an optional setting.

         You can get the IDs in the following ways:

         {% include [acl-grantee](../../../_includes/storage/acl-grantee.md) %}

       * `Action`: [Action](../../s3/api-ref/policy/actions.md) to allow when the policy is triggered. The possible values are `s3:GetObject`, `s3:PutObject`, and `*` (if you need to apply the policy to all actions).
       * `Resource`: Resource to apply the rule to.
       * `Condition`: [Condition](../../s3/api-ref/policy/conditions.md) to check. This is an optional setting.

         {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

         {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

     Once the configuration is complete, save it to a file named `policy.json`.
  1. Run this command:

     ```bash
     aws s3api put-bucket-policy \
       --endpoint https://{{ s3-storage-host }} \
       --bucket <bucket_name> \
       --policy file://policy.json
     ```

- {{ TF }} {#tf}

  {% include [terraform-role](../../../_includes/storage/terraform-role.md) %}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  {% include [iam-auth-note](../../../_includes/storage/iam-auth-note.md) %}

  To edit a bucket policy, you can use these resources:
  * [yandex_storage_bucket_policy](#tf-storage-bucket-policy)
  * [yandex_storage_bucket](#tf-storage-bucket) (obsolete)

  **yandex_storage_bucket_policy** {#tf-storage-bucket-policy}

  1. Open the {{ TF }} configuration file and specify the policy using the `yandex_storage_bucket_policy` resource:

     ```hcl
     resource "yandex_storage_bucket_policy" "bpolicy" {
       bucket = "my-policy-bucket"
       policy = <<POLICY
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:*",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         },
         {
           "Effect": "Deny",
           "Principal": "*",
           "Action": "s3:PutObject",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         }
       ]
     }
     POLICY
     }
     ```

     {% include [s3-policy-tf-params](../../../_includes/storage/s3-policy-tf-params.md) %}

     For more information about `yandex_storage_bucket_policy` properties, see the [relevant provider documentation]({{ tf-provider-resources-link }}/storage_bucket_policy).

  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     You can check the update using the [management console]({{ link-console-main }}).

  **yandex_storage_bucket (obsolete)** {#tf-storage-bucket}

  1. Open the {{ TF }} configuration file and specify the policy in the `policy` parameter for the `yandex_storage_bucket` resource:

     ```hcl
     resource "yandex_storage_bucket" "mybucket" {
       bucket     = "my-policy-bucket"
       policy     = <<POLICY
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:*",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         },
         {
           "Effect": "Deny",
           "Principal": "*",
           "Action": "s3:PutObject",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         }
       ]
     }
     POLICY
     }
     ```

     {% include [s3-policy-tf-params](../../../_includes/storage/s3-policy-tf-params.md) %}

     For more information about `yandex_storage_bucket` properties, see the [relevant provider documentation]({{ tf-provider-resources-link }}/storage_bucket).

  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     You can check the update using the [management console]({{ link-console-main }}).

- API {#api}

  To manage a bucket policy, use the [update](../../api-ref/Bucket/update.md) REST API method for the [Bucket](../../api-ref/Bucket/index.md) resource, the [BucketService/Update](../../api-ref/grpc/Bucket/update.md) gRPC API call, or the [PutBucketPolicy](../../s3/api-ref/policy/put.md) S3 API method. If the bucket already had a bucket policy configured, it will be completely overwritten once you apply the new policy.

{% endlist %}

{% include [storage-note-empty-policy](../../_includes_service/storage-note-empty-policy.md) %}

## Viewing a policy {#view-policy}

The minimum role required to view a bucket policy is `storage.configViewer`. For more information, see the [role description](../../../storage/security/index.md#storage-config-viewer).

To view the bucket policy applied to a bucket:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services.
  1. Select the bucket from the list.
  1. In the left-hand menu, select **{{ ui-key.yacloud.storage.bucket.switch_security }}** and go to the **{{ ui-key.yacloud.storage.bucket.switch_policy }}** tab.

- AWS CLI {#aws-cli}

  Run this command:

  ```bash
  aws --endpoint https://{{ s3-storage-host }} s3api get-bucket-policy \
    --bucket <bucket_name> \
    --output text
  ```

  Result:

  ```json
  {
    "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":{\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"s3:GetObject\",\"Resource\":\"arn:aws:s3:::<bucket_name>/*\",\"Condition\":{\"Bool\":{\"aws:SecureTransport\":\"true\"}}}}"
  }
  ```

  For more information about parameters, see the [data schema](../../s3/api-ref/policy/scheme.md) description.

- API {#api}

  Use the [GetBucketPolicy](../../s3/api-ref/policy/get.md) S3 API method.

{% endlist %}

## Deleting a policy {#delete-policy}

The minimum role required to delete a bucket policy is `storage.configurer`. For more information, see the [role description](../../../storage/security/index.md#storage-configurer).

To delete a bucket policy:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services.
  1. Select the bucket from the list.
  1. In the left-hand menu, select **{{ ui-key.yacloud.storage.bucket.switch_security }}** and go to the **{{ ui-key.yacloud.storage.bucket.switch_policy }}** tab.
  1. Click ![options](../../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.storage.bucket.policy.button_policy-delete }}**.
  1. Click **{{ ui-key.yacloud.common.delete }}**.

- AWS CLI {#aws-cli}

  Run this command:

  ```bash
  aws --endpoint https://{{ s3-storage-host }} s3api delete-bucket-policy \
    --bucket <bucket_name>
  ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  {% include [iam-auth-note](../../../_includes/storage/iam-auth-note.md) %}

  You can use the `yandex_storage_bucket_policy` and `yandex_storage_bucket` resources to specify the policy (this method is deprecated).

  1. Open the {{ TF }} configuration file describing the bucket policy.

      * If you applied a bucket policy using the `yandex_storage_bucket` resource:

        {% cut "yandex_storage_bucket" %}
 
        1. Find the parameters of the previously created bucket policy to delete in the configuration file:

            ```hcl
            resource "yandex_storage_bucket" "b" {
              bucket = "my-policy-bucket"
              policy = <<POLICY
            {
              "Version": "2012-10-17",
              "Statement": [
                {
                  "Effect": "Allow",
                  "Principal": "*",
                  "Action": "s3:*",
                  "Resource": [
                    "arn:aws:s3:::my-policy-bucket/*",
                    "arn:aws:s3:::my-policy-bucket"
                  ]
                },
                {
                  "Effect": "Deny",
                  "Principal": "*",
                  "Action": "s3:PutObject",
                  "Resource": [
                    "arn:aws:s3:::my-policy-bucket/*",
                    "arn:aws:s3:::my-policy-bucket"
                  ]
                }
              ]
            }


            
            POLICY
            }
            ```

        1. Delete the `policy` field describing the bucket policy settings from the configuration file.

        {% endcut %}

      * If you applied a bucket policy using the `yandex_storage_bucket_policy` resource:

        {% cut "yandex_storage_bucket_policy" %}
        
        1. Find the parameters of the previously created bucket policy to delete in the configuration file:
      
            ```hcl
            resource "yandex_storage_bucket_policy" "bpolicy" {
              bucket = "my-policy-bucket"
              policy = <<POLICY
            {
              "Version": "2012-10-17",
              "Statement": [
              {
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:*",
                "Resource": [
                  "arn:aws:s3:::my-policy-bucket/*",
                  "arn:aws:s3:::my-policy-bucket"
                ]
              },
              {
                "Effect": "Deny",
                "Principal": "*",
                "Action": "s3:PutObject",
                "Resource": [
                  "arn:aws:s3:::my-policy-bucket/*",
                  "arn:aws:s3:::my-policy-bucket"
                ]
              }
              ]
            }
            POLICY
            }
            ```

        1. Delete the `yandex_storage_bucket_policy` section describing the bucket policy settings from the configuration file.

        {% endcut %}

  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     This will delete the bucket policy from the specified folder. You can check the bucket policy deletion using the [management console]({{ link-console-main }}).

- API {#api}

  Use the [DeleteBucketPolicy](../../s3/api-ref/policy/delete.md) S3 API method.

{% endlist %}

## See also {#see-also}

* [{#T}](../../concepts/policy.md#config-examples)