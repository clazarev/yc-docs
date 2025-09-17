---
title: '{{ serverless-containers-full-name }} release notes'
description: This section contains {{ serverless-containers-name }} release notes.
---

# {{ serverless-containers-full-name }} release notes

## July 2025 {#july-2025}

### Updates {#updates}

* Increased the default number of [user networks](concepts/networking.md#user-network) per cloud from 1 to 10. This is no longer a limit but a [quota](concepts/limits.md#serverless-containers-quotas), which can be changed through a request to support.

## April 2025 {#april-2025}

### Fixes and improvements {#fixes-improvements}

* Container revision building was accelerated more than two-fold.

## February 2025 {#february-2025}

### Updates {#updates}

* Added support for [setting up the container metadata service](operations/metadata-options.md).

## December 2024 {#december-2024}

### Updates {#updates}

* Added support for [container mode](operations/update-runtime.md#console_1) selection in the management console.

## November 2024 {#november-2024}

### Updates {#updates}

* Added support for [container mode](operations/update-runtime.md) selection in the CLI, API, and {{ TF }}.

## September — October 2024 {#sept-oct-2024}

### Updates {#updates}

* Added support for [long-lived containers](concepts/long-lived-containers.md).
* Added support for sending [container termination notifications](concepts/termination-notifications.md).
* Added support for [mounting ephemeral disks](concepts/mounting.md#mount-ephemeral-storage).

## April 2024 {#april-2024}

### Fixes and improvements {#fixes-improvements}

* Removed the [user network](concepts/networking#user-network) requirement to have a subnet in the `ru-central1-c` availability zone because [this zone is discontinued](../overview/concepts/region.md).

## March 2024 {#march-2024}

### Updates {#updates}

* Added support for editing all trigger parameters in {{ TF }}.
* [{{ objstorage-full-name }}](concepts/mounting.md) bucket mounting to a container is now at the [General Availability](../overview/concepts/launch-stages.md) stage.

### Fixes and improvements {#fixes-improvements}

* Increased the maximum batch size in a [{{ message-queue-full-name }}](concepts/trigger/ymq-trigger.md) trigger to 1,000 messages.

## January – February 2024 {#jan-feb-2024}

### Updates {#updates}

* Added support for container logging settings in {{ TF }}.
* Added support for [mounting {{ objstorage-full-name }}](concepts/mounting.md) buckets to a container via the CLI and {{ TF }}.
