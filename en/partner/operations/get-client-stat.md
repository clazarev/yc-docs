---
title: Viewing details of service consumption by customers
description: Follow this guide to view detailed information on how the customers are using the services.
---

# Viewing details of service consumption by customers

You can view detailed information on how the customers are using the services:

* In the management console
* On the partner portal

{% list tabs group=instructions %}

- Management console {#console}

  To view charts and tables with information about {{ yandex-cloud }} service usage:

  1. In the [management console]({{ link-console-main }}), click ![image](../../_assets/console-icons/dots-9.svg) **All services**.
  1. Select ![image](../../_assets/console-icons/credit-card.svg) [**{{ billing-name }}**]({{ link-console-billing }}).
  1. Click the account you need and select ![image](../../_assets/console-icons/layout-cells-large.svg) **Usage details**.

     For more information about the settings of the **Usage details** page, see the [relevant guide on {{ billing-name }}](../../billing/operations/check-charges.md).

- Partner portal {#partner}

  Log in to the [partner portal]({{ link-cloud-partners }}) with the Yandex ID linked to your partner account in {{ yandex-cloud }}. There are several ways to check details of service usage by customers:

  * **Dashboard** section

    1. In the left-hand panel, select ![icon](../../_assets/console-icons/layout-header-side-content.svg) **Dashboard**.
    1. Select the customer's account from the list and click it.
    1. Go to the **Service usage** tab.

  * **Rewards** section

    1. In the left-hand panel, select ![icon](../../_assets/console-icons/medal.svg) **Rewards**.
    1. Specify the statistics period. The chart will display the total consumption by month for all customers.

       {% note info %}

       You can view statistics for up to 92 days.

       {% endnote %}

       You can download detailed statistics on customers as a `.csv` file by clicking **Download CSV**.

{% endlist %}

You can also [view your service usage information](../../billing/operations/dashboard.md) in {{ datalens-full-name }}.
