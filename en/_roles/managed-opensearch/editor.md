The `managed-opensearch.editor` role allows you to manage OpenSearch clusters and view their logs, as well as get information on quotas and resource operations.

Users with this role can:
* View information on OpenSearch [clusters](../../managed-opensearch/concepts/index.md), as well as create, modify, delete, run, and stop them.
* Restore OpenSearch clusters from backups.
* View OpenSearch cluster logs.
* View information on [quotas](../../managed-opensearch/concepts/limits.md#quotas) of Managed Service for OpenSearch.
* View information on resource operations for Managed Service for OpenSearch.

This role includes the `managed-opensearch.viewer` and `managed-opensearch.restorer` permissions.

To create OpenSearch clusters, you also need the `vpc.user` role.