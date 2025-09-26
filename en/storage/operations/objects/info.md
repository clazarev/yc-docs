---
title: Getting information about a bucket object in {{ objstorage-full-name }}
description: Follow this guide to get information about a bucket object in {{ objstorage-name }}.
---

# Getting information about an object

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services and go to the bucket you need.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/folder-tree.svg) **{{ ui-key.yacloud.storage.bucket.switch_files }}**.
  1. Click the name of the object you need.

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for getting information about an object:

      ```bash
      yc storage s3api head-object --help
      ```

  1. {% include [bucket-list-cli](../../../_includes/storage/bucket-list-cli.md) %}
  1. Run this command:

      ```bash
      yc storage s3api head-object \
        --bucket <bucket_name> \
        --key <object_key>
      ```

      Where:

      * `--bucket`: Name of your bucket.
      * `--key`: Object [key](../../concepts/object.md#key).

      Result:

      ```bash
      etag: '"d41d8cd98f00b204e9800998********"'
      request_id: 6428ce25********
      accept_ranges: bytes
      content_type: application/octet-stream
      last_modified_at: "2024-10-08T12:36:36Z"
      server_side_encryption: aws:kms
      sse_kms_key_id: abj497vtg3h0********
      ```
  
- API {#api}

  To get information about an object, use the [getObjectMeta](../../s3/api-ref/object/getobjectmeta.md) S3 API method.

{% endlist %}
