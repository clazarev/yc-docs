---
title: '{{ maf-full-name }} release notes'
description: This section contains {{ maf-name }} release notes.
---

# {{ maf-full-name }} release notes

## May 2025 {#may-2025}

Added the **{{ ui-key.yacloud.mdb.maintenance.title_maintenance }}** section to the management console.

## November 2024 {#nov-2024}

Added the option to select a [configuration](concepts/index.md#presets) (vCPUs and RAM) for each cluster component when [creating](operations/cluster-create.md) or [updating](operations/cluster-update.md) it.

## September 2024 {#sep-2024}

* On September 16, 2024, {{ maf-name }} entered the [General Availability](../overview/concepts/launch-stages.md) stage. Since then its use is charged according to the [pricing policy](pricing.md).
* Added the option to run operations on an {{ AF }} cluster using the [{{ yandex-cloud }} CLI](../cli/cli-ref/managed-airflow/cli-ref/index.md), [{{ TF }}](tf-ref.md), and [API](api-ref/authentication.md).

## June 2024 {#jun-2024}

* Now {{ AF }} task logs are stored in {{ objstorage-full-name }}. This addresses the issue of unavailable logs on disabled workers after autoscaling is over.
* You can now store connections, variables, and configuration data used in DAG files in {{ lockbox-full-name }}. For more information, see [Storing connections in {{ lockbox-full-name }}](operations/lockbox-secrets-in-maf-cluster.md).

## May 2024 {#may-2024}

Now you can attach to a cluster a service account it will use to access other {{ yandex-cloud }} services, e.g., {{ cloud-logging-name }}, {{ monitoring-full-name }}, and {{ lockbox-full-name }}. It also enables the use of the full {{ yandex-cloud }} SDK functionality when writing DAG scripts with no need for additional authorization setup.

## April 2024 {#apr-2024}

* Now you can install deb packages as dependencies when [creating](operations/cluster-create.md) or [updating](operations/cluster-update.md) a cluster.
* Now you will get a clear error text in case of issues when installing pip and deb dependencies. Moreover, if you specified invalid packages, the update cluster operation will roll back, allowing you to edit the input without contacting support.
* Scripts in DAG files now run faster.
