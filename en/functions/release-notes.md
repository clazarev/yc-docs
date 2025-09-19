---
title: '{{ sf-full-name }} release notes'
description: This section contains {{ sf-name }} release notes.
---

# {{ sf-full-name }} release notes

## July 2025 {#july-2025}

### Updates {#updates}

* Increased the default number of [user networks](concepts/networking.md#user-network) per cloud from 1 to 10. This is no longer a limit but a [quota](concepts/limits.md#functions-quotas), which can be changed through a request to support.

## June 2025 {#june-2025}

### Updates {#updates}

* Added support for Node.js 22 and Go 1.23.

## February 2025 {#february-2025}

### Updates {#updates}

* Added support for [setting up the function metadata service](operations/function/metadata-options.md).

## September — October 2024 {#sept-oct-2024}

### Updates {#updates}

* Added support for [long-lived functions](concepts/long-lived-functions.md).
* Added support for sending [function termination notifications](concepts/termination-notifications.md).
* Added support for [mounting ephemeral disks](concepts/mounting.md#mount-ephemeral-storage).
* Added support for [concurrent function calls of a single instance](concepts/function.md#concurrency).

## August 2024 {#august-2024}

### Updates {#updates}

* Added support for the [Kotlin](lang/kotlin/index.md) runtime environment.

## May – June 2024 {#may-june-2024}

### Updates {#updates}

* Added support for the `concurrency` parameter in the CLI, API, and {{ TF }}.

### Fixes and improvements {#fixes-improvements}

* Fixed the error that occurred when creating function versions with the `golang` runtime environment that use the [opentracing-go](https://github.com/opentracing/opentracing-go) package.

## April 2024 {#april-2024}

### Updates {#updates}

* Added support for [allocating RAM for the `/tmp` directory](operations/function/allocate-memory-tmp.md) for the function version with at least 1 GB of memory.

### Fixes and improvements {#fixes-improvements}

* Fixed the `yandex_function_scaling_policy` import error in {{ TF }}.
* Removed the [user network](concepts/networking#user-network) requirement to have a subnet in the `ru-central1-c` availability zone because [this zone is discontinued](../overview/concepts/region.md).

## March 2024 {#march-2024}

### Updates {#updates}

* Added support for editing all trigger parameters in {{ TF }}.
* [{{ objstorage-full-name }} bucket mounting](concepts/mounting.md) to a function is now at the [General Availability](../overview/concepts/launch-stages.md) stage.

### Fixes and improvements {#fixes-improvements}

* Increased the maximum batch size in a [{{ message-queue-full-name }}](concepts/trigger/ymq-trigger.md) trigger to 1,000 messages.

## January – February 2024 {#jan-feb-2024}

### Updates {#updates}

* Added support for [mounting {{ objstorage-full-name }} buckets](concepts/mounting.md) to a function via the CLI and {{ TF }}.
* Added support for function logging settings in {{ TF }}.

### Fixes and improvements {#fixes-improvements}

* Fixed the error encountered when displaying the quota for the number of function tags.
