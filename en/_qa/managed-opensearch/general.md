#### How are {{ OS }} clusters maintained? {#service-window}

In {{ mos-short-name }}, maintenance implies:

* Automatic installation of {{ OS }} updates and fixes for your hosts.
* Changes to the host class and storage size.
* Other {{ mos-short-name }} maintenance activities.

For more information, see [Maintenance](../../managed-opensearch/concepts/maintenance.md).

#### Is cluster backup enabled by default? {#default-backup}

Yes, automatic [backup](../../managed-opensearch/concepts/backup.md) is enabled by default and takes place every hour. All backups are incremental and store only the data that has changed since the previous backup to save storage space.

Automatic backups are stored for two weeks.

#### Which {{ OS }} version does {{ mos-short-name }} use? {#dbms-version}

{{ mos-short-name }} uses the {{ OS }} versions maintained and supported by the vendor. For more information, see [{#T}](../../managed-opensearch/concepts/update-policy.md).

#### What happens when a new {{ OS }} version is released? {#new-version}

When a new minor version is released, the cluster software is [automatically updated](../../managed-opensearch/concepts/update-policy.md) after testing. Clusters with an unsupported {{ OS }} version will also be updated automatically.

The owner of the affected clusters will receive a notice of expected work times and database availability.

{% include [logs](../logs.md) %}

{% include [log-duration](../../_includes/mdb/log-duration-qa.md) %}

#### How do I set up an alert that triggers as soon as a certain percentage of disk space has been used up? {#disk-space-percentage}

[Create an alert](../../managed-opensearch/operations/monitoring.md#monitoring-integration) with the `disk.used_bytes` metric in {{ monitoring-full-name }}. This metric shows the disk space usage in the {{ mos-name }} cluster.

For `disk.used_bytes`, use notification thresholds. Their recommended values are as follows:

* `{{ ui-key.yacloud_monitoring.alert.status_alarm }}`: 90% of disk space
* `{{ ui-key.yacloud_monitoring.alert.status_warn }}`: 80% of disk space

Thresholds are set in bytes only. For example, the recommended values for a 100 GB disk are as follows:

* `{{ ui-key.yacloud_monitoring.alert.status_alarm }}`: `96636764160` bytes (90%).
* `{{ ui-key.yacloud_monitoring.alert.status_warn }}`: `85899345920` bytes (80%).

#### Why is my cluster slow even though there are still some computing resources to spare? {#throttling}

{% include [throttling](../throttling.md) %}

To increase the maximum IOPS and bandwidth values and make throttling less likely, increase the storage size or switch to a faster disk type by [restoring the cluster](../../managed-opensearch/operations/cluster-backups.md#restore) from a backup.

#### Can I connect to cluster hosts over SSH or get superuser privileges on hosts? {#connect-ssh}

{% include [connect-via-ssh](../../_includes/mdb/connect-via-ssh.md) %}

#### What should I do if I get a revocation check error when using PowerShell to obtain an SSL certificate? {#get-ssl-error}

Here is the full text of the error:

```text
curl: (35) schannel: next InitializeSecurityContext failed: Unknown error (0x80092012)
The revocation function was unable to check revocation for the certificate
```
This means, when connecting to the website, the service was unable to check whether or not its certificate was listed among revoked ones.

To fix this error:

* Make sure the corporate network settings do not block the check.
* Run the following command with the `--ssl-no-revoke` parameter:

   ```powershell
   mkdir $HOME\.opensearch; curl --ssl-no-revoke --output $HOME\.opensearch\root.crt {{ crt-web-path }}
   ```

#### How can I fix the no permission error when assigning a service account to a cluster? {#attach-service-account}

{% include notitle [attach-sa](../../_qa/attach-sa.md) %}

#### Why do I get an `Unable to confirm permission` error when activating a transfer? {#data-transfer-error}

Here is the full text of the error:

```text
Unable to confirm permission 'data-transfer.transfers.createExternal'
```

This error occurs if the transfer is activated in or from a custom {{ OS }} installation database, but the {{ OS }} endpoint settings have no subnet ID specified.

To fix this error, specify a subnet ID in the {{ OS }} endpoint settings, even if the source and target can access each other without the internet.

#### What is {{ mos-short-name }}'s share of database management and maintenance work? {#services}

{% include [responsibilities-link](../../_includes/mdb/responsibilities-link.md) %}