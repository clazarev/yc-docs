# Setting up Delta Lake in multi-cluster mode

When in multi-cluster mode, {{ dataproc-name }} uses a [{{ ydb-full-name }}](../../../ydb/index.yaml) database to manage access to Delta Lake tables from different clusters and {{ SPRK }} jobs.

For more information about Delta Lake, see the [Delta Lake in {{ dataproc-name }}](../../concepts/deltalake.md) section and the [Delta Lake documentation](https://docs.delta.io/latest/index.html).


{% include [deltalake-disclaimer](../../../_includes/data-processing/deltalake-disclaimer.md) %}


## Set up your infrastructure {#prereq}

1. [Create a serverless {{ ydb-name }} database](../../../ydb/operations/manage-databases.md#create-db-serverless).
1. [Create a service account](../../../iam/operations/sa/create.md) with the `ydb.editor` role for access to {{ ydb-short-name }}.
1. [Create a static access key](../../../iam/operations/authentication/manage-access-keys.md#create-access-key) for the service account.
1. [Create a {{ lockbox-full-name }} secret](../../../lockbox/operations/secret-create.md) and place into it the static key data as two `key-value` pairs:

    * Key: `key-id`; value: `<static_key_ID>`.
    * Key: `key-secret`; value: `<static_key_secret_part>`.

1. Set up one or more {{ dataproc-name }} clusters to work with Delta Lake:

    1. If you do not have a {{ dataproc-name }} cluster, [create one](../cluster-create.md).
    1. If you attached a [{{ objstorage-full-name }} bucket](../../../storage/concepts/bucket.md) to your cluster:

        1. Create a folder named `warehouse` in the bucket.
        1. [Set](../../concepts/settings-list.md#change-properties) `spark.sql.warehouse.dir` to `s3a://<bucket_name>/warehouse/`.

    1. [Create a {{ metastore-full-name }} cluster](../../../metadata-hub/operations/metastore/cluster-create.md) and [connect](../../../metadata-hub/operations/metastore/data-processing-connect.md) it to your {{ dataproc-name }} cluster.

1. Assign the `lockbox.payloadViewer` role to the service account you used to create the {{ dataproc-name }} clusters. You can do this:

    * [Only for the secret you created earlier](../../../lockbox/operations/secret-access.md)
    * [At the entire folder level](../../../iam/operations/sa/assign-role-for-sa.md)

## Set up the component properties to work with Delta Lake {#settings}

1. Download the archive with the required Delta Lake libraries and add-ons to connect to {{ ydb-name }}:

    * [Delta Lake 2.0.2](https://github.com/yandex-cloud/yc-delta/releases/download/v1.1/yc-delta20-multi-dp21-1.1-fatjar.jar) for {{ dataproc-name }} 2.1.0 or 2.1.3
    * [Delta Lake 2.3.0](https://github.com/yandex-cloud/yc-delta/releases/download/v1.1/yc-delta23-multi-dp21-1.1-fatjar.jar) for {{ dataproc-name }} 2.1.4 and higher

    You can check out the source code for add-ons to connect to {{ ydb-short-name }} in the repository:

    * [Add-ons for Delta Lake 2.0.2](https://github.com/yandex-cloud/yc-delta/blob/develop/yc-delta20)
    * [Add-ons for Delta Lake 2.3.0](https://github.com/yandex-cloud/yc-delta/blob/develop/yc-delta23)

1. Add the downloaded archive to the dependencies of all clusters or individual jobs that need access to Delta Lake tables. There are two ways to do this:

    * Save the archive to the {{ objstorage-name }} bucket and provide the file URL in the `spark.jars` property:

        `spark.jars=s3a://<bucket_name>/<file_path>`

        Make sure the cluster service account has read access to the bucket.

    * Copy the archive to all cluster nodes manually or using [initialization scripts](../../concepts/init-action.md) and provide the full file path in the `spark.driver.extraClassPath` and `spark.executor.extraClassPath` properties.

1. Set the following [properties](../../concepts/settings-list.md) at the level of clusters or individual {{ SPRK }} jobs that need access to Delta Lake tables:

    * Set `spark.sql.extensions` to `io.delta.sql.DeltaSparkSessionExtension`.
    * Set `spark.sql.catalog.spark_catalog` to `org.apache.spark.sql.delta.catalog.DeltaCatalog`.
    * Set `spark.delta.logStore.s3a.impl` to `ru.yandex.cloud.custom.delta.YcS3YdbLogStore`.
    * Set `spark.io.delta.storage.S3DynamoDBLogStore.ddb.endpoint` to the Document API endpoint value available on the **{{ ui-key.yacloud.ydb.database.switch_overview}}** tab of your database in the [management console]({{ link-console-cloud }}).
    * Set `spark.io.delta.storage.S3DynamoDBLogStore.ddb.lockbox` to the {{ lockbox-short-name }} secret ID value available on the **{{ ui-key.yacloud.lockbox.label_section-overview}}** tab of your {{ lockbox-short-name }} in the [management console]({{ link-console-cloud }}).

You can now use Delta Lake in multi-cluster mode.

## Delta Lake use case {#example}

This use case was tested on a {{ dataproc-name }} cluster of version 2.1.7.

1. [Use SSH to connect](../connect-ssh.md) to the {{ dataproc-name }} cluster's master host.

1. Run an {{ SPRK }} session in the cluster by providing the required parameters:

    ```bash
    spark-sql  \
        --conf spark.jars=s3a://<bucket_name>/yc-delta23-multi-dp21-1.1-fatjar.jar \
        --conf spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension \
        --conf spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.YcDeltaCatalog \
        --conf spark.delta.logStore.s3a.impl=ru.yandex.cloud.custom.delta.YcS3YdbLogStore \
        --conf spark.io.delta.storage.S3DynamoDBLogStore.ddb.endpoint=<Document_API_endpoint> \
        --conf spark.io.delta.storage.S3DynamoDBLogStore.ddb.lockbox=<secret_ID>
    ```

1. In your active session, create a database and switch to it:

    ```sql
    CREATE DATABASE testdelta;
    USE testdelta;
    ```

1. Create a test table and populate it with data:

    ```sql
    CREATE TABLE tab1(a INTEGER NOT NULL, b VARCHAR(100)) USING DELTA;
    INSERT INTO tab1 VALUES (1,'One'), (2,'Two'), (3,'Three');
    ```

1. Replace the `b` column values by adding to them the `a` column values converted to a string:

    ```sql
    UPDATE tab1 SET b=b || ' ** ' || CAST(a AS VARCHAR(10));
    ```

1. Check the result:

    ```sql
    SELECT * FROM tab1;
    ```

    ```sql
    3	Three ** 3
    2	Two ** 2
    1	One ** 1
    ```

## Additional multi-cluster mode settings for production clusters in {{ dataproc-name }} {#prod-configs}

To improve Delta Lake performance and streamline data storage when using multi-cluster mode, configure additional {{ ydb-short-name }} settings.

### Setting up {{ ydb-name }} throughput {#throughput}

By default, a {{ ydb-short-name }} in serverless mode is created with a [throughput](../../../ydb/concepts/serverless-and-dedicated.md#capacity) of 10 request units per second. This may be insufficient for intensive use of Delta Lake tables.

To avoid Delta Lake performance degradation due to insufficient {{ ydb-short-name }} throughput, track the **Document API units overflow** parameter on the chart when [monitoring {{ ydb-short-name }}](../../../ydb/operations/monitoring.md). Increase the throughput limit if needed.

There is a total throughput limit that applies to all {{ ydb-short-name }} databases in the cloud, which depends on the quota. If necessary, contact [support]({{ link-console-support }}) to have your quota increased.

### Setting up auto cleanup {#auto-vacuum}

When working with Delta Lake, metadata versions that are not in use may accumulate in a {{ ydb-short-name }} table or {{ objstorage-name }} buckets. You can streamline storage use and boost Delta Lake performance using a [ready-made script](https://github.com/yandex-cloud/yc-delta/blob/develop/cf-cleanup/cfunc.py) that will automatically clean up outdated metadata from the {{ ydb-short-name }} table and bucket on a regular basis.

The script is installed in the cloud as two [serverless functions](../../../functions/concepts/index.md):

* Function for cleaning up data in the {{ ydb-short-name }} table. It is invoked automatically once an hour.
* Function for cleaning up data in the buckets. It is invoked automatically once a day.

To add these cleanup functions to your cloud:

1. {% include [cli-install](../../../_includes/cli-install.md) %}
1. Download files from the [cf-cleanup](https://github.com/yandex-cloud/yc-delta/tree/develop/cf-cleanup) folder:

    * `cfunc.py`: Cleanup script source code.
    * `delta-prefixes.txt`: File with prefixes of paths to temporary Delta Lake files in buckets.
    * `pack.sh`: ZIP archive creation script.
    * `requirements.txt`: File with environment requirements to install functions.

    Save these files to the `cf-cleanup` folder in the working directory.

1. Make the `pack.sh` file executable:

    ```bash
    chmod +x ./cf-cleanup/pack.sh
    ```

1. In the `delta-prefixes.txt` file, specify the paths to the {{ objstorage-name }} bucket folders with temporary Delta Lake files. Make sure to provide each path in a new line in the following format:

    ```txt
    BucketName Mode PathPrefix
    ```

    The `Mode` field can take the following values:

    * `W`: Warehouse, path for storing multiple databases.
    * `D`: Database, path for storing a single database.
    * `T`: Table, path for storing a specific single table.

    Example:

    ```txt
    mybucket1 W warehouse/
    mybucket2 D warehouse/testdelta2.db/
    mybucket3 T warehouse/testdelta3.db/tab1/
    ```

1. Place the `delta-prefixes.txt` file to your {{ objstorage-name }} bucket.
1. Download and save the files for managing the cleanup functions to the working directory:

    * [ddb-maint-config.sh](https://github.com/yandex-cloud/yc-delta/blob/develop/ddb-maint-config.sh): Setup parameters
    * [ddb-maint-setup.sh](https://github.com/yandex-cloud/yc-delta/blob/develop/ddb-maint-setup.sh): Setup script
    * [ddb-maint-remove.sh](https://github.com/yandex-cloud/yc-delta/blob/develop/ddb-maint-remove.sh): Removal script

1. In the `ddb-maint-config.sh` file, specify the following parameters:

    * `sa_name`: Name of the service account that will be created to use the functions.
    * `cf_ddb_name`: Name of the serverless database cleanup function; it must be unique within the folder.
    * `cf_s3_name`: Name of the serverless bucket cleanup function; it must be unique within the folder.
    * `docapi_endpoint`: Document API endpoint. You can find it on the **{{ ui-key.yacloud.ydb.database.switch_overview}}** tab of your {{ ydb-short-name }} database in the [management console]({{ link-console-cloud }}).
    * `docapi_table`: Name of the Delta Lake table to clean.
    * `s3_prefix_file`: Path to the `delta-prefixes.txt` file in the {{ objstorage-name }} bucket, e.g., `s3://<bucket_name>/delta-prefixes.txt`.

1. Run the setup script in your local directory:

    ```bash
    bash ./ddb-maint-setup.sh
    ```

This will add to the cloud the functions for cleaning up temporary files in {{ ydb-short-name }} tables and {{ objstorage-name }} buckets. You can check the new functions using the [management console]({{ link-console-cloud }}).

If you no longer need the cleanup functions, run this script to remove them:

```bash
bash ./ddb-maint-remove.sh
```

The `spark.io.delta.storage.S3DynamoDBLogStore.ddb.ttl` Spark property sets the TTL for metadata records, which is `86400` seconds (24 hours) by default. The actual TTL for a specific record may be longer, as it depends on when the cleanup function was run.
