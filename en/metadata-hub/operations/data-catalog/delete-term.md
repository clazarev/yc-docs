# Deleting a term


{% note info %}

{% include [preview-pp](../../../_includes/preview-pp.md) %}

{% endnote %}


Before you delete a term, [delete](delete-term-child.md) all its child terms.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [resource folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) you created the metadata catalog in.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}**.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/folder-magnifier.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_data-catalog }}**.
  1. In the list that opens, select the metadata catalog you want to delete a term in.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/book.svg) **{{ ui-key.yacloud.data-catalog.label_terms-and-glossaries }}** and select the glossary containing the term.
  1. In the term list, click ![image](../../../_assets/console-icons/ellipsis.svg) next to the term and select **{{ ui-key.yacloud.common.delete }}**.

      {% include [search-tip](../../../_includes/metadata-hub/tip-search-term.md) %}

  1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

{% endlist %}
