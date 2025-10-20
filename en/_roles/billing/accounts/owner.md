When creating your billing account, you get the `billing.accounts.owner` role automatically. Any user with the `billing.accounts.owner` role can revoke this role from the billing account creator and change the owner.

{% cut "In Yandex Cloud Billing, users with this role can:" %}

* Display [billing accounts](../../../billing/concepts/billing-account.md) in the list of all accounts.
* View billing account data.
* View [client offers](../../../billing/concepts/glossary.md#client-offer).
* View info on the [access permissions](../../../iam/concepts/access-control/index.md) granted for the relevant billing accounts and modify such permissions.
* Activate, deactivate, or modify the [technical support](../../../support/overview.md) service plan, as well as change the billing account from which the payment is debited.
* View and download [reporting (or closing) documents](../../../billing/payment/documents.md).
* Generate new [reconciliation reports](../../../billing/concepts/act.md#reconciliation-report).
* View and download generated reconciliation reports.
* Get and view notifications on consumption.
* Monitor expenses.
* [View usage details](../../../billing/operations/check-charges.md).
* [Export details](../../../billing/operations/get-folder-report.md).
* Create [budgets](../../../billing/concepts/budget.md).
* [Reserve resource usage](../../../billing/concepts/cvos.md).
* Top up their [personal account](../../../billing/concepts/personal-account.md) using a bank account.
* Top up their personal account using a credit or debit card.
* Link [clouds](../../../resource-manager/concepts/resources-hierarchy.md#cloud) to a billing account.
* Rename billing accounts.
* Changing payer contact details.
* Change payment details.
* Change their credit or debit card details.
* Change the payment method.
* Redeem promo codes.
* Activate the [trial period](../../../billing/concepts/trial-period.md).
* Activate the [paid version](../../../getting-started/free-trial/concepts/upgrade-to-paid.md).
* [Delete](../../../billing/operations/delete-account.md) billing accounts.

{% endcut %}

{% cut "On the Yandex Cloud partner portal, users with this role can:" %}

* [Create](../../../partner/program/var-pin-client.md#client-entry) customer records ([subaccounts](../../../partner/terms.md#sub-account)).
* View the list of subaccounts and info on them, including personal data.
* Update subaccount records.
* Activate subaccounts.
* Suspend subaccounts.
* Re-activate subaccounts.
* Delete subaccounts without customer confirmation.
* Link [clouds](../../../resource-manager/concepts/resources-hierarchy.md#cloud) to subaccounts.
* [Manage](../../../partner/operations/access/partners-account.md) access permissions to subaccounts.
* [View](../../../partner/operations/get-client-stat.md) the details of how the customers use services.
* View the list of [partner discounts](../../../partner/portal.md#premium) and info on them.

{% endcut %}

This role includes the `billing.accounts.admin` and `billing.accounts.varWithoutDiscounts` permissions.
