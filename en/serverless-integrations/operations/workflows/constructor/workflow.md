---
title: Adding a synchronous execution of a {{ sw-name }} workflow to another {{ sw-name }} workflow
description: Follow this guide to add a synchronous execution of a {{ sw-full-name }} workflow to another {{ sw-name }} workflow using the workflow step constructor.
---

# Synchronous execution of another {{ sw-full-name }} workflow

{% list tabs %}

- Management console {#console}

  1. {% include [integrations-step1](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step1.md) %} 
  1. From the **{{ ui-key.yacloud.serverless-workflows.workflow-editor_integration-group-label }}** section on the left side of the screen, drag the ![graph-node](../../../../_assets/console-icons/graph-node.svg) **{{ sw-name }}** element to the relevant workflow location in the constructor window.

      {% include [step-drag-destination-desc](../../../../_includes/serverless-integrations/workflows-constructor/step-drag-destination-desc.md) %}
  1. In the workflow schema window, click the new **{{ sw-name }}** section to select it.
  1. {% include [integrations-step-select-settings-tab](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-select-settings-tab.md) %}

      1. {% include [integrations-name-the-step](../../../../_includes/serverless-integrations/workflows-constructor/integrations-name-the-step.md) %}
      1. In the **{{ ui-key.yc-serverless-workflows.dynamic-forms.workflow_properties_serverless_workflow_view_spec_layout_title }}** field, select another {{ sw-full-name }} workflow you want to execute in sync with the current one.
      1. In the **{{ ui-key.yc-serverless-workflows.dynamic-forms.workflow_properties_workflow_input_view_spec_layout_title }}** field, specify the input data for the workflow execution.
      1. {% include [integrations-step-timeout](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-timeout.md) %}
      1. {% include [integrations-step-retry-policy](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-retry-policy.md) %}
  1. {% include [integrations-step-input-tab](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-input-tab.md) %}
  1. {% include [integrations-step-output-tab](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-output-tab.md) %}
  1. {% include [integrations-step-add-catch-rule](../../../../_includes/serverless-integrations/workflows-constructor/integrations-step-add-catch-rule.md) %}

{% endlist %}

## See also {#see-also}

* [YaWL specification](../../../concepts/workflows/yawl/integration/workflow.md)
* [{#T}](../workflow/create-constructor.md)
* [{#T}](../workflow/update.md)
