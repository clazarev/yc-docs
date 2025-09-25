---
title: '{{ mch-full-name }} release notes'
description: This section contains {{ mch-name }} release notes.
---

# {{ mch-full-name }} release notes

## July 2025 {#jul-2025}

* New {{ CH }} versions are out: [25.3](https://clickhouse.com/blog/clickhouse-release-25-03), [25.4](https://clickhouse.com/blog/clickhouse-release-25-04), [25.5](https://clickhouse.com/blog/clickhouse-release-25-05), and [25.6](https://clickhouse.com/blog/clickhouse-release-25-06).
* The backup process is now up to 6 times faster.
* Added support for extension management via the [{{ yandex-cloud }} CLI](../cli/quickstart.md) and API.


## July 2025 {#jul-2025}

Now you can [encrypt storage disks](concepts/storage.md#disk-encryption) with a custom KMS key when creating a cluster and restoring it from a backup.


## April 2025 {#apr-2025}

Added [integration with {{ connection-manager-name }}](operations/update.md#conn-man). Now, a connection in {{ connection-manager-name }} is automatically created for all new clusters.

## March 2025 {#mar-2025}

A new version is out: {{ CH }} [25.2](https://clickhouse.com/blog/clickhouse-release-25-02).

## February 2025 {#feb-2025}

* A new version is out: {{ CH }} [25.1](https://clickhouse.com/blog/clickhouse-release-25-01).
* You can now [restore a cluster with enabled sharding from a backup](operations/cluster-backups.md#restore).

## January 2025 {#jan-2025}

A new version is out: {{ CH }} [24.12](https://clickhouse.com/blog/clickhouse-release-24-12).

## November 2024 {#nov-2024}

A new version is out: {{ CH }} [24.10](https://clickhouse.com/blog/clickhouse-release-24-10).

## October 2024 {#oct-2024}

* A new version is out: {{ CH }} [24.9](https://clickhouse.com/blog/clickhouse-release-24-09).
* Now you can [specify a retention period for your automatic backups](operations/cluster-backups.md#set-backup-retain).

## September 2024 {#sep-2024}

A new version is out: {{ CH }} [24.8 LTS](https://clickhouse.com/blog/clickhouse-release-24-08).

## July 2024 {#jul-2024}

* A new version is out: {{ CH }} [24.6](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-246-2024-07-01).
* Now you can automatically convert non-replicated tables to [replicated](./concepts/replication.md#replicated-tables) ones when [adding {{ ZK }} hosts](./operations/zk-hosts.md). This streamlines migration to a fault-tolerant configuration for single-host clusters.
* Now you can delete [backups](./concepts/backup.md) manually.

## June 2024 {#jun-2024}

A new version is out: {{ CH }} [24.5](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-245-2024-05-30).

## May 2024 {#may-2024}

A new version is out: {{ CH }} [24.4](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-244-2024-04-30).

## April 2024 {#apr-2024}

A new version is out: {{ CH }} [24.3 LTS](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-243-lts-2024-03-27).

## March 2024 {#mar-2024}

* A new version is out: {{ CH }} [24.2](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-242-2024-02-29).
* Now you can create or delete multiple hosts within a single operation. This will help speed up adding or deleting cluster hosts in bulk.
* Doubled the [limit](./concepts/limits.md#mch-limits) on the size of `network-ssd-nonreplicated` drives from 8 TB to 16 TB. Now you can expand your cluster without adding additional shards.

## February 2024 {#feb-2024}

* New {{ CH }} versions are out: [23.9](https://clickhouse.com/docs/en/whats-new/changelog/2023#239), [23.10](https://clickhouse.com/docs/en/whats-new/changelog/2023#2310), [23.11](https://clickhouse.com/docs/en/whats-new/changelog/2023#2311), [23.12](https://clickhouse.com/docs/en/whats-new/changelog/2023#2312), and [24.1](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-241-2024-01-30).
* Now you can [restart cluster hosts](./operations/hosts.md#restart). In most cases, users do not need to restart hosts. This is an emergency troubleshooting tool in case of memory leaks or unresponsive internal DBMS processes.

## Q2 2023 {#q2-2023}

New {{ CH }} versions are out: [23.4](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-234-2023-04-26) and [23.5](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-235-2023-06-08).

## Q1 2023 {#q1-2023}

A new version is out: {{ CH }} [23.3](https://clickhouse.com/docs/en/whats-new/changelog#-clickhouse-release-233-lts-2023-03-30).

## Q4 2022 {#q4-2022}

* New {{ CH }} versions are out: [22.10](https://clickhouse.com/docs/en/whats-new/changelog/2022/#-clickhouse-release-2210-2022-10-25) and [22.11](https://clickhouse.com/docs/en/whats-new/changelog/2022/#-clickhouse-release-2211-2022-11-17).
* You can now create {{ ZK }} hosts with [non-replicated SSD storage](concepts/storage.md).
* You can now set up {{ ZK }} hosts when restoring a cluster from a backup.
* You can now set up hybrid storage when restoring a cluster from a backup.
* You can now run SQL queries from your browser using a [built-in SQL editor](./operations/connect/clients.md#inline-editor).
* Added the **Data parts**, **Databases**, **Inode usage**, **Rows of MergeTree tables**, and **Tables** charts on the [cluster monitoring](operations/monitoring.md#monitoring-cluster) page.
* The **Failed insert queries per host**, **Failed queries per host**, and **Failed select queries per host** charts now show the percentage of failed queries rather than their number.
* The **Max replication queue across tables** chart now shows the total replication queue of all tables and is called **Replication queue**.
* Renamed the following charts:
    * **CPU** → **CPU usage**.
    * **Disk bytes** → **Disk read/write bytes**.
    * **Memory** → **Memory usage**.
    * **Read parts per host** → **Read rows per host**.

## Q3 2022 {#q3-2022}

* Made the following parameters optional for the `{{ yc-mdb-ch }} cluster restore` command:

    * `--name`
    * `--description`
    * `--host`
    * `--user`

    Unless these parameters are explicitly specified, their values will be taken from the source cluster. At the same time, {{ CH }} and {{ ZK }} hosts will have the same configuration as in the source cluster once restored. {{ CH }} hosts will only be restored for the shards whose backups are selected for restoration.

* Added the `data_cache_enabled`, `data_cache_max_size`, and `move_factor` settings for hybrid storage.

## Q2 2022 {#q2-2022}

* A new version is out: {{ CH }} 22.5 ([revision list](https://clickhouse.com/docs/en/whats-new/changelog/2022/#-clickhouse-release-225-2022-05-19)).
* Added support for `SYSTEM UNFREEZE`. The query completely clears your cloud storage of the cluster backup, including table data that may take up storage space after deletion. For more information, visit [GitHub](https://github.com/ClickHouse/ClickHouse/pull/36424).
* Reduced the time to restore a cluster from a backup.
* Fixed the bug that led to deletion of pre-configured topic access passwords when editing the {{ KF }} topic connection list.
* You can now grant permissions to system tables (`_system`) to users.
* Enabled the [force_remove_data_recursively_on_drop](https://github.com/ClickHouse/ClickHouse/pull/30054) setting to prevent `Directory not empty` errors when deleting databases.

## Q1 2022 {#q1-2022}

* A new version is out: {{ CH }} 22.3 LTS ([revision list](https://github.com/ClickHouse/ClickHouse/blob/master/CHANGELOG.md)).
* Added the option to restore an entire sharded cluster from a backup.
* Added the option to change the settings that enable public access to a host.

{% include [clickhouse-disclaimer](../_includes/clickhouse-disclaimer.md) %}
