---
title: Updating an {{ dataproc-name }} cluster
description: After creating an {{ dataproc-name }} cluster, you can edit its basic and advanced settings.
---

# Updating an {{ dataproc-name }} cluster

After creating a cluster, you can edit its basic and advanced settings.

You can disable sending cluster logs to {{ cloud-logging-full-name }}. For more information, see [Working with logs](logging.md#disable-logs).

You can also move an {{ dataproc-name }} cluster to a different availability zone. This process depends on the cluster type:

* [Migrating a lightweight cluster to a different availability zone](migration-to-an-availability-zone.md).
* [Migrating an HDFS cluster to a different availability zone](../tutorials/hdfs-cluster-migration.md).

{% list tabs group=instructions %}

- Management console {#console}

    To change an {{ dataproc-name }} cluster’s settings:

    1. Navigate to the [folder dashboard]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_data-proc }}**.
    1. Select the cluster and click **{{ ui-key.yacloud.mdb.clusters.button_action-edit }}** in the top panel.
    1. Change the cluster name and description in the **{{ ui-key.yacloud.mdb.forms.base_field_name }}** and **{{ ui-key.yacloud.mdb.forms.base_field_description }}** fields.
    1. Add or delete cluster [labels](../../resource-manager/concepts/labels.md) in the **{{ ui-key.yacloud.component.label-set.label_labels }}** field:
    1. Update cluster settings:

        * **{{ ui-key.yacloud.mdb.forms.base_field_service-account }}**: [Service account](../../iam/concepts/users/service-accounts.md) to which you need to grant access to the {{ dataproc-full-name }} cluster.

            Select an existing service account or [create a new one](../../iam/operations/sa/create.md).

            To update a service account in a {{ dataproc-name }} cluster, [assign](../../iam/operations/roles/grant.md) the [iam.serviceAccounts.user](../../iam/security/index.md#iam-serviceAccounts-user) role or higher to your {{ yandex-cloud }} account.

            {% include [mdb-service-account-update](../../_includes/mdb/service-account-update.md) %}

        * **{{ ui-key.yacloud.mdb.forms.config_field_properties }}**: Cluster [component properties](../concepts/settings-list.md).

            Add, edit, or delete the required properties:

            {% note tip %}

            If you want a property to be included into a configuration file [relating to a specific component](../concepts/settings-list.md#available-properties), specify a [prefix](../concepts/settings-list.md) for the key.

            {% endnote %}

        * **{{ ui-key.yacloud.mdb.forms.config_field_form-bucket-type }}**: [Bucket](../../storage/concepts/bucket.md) name selection format, **{{ ui-key.yacloud.forms.label_form-list }}** or **{{ ui-key.yacloud.forms.label_form-id }}**.

        * **{{ ui-key.yacloud.mdb.forms.config_field_bucket }}**: Name of the bucket that the cluster will use.

            Depending on the format you selected, either pick a name from the list or specify it manually. You can request the bucket name with the [list of buckets in the folder](../../storage/operations/buckets/get-info.md#get-information).

        * **{{ ui-key.yacloud.mdb.forms.field_security-group }}**: [Security groups](../concepts/network.md#security-groups) that the cluster will use.

            Select one or more security groups. If the required security group is not in the list, [create it](../../vpc/operations/security-group-create.md).

            {% note warning %}

            Incorrect security group settings may cause performance issues in an {{ dataproc-full-name }} cluster. For more information on setting up security groups, see [this guide](security-groups.md).

            {% endnote %}

        * **{{ ui-key.yacloud.mdb.forms.config_field_ui_proxy }}**: Option that manages access to [component web interfaces](../concepts/interfaces.md) via [UI Proxy](connect-interfaces.md#ui-proxy).

            {% include [ui-proxy-sg-warning](../../_includes/data-processing/ui-proxy-sg-warning.md) %}

        * **{{ ui-key.yacloud.serverless-functions.triggers.form.field_log-group }}**: {{ cloud-logging-full-name }} [log group](../../logging/concepts/log-group.md) the cluster will send logs to.

            Select the default log group or another existing log group. If the required log group is not in the list, [create it](../../logging/operations/create-group.md).

            To enable the cluster to send logs, [assign](../../iam/operations/roles/grant.md) the `logging.writer` role to its service account. For more information, see the [{{ cloud-logging-full-name }} documentation](../../logging/security/index.md).

    1. In the advanced cluster settings, select the required **{{ ui-key.yacloud.mdb.forms.label_deletion-protection }}** value.

        This option manages cluster protection against accidental deletion by a user.

        Even with cluster deletion protection enabled, one can still connect to the cluster manually and delete its data.

    1. Click **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To change an {{ dataproc-name }} cluster’s settings:

    1. View the description of the CLI command for updating the cluster:

        ```bash
        {{ yc-dp }} cluster update --help
        ```

    1. To edit the [log group](../../logging/concepts/log-group.md) the cluster logs go to, provide the log group ID in the `--log-group-id` parameter:

        ```bash
        {{ yc-dp }} cluster update <cluster_name_or_ID> \
           --log-group-id=<log_group_ID>
        ```

        You can request the log group ID with the [list of log groups in the folder](../../logging/operations/list.md).

    1. To protect a cluster from accidental deletion by a user of your cloud, add the `--deletion-protection` parameter:

        ```bash
        {{ yc-dp }} cluster update <cluster_name_or_ID> \
           --deletion-protection
        ```

        Even with cluster deletion protection enabled, one can still connect to the cluster manually and delete its data.

    1. To update [component properties](../concepts/settings-list.md), provide the required properties in the `--property` parameter:

        ```bash
        {{ yc-dp }} cluster update <cluster_name_or_ID> \
           --property "<key_1_prefix>:<key_1>=<value>", "<key_2_prefix>:<key_2>=<value>", ...
        ```

        {% note warning %}

        Using the `--property` parameter will reset all component properties that were not explicitly provided in the parameter to their defaults. To save the properties you updated, list them in your request along with the ones you want to update.

        {% endnote %}

    You can get the cluster ID and name with the [list of clusters in the folder](cluster-list.md#list).

- {{ TF }} {#tf}

    To change an {{ dataproc-name }} cluster’s settings:

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [Creating clusters](cluster-create.md).

    1. To activate cluster deletion protection and access to the [web interfaces](../concepts/interfaces.md) of the {{ dataproc-name }} components, update the values in the appropriate fields of the {{ dataproc-name }} cluster description:

        ```hcl
        resource "yandex_dataproc_cluster" "data_cluster" {
          ...
          deletion_protection = true
          ui_proxy            = true
          ...
        }
        ```

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information about the resources you can create with {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/dataproc_cluster).

{% endlist %}
