---
title: '{{ postbox-full-name }} release notes'
description: This section contains {{ postbox-name }} release notes.
---

# {{ postbox-full-name }} release notes

## August 2025 {#august-2025}

### Updates {#updates}

* Added support for [click tracking](concepts/click-tracking.md) in emails.

## July 2025 {#july-2025}

### Updates {#updates}

* Supported [authentication with an {{ iam-short-name }} token](api-ref/authentication.md#iam-ses-example).
* Supported notifications about the following events:
    * [Email opened](concepts/notification.md#open)
    * [Email delivery delayed](concepts/notification.md#delayed-delivery)
    * [Recipient unsubscribed](concepts/notification.md#subscription)
* Supported [email delivery over a secure connection](aws-compatible-api/api-ref/put-configuration-set-delivery-options.md).

## May 2025 {#may-2025}

### Updates {#updates}

* Added support for {{ TF }}: you can now create addresses and check custom domain ownership using {{ TF }}. For more information, see [{#T}](tutorials/domain-identity-creating.md)

## March 2025 {#march-2025}

### Updates {#updates}

* Added support for SMTPS: users whose email client does not support STARTTLS can now send emails using {{ postbox-full-name }}.
* Added support for [email templating](operations/send-templated-email.md).

## January 2025 {#january-2025}

### Updates {#updates}

* Added support for sending [notifications about email delivery](concepts/notification.md) to a {{ yds-full-name }} data stream.
* Added support for [API key authentication](operations/send-email.md#smtp-send) when sending emails from the email client over SMTP.

## August 2024 {#august-2024}

### Updates {#updates}

* Added support for [logging](operations/logs-write.md) to the execution log.
