---
title: How to connect to a database in a {{ MY }} cluster in {{ mmy-full-name }}
description: Follow this guide to connect to a database in a {{ MY }} cluster.
---

# Connecting to a database in a {{ MY }} cluster

You can connect to {{ mmy-short-name }} cluster hosts:

{% include [cluster-connect-note](../../_includes/mdb/mmy/cluster-connect-note.md) %}

{% note warning %}

If only some cluster hosts have public access configured, the master may not be accessible from the internet when it [changes automatically](../concepts/replication.md#master-failover).

{% endnote %}

The maximum number of connections is defined by the [Max connections](../concepts/settings-list.md#setting-max-connections) setting that [depends on the host class](../concepts/settings-list.md#settings-instance-dependent).

For more information, see [{#T}](../concepts/network.md).


## Configuring security groups {#configure-security-groups}

{% include [sg-rules](../../_includes/mdb/sg-rules-connect.md) %}

Rule settings depend on the connection method you select:

{% list tabs group=connection_method %}

- Over the internet {#internet}

    [Configure all the cluster security groups](../../vpc/operations/security-group-add-rule.md) to allow incoming traffic on port {{ port-mmy }} from any IP address. To do this, create the following rule for inbound traffic:

    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `{{ port-mmy }}`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.common.label_tcp }}`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**: `0.0.0.0/0`

- From a VM in {{ yandex-cloud }} {#cloud}

    1. [Configure all the cluster security groups](../../vpc/operations/security-group-add-rule.md) to allow incoming traffic on port {{ port-mmy }} from the security group where the VM is located. To do this, create the following rule for incoming traffic in these groups:

        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `{{ port-mmy }}`
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.common.label_tcp }}`
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-sg }}`
        * Security group: If your cluster and VM are in the same security group, select `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-sg-type-self }}` (`Self`). Otherwise, specify the VM security group.

    1. [Configure the VM's security group](../../vpc/operations/security-group-add-rule.md) to allow VM connections and traffic between the VM and the cluster hosts.

        For example, you can set the following rules for a VM:

        * For incoming traffic:
           * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `{{ port-ssh }}`
           * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.common.label_tcp }}`
           * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`
           * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**: `0.0.0.0/0`

            This rule allows [VM connections](../../compute/operations/vm-connect/ssh.md#vm-connect) over SSH.

        * For outgoing traffic:
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `{{ port-any }}`
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` (`Any`)
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**: `0.0.0.0/0`

            This rule allows all outgoing traffic, thus enabling you not only to connect to the cluster but also to install the certificates and utilities your VM needs for the connection.

{% endlist %}

{% note info %}

You can specify more granular rules for your security groups, such as only allowing traffic within specific subnets.

You must configure security groups correctly for all subnets where the cluster hosts will reside. If security group settings are incomplete or incorrect, you may lose access to the cluster.

{% endnote %}

For more information about security groups, see [{#T}](../concepts/network.md#security-groups).


## Getting an SSL certificate {#get-ssl-cert}

{{ MY }} hosts with public access only support encrypted connections. To use them, get an SSL certificate:

{% include [install-certificate](../../_includes/mdb/mmy/install-certificate.md) %}

{% include [ide-ssl-cert](../../_includes/mdb/mdb-ide-ssl-cert.md) %}

## FQDNs of {{ MY }} hosts {#fqdn}

To connect to a host, you need its fully qualified domain name ([FQDN](../concepts/network.md#hostname)). You can use the [FQDN of a particular host](#get-fqdn) in the cluster or a special FQDN always pointing to the [current master host](#fqdn-master) or the [least lagging replica](#fqdn-replica).

Example of the host FQDN:

```text
{{ host-name }}.{{ dns-zone }}
```

### Getting a host FQDN {#get-fqdn}

There are several ways to get a {{ MY }} host FQDN:

* Look up the FQDN in the management console:

    1. Navigate to the cluster page.
    1. Navigate to **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}**.
    1. Copy the **{{ ui-key.yacloud.mdb.cluster.hosts.host_column_name }}** column value.

* In the [management console]({{ link-console-main }}), copy the command for connecting to the cluster. This command contains the host FQDN. To get the command, go to the cluster page and click **{{ ui-key.yacloud.mdb.clusters.button_action-connect }}**.

* [Request a list of cluster hosts](../operations/hosts.md#list) using the CLI or API.

## Special FQDNs {#special-fqdns}

Alongside [regular FQDNs](connect.md#fqdn), {{ mmy-name }} provides several special FQDNs, which can also be used when connecting to a cluster.

{% include [special-fqdns-info](../../_includes/mdb/special-fqdns-info.md) %}

{% note warning %}

If, when the [master host is changed automatically](../concepts/replication.md#master-failover), a host with no public access becomes a new master or the most recent replica, you will not be able to access this host from the internet. To avoid this, [enable public access](hosts.md#update) for all cluster hosts.

{% endnote %}

### Current master {#fqdn-master}

An FQDN in `c-<cluster_ID>.rw.{{ dns-zone }}` format always points to the current master host in the cluster. You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

When connecting to this FQDN, both read and write operations are allowed.

{% include [special-fqdns-warning](../../_includes/mdb/special-fqdns-warning.md) %}

### Most recent replica {#fqdn-replica}

An FQDN in `c-<cluster_ID>.ro.{{ dns-zone }}` format points to a [replica](../concepts/replication.md) that is most up-to-date with the master host. You can request the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

**Specifics:**

* When connected to this FQDN, you are only allowed to perform read operations.
* If there are no active replicas in a cluster, you cannot connect to this FQDN as the respective DNS CNAME record will point to a null object (`null`).


## Connecting from graphical IDEs {#connection-ide}

{% include [ide-environments](../../_includes/mdb/mdb-ide-envs.md) %}

You can only use graphical IDEs to connect to public cluster hosts with an SSL certificate.

{% include [note-connection-ide](../../_includes/mdb/note-connection-ide.md) %}

{% list tabs group=ide %}

- DataGrip {#datagrip}

  1. Create a data source:
     1. Select **File** → **New** → **Data Source** → **{{ MY }}**.
     1. On the **General** tab:
        1. Configure the connection as follows:
           * **Host**: [FQDN of any {{ MY }}](#fqdn) host or a [special FQDN](#fqdn-master).
           * **Port**: `{{ port-mmy }}`.
           * **User**, **Password**: DB user name and password.
           * **Database**: Name of the DB to connect to.
        1. Click **Download** to download the connection driver.
     1. On the **SSH/SSL** tab:
         1. Enable **Use SSL**.
         1. In the **CA file** field, specify the path to the file with an [SSL certificate for the connection](#get-ssl-cert).
  1. To test the connection, click **Test Connection**. If the connection is successful, you will see the connection status and information about the DBMS and driver.
  1. Click **OK** to save the data source.

- DBeaver {#dbeaver}

  1. Create a new DB connection:
     1. In the **Database** menu, select **New connection**.
     1. Select **{{ MY }}** from the DB list.
     1. Click **Next**.
     1. Specify the connection settings on the **Main** tab:
        * **Server**: [FQDN of any {{ MY }}](#fqdn) host or a [special FQDN](#fqdn-master).
        * **Port**: `{{ port-mmy }}`.
        * **Database**: DB to connect to.
        * **Username**, **Password**: DB username and password.
     1. On the **SSL** tab:
         1. Enable **Use SSL**.
         1. In the **Root certificate** field, specify the path to the saved [SSL certificate](#get-ssl-cert) file.
         1. Under **Advanced**:
            1. Enable **Require SSL**.
            1. Enable **Verify server certificate**.
  1. Click **Test Connection ...**. If the connection is successful, you will see the connection status and information about the DBMS and driver.
  1. Click **Done** to save the database connection settings.

{% endlist %}


## Connecting from {{ websql-full-name }} {#websql}

{% include [WebSQL](../../_includes/mdb/mmy/websql.md) %}



## Before you connect from a Docker container {#connection-docker}

To connect to a {{ mmy-name }} cluster from a Docker container, add the following lines to the Dockerfile:

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

  ```bash
  RUN apt-get update && \
      apt-get install mysql-client --yes
  ```


- Connecting with SSL {#with-ssl}

  ```bash
  RUN apt-get update && \
      apt-get install wget mysql-client --yes && \
      mkdir --parents ~/.mysql && \
      wget "{{ crt-web-path }}" \
           --output-document ~/.mysql/root.crt && \
      chmod 0600 ~/.mysql/root.crt
  ```

{% endlist %}

## Examples of connection strings {#connection-string}

{% include [conn-strings-environment](../../_includes/mdb/mdb-conn-strings-env.md) %}

You can only connect to public {{ MY }} hosts using an SSL certificate.

{% include [mmy-connection-strings](../../_includes/mdb/mmy/code-examples.md) %}
