---
title: Configuring HTTPS for hosting in {{ objstorage-full-name }}
description: Follow this guide to configure HTTPS for hosting in {{ objstorage-name }}.
---

# Configuring HTTPS

If you use a bucket without a period in its name to [host a static website](../../concepts/hosting.md), that website will be accessible by default over HTTP and HTTPS at the following URLs:

* `http(s)://example.website.yandexcloud.net`
* `http(s)://website.yandexcloud.net/example`

This bucket does not require uploading your own security certificate, but it also does not support your own domains.

If you use a bucket with a period in its name to host a static website, that website will be accessible by default over HTTP and HTTPS at the following URLs:

* `http://example.com.website.yandexcloud.net`
* `http(s)://website.yandexcloud.net/example.com`

This bucket also [supports your own domains](./own-domain.md). By default, when addressing the website by domain name, it is only accessible over HTTP, e.g., at `http://example.com`. To make your website accessible by its domain name over HTTPS, upload your own security certificate and the applicable secret key.


{% include [tls-support-alert](../../../_includes/storage/tls-support-alert.md) %}


{{ objstorage-name }} only supports [PEM](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail)-encoded certificates.

{% note info %}

The bucket becomes accessible over HTTPS within 30 minutes of uploading the certificate.

{% include [redirect-https](../../../_includes/storage/redirect-https.md) %}

{% endnote %}

## Selecting a certificate from {{ certificate-manager-name }} {#cert-manager}

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services and go to the bucket in question.
    1. In the left-hand panel, select ![image](../../../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}**.
    1. Select the **{{ ui-key.yacloud.storage.bucket.switch_https }}** tab.
    1. Click **{{ ui-key.yacloud.storage.bucket.https.button_empty-action }}**.
    1. In the **{{ ui-key.yacloud.storage.bucket.https.field_source }}** field, select **{{ ui-key.yacloud.storage.bucket.https.value_method-certificate-manager }}**.
    1. In the **{{ ui-key.yacloud.storage.bucket.https.field_certificate }}** field, select the certificate from the list that opens. 
    
        {% note info %}
        
        If you do not have a certificate in {{ certificate-manager-full-name }} yet, click **Go to {{ certificate-manager-name }}** and follow [this guide](../../../certificate-manager/quickstart/index.md) to create your first certificate.
        
        {% endnote %}

    1. Click **{{ ui-key.yacloud.storage.bucket.https.button_save }}**.

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for editing a bucket ACL:

     ```bash
     yc storage bucket update --help
     ```  

  1. Run this command:

     ```bash
     yc storage bucket set-https --name <bucket_name> --certificate-id <certificate_ID> 
     ```  

     Where:
     * `--name`: Name of the bucket to configure HTTPS for.
     * `--certificate-id`: Certificate ID in {{ certificate-manager-name }}.

     Result:

     ```text
     source_type: SOURCE_TYPE_MANAGED_BY_CERTIFICATE_MANAGER
     certificate_id: fpqe2g0hfr0e********
     ```

- {{ TF }} {#tf}

  {% include [terraform-role](../../../_includes/storage/terraform-role.md) %}
 
  {% include [terraform-definition](../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  
  {% include [terraform-install](../../../_includes/terraform-install.md) %}


  To select a certificate from {{ certificate-manager-name }}:

  1. Open the {{ TF }} configuration file and add the `https` section to the bucket description:

     ```hcl
     ...
     resource "yandex_storage_bucket" "b" {
       bucket = "my-policy-bucket"

       https {
         certificate_id = "<certificate_ID>"
       }
     }
     ...
     ```

     Where:
     * `certificate_id`: Certificate ID in {{ certificate-manager-name }} that will be used for the bucket.

     For more information about the `yandex_storage_bucket` resource parameters in {{ TF }}, see [this TF provider article]({{ tf-provider-resources-link }}/storage_bucket#bucket-https-certificate).

  1. Check the configuration using this command:

     ```bash
     terraform validate
     ```
     
     If the configuration is correct, you will get this message:
     
     ```bash
     Success! The configuration is valid.
     ```

  1. Run this command:

     ```bash
     terraform plan
     ```
  
     You will see a detailed list of resources. No changes will be made at this step. If the configuration contains any errors, {{ TF }} will show them.

  1. Apply the changes:

     ```bash
     terraform apply
     ```
     
  1. Confirm the changes: type `yes` into the terminal and press **Enter**.

     You can check the selected certificate using the [management console]({{ link-console-main }}).

- API {#api}

  To select a certificate from {{ certificate-manager-name }}, use the [setHTTPSConfig](../../api-ref/Bucket/setHTTPSConfig.md) REST API method for the [Bucket](../../api-ref/Bucket/index.md) resource or the [BucketService/SetHTTPSConfig](../../api-ref/grpc/Bucket/setHTTPSConfig.md) gRPC API call.

{% endlist %}

## Uploading a custom security certificate {#own}

To [upload](../../../certificate-manager/operations/import/cert-create.md) a custom certificate, use {{ certificate-manager-name }}.

When uploading a certificate chain, make sure it starts with the domain certificate and ends with the root one. To create a chain file, use the following command:

```bash
cat domain.pem intermediate.pem rootca.pem > bundle.pem
```

Where `domain.pem` is the domain certificate, `intermediate.pem` is the intermediate certificate, `rootca.pem` is the root certificate, and `bundle.pem` is the resulting chain.

To upload a certificate:

{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services and go to the bucket in question.
   1. In the left-hand panel, select ![image](../../../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}**.
   1. Select the **{{ ui-key.yacloud.storage.bucket.switch_https }}** tab.
   1. Click **{{ ui-key.yacloud.storage.bucket.https.button_empty-action }}**.
   1. In the **{{ ui-key.yacloud.storage.bucket.https.field_source }}** field, select **{{ ui-key.yacloud.storage.bucket.https.value_method-custom }}**.
   1. Add **{{ ui-key.yacloud.storage.bucket.https.field_certificate }}** and **{{ ui-key.yacloud.storage.bucket.https.field_private-key }}**.
   1. Click **{{ ui-key.yacloud.storage.bucket.https.button_save }}**.

- API {#api}

  To upload a custom security certificate, use the [setHTTPSConfig](../../api-ref/Bucket/setHTTPSConfig.md) REST API method for the [Bucket](../../api-ref/Bucket/index.md) resource or the [BucketService/SetHTTPSConfig](../../api-ref/grpc/Bucket/setHTTPSConfig.md) gRPC API call.

{% endlist %}

{% note info %}

{% include [public-link](../../../_includes/storage/public-link.md) %}

{% endnote %}