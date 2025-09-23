| Service | Price of 1 GB per month, without VAT |
| ----- | ----- |
| Data storage in storage groups of SSD drives | {{ sku|USD|ydb.cluster.v1.ssd|month|string }} |
| Storage of on-demand backups in {{ objstorage-full-name }} | {{ sku|USD|ydb.db.backup.v1|month|string }} |
| YDB. Temporary storage (spilling) | $0.10719 |


{% note info "Minimum group size" %}

A single [storage group](../../ydb/concepts/resources.md#storage-groups) can store up to 100 GB of user data. The minimum granularity of space allocation for a DB is one storage group.

{% endnote %}

To create on-demand backups in {{ ydb-name }}, specify a bucket in {{ objstorage-name }}.
