---
title: Terms and definitions for {{ ydb-full-name }}
description: This page presents terms and definitions for {{ ydb-name }}.
sourcePath: overlay/public_talks.md
---

# Terms and definitions for {{ ydb-name }}

## Database {#database}

You can create a [{{ ydb-short-name }}]({{ ydb.docs }}/concepts/databases#database) database in {{ ydb-name }} in a serverless configuration or with [dedicated servers](../operations/manage-databases.md#create-db-serverless). For more information about differences between configurations, see [Serverless and dedicated modes](serverless-and-dedicated.md).

### Serverless database {#serverless}

Computing resources for serverless {{ ydb-short-name }} DBs in {{ ydb-name }} are provided automatically for running DB queries.

The payment amount depends on the CPU capacity actually used and the I/O operations required to run a database query. The amount of data stored in the database is paid additionally.

### Database on dedicated resources {#resource-presets}

A {{ ydb-name }} DB is provided on VMs running on platforms supported by {{ compute-full-name }}. For more information, see [{#T}](../../compute/concepts/vm-platforms.md).

{% note warning %}

For reliable and stable performance, a database needs multiple slots. A database run in the production environment must have at least three slots.

{% endnote %}

In {{ ydb-name }}, the following VM configurations are available:

| Platform | Configuration name | Number of vCPUs | Guaranteed vCPU share | RAM, GB |
| ----- | ----- | ----- | ----- | ----- |
| **Intel Cascade Lake** | medium | 8 | 100% | 32 |
| **Intel Cascade Lake** | medium-m64 | 8 | 100% | 64 |
| **Intel Cascade Lake** | medium-m96 | 8 | 100% | 96 |
| **Intel Cascade Lake** | large | 12 | 100% | 48 |
| **Intel Cascade Lake** | xlarge | 16 | 100% | 64 |
| **Intel Cascade Lake** | olap-c16-m128 | 16 | 100% | 128 |
| **Intel Cascade Lake** | oltp-c16-m128 | 16 | 100% | 128 |

Databases run on allocated computing resources are paid on an hourly basis. The amount of data storage for the database is paid additionally (see [Storage groups](#storage-groups) below).

## Cluster {#cluster}

A {{ ydb-short-name }} cluster is a set of {{ ydb-short-name }} nodes the load is distributed across.

The {{ ydb-name }} team takes care of cluster maintenance, while application developers can work directly with {{ ydb-short-name }}.

For more information about {{ ydb-short-name }} clusters, see the [documentation]({{ ydb.docs }}/concepts/databases#cluster).

## Storage groups {#storage-groups}

{{ ydb-full-name }} uses three disks in each of the three availability zones, both for databases on dedicated resources and serverless databases. This configuration ensures fault tolerance if a zone or disk fails, as well as provides a redundancy factor of 3.

For more information about storage groups in {{ ydb-short-name }}, see the [documentation]({{ ydb.docs }}/concepts/glossary#storage-group).

## Temporary storage (spilling) {#spilling}

{{ ydb-full-name }} OLAP databases use **spilling**, a memory management mechanism to save temporary computation data to the disk if there is not enough RAM. Spilling enables user queries that involve processing massive amounts of data, when temporary data exceeds the available node memory.

For more information about spilling in {{ ydb-short-name }}, see [this guide]({{ ydb.docs }}/concepts/spilling).

## Regions and availability zones {#regions-az}

{{ ydb-name }} databases are hosted in three availability zones of the central Russia `{{ region-id }}` region. {{ ydb-name }} ensures the databases are fully available if any availability zone fails.

You can learn more about {{ yandex-cloud }} availability zones in [{#T}](../../overview/concepts/geo-scope.md). To move a database to a different availability zone, follow [this guide](../operations/migration-to-an-availability-zone.md).
