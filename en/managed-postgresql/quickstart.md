---
title: Getting started with {{ mpg-full-name }}
description: Follow this guide to create a {{ PG }} cluster and connect to it.
---

# Getting started with {{ mpg-name }}

To get started:
* [Create a DB cluster](#cluster-create).
* [Connect to the DB](#connect).


## Getting started {#before-you-begin}

1. Navigate to the [management console]({{ link-console-main }}) and log in to {{ yandex-cloud }} or sign up if not signed up yet.

1. If you do not have a folder yet, create one:

   {% include [create-folder](../_includes/create-folder.md) %}

1. [Assign](../iam/operations/roles/grant.md) the [{{ roles-vpc-user }}](../vpc/security/index.md#vpc-user) role and the [{{ roles.mpg.editor }} role or higher](security/index.md#roles-list) to your {{ yandex-cloud }} account to be able to create a cluster.

    {% include [note-managing-roles](../_includes/mdb/note-managing-roles.md) %}

1. You can connect to DB clusters from both inside and outside {{ yandex-cloud }}:

   * To connect from inside {{ yandex-cloud }}, create a VM in the same cloud network as the DB cluster (on [Linux](../compute/quickstart/quick-create-linux.md)).

   * To connect to the cluster from the internet, request public access to hosts when creating the cluster.

   {% note info %}

   The next step requires connecting to the cluster from a [Linux](../compute/quickstart/quick-create-linux.md)-based VM.

   {% endnote %}

1. [Connect](../compute/operations/vm-connect/ssh.md) to your VM over SSH.

1. Install the required dependencies and the {{ PG }} client:

   ```bash
   sudo apt update && sudo apt install -y postgresql-client
   ```


## Create a cluster {#cluster-create}

1. In the management console, select the folder where you want to create a DB cluster.
1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
1. Specify your cluster settings and click **{{ ui-key.yacloud.mdb.forms.button_create }}**. For more information, see [Creating a cluster](operations/cluster-create.md).
1. Wait until the cluster is ready: its status on the {{ mpg-short-name }} dashboard will change to **Running** and its state, to **Alive**. This may take some time.
1. Optionally, [transfer data](../data-transfer/tutorials/managed-postgresql.md#quick-transfer) to the cluster database.

## Connect to the DB {#connect}


1. If you are using security groups for a cloud network, [configure them](operations/connect.md#configuring-security-groups) to enable all relevant traffic between the cluster and the connecting host.


1. To connect to the DB server, get an SSL certificate:

    {% include [install-certificate](../_includes/mdb/mpg/install-certificate.md) %}

1. Use the `psql` command to connect:

    {% include [default-connstring](../_includes/mdb/mpg/default-connstring.md) %}


## What's next {#whats-next}

* Read about the [service concepts](concepts/index.md).
* Learn more about [creating a cluster](operations/cluster-create.md) and [connecting to a database](operations/connect.md).
* Check [questions and answers](qa/general.md).
