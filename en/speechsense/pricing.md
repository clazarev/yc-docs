---
title: '{{ speechsense-full-name }} pricing policy'
description: This article covers the {{ speechsense-name }} pricing policy.
editable: false
---

# {{ speechsense-full-name }} pricing policy



{% note tip %}




To calculate the cost of analytics for [text](https://yandex.cloud/en/prices?state=5d33b1fb1362#calculator) and [audio](https://yandex.cloud/en/prices?state=b22c4c9e4f90#calculator) dialogues, use the calculator on the {{ yandex-cloud }} website or see the pricing data in this section.


{% endnote %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}


{% include [currency-choice](../_includes/pricing/currency-choice.md) %}


{% include [vat](../_includes/vat.md) %}

## What goes into the cost of using {{ speechsense-name }} {#rules}

When analyzing audio recordings of dialogs, the cost of using {{ speechsense-name }} depends on the duration of two-channel audio files. Single-channel audio is converted and charged as two-channel. The billing unit is a one-second segment of two-channel audio.

When analyzing text dialogs, the cost of using {{ speechsense-name }} depends on the number of characters in the dialog. Billing unit: 10 characters. The number of characters in each dialog is rounded up to the tens. The count includes all the dialog characters.

Automatic recalculation of modified and newly created dictionary and semantic [tags](../speechsense/concepts/tags.md) is not charged.

## Prices for the Russia region {#prices}

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

### Audio dialog analysis {#speech}



{% include [usd.md](../_pricing/speechsense/usd-speechsense.md) %}


### Text dialog analysis {#text}



{% include [usd.md](../_pricing/speechsense/usd-text-dialogs.md) %}


### Cost of model usage in the assistant {#price-model-assistant}



{% include [usd-model.md](../_pricing/speechsense/usd-model-assistant.md) %}


### Example of calculating the cost of audio analysis {#price-example-speech}



{% include [usd-speechsense](../_pricing_examples/speechsense/usd-speechsense.md) %}


### Example of calculating the cost of text dialog analysis {#price-example-text}

#### Example 1 {#example-1}

Let's assume that 90,000,015 characters were analyzed during one month of using {{ speechsense-name }}. The number of characters in each dialog is rounded up to the tens. This means, the billable amount is 90,000,020 characters, or 90,000.02 thousand characters.



{% include [usd-speechsense-text-100k](../_pricing_examples/speechsense/usd-speechsense-text-100k.md) %}


#### Example 2 {#example-2}

Let's assume that 150,000,023 characters were analyzed during one month of using {{ speechsense-name }}. The number of characters in each dialog is rounded up to the tens. This means, the billable amount is 150,000,030 characters, or 150,000.03 thousand characters.



{% include [usd-speechsense-text-500k](../_pricing_examples/speechsense/usd-speechsense-text-500k.md) %}


#### Example 3 {#example-3}

Let's assume that 685,000,000 characters, or 685,000 thousand characters, were analyzed during one month of using {{ speechsense-name }}.



{% include [usd-speechsense-text-1M](../_pricing_examples/speechsense/usd-speechsense-text-1M.md) %}


