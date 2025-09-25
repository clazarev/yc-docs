---
title: Getting started with {{ mmg-full-name }}
description: Follow this guide to set up and configure an {{ SD }} cluster.
---

# Getting started with {{ mmg-name }}

To get started:
1. [Create a cluster](#cluster-create).
1. [Connect to the DB](#connect).


## Getting started {#before-you-begin}

1. Navigate to the [management console]({{ link-console-main }}) and log in to {{ yandex-cloud }} or sign up if not signed up yet.

1. If you do not have a folder yet, create one:

   {% include [create-folder](../_includes/create-folder.md) %}

1. [Assign](../iam/operations/roles/grant.md) the [{{ roles-vpc-user }}](../vpc/security/index.md#vpc-user) role and the [{{ roles.mmg.editor }} role or higher](security/index.md#roles-list) to your {{ yandex-cloud }} account. These roles allow you to create a cluster.

    {% include [note-managing-roles](../_includes/mdb/note-managing-roles.md) %}

1. You can connect to DB clusters from both inside and outside {{ yandex-cloud }}:

   * To connect from inside {{ yandex-cloud }}, create a VM in the same cloud network as the DB cluster (with [Linux](../compute/quickstart/quick-create-linux.md)).

   * To connect to the cluster from the internet, request public access to hosts when creating the cluster.

   {% note info %}

   The next step requires connecting to the cluster from a [Linux](../compute/quickstart/quick-create-linux.md)-based VM.

   {% endnote %}

1. [Connect](../compute/operations/vm-connect/ssh.md) to your VM over SSH.

1. Install {{ MG }} Shell:

   ```bash
   cd ~/ && \
   wget https://repo.mongodb.org/apt/ubuntu/dists/focal/mongodb-org/4.4/multiverse/binary-amd64/mongodb-org-shell_4.4.1_amd64.deb && \
   sudo dpkg -i mongodb-org-shell_4.4.1_amd64.deb
   ```


## Create a cluster {#cluster-create}

1. In the [management console]({{ link-console-main }}), select the folder where you want to create a DB cluster.
1. Select **Yandex StoreDoc**.
1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
1. Specify your cluster settings and click **{{ ui-key.yacloud.mdb.clusters.button_create }}**. For more information, see [Creating a cluster](operations/cluster-create.md).
1. Wait until the cluster is ready: its status on the {{ mmg-short-name }} dashboard will change to **Running** and its state, to **Alive**. This may take some time.

## Connect to the DB {#connect}


1. If you are using security groups for a cloud network, [configure them](operations/connect/index.md#configuring-security-groups) to enable all relevant traffic between the cluster and the connecting host.


1. Get an SSL certificate:

   {% include [install-certificate](../_includes/mdb/mmg/install-certificate.md) %}

1. Connect to the cluster using the {{ SD }} CLI:

   {% include [default-connstring-old](../_includes/mdb/mmg/default-connstring-old.md) %}

    To learn how to get host FQDN, see [this guide](operations/connect/index.md#get-fqdn).


## What's next {#whats-next}

* Read about the [service concepts](concepts/index.md).
* Learn more about [creating a cluster](operations/cluster-create.md) and [connecting to a database](operations/connect/index.md).
* Check [questions and answers](qa/general.md).
