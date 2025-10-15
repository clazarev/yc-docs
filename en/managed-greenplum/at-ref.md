---
title: '{{ mgp-full-name }} event reference in {{ at-full-name }}'
description: This page provides a reference for {{ mgp-name }} events tracked in {{ at-name }}.
---

# {{ at-full-name }} event reference

{{ at-name }} supports tracking control plane events for {{ mgp-full-name }}. For more information, see [{#T}](../audit-trails/concepts/format.md).

The general format of the `event_type` field value is as follows:

```text
{{ at-event-prefix }}.audit.mdb.greenplum.<event_name>
```

{% include [mgp-events](../_includes/audit-trails/events/managed-greenplum-events.md) %}

{% include [greenplum-trademark](../_includes/mdb/mgp/trademark.md) %}
