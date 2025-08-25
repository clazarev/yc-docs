

You can save data from a [{{ dataproc-full-name }} cluster](../../../data-proc/concepts/index.md) to a [{{ objstorage-full-name }} bucket](../../../storage/concepts/bucket.md) using a separate [{{ metastore-full-name }} cluster](../../../metadata-hub/concepts/metastore.md) to store table metadata. Thus you can access the saved data from a different {{ dataproc-name }} cluster that has access to the bucket and is connected to the same {{ metastore-name }} cluster.

To set up shared use of tables by two {{ dataproc-name }} clusters through {{ metastore-name }}:

1. [Connect {{ dataproc-name }} to {{ metastore-name }}](#connect).
1. [Create a test table](#create-table).
1. [Get data in the second cluster](#test-target).

If you no longer need the resources you created, [delete them](#clear-out).

If a {{ dataproc-name }} cluster contains tables that should be available in another {{ dataproc-name }} cluster, [transfer the tables](../../../data-proc/tutorials/metastore-import.md) to the appropriate cluster using {{ metastore-name }}.

{% note warning %}

{% include [connect-metastore-to-s3-with-policy](../../_tutorials_includes/metastore-to-s3-with-policy.md) %}

{% endnote %}

## Required paid resources {#paid-resources}

The infrastructure support cost includes:

* Fee for the {{ dataproc-name }} cluster computing resources and storage (see [{{ dataproc-name }} pricing](../../../data-proc/pricing.md)).
* Fee for data storage and operations in a bucket (see [{{ objstorage-full-name }} pricing](../../../storage/pricing.md)).
* Fee for NAT gateway usage and outbound traffic (see [{{ vpc-full-name }} pricing](../../../vpc/pricing.md#nat-gateways)).

## Getting started {#before-you-begin}

Set up your infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a service account](../../../iam/operations/sa/create.md) named `dataproc-s3-sa` and assign the `dataproc.agent` and `dataproc.provisioner` roles to it.
    1. {% include [basic-before-buckets](../../../_includes/data-processing/tutorials/basic-before-buckets.md) %}
    1. [Create a cloud network](../../../vpc/operations/network-create.md) named `dataproc-network`.
    1. In `dataproc-network`, [create a subnet](../../../vpc/operations/subnet-create.md) in any availability zone.
    1. [Set up a NAT gateway](../../../vpc/operations/create-nat-gateway.md) for the subnet you created.
    1. [Create two {{ dataproc-name }} clusters](../../../data-proc/operations/cluster-create.md) named `dataproc-source` and `dataproc-target` in any [suitable host configuration](../../../data-proc/concepts/instance-types.md) with the following settings:

        * **{{ ui-key.yacloud.mdb.forms.base_field_environment }}**: `PRODUCTION`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_services }}**:
            * `SPARK`
            * `YARN`
        * **{{ ui-key.yacloud.mdb.forms.base_field_service-account }}**: `dataproc-sa`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_properties }}**: `spark:spark.sql.hive.metastore.sharedPrefixes` with the `com.amazonaws,ru.yandex.cloud` value. It is required for PySpark jobs and integration with {{ metastore-name }}.
        * **{{ ui-key.yacloud.mdb.forms.config_field_bucket }}**: Bucket you created for output data.
        * **{{ ui-key.yacloud.mdb.forms.config_field_network }}**: `dataproc-network`.

    1. If the cloud network uses [security groups](../../../vpc/concepts/security-groups.md), [add](../../../vpc/operations/security-group-add-rule.md) the following rule for outgoing traffic to the {{ dataproc-name }} cluster security group:

        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `{{ port-metastore }}`
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` (`Any`)
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**: `0.0.0.0/0`

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [dataproc-to-dataproc.tf](https://github.com/yandex-cloud-examples/yc-data-proc-to-data-proc/blob/main/dataproc-to-dataproc.tf) configuration file to the same working directory.

        This file describes:

        * [Network](../../../vpc/concepts/network.md#network).
        * [Subnet](../../../vpc/concepts/network.md#subnet).
        * [NAT gateway](../../../vpc/concepts/gateways.md) and route table required for {{ dataproc-name }}.
        * [Security groups](../../../vpc/concepts/security-groups.md) for the {{ dataproc-name }} clusters.
        * Service account for the {{ dataproc-name }} cluster.
        * Service account to create buckets in {{ objstorage-name }}.
        * Buckets for input and output data.
        * Two {{ dataproc-name }} clusters.

    1. Specify the following in the `dataproc-to-dataproc.tf` file:

        * `folder_id`: Cloud folder ID, same as in the provider settings.
        * `input-bucket`: Name of the input data bucket.
        * `output-bucket`: Name of the output data bucket.
        * `dp_ssh_key`: Absolute path to the public key for the {{ dataproc-name }} clusters. For more information, see [{#T}](../../../data-proc/operations/connect.md#data-proc-ssh).

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        {{ TF }} will show any errors found in your configuration files.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Connect {{ dataproc-name }} to {{ metastore-name }} {#connect}

1. [Create a {{ metastore-name }} cluster](../../../metadata-hub/operations/metastore/cluster-create.md) in `dataproc-network`.

1. [Add](../../../data-proc/operations/cluster-update.md) the `spark:spark.hive.metastore.uris` property with the `thrift://<{{ metastore-name }}_cluster_IP_address>:{{ port-metastore }}` value to the {{ dataproc-name }} cluster settings.

   To find out the {{ metastore-name }} cluster IP address, select **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}** in the [management console]({{ link-console-main }}) and then select the ![image](../../../_assets/console-icons/database.svg) **{{ ui-key.yacloud.metastore.label_metastore }}** page in the left-hand panel. Copy the **{{ ui-key.yacloud.metastore.field_metastore-endpoint-ip }}** column value for the cluster in question.

## Create a test table {#create-table}

In the `dataproc-source` cluster, create a test table named `countries` and upload it to {{ objstorage-name }}:

{% list tabs %}

- Spark

    1. Prepare a script file:

        1. Create a local file named `create-table.py` and paste the following script to it:

            {% cut "create-table.py" %}

            ```python
            from pyspark.sql.types import *
            from pyspark.sql import SparkSession

            # Creating a Spark session
            spark = SparkSession.builder \
                .appName("create-table") \
                .enableHiveSupport() \
                .getOrCreate()

            # Creating a data schema
            schema = StructType([StructField('Name', StringType(), True),
            StructField('Capital', StringType(), True),
            StructField('Area', IntegerType(), True),
            StructField('Population', IntegerType(), True)])

            # Creating a dataframe
            df = spark.createDataFrame([('Australia', 'Canberra', 7686850, 19731984), ('Austria', 'Vienna', 83855, 7700000)], schema)

            # Writing the dataframe to a bucket as a countries table
            df.write.mode("overwrite").option("path","s3a://<output_bucket_name>/countries").saveAsTable("countries")
            ```

            {% endcut %}

        1. In the script, specify the name of the output bucket where the file with the `countries` table will be saved.

        1. In the input bucket, create a folder named `scripts` and [upload](../../../storage/operations/objects/upload.md#simple) the `create-table.py` file to it.

    1. [Create a PySpark job](../../../data-proc/operations/jobs-pyspark.md#create) by specifying the path to the script file in the **{{ ui-key.yacloud.dataproc.jobs.field_main-python-file }}** field: `s3a://<input_bucket_name>/scripts/create-table.py`.

    1. Wait for the job to complete and make sure the output bucket's `countries` folder contains the `part-00000-...` file.

{% endlist %}

The data from the created table is now stored in the {{ objstorage-name }} bucket and the table metadata is stored in the {{ metastore-name }} cluster. Now you can [delete](../../../data-proc/operations/cluster-delete.md) the `dataproc-source` cluster.

## Get data in the second cluster {#test-target}

Upload the `countries` table metadata to the `dataproc-target` cluster and make sure the table is accessible in the cluster for further operations:

{% list tabs %}

- Spark

    1. Prepare a script file:

        1. Create a local file named `obtain-table.py` and paste the following script to it:

            {% cut "obtain-table.py" %}

            ```python
            from pyspark.sql import SparkSession

            # Creating a Spark session
            spark = SparkSession.builder \
                .appName("obtain-table") \
                .enableHiveSupport() \
                .getOrCreate()

            spark.catalog.listDatabases()

            # Getting the countries table metadata from Metastore
            df = spark.sql("describe extended countries")

            # Requesting data from the countries table
            df = spark.sql("select * from countries")

            # Testing table-to-bucket transfer
            df.repartition(1).write.csv(path='s3a://<output_bucket_name>/csv', header=True, sep=',')
            ```

            {% endcut %}

        1. In the script, specify the name of the output bucket where the CSV file with the `countries` table will be saved.
        1. [Upload](../../../storage/operations/objects/upload.md#simple) the `obtain-table.py` file to the input data bucket’s `scripts` folder.

    1. [Create a PySpark job](../../../data-proc/operations/jobs-pyspark.md#create) by specifying the path to the script file in the **{{ ui-key.yacloud.dataproc.jobs.field_main-python-file }}** field: `s3a://<input_bucket_name>/scripts/obtain-table.py`.
    1. Wait for the job to complete and make sure the output bucket contains the `csv` folder with a table in CSV format.

{% endlist %}

## Delete the resources you created {#clear-out}

Some resources are not free of charge. Delete the resources you no longer need to avoid paying for them:

1. [Delete the {{ metastore-name }} cluster](../../../metadata-hub/operations/metastore/cluster-delete.md).
1. [Delete the objects](../../../storage/operations/objects/delete.md) from the buckets.
1. Delete other resources depending on how they were created:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. [{{ dataproc-name }} clusters](../../../data-proc/operations/cluster-delete.md).
        1. [{{ objstorage-name }} buckets](../../../storage/operations/buckets/delete.md).
        1. [Subnet](../../../vpc/operations/subnet-delete.md).
        1. [Route table](../../../vpc/operations/delete-route-table.md).
        1. [NAT gateway](../../../vpc/operations/delete-nat-gateway.md).
        1. [Cloud network](../../../vpc/operations/network-delete.md).
        1. [Service account](../../../iam/operations/sa/delete.md).

    - {{ TF }} {#tf}

        {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

    {% endlist %}
