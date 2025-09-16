---
title: '{{ mpg-full-name }} release notes'
description: This section contains {{ mpg-name }} release notes.
---

# {{ mpg-full-name }} release notes

## July 2025 {#jul-2025}

Now you can [upgrade clusters](operations/cluster-version-update.md) to {{ PG }} version 17 using standard tools without {{ data-transfer-full-name }}.


## May 2025 {#may-2025}

Now you can [encrypt storage disks](concepts/storage.md#disk-encryption) with a custom KMS key when creating a cluster and restoring it from a backup.


## April 2025 {#apr-2025}

Added [integration with {{ connection-manager-name }}](operations/update.md#conn-man). Now, a connection in {{ connection-manager-name }} is automatically created for all new clusters.

## March 2025 {#mar-2025}

* Now you can use [backup policies](operations/backup-retention-policies.md).
* Added the [{{ ui-key.yacloud.mdb.maintenance.title_maintenance }}](operations/cluster-maintenance.md) section to the management console.

## February 2025 {#feb-2025}

Now you can [upgrade clusters](operations/cluster-version-update.md) to {{ PG }} version 16 using standard tools without {{ data-transfer-full-name }}.

## January 2025 {#jan-2025}

[logerrors](https://github.com/munakoiso/logerrors) is now a system extension enabled by default in new clusters.

## October 2024 {#oct-2024}

* Now you can [specify a retention period for your automatic backups](operations/cluster-backups.md#set-backup-retain).
* Added the `mdb_superuser` [role](concepts/roles.md) for users who are not database owners but need to manage privileges as owners.

## September 2024 {#sep-2024}

* Added support for {{ PG }} [version 17]({{ pg.docs.org }}/release/17.0/). Learn how to update a cluster in [{#T}](operations/cluster-version-update.md).
* Added support for the [postgresql_anonymizer](https://gitlab.com/dalibo/postgresql_anonymizer) (`anon`) extension for data anonymization.

## May 2024 {#may-2024}

Users with the `mdb_admin` role can now use the `pg_stat_reset_single_table_counters()` and `pg_stat_reset_single_function_counters()` functions.

## March 2024 {#mar-2024}

* Now you can configure incremental [auto increase of the cluster storage size](./operations/storage-space.md#disk-size-autoscale). Users can set the disk storage usage threshold and the maximum disk size. Upon reaching the threshold, the disk size will automatically increase in fixed increments up to its maximum.
* Now you can upgrade {{ PG }} for 1C to versions 13, 14, and 15.
* Installed the updates from versions [16.2, 15.6, 14.11, 13.14, and 12.18](https://www.postgresql.org/about/news/postgresql-162-156-1411-1314-and-1218-released-2807/).
* Expanded the permissions for users with the `mdb_admin` [role](./concepts/roles.md#mdb-admin). Now they can:
  
  * Grant additional users permissions to use the `pg_cron` extension.
  * Reset statistics using the `pg_stat_reset_shared()` function.
  * Terminate the `autovacuum` sessions.

* Now you can audit {{ mpg-name }} cluster connection events and `pgaudit` events in {{ at-full-name }}.

## Q4 2023 {#q4-2023}

Added support for {{ PG }} version 16.

Key changes in the new version:

* Added the `pg_stat_io` view for input/output monitoring.
* Added parallel implementation of a hash full join and a hash right join.
* Improved the performance of the *vacuum freeze* operation.
* Added the logical replication setting from a replica.
* New version 16 clusters use ICU locales instead of libc.

Learn how to update a cluster in [{#T}](operations/cluster-version-update.md).

For more information about the updates in {{ PG }} version 16, see [this {{ PG }} article]({{ pg.docs.org }}/release/16.0/).

## Q3 2023 {#q3-2023}

{{ PG }} 11 is [no longer](https://www.postgresql.org/about/news/postgresql-154-149-1312-1216-1121-and-postgresql-16-beta-3-released-2689/) supported. Starting August 14, you can no longer create new clusters of this version, and the existing clusters were automatically updated on September 12 during the maintenance window.

## Q2 2023 {#q2-2023}

* You can now export raw data on performance diagnostics using the [PerformanceDiagnosticsService/ListRawSessionStates](api-ref/grpc/PerformanceDiagnostics/listRawSessionStates.md) and [PerformanceDiagnosticsService/ListRawStatements](api-ref/grpc/PerformanceDiagnostics/listRawStatements.md) gRPC API calls.
* Added the `Session duration timeout` setting that controls the lifetime of the longest active session or transaction. The default value is 12 hours.

## Q4 2022 {#q4-2022}

* Added support for {{ PG }} version 15.

    Key changes in the new version:

    * Added support for the `SQL MERGE` command that enables performing the `INSERT`, `UPDATE`, or `DELETE` action depending on the conditions.
    * You can now specify column lists and row filter conditions. This allows selective publication of table contents with logical replication.
    * You can now specify [ICU]({{ pg.docs.org }}/15/locale.html#id-1.6.11.3.7) for a database by default.
    * Improved performance, in particular, for in-memory and on-disk sorting.

    Learn how to update a cluster in [{#T}](operations/cluster-version-update.md).

    For more information about the updates in {{ PG }} version 15, see [this {{ PG }} article]({{ pg.docs.org }}/release/15.0/).

* Added support for the [pgaudit](https://www.pgaudit.org/) and [pg_prewarm]({{ pg-docs }}/pgprewarm.html) extensions.
* You can now restore a cluster from a backup in a different folder using any interface.

## Q3 2022 {#q3-2022}

* Added the `Inode usage`, `Memory usage`, and `Free space` charts to the [cluster monitoring](operations/monitoring.md#monitoring-cluster) tab.
* {{ PG }} 10 is [no longer](https://www.postgresql.org/about/news/postgresql-143-137-1211-1116-and-1021-released-2449/) supported. Starting August 15, you can no longer create new clusters of this version, and an automatic cluster update is scheduled from September 1 as part of the maintenance window. To learn how to update a cluster on your own, see [{#T}](operations/cluster-version-update.md).
* You can now create a database from a template with the CLI and {{ TF }}.
* Added support for the `pg_cron` [extension](operations/extensions/pg_cron.md).
* Installed the [version 14.4 update]({{ pg.docs.org }}/release/14.4/) with index corruption issues fixed.

## Q2 2022 {#q2-2022}

* Installed the [updates](https://www.postgresql.org/about/news/postgresql-143-137-1211-1116-and-1021-released-2449/) from versions 14.3, 13.7, 12.11, 11.16, and 10.21.
* You can now create a cluster of versions 13 and 14 for 1C.
* Added the ability to upgrade from version 13 to 14.
* Added the `mdb_monitor` role that includes the privileges of the [pg_monitor](https://www.postgresql.org/docs/10/default-roles.html) system role. For more information, see [Assigning roles](concepts/roles.md#mdb-monitor).
* Removed the `database` and `user` sections in the {{ TF }} configuration files and added the `yandex_mdb_postgresql_database` and `yandex_mdb_postgresql_user` resources instead.
* [Odyssey 1.3](https://www.postgresql.org/about/news/odyssey-13-released-2476/) is now available with support for prepared statements in transaction pooling mode. You can use the `pool_reserve_prepared_statement` setting to activate a pool of prepared database statements where Odyssey will prepare session statements as needed.
* Added the `max_logical_replication_workers` and `max_replication_slots` settings.

## Q1 2022 {#q1-2022}

* Added the `pgcompacttable`, `clickhouse_fdw`, and `orafce` [extensions](operations/extensions/cluster-extensions.md). 
* You can now create a new database from a template.
