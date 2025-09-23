---
title: '{{ sd-full-name }} pricing policy'
description: This article covers the {{ sd-name }} pricing policy.
editable: false
---

# {{ sd-full-name }} pricing policy



{% include [currency-choice](../_includes/pricing/currency-choice.md) %}

{% include [vat](../_includes/vat.md) %}

The applicable {{ sd-name }} pricing depends on the module you are using:

* [Cloud Infrastructure Entitlement Management ({{ ciem-name }}) module](concepts/ciem.md) is free of charge.
* [For {{ dspm-name }})](concepts/dspm.md), see [below](#dspm-rules).
* For [{{ atr-name }}](concepts/access-transparency.md), see [{{ atr-name }}](#atr-rules) below.
* For [{{ ycdr-full-name }}](concepts/ycdr.md), upon agreement with your account manager.

## What goes into the cost of using {{ sd-name }} {#rules}

### {{ atr-name }} {#atr-rules}

The cost for using {{ atr-name }} is monthly-based and depends on the overall resource consumption over the previous month in the organization for which {{ atr-name }} is enabled. You can estimate the resource consumption for your organization based on the [usage details by service](../billing/operations/check-charges.md#services_1) for each [billing account](../billing/concepts/billing-account.md) associated with your organization.

The price is calculated in the beginning of the calendar month and does not change over this month. The price does not depend on the number of days in the calendar month when {{ atr-name }} is used from the first day of the month.

When {{ atr-name }} is used from the second day of the calendar month or later, only the remaining time until the end of the month is charged, on an hourly basis.

If you cancel your {{ atr-name }} subscription in the [management console]({{ link-console-main }}), charges will stop from the first day of the next calendar month.

### Data Security Posture Management ({{ dspm-name }}) {#dspm-rules}

When performing [data scan](operations/dspm/create-scan.md), {{ dspm-name }} sends requests to files in {{ objstorage-full-name }} buckets. Such requests are charged as per the [{{ objstorage-name }} pricing policy](../storage/pricing.md). Using {{ dspm-name }} is charged separately.

## Prices for the Russia region {#prices}

### {{ atr-name }} {#atr-pricing}



{% include notitle [usd.md](../_pricing/security-deck/access-transparency/usd.md) %}


### Data Security Posture Management ({{ dspm-name }}) {#dspm-pricing}

#### Data scan (by size) {#scan-by-volume}



{% include notitle [usd.md](../_pricing/security-deck/dspm/usd.md) %}


#### Scanning by the number of files {#scan-by-amount}

Image scanning is charged separately.



{% include notitle [usd-scan-by-amount.md](../_pricing/security-deck/dspm/usd-scan-by-amount.md) %}


#### Scanning by the number of images {#scan-by-image-amount}



{% include notitle [usd-scan-by-amount.md](../_pricing/security-deck/dspm/usd-scan-by-image-amount.md) %}


#### Cost calculation example {#amount-example}

Let’s assume you set up scanning a bucket with 50 text files, their overall size being 5 GB. All files were successfully scanned. The total cost per scan is:



{% include [usd-scan-by-amount](../_pricing_examples/security-deck/usd-scan-by-amount.md) %}

