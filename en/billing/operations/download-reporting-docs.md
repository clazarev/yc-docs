---
title: Downloading reporting documents
description: Learn how to download an archive with closing documents, request a reconciliation report, completion report, and electronic invoices.
---

# Viewing your expense history and downloading reporting documents in {{ billing-name }}

Individuals, companies, and individual entrepreneurs can view their expense history. Companies and individual entrepreneurs that are residents of Russia, Kazakhstan, or Belarus can download reporting documents in {{ billing-name }}.

{% list tabs group=customers %}

- Businesses and individual entrepreneurs {#businesses}
    
    {% include [billing.accounts.account](../../_includes/billing/accountant-role.md) %}

    ## Limitations {#restrictions}

    The following restrictions apply when downloading reporting documents:
    * Maximum period for ordering documents is 15 months from the current date.
    * For each document, you can request the originals no more than three times.
    * This feature is not available for billing accounts with activated electronic document management (EDM).
    * If your request includes documents with different EDM statuses, {{ billing-name }} will process only those documents for which EDM is disabled.
    * This feature is not available for [subaccounts](../../partner/terms.md#sub-account).
    
    ## Companies and individual entrepreneurs of Russia {#legal-entities-russia}

    Companies and individual entrepreneurs can download [reports](../concepts/act.md) and [invoices](../concepts/invoice.md), as well as request [reconciliation reports](../concepts/act.md#reconciliation-report).

    ### Downloading closing documents {#download-closing-docs}

    {% include [download-closing-docs](../_includes/download-closing-docs.md) %}

    ### Requesting a reconciliation report {#download-acts}

    To generate a reconciliation report, submit a request in {{ billing-name }}. After your request is processed, the document will become available for download.

    You can request a reconciliation report for the current month seven business days after the month ends.

    To request a reconciliation report:

    1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
    1. Select a billing account.
    1. Navigate to **{{ ui-key.yacloud_billing.billing.account.switch_acts }}**.
    1. Open the **{{ ui-key.yacloud_billing.billing.account.tab_reconciliation-reports-title }}** tab.
    1. Click **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_request-report }}**. In the window that opens, select the period to generate the reconciliation report for and click **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_request-report-short }}**.
    1. As soon as the request status changes to **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.value_completed }}**, the download button will appear in the **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.field_actions }}** column. Select **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_download-with-facsimile }}** or **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_download-without-facsimile }}**.
    1. Click the button with the selected download type. The document will open in a new window and become available for saving.

    To exchange original documents, download an unsigned report, print and sign two copies, and send them as follows:

    * By post: Sadovnicheskaya St. 82, bldg. 2, Moscow, Russia, 115035.
          Put "For the Project Transaction Support Group" on the envelope without giving any names.
   
    * By courier: Sadovnicheskaya St. 82, bldg. 2, Moscow, Russia, 115035.
          Enter the building from Sadovnicheskaya Street, between entrances 5 and 6 of the Aurora Business Center.
          The delivery reception desk is open from 9 am to 6 pm, GMT+3, Monday to Friday.
          To call the office phone number, dial +7 495 739-70-00, then press 1; once the voice menu is on, dial 7704.

    As soon as the documents are signed, your copy will be sent to you.

    ## Companies and individual entrepreneurs of Kazakhstan {#legal-entities-kazakhstan}

    Companies and individual entrepreneurs can download [reports](../concepts/act.md), get [invoices](../concepts/invoice.md), as well as request [reconciliation reports](../concepts/act.md#reconciliation-report).

    ### Completion report {#report-of-completion}

    Completion reports can be downloaded by companies and individual entrepreneurs that are residents of Kazakhstan.

    Documents are generated on the last day of the reporting period (month). The completion report will become available for download seven business days after the reporting period ends. Acceptance certificates for previous reporting periods are also available in {{ billing-name }}.

    To request a completion report:

    1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
    1. Select a billing account.
    1. Navigate to **{{ ui-key.yacloud_billing.billing.account.switch_acts }}**.
    1. Open the **{{ ui-key.yacloud_billing.billing.account.tab_acts-title }}** tab.
    1. Next to the relevant reporting period, click ![image](../../_assets/console-icons/ellipsis.svg) → **{{ ui-key.yacloud_billing.billing.account.button_download-action }}**. This will open a window with reports for the selected period.
    1. To the right of the document, click ![image](../../_assets/console-icons/ellipsis.svg) → **{{ ui-key.yacloud.common.open }}**. The document will open in a new browser tab and will be available for saving.
    1. To download reports for multiple periods as a single archive, select the required periods in the left-hand column (or select the checkbox in the table header to select all periods) and click **{{ ui-key.yacloud_billing.billing.account.acts_batch-download-text_pdf }}** or **{{ ui-key.yacloud_billing.billing.account.acts_batch-download-text_zip }}**.

    ### Electronic invoice {#electronic-invoice}

    Electronic invoices (e-invoices) are transmitted to the [Information System for Collection and Processing of E-invoices](https://esf.gov.kz:8443/esf-web/login) within 15 calendar days of the turnover date.

    ### Requesting a reconciliation report {#download-acts-kazakhstan}

    To generate a reconciliation report, submit a request in {{ billing-name }}. After your request is processed, the document will become available for download.

    You can request a reconciliation report for the current month seven business days after the month ends.

    To request a reconciliation report:

    1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
    1. Select a billing account.
    1. Navigate to **{{ ui-key.yacloud_billing.billing.account.switch_acts }}**.
    1. Open the **{{ ui-key.yacloud_billing.billing.account.tab_reconciliation-reports-title }}** tab.
    1. Click **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_request-report }}**. In the window that opens, select the period to generate the reconciliation report for and click **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_request-report-short }}**.
    1. As soon as the request status changes to **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.value_completed }}**, the download button will appear in the **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.field_actions }}** column. Select **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_download-with-facsimile }}** or **{{ ui-key.yacloud_billing.billing.account.reconciliation-reports.action_download-without-facsimile }}**.
    1. Click the button with the selected download type. The document will open in a new window and become available for saving.
    
    To exchange original documents, download the unsigned report, print two copies, sign them, and send them to: 11/1 Al-Farabi Avenue, Bostandyq District, Almaty, Republic of Kazakhstan, 050059, reception. As soon as the documents are signed, your copy will be sent to you.

    ## Companies and individual entrepreneurs of Belarus {#legal-entities-belarus}

    Companies and individual entrepreneurs can download [reports](../concepts/act.md) and [invoices](../concepts/invoice.md).

    ### Downloading closing documents {#download-closing-docs}

    {% include [download-closing-docs](../_includes/download-closing-docs.md) %}

- Individuals {#individuals}

  ## Viewing your expense history {#expense-history}

  Individuals can view their monthly spending history in {{ billing-name }}.

  To view your expense history:

    1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
    1. Select a billing account.
    1. Navigate to **{{ ui-key.yacloud_billing.billing.account.switch_expences }}**.

  Individuals cannot download reporting documents.

{% endlist %}
