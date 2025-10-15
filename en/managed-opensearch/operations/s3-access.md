---
title: Configuring access to {{ objstorage-full-name }} from a {{ OS }} cluster
description: In this tutorial, you will learn how to configure access to {{ objstorage-name }} to use it as an {{ OS }} snapshot repository.
---

# Configuring access to {{ objstorage-name }} from an {{ OS }} cluster


{{ mos-name }} supports using {{ objstorage-full-name }} as an {{ OS }} snapshot repository. This allows you to use {{ objstorage-name }} to [store backups](cluster-backups.md). For more information about snapshot repositories, see the [{{ OS }} documentation]({{ os.docs }}/opensearch/snapshots/snapshot-restore/).


To access {{ objstorage-name }} bucket data from a cluster:

1. [Attach the service account to the cluster](#connect-service-account).
1. [Configure access permissions](#configure-acl).
1. [Connect a snapshot repository](#register-snapshot-repository).


Before you begin, [assign](../../iam/operations/roles/grant.md) the [iam.serviceAccounts.user](../../iam/security/index.md#iam-serviceAccounts-user) role or higher to your {{ yandex-cloud }} account. You will need this role in the following cases:


* To create or update a cluster and link it to a service account.
* To restore a cluster linked to a service account from its backup.

## Assign the service account to the cluster {#connect-service-account}


1. When [creating](cluster-create.md) or [updating](update.md) a cluster, either select an existing [service account](../../iam/concepts/users/service-accounts.md) or [create a new one](../../iam/operations/sa/create.md).

1. [Assign](../../iam/operations/sa/assign-role-for-sa.md) the `storage.editor` role to this account.


## Configure access permissions {#configure-acl}

{% list tabs group=instructions %}

- Management console {#console}

    
    1. In the [management console]({{ link-console-main }}), select the folder with the appropriate bucket. If there is no such bucket, [create](../../storage/operations/buckets/create.md) one.


    1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
    1. Select the **{{ ui-key.yacloud.storage.buckets.label_title }}** tab.
    1. Set up the [bucket ACL](../../storage/operations/buckets/edit-acl.md):
        1. In the **{{ ui-key.yacloud.component.acl-dialog.label_select-placeholder }}** drop-down list, specify the service account [assigned to the cluster](#connect-service-account).
        1. Set the `READ and WRITE` permissions for this service account.
        1. Click **{{ ui-key.yacloud.common.add }}** and **{{ ui-key.yacloud.common.save }}**.

{% endlist %}

## Connect a snapshot repository {#register-snapshot-repository}

{% note alert %}

If a bucket is registered in an {{ OS }} cluster as a snapshot repository, do not edit the bucket contents manually as this will disrupt the {{ OS }} snapshot mechanism.

{% endnote %}

1. [Connect](connect.md) to the cluster.
1. Register the bucket as a snapshot repository using the public [{{ OS }} API]({{ os.docs }}/opensearch/snapshot-restore/#register-repository):

    ```http
    PUT --cacert ~/.opensearch/root.crt https://admin:<password>@<ID_of_{{ OS }}_host_with_DATA_role>.{{ dns-zone }}:{{ port-mos }}/_snapshot/<repository_name>
    ```

    In the request parameters, specify the bucket associated with the cluster service account:

    ```bash
    curl --request PUT \
         "https://admin:<password>@<ID_of_{{ OS }}_host_with_DATA_role>.{{ dns-zone }}:{{ port-mos }}/_snapshot/<repository_name>" \
         --cacert ~/.opensearch/root.crt \
         --header "Content-Type: application/json" \
         --data '{
           "type": "s3",
           "settings": {
             "endpoint": "{{ s3-storage-host }}",
             "bucket": "<bucket_name>"
           }
         }'
    ```
