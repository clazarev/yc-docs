# External tables

{{ GP }} allows you to work with data from sources that are external to a {{ mgp-name }} cluster. This functionality uses _external tables_, which are special objects in a {{ GP }} database that reference external source tables, buckets, or files. External DBMS data is accessed via the [{{ GP }} Platform Extension Framework](../operations/external-tables.md) (PXF) protocol; files on external file servers are accessed via the [{{ GP }} Parallel File Server](../operations/gpfdist/connect.md) (`gpfdist`) utility.

With external tables, you can:

* Query external data sources.
* Load datasets from external sources into a {{ GP }} database.
* Join local and external tables in queries.
* Write data to external tables or files.

{% note info %}

For security reasons, {{ mgp-name }} does not support the creation of external web tables that use shell scripts.

{% endnote %}

## External data sources for PXF operations {#pxf-data-sources}

{{ mgp-name }} uses _external data sources_ to create external tables. Each source is similar to a web server configuration used to access data in external DBMS's. This is why sources are only used for PXF operations.

Sources enable you to do the following:

* In the configuration, specify the parameters that cannot be included in an [SQL query for creating a PXF external table](../operations/pxf/create-table.md).
* Avoid explicitly specifying the user password in an SQL query for creating an external table.
* Simplify your SQL query for creating a table: with a dedicated source properly configured, there is no need to list configuration parameters in your query.
* Simplify your configuration update: it is enough to redefine the parameters at the source only once without changing them for each table separately.


## Use cases {#examples}

* [{#T}](../tutorials/config-server-for-s3.md)
* [{#T}](../tutorials/pxf-named-queries.md)

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}
