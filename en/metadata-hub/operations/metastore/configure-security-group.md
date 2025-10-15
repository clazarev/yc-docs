---
title: Configuring {{ metastore-full-name }} cluster security groups
description: Follow this guide to configure security groups for a {{ metastore-name }} cluster.
---

# Configuring {{ metastore-name }} cluster security groups

If the cloud network uses security groups, they can hinder the {{ metastore-name }} cluster performance. Set up the default security group to work with {{ metastore-name }}. To do this, [add](../../../vpc/operations/security-group-add-rule.md) the following rules to it:

* For incoming client traffic:

    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `30000-32767`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` (`Any`)
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**: `0.0.0.0/0`

* For incoming load balancer traffic:

    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `10256`
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` (`Any`)
    * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-sg-type-balancer }}`

If you plan to use multiple security groups for a cluster, enable all traffic between these groups.

{% note info %}

You can specify more granular rules for your security groups, such as only allowing traffic within specific subnets.

{% endnote %}

{% include [metastore-trademark](../../../_includes/metadata-hub/metastore-trademark.md) %}
