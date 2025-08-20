---
title: Storage in {{ mpg-full-name }}
description: In this article, you will learn what storage is in {{ mpg-name }}, how to manage disk space, and how to select the right disk type when creating a cluster.
---

# {{ mpg-name }} storage



{{ mpg-name }} allows you to use network and local storage drives for database clusters. Network drives are based on network blocks, which are virtual disks in the {{ yandex-cloud }} infrastructure. Local disks are physically located on the database host servers.

{% include [storage-type-nrd](../../_includes/mdb/mpg/storage-type.md) %}

## Selecting disk type when creating a cluster {#storage-type-selection}

The number of hosts you can create together with a {{ PG }} cluster depends on the selected disk type:

* You can create a cluster only with three or more hosts when using the following disk types:

    * Local SSDs (`local-ssd`)
    * Non-replicated SSDs (`network-ssd-nonreplicated`)

    This cluster will be fault-tolerant.

    Local SSD storage has an effect on how much a cluster will cost: you pay for it even if it is stopped. For more information, see [Pricing policy](../pricing.md).

* You can add any number of hosts within the current quota when using the following disk types:

    * Network HDDs (`network-hdd`)
    * Network SSDs (`network-ssd`)
    * Ultra high-speed network SSDs with three replicas (`network-ssd-io-m3`)

For more information about limits on the number of hosts per cluster, see [Quotas and limits](./limits.md).



## Disk encryption {#disk-encryption}

{% include [disk-encryption](../../_includes/mdb/disk-encryption.md) %}


## Disk space management {#manage-storage-space}

When the storage is more than 97% full, the host automatically switches to read-only mode. All DBs get the `DEFAULT_TRANSACTION_READ_ONLY = TRUE` setting through the `ALTER DATABASE` query.

In this mode, the `INSERT`, `DELETE`, or `UPDATE` queries result in an error.


You can monitor storage utilization on cluster hosts [by setting up alerts in {{ monitoring-full-name }}](../operations/storage-space.md#set-alert):


### Recovering a cluster from read-only mode {#read-only-solutions}

Use one of these methods:

* [Increase the storage capacity](../operations/storage-space.md#change-disk-size) to exceed the threshold value. {{ mpg-short-name }} will then disable read-only mode automatically.

* [Disable read-only mode manually](../operations/storage-space.md#read-only-solutions) and free up storage space by deleting some data.

    {% note alert %}

    When doing so, make sure the amount of free disk space never reaches zero. Otherwise, since the fail-safe mechanism is disabled, {{ PG }} will crash and the cluster will stop operating.

    {% endnote %}

### Automatic increase of storage size {#auto-rescale}

Automatic storage size increase prevents situations where the disk runs out of free space and hosts switch to read-only mode. The storage size increases upon reaching the specified threshold percentage of the total capacity. There are two thresholds:

* Scheduled increase threshold: To plan this increase, an algorithm analyzes data from the last few hours and estimates how quickly the storage is filling up. If the calculations show that the specified threshold will be exceeded by the start of the nearest [maintenance window](maintenance.md#maintenance-window), the system schedules a storage increase. If a check at the maintenance start shows that the threshold was indeed exceeded, the storage size is increased.

* Immediate increase threshold: When reached, the storage size increases immediately.

You can use either one or both thresholds. If you set both, make sure the immediate increase threshold is higher than the scheduled one.

{% include [storage-resize-steps](../../_includes/mdb/mpg/storage-resize-steps.md) %}

You can configure automatic increase of storage size when [creating](../operations/cluster-create.md) or [updating a cluster](../operations/storage-space.md#disk-size-autoscale). If you set the scheduled increase threshold, you also need to configure the maintenance window schedule.

{% include [warn-storage-resize](../../_includes/mdb/mpg/warn-storage-resize.md) %}

## Use cases {#examples}

* [{#T}](../tutorials/data-recording.md)
* [{#T}](../tutorials/logging.md)
* [{#T}](../tutorials/mlflow-datasphere.md)
* [{#T}](../tutorials/datalens.md)
