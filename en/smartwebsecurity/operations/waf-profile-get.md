---
title: How to get information about a WAF profile in {{ sws-full-name }}
description: Follow this guide to get information about a WAF profile in {{ sws-full-name }}.
---

# Getting information about a WAF profile

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) containing the [WAF profile](../concepts/waf.md).
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_smartwebsecurity }}**.
  1. In the left-hand panel, select ![image](../../_assets/smartwebsecurity/waf.svg) **{{ ui-key.yacloud.smart-web-security.waf.label_profiles }}**.
  1. Select the profile in question.
  1. The **{{ ui-key.yacloud.common.overview }}** page will show the profile details:
     * Basic profile parameters.
     * Security profiles the WAF profile is attached to.
     * Rule sets included in the profile, number of active rules in each set, as well as anomaly threshold and paranoia level for the OWASP set.

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To get information about a {{ sws-full-name }} [WAF profile](../concepts/waf.md) created with {{ TF }}:

  1. Add the `data` and `output` sections to the {{ TF }} configuration file:

     ```hcl
     data "yandex_sws_waf_profile" "default" {
       name = "<WAF_profile_name>"
     }

     output "profile-created" {
       value = data.yandex_sws_waf_profile.default.created_at
     }
     ```

     Where:
     * `data "yandex_sws_waf_profile"`: Description of the WAF profile as a data source:
       * `name`: WAF profile name.
     * `output "profile-created"`: Output variable that contains information about the WAF profile creation timestamp:
       * `value`: Return value.

     You can replace `created_at` with any other parameter to get the information you need. For more information about the `yandex_sws_waf_profile` data source properties, see [this {{ TF }} provider article]({{ tf-provider-datasources-link }}/sws_waf_profile).

  1. Create the resources:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     {{ TF }} will create the required resources and display their output variables. To check the results, run this command:

     ```bash
     terraform output
     ```

     Result:

     ```text
     profile-created = "2024-12-13T17:45:34Z"
     ```

- API {#api}

  To get information about a {{ sws-full-name }} [WAF profile](../concepts/waf.md), use the [get](../waf/api-ref/WafProfile/get.md) REST API method for the [WafProfile](../waf/api-ref/WafProfile/index.md) resource or the [WafProfile/Get](../waf/api-ref/grpc/WafProfile/get.md) gRPC API call.

{% endlist %}