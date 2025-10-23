---
title: Replication in {{ mch-full-name }}
description: In this tutorial, you will learn how cluster host replication works in {{ mch-full-name }}.
---

# Replication in {{ mch-name }}

In {{ CH }}, replication is performed if the cluster meets all these conditions:

* There is at least one shard with two or more hosts.
* Host coordination tool is set up.

A {{ mch-name }} cluster with enabled replication is fault-tolerant. In such a cluster, you can create [replicated tables](#replicated-tables) and [replicated databases](#replicated-db).

With {{ mch-name }}, you can use one of the following tools to coordinate hosts and distribute queries among them:

* [{{ CK }}](#ck)
* [{{ ZK }}](#zk) (default)

## {{ CK }} {#ck}

{% include [ClickHouse Keeper preview note](../../_includes/mdb/mch/note-ck-preview.md) %}

{{ CK }} is a service for data replication and running distributed DDL queries; it implements the {{ ZK }}-compatible client-server protocol. Unlike {{ ZK }}, {{ CK }} does not require separate hosts for its operation and runs on {{ CH }} hosts. You can enable {{ CK }} support only when [creating a cluster](../operations/cluster-create.md).

Using {{ CK }} is associated with the following limitations:

* You can only create clusters of three or more hosts.
* {{ CK }} support cannot be enabled or disabled after creating a cluster.
* You cannot switch clusters using {{ ZK }} hosts to {{ CK }}.
* [To migrate a host](../operations/host-migration.md) from {{ CK }} to a different availability zone, you have to contact [support]({{ link-console-support }}).

You can learn more about {{ CK }} in [this {{ CH }} guide]({{ ch.docs }}/operations/clickhouse-keeper/).

## {{ ZK }} {#zk}

{{ ZK }} is a coordination tool you can use to distribute queries among {{ CH }} hosts. For successful replication, a {{ mch-name }} cluster must have [three or five {{ ZK }} hosts](../qa/cluster-settings.md#zookeeper-hosts-number).

If your cluster consists of one {{ CH }} host or several single-host shards and was originally created without {{ CK }} support, you must enable fault tolerance for the cluster before adding new hosts. To do this, [add three or five {{ ZK }} hosts to the cluster](../operations/zk-hosts.md#add-zk). If the cluster already has {{ ZK }} hosts, you can [add {{ CH }} hosts](../operations/hosts.md#add-host) to any shards.


If you are creating a cluster with two or more {{ CH }} hosts per shard, three {{ ZK }} hosts will be automatically added to the cluster. At this point, you can only set up their configuration. Mind the following:

* If a cluster in the [virtual network](../../vpc/concepts/network.md) has subnets in each [availability zone](../../overview/concepts/geo-scope.md), a {{ ZK }} host is automatically added to each subnet if you do not explicitly specify the settings for such hosts. You can explicitly specify three {{ ZK }} hosts and their settings when creating a cluster, if required.
* If a cluster in the virtual network has subnets only in certain availability zones, you need to explicitly specify three {{ ZK }} hosts and their settings when creating a cluster.

* If you did not specify any subnets for these hosts, {{ mch-short-name }} will automatically distribute them among the subnets of the network the {{ CH }} cluster is connected to.


The minimum number of cores per {{ ZK }} host depends on the total number of cores on {{ CH }} hosts:

| Total number of {{ CH }} host cores | Minimum number of cores per {{ ZK }} host |
|-------------------------------------------|-------------------------------------------------------|
| Less than 48                                  | 2                                                     |
| 48 or higher                                | 4                                                     |

You can change {{ ZK }} host class and storage size when [updating cluster settings](../operations/update.md#change-resource-preset). You cannot change {{ ZK }} settings or connect to such hosts.

{% note warning %}

{{ ZK }} hosts, if any, are counted in when calculating [resource usage]({{ link-console-quotas }}) and cluster cost.

{% endnote %}

## Replicated tables {#replicated-tables}

{{ CH }} supports automatic replication only for tables on [the ReplicatedMergeTree engine]({{ ch.docs }}/engines/table-engines/mergetree-family/replication/). To enable replication, you can create the tables on each host separately or use a distributed DDL query.

{% note warning %}

We recommend creating replicated tables on all cluster hosts. Otherwise, you may lose data when restoring a cluster from a [backup](backup.md) or [migrating cluster hosts](../operations/host-migration.md) to a different availability zone.

{% endnote %}

To create a `ReplicatedMergeTree` table on a specific {{ CH }} host, run the following query:

```sql
CREATE TABLE db_01.table_01 (
    log_date date,
    user_name String) ENGINE = ReplicatedMergeTree ('/table_01', '{replica}'
)
PARTITION BY log_date
ORDER BY
    (log_date, user_name);
```

Where:

* `db_01`: Database name.
* `table_01`: Table name.
* `/table_01`: Path to the table in {{ ZK }} or {{ CK }}, which must start with a forward slash `/`.
* `{replica}`: Host ID macro substitution.

To create replicated tables on all cluster hosts, send [a distributed DDL request]({{ ch.docs }}/sql-reference/distributed-ddl/):

```sql
CREATE TABLE db_01.table_01 ON CLUSTER '{cluster}' (
    log_date date,
    user_name String) ENGINE = ReplicatedMergeTree ('/table_01', '{replica}'
)
PARTITION BY log_date
ORDER BY
    (log_date, user_name);
```

The `'{cluster}'` argument will be automatically resolved to the {{ CH }} cluster ID.

To learn how to manage the interaction between replicated and distributed tables in a {{ CH }} cluster, see [Sharding](sharding.md).

## Replicated databases {#replicated-db}

When [creating a database](../operations/databases.md#add-db) in {{ CH }}, you can select the Replicated engine. It supports table metadata replication across all database replicas. The set of tables and their schemas will be the same for all replicas.

You set the engine when creating the database and cannot change it for this database.

## Use cases {#examples}

* [{#T}](../tutorials/rdbms-to-clickhouse.md)
* [{#T}](../tutorials/ydb-to-clickhouse.md)
* [{#T}](../tutorials/object-storage-to-clickhouse.md)
* [{#T}](../tutorials/mysql-to-clickhouse.md)

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}
