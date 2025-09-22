---
title: Правила тарификации для {{ metadata-hub-full-name }}
description: В статье содержатся правила тарификации сервиса {{ metadata-hub-name }}.
editable: false
---

# Правила тарификации для {{ metadata-hub-full-name }}

Использование {{ connection-manager-name }}, {{ schema-registry-name }} и {{ data-catalog-name }} не тарифицируется. За потребление других ресурсов {{ yandex-cloud }}, которые вы используете в процессе работы с этими сервисами (например, сервисов управляемых баз данных или [{{ lockbox-name }}](../lockbox/pricing.md)), взимается плата в соответствии с их [тарифами]({{ link-cloud-calculator }}).

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}


{% include [vat](../_includes/vat.md) %}

## Тарификация кластеров {{ metastore-full-name }} {#metastore}

{% note info %}

Приведенные правила тарификации действуют с 22 сентября 2025 года.

{% endnote %}

При работе с {{ metastore-full-name }} вы оплачиваете вычислительные ресурсы кластера. Стоимость начисляется за каждый час работы кластера. Минимальная единица тарификации — час (например, стоимость 1,5 часа работы кластера равна стоимости 2 часов).

Остановленный кластер (статус `Stopped`) не тарифицируется.

{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}


### Цены для региона Россия {#metastore-prices}



{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

{% include [pricing-month-term](../_includes/mdb/pricing-month-term.md) %}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-prices](../_pricing/metastore/rub.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-prices](../_pricing/metastore/kzt.md) %}

{% endlist %}




{% include [metastore-trademark](../_includes/metadata-hub/metastore-trademark.md) %}
