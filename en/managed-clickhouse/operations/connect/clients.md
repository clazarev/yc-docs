---
title: Connecting to a {{ CH }} cluster in {{ mch-full-name }}
description: Follow this guide to connect to a database in a {{ CH }} cluster using command line tools, browser, graphical IDEs, and Docker container.
---

# Connecting to a {{ CH }} cluster from applications

This section provides settings for connecting to {{ mch-name }} cluster hosts using [command line tools](#command-line-tools), [graphical IDEs](#ide), [browser](#browser), and [Docker container](#docker). To learn how to connect from your application code, see [Code examples](code-examples.md).

You can only connect to public {{ CH }} cluster hosts using [SSL certificates](index.md#get-ssl-cert). The examples below assume that `{{ crt-local-file-root }}` and `{{ crt-local-file-int }}` certificates are:

* Located in the `{{ crt-local-dir }}` directory (for Ubuntu).
* Imported to the trusted root certificate store (for Windows).

Connecting without SSL certificates is only supported for non-public hosts. If this is the case, internal virtual network traffic will not be encrypted for database connections.

Before connecting, [configure security groups](index.md#configure-security-groups) for the cluster, if required.

The examples for Linux were tested in the following environment:

* {{ yandex-cloud }} VM running Ubuntu 20.04 LTS.
* Bash: `5.0.16`.

The examples for Windows were tested in the following environment:

* Local machine with Windows 10 Pro build `19042.1052`.
* PowerShell: `5.1.19041`.

## Command line tools {#command-line-tools}

{% include [see-fqdn-in-console](../../../_includes/mdb/see-fqdn-in-console.md) %}


### clickhouse-client {#clickhouse-client}

**Before connecting**:

1. Connect the {{ CH }} [DEB repository]({{ ch.docs }}/getting-started/install/#install-from-deb-packages):

    ```bash
    sudo apt update && sudo apt install --yes apt-transport-https ca-certificates dirmngr && \
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 8919F6BD2B48D754 && \
    echo "deb https://packages.{{ ch-domain }}/deb stable main" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
    ```

1. Install the dependencies:

    ```bash
    sudo apt update && sudo apt install --yes clickhouse-client
    ```

1. Download the configuration file for `clickhouse-client`:

   {% include [ClickHouse client config](../../../_includes/mdb/mch/client-config.md) %}

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

    ```bash
    clickhouse-client --host <FQDN_of_any_{{ CH }}_host> \
                      --user <username> \
                      --database <DB_name> \
                      --port 9000 \
                      --ask-password
    ```


- Connecting with SSL {#with-ssl}

  {% include [default-connstring](../../../_includes/mdb/mch/default-connstring.md) %}

{% endlist %}

To learn how to get host FQDN, see [this guide](fqdn.md).

After you run the command, enter the user password to complete the connection process.

After connecting to the DBMS, run the `SELECT version();` command.

### mysql-client {#mysql-client}

{% note warning %}

Use the {{ MY }} client connection interface only if using the standard {{ CH }} interfaces is not possible for any reason.

{% endnote %}

Before connecting, install the `mysql` utility:

```bash
sudo apt update && sudo apt install --yes mysql-client
```

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

    ```bash
    mysql --host=<FQDN_of_any_{{ CH }}_host> \
          --port={{ port-mmy }} \
          --ssl-mode=DISABLED \
          --user <username> \
          --password \
          <DB_name>
    ```


- Connecting with SSL {#with-ssl}

    ```bash
    mysql --host=<FQDN_of_any_{{ CH }}_host> \
          --port={{ port-mmy }} \
          --ssl-ca={{ crt-local-dir }}{{ crt-local-file-root }} \
          --ssl-mode=VERIFY_IDENTITY \
          --user=<username> \
          --password \
          <DB_name>
    ```

{% endlist %}

To learn how to get host FQDN, see [this guide](fqdn.md).

After you run the command, enter the user password to complete the connection process.

After connecting to the DBMS, run the `SELECT version();` command.

### cURL {#curl}

#### Linux (Bash)/macOS (Zsh) {#curl-bash-zsh}

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

    ```bash
    curl --header "X-ClickHouse-User: <DB_username>" \
         --header "X-ClickHouse-Key: <DB_user_password>" \
         'http://<FQDN_of_any_{{ CH }}_host>:8123/?database=<DB_name>&query=SELECT%20version()'
    ```


- Connecting with SSL {#with-ssl}

    ```bash
    curl --cacert {{ crt-local-dir }}{{ crt-local-file-root }} \
         --header "X-ClickHouse-User: <DB_username>" \
         --header "X-ClickHouse-Key: <DB_user_password>" \
         'https://<FQDN_of_any_{{ CH }}_host>:8443/?database=<DB_name>&query=SELECT%20version()'
    ```

{% endlist %}

To learn how to get host FQDN, see [this guide](fqdn.md).

#### Windows (PowerShell) {#curl-powershell}

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

    ```powershell
    curl.exe `
        --header "X-ClickHouse-User: <DB_username>" `
        --header "X-ClickHouse-Key: <DB_user_password>" `
        'http://<FQDN_of_any_{{ CH }}_host>:8123/?database=<DB_name>&query=SELECT+version()'
    ```


- Connecting with SSL {#with-ssl}

    ```powershell
    curl.exe `
        --header "X-ClickHouse-User: <DB_username>" `
        --header "X-ClickHouse-Key: <DB_user_password>" `
        'https://<FQDN_of_any_{{ CH }}_host>:8443/?database=<DB_name>&query=SELECT+version()'
    ```

{% endlist %}

To learn how to get host FQDN, see [this guide](fqdn.md).

## Connecting from graphical IDEs {#ide}

{% include [ide-environments](../../../_includes/mdb/mdb-ide-envs.md) %}

You can only use graphical IDEs to connect to public cluster hosts with an SSL certificate.

{% include [note-connection-ide](../../../_includes/mdb/note-connection-ide.md) %}

### DataGrip {#datagrip}

1. Create a data source:
    1. Select **File** → **New** → **Data Source** → **{{ CH }}**.
    1. On the **General** tab:
        1. Configure the connection as follows:
            * **Host**: [FQDN of any {{ CH }}](fqdn.md) host or a [special FQDN](fqdn.md#auto).
            * **Port**: `{{ port-mch-http }}`.
            * **User**, **Password**: DB user name and password.
            * **Database**: Name of the DB to connect to.
        1. Click **Download** to download the connection driver.
    1. On the **SSH/SSL** tab:
        1. Enable **Use SSL**.
        1. Specify the path to the directory that contains the file with the downloaded [SSL certificate for the connection](index.md#get-ssl-cert).
1. Click **Test Connection**. If the connection is successful, you will see the connection status and information about the DBMS and driver.
1. Click **OK** to save the data source.

### DBeaver {#dbeaver}

1. Create a new DB connection:
    1. In the **Database** menu, select **New connection**.
    1. Select **{{ CH }}** from the DB list.
    1. Click **Next**.
    1. Specify the connection settings on the **Main** tab:
        * **Host**: [FQDN of any {{ CH }}](fqdn.md) host or a [special FQDN](fqdn.md#auto).
        * **Port**: `{{ port-mch-http }}`.
        * **DB/Schema**: Name of the DB to connect to.
        * Under **Authentication**, specify the DB user name and password.
    1. On the **Driver properties** tab:
        1. Click **Download** in the new window where the system prompts you to download the driver files.
        1. Specify the [SSL connection](index.md#get-ssl-cert) parameters in the driver property list:
            * `ssl:true`
            * `sslrootcert:<path_to_saved_SSL_certificate_file>`.
1. Click **Test connection ...**. If the connection is successful, you will see the connection status and information about the DBMS and driver.
1. Click **Done** to save the database connection settings.

{% endlist %}

## Connecting to a cluster from your browser {#browser}

To run SQL queries from your browser, use:


* [{{ CH }}](#inline-editor) built-in SQL editor


* [{{ websql-full-name }}](#websql)




### {{ CH }} built-in SQL editor {#inline-editor}

To connect to a cluster host from the built-in SQL editor, specify the following in the browser address bar:

```text
https://<FQDN_of_any_{{ CH }}_host>:8443/play
```

You can only connect to public cluster hosts. To learn how to get host FQDN, see [this guide](fqdn.md).

To connect to a cluster with [automatic selection of an available host](fqdn.md#auto), use the following URL:

* `https://c-<cluster_ID>.rw.{{ dns-zone }}:8443/play`: To connect to an available cluster host.
* `https://<shard_name>.c-<cluster_ID>.rw.{{ dns-zone }}:8443/play`: To connect to an available [shard](../../concepts/sharding.md) host.

To run database queries, enter your username and password in the top right corner of the page.


When you connect from the built-in editor, SQL queries run separately without creating a shared session with the {{ CH }} server. Therefore, queries running within the session, such as `USE` or `SET`, have no effect.



### {{ websql-full-name }} {#websql}

{% include notitle [preview](../../../_includes/note-preview.md) %}

Use [{{ websql-full-name }}](../../../websql) to connect to a {{ CH }} cluster in {{ yandex-cloud }}.

{% include notitle [connect-to-cluster](../../../_includes/websql/connect-to-cluster.md) %}

{% include notitle [execute-sql](../../../_includes/websql/execute-sql.md) %}


## Before you connect from a Docker container {#docker}

To connect to a {{ mch-name }} cluster from a Docker container, add the following lines to the Dockerfile:

{% list tabs group=connection %}


- Connecting without SSL {#without-ssl}

   ```bash
   # Connecting the DEB repository
   RUN apt-get update && \
       apt-get install wget --yes apt-transport-https ca-certificates dirmngr && \
       apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 8919F6BD2B48D754 && \
       echo "deb https://packages.{{ ch-domain }}/deb stable main" | tee \
       /etc/apt/sources.list.d/clickhouse.list && \
       # Installing dependencies
       apt-get update && \
       apt-get install wget clickhouse-client --yes && \
       # Downloading the configuration file for clickhouse-client
       mkdir --parents ~/.clickhouse-client && \
       wget "https://{{ s3-storage-host-doc-files }}/clickhouse-client.conf.example" \
            --output-document ~/.clickhouse-client/config.xml
   ```


- Connecting with SSL {#with-ssl}

   ```bash
   # Connecting the DEB repository
   RUN apt-get update && \
       apt-get install wget --yes apt-transport-https ca-certificates dirmngr && \
       apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 8919F6BD2B48D754 && \
       echo "deb https://packages.{{ ch-domain }}/deb stable main" | tee \
       /etc/apt/sources.list.d/clickhouse.list && \
       # Installing dependencies
       apt-get update && \
       apt-get install wget clickhouse-client --yes && \
       # Downloading the configuration file for clickhouse-client
       mkdir --parents ~/.clickhouse-client && \
       wget "https://{{ s3-storage-host-doc-files }}/clickhouse-client.conf.example" \
            --output-document ~/.clickhouse-client/config.xml && \
       # Getting SSL certificates
       mkdir --parents {{ crt-local-dir }} && \
       wget "{{ crt-web-path-root }}" \
            --output-document {{ crt-local-dir }}{{ crt-local-file-root }} && \
       wget "{{ crt-web-path-int }}" \
            --output-document {{ crt-local-dir }}{{ crt-local-file-int }} && \
       chmod 655 \
            {{ crt-local-dir }}{{ crt-local-file-root }} \
            {{ crt-local-dir }}{{ crt-local-file-int }} && \
       update-ca-certificates
   ```

{% endlist %}

{% include [clickhouse-disclaimer](../../../_includes/clickhouse-disclaimer.md) %}
