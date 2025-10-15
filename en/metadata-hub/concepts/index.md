---
title: '{{ metadata-hub-full-name }} concepts'
description: In this tutorial, you will learn about connection and schema registry.
---

# About {{ metadata-hub-full-name }}

{{ metadata-hub-full-name }} is a service that provides data management capabilities in {{ yandex-cloud }}:

* Automatic creation and management of database connection parameters.
* Storing, getting schemas, and checking the evolution of data exchange schemas.
* Creating and managing {{ metastore-full-name }} clusters.
* Search and visualization of meta information about data storages and links between them.

## Connection management {#connection-manager}


{% include [connection](../../_includes/metadata-hub/connection-definition.md) %}


## Table metadata management {#metastore}

{% include [connection](../../_includes/metadata-hub/metastore-definition.md) %}


### Use cases {#examples-metastore}

* [{#T}](../tutorials/metastore-import.md)
* [{#T}](../tutorials/sharing-tables.md)

## Data schema registry {#schema-registry}


{% include [preview-pp](../../_includes/preview-pp.md) %}


{% include [connection](../../_includes/metadata-hub/schema-registry-definition.md) %}

With a schema registry, you can define schemas for your data formats and versions and register them in the registry. After registering a schema, you can use it jointly in various systems and applications. When a supplier sends data to a message recipient, the data schema is included in the message title, and the schema registry ensures that the schema is valid and compatible with the expected one for the subject.

### Use cases {#examples-schema-registry}

* [{#T}](../tutorials/schema-registry-cdc-debezium-kafka.md)


## Metadata collection and markup {#data-catalog}


{% include [preview-pp](../../_includes/preview-pp.md) %}


[{{ data-catalog-name }}](data-catalog.md) allows you to collect, analyze, and mark up metadata drawn from various sources. You can upload structural metadata, e.g., list of tables in a managed database cluster, their schemas, links between tables.

You can use {{ data-catalog-name }} to:

* Collect, store, and organize metadata.
* Find a dashboard with relevant business indicators.
* Analyze and interpret business indicators.
* Find data for your business needs.
* Find information sources behind a particular object.
* Find data owners, including passive ownership through subscription.
* Build a schema for data consumer.


{% include [metastore-trademark](../../_includes/metadata-hub/metastore-trademark.md) %}
