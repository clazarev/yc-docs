---
title: Resource relationships in {{ mkf-full-name }}
description: '{{ KF }} is a distributed cross-application communication system that uses the _publication-subscription_ principle. With {{ mkf-full-name }}, you can deploy and maintain {{ KF }} {{ versions.console.str }} server clusters in the {{ yandex-cloud }} infrastructure. Each {{ mkf-name }} cluster consists of one or more broker hosts, where topics and topic partitions are located.'
---

# Resource relationships in {{ mkf-name }}

{{ KF }} is a distributed cross-application communication system that uses the _publication-subscription_ principle.

{{ mkf-full-name }} helps you deploy and maintain clusters of {{ versions.console.str }} {{ KF }} servers in the {{ yandex-cloud }} infrastructure.


Each {{ mkf-name }} cluster consists of one or more [broker hosts](brokers.md), where [topics](topics.md) and topic partitions are located. Cluster hosts may reside in different availability zones. You can learn more about {{ yandex-cloud }} availability zones in [Platform overview](../../overview/concepts/geo-scope.md).

If all broker hosts are created in the same availability zone, you cannot change their number.


Cluster hosts accept [connections](../operations/connect/clients.md) from clients, such as data [producer](producers-consumers.md) and [consumer](producers-consumers.md) applications. Producers send messages to specific cluster topics and consumers read messages from them. Thus, you will not need to reconfigure consumers if a producer changes.

{% include [responsibilities-link](../../_includes/mdb/responsibilities-link.md) %}

## Host configuration depending on {{ KF }} version {#version}

Different {{ KF }} versions use different tools to store cluster metadata, state, and configuration:

{% include notitle [kafka-versions.md](../../_includes/mdb/mkf/kafka-versions.md) %}

{% note info %}

{% include notitle [default-config](../../_includes/mdb/mkf/create-cluster.md#default-config) %}

{% endnote %}

### Host configuration in clusters with {{ ZK }} {#zookeeper}

{{ ZK }} is automatically configured and placed in a cluster based on the number of broker hosts:

* If the {{ KF }} cluster consists of one broker host, {{ ZK }} is hosted on the same host.
* If the {{ KF }} cluster consists of two or more hosts, {{ ZK }} is hosted separately from brokers on three additional hosts. These hosts are added to the cluster automatically.

You cannot delete {{ ZK }} hosts. The number of {{ ZK }} hosts is fixed.

For more information on creating a {{ mkf-name }} cluster with {{ ZK }}, see [this guide](../operations/cluster-create.md#create-cluster).

### Host configuration in clusters with {{ kraft-name }} {#kraft}

The [{{ kraft-name }} protocol](kraft.md) (abbreviated {{ kraft-short-name }}) is used to store metadata instead of {{ ZK }}.

{% include [kraft-cluster-topology](../../_includes/mdb/mkf/kraft-cluster-topology.md) %}

## Hosting cluster hosts {#hosting}

VMs for cluster hosts can be hosted on:
* {{ yandex-cloud }} *regular hosts*.

  These are physical servers for hosting cluster VMs. They are randomly selected from a pool of available hosts that meet the selected cluster configuration.


* {{ yandex-cloud }} *dedicated hosts*.


  These are physical servers reserved exclusively for your VMs. Such VMs ensure the operation of both the cluster and your other services that support dedicated hosts. The hosts are selected from *dedicated host groups* specified when creating a cluster.

  A cluster with multiple broker hosts needs at least three groups of dedicated hosts to operate. This enables the placement of {{ ZK }} and {{ kraft-short-name }} hosts.

  This placement configuration ensures physical isolation of VMs. A {{ mkf-name }} cluster using dedicated hosts includes all features of a regular cluster.

  For more information, see [{#T}](../../compute/concepts/dedicated-host.md).


For more information, see the [{{ KF }}](https://kafka.apache.org/intro) documentation.
