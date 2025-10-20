---
title: Mounting file systems to {{ serverless-containers-full-name }}
description: In this tutorial, you will learn how to mount file systems to {{ serverless-containers-name }}.
---

# Mounting external resources to a container file system

You can mount [buckets](../../storage/concepts/bucket.md) and one ephemeral disk to {{ serverless-containers-name }}. To avoid data access errors, all mount points for ephemeral disks and buckets must be unique.

{% endnote %}

## Mounting buckets {#mount-backet}

Mounting buckets allows you to access them through the file system interface. In the container revision settings, the user can specify one or more mount paths. Use this path to access the directory the bucket will be mounted to. Do not use this path for anything other than an empty directory; otherwise, the container initialization may result in an error, and the mounted buckets will become unavailable.

You can mount the whole bucket or a specific [folder](../../storage/concepts/object.md#folder).

The `/run/storage_sync_socket` path is reserved for the Unix socket. To avoid loss of information when reading/writing data from/to the bucket:
* Do not put anything in the Unix socket path destination.
* Do not read from the Unix socket or write to it.


{% include [roles-for-bucket-mounting](../../_includes/functions/roles-for-bucket-mounting.md) %}

## Mounting an ephemeral disk {#mount-ephemeral-storage}

{% include [preview](../../_includes/note-preview.md) %}

To mount an ephemeral disk, you can specify a mount path. Use this path to access the directory the ephemeral disk will be mounted to. Do not use this path for anything other than an empty directory; otherwise, container initialization may result in an error, and the mounted ephemeral disk will become unavailable. To mount the ephemeral disk correctly, provide the full absolute path.

An ephemeral disk of a given size is created when the container is called just before the user code is executed. It is formatted into the [ext4](https://en.wikipedia.org/wiki/Ext4) file system and mounted at the specified path. The ephemeral disk's size must be a multiple of 1 GB.

The user code will have read-write access to the ephemeral disk throughout the container's lifetime.

If a container is called multiple times, there is a chance that its repeated calls will be processed by the same instance. In this case, the data remaining on the disk after the previous call can be accessed. Such container behavior is not determinated, and you cannot rely on the data being saved between calls.

{% note info %}

When the container's lifecycle ends, the ephemeral disk is permanently deleted. Use this type of disk space only to save temporary state.

{% endnote %}


## See also {#see-also}

* [Mounting buckets to a function](../../functions/operations/function/mount-bucket.md)
* [Mounting an ephemeral disk to a function](../../functions/operations/function/mount-ephemeral-disk.md)
* [Mounting buckets to a container](../../serverless-containers/operations/mount-bucket.md)
* [Mounting an ephemeral disk to a container](../../serverless-containers/operations/mount-ephemeral-disk.md)
