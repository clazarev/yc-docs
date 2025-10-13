# Object

Objects reside in buckets and contain user data in the format you uploaded it.

An object ID is a string [key](#key).

Along with the object, {{ objstorage-name }} stores [user](#user-meta) and [system](#system-meta) metadata.

{{ objstorage-name }} allows you to store objects in storages of various classes depending on how long you want to store your objects and how often you access them. For more information, see [{#T}](storage-class.md).

{{ objstorage-name }} supports the following operations with objects:

* Uploading an object to a storage.
* Downloading an object from a storage.
* Copying an object within a storage, e.g., from one bucket to another.
* Deleting an object.
* [Partial object update](object-patch.md) in a storage.

You can also combine these operations using the [tools](../tools/index.md).

You can configure [lifecycles](lifecycles.md) for bucket objects.

{{ objstorage-name }} limits the size of objects and their metadata. For more information, see [{#T}](limits.md).

## Key {#key}

A key is an ID of an object in a bucket.

Objects are stored in a flat structure, although GUI-based [tools](../tools/index.md) allow you to use {{ objstorage-name }} as a hierarchical file system. Hierarchical view is possible because you can write keys as paths in a file system, e.g., `top_level_prefix/subprefix/text_data.txt`. In the {{ yandex-cloud }} management console, prefixes are called directories.

A key must:

* Be UTF-8 encoded.
* Be less than 1024 bytes in size.
* Not contain these characters: `: * ? " < > | !`.

The following characters are safe to use in a key: `[a-zA-Z0-9]`, `-`, `_`, `/`, `\`. Other characters may cause various issues in {{ objstorage-name }}.

### Directory {#folder}

There are no directories in {{ objstorage-name }}, but GUI-based file management clients, such as [CyberDuck](../tools/cyberduck.md) and the {{ objstorage-name }} interface in the {{ yandex-cloud }} management console, can emulate them. Such an emulated directory is a zero-size object. Its key is included as a prefix in the keys of other objects. For example, an object with the `x` key and zero size is a directory in the management console, while an object with the `x/y.txt` key is the `y.txt` object located in the `x` directory.

Each [tool](../tools/index.md) manages objects and directories according to its own logic, which is described in the respective documentation.

{% note info %}

Deleting directories with objects is an asynchronous operation. Once it starts, {{ objstorage-name }} prepares a list of objects to delete, and then deletes them. If during the process you upload an object to {{ objstorage-name }} to a directory marked for deletion, the object will upload successfully. After both operations in {{ objstorage-name }} are complete, the directory marked for deletion will remain and contain the newly uploaded file.

{% endnote %}

## Object URL {#object-url}

You can specify a link to a bucket object in one of the following formats:
- For a bucket with public access:
  - `http(s)://<bucket>.{{ s3-storage-host }}/<key>`
  - `https://{{ s3-storage-host }}/<bucket>/<key>`
- For a bucket with restricted access:
  - `http(s)://<bucket>.{{ s3-storage-host }}/<key>?<parameters>`
  - `https://{{ s3-storage-host }}/<bucket>/<key>?<parameters>`

Where:

* `<bucket>`: Bucket name.
* `<key>`: [Key](#key) (file path).
* `<parameters>`: Additional parameters for accessing a bucket with restricted access, e.g., a signature and validity period.

{% include [public-link](../../_includes/storage/public-link.md) %}

For a bucket with restricted access, the service generates a temporary pre-signed URL that allows you to download an object even from a bucket with restricted access. You can read more about pre-signed URLs, their generation, and their use [here](pre-signed-urls.md).


{% include [bucket-https](../../_includes/storage/bucket-https.md) %}


{% include [storage-dotnet-host](../_includes_service/storage-dotnet-host.md) %}

## Metadata {#metadata}

Metadata is stored with the object as `key-value` pairs. This can be either system or user metadata.

You can use the metadata to [search](../operations/objects/object-meta.md#tags-searching) for objects.

### System metadata {#system-meta}

System metadata is defined by {{ objstorage-name }}.

Key | Description
----- | -----
`Date` | Date and time the request to upload an object to {{ objstorage-name }} was sent.
`Content-Length` | Object size in bytes.
`Last-Modified` | Date the object was created or last modified.
`Content-MD5` | Object's base64-encoded MD5 hash.
`Cache-Control` | Value of the `Cache-Control` HTTP header provided by the client when saving the object to the bucket. {{ objstorage-name }} later returns this header to clients when responding to a request for the object or its metadata.<br/><br/>For example, the `Cache-Control: max-age=200` header means the object expires 200 seconds after the client receives it. You can read more about it in [RFC 7234](https://tools.ietf.org/html/rfc7234#section-5.2).
`Expires` | Value of the `Expires` HTTP header provided by the client when saving the object to the bucket. {{ objstorage-name }} later returns this header to clients when responding to a request for an object or its metadata.<br/><br/>For example, the `Expires: Thu, 15 Apr 2020 20:00:00 GMT` header means the object expires at 20:00:00 GMT on April 15, 2020. You can read more about it in [RFC 7234](https://tools.ietf.org/html/rfc7234#section-5.3).

### User-defined metadata {#user-meta}

When uploading an object to {{ objstorage-name }}, you can provide its metadata as `key-value` pairs.

In an Amazon S3-compatible HTTP API, metadata is provided as HTTP headers. The header name must start with `X-Amz-Meta-`. For an object request via the HTTP API, {{ objstorage-name }} returns metadata as HTTP headers with the same prefix.

Metadata keys must consist of [ASCII characters](https://{{ lang }}.wikipedia.org/wiki/ASCII) only. The headers will be transformed as follows: `X-Amz-Meta-foo-bar_baz` → `X-Amz-Meta-Foo-Bar_baz`, where `Foo-Bar_baz` is the key of the metadata that will be saved with the object.

{% note info %}

The PUT request header must not exceed 8 KB. The maximum size of user-defined metadata in this header is 2 KB.

{% endnote %}

For more information, see [{#T}](../operations/objects/object-meta.md).

## Conditional writes {#conditional-writes}

To avoid accidental overwriting and conflicts during concurrent uploads, you can set object write conditions. The conditions are set using [S3 API](../s3/index.md) headers:

* For the [upload](../s3/api-ref/object/upload.md) and [completeUpload](../s3/api-ref/multipart/completeupload.md) methods:

    * `If-Match`: The write will only be performed if an object already exists for the specified key and its current `ETag` matches the values in the header.
    * `If-None-Match`: The write will only be performed if there is no object with the same name in the bucket for the specified key.

* For the [copy](../s3/api-ref/object/copy.md) and [copyPart](../s3/api-ref/multipart/copypart.md) methods:

    * `X-Amz-Copy-Source-If-Match`: The write will only be performed if an object already exists for the specified key and its current `ETag` matches the values in the header.
    * `X-Amz-Copy-Source-If-None-Match`: The write will only be performed if there is no object with the same name in the bucket for the specified key.

You can use the conditions for object uploads and multipart uploads, when copying whole objects of parts of objects. For more information, see the [Conditional object upload](../operations/objects/upload.md#conditional-writes) guide.

Response codes:

* `404` (`Not Found`): Object for the key is not found. Only for `If-Match`.
* `409` (`Conflict`): The condition was met; however, the key was modified by another write operation during the upload.
* `412` (`Precondition Failed`): Condition not met.

[Multipart upload](../operations/objects/multipart-upload.md#conditional-writes) features:

* Multipart uploads initiate without any conditions. Headers are applied at the `Complete Multipart Upload` stage.
* If getting response code `409`, you have to manually [interrupt](../operations/objects/deleting-multipart.md) the current multipart upload and start a new one.


    {% note warning %}

    Until you interrupt the multipart upload, the uploaded parts remain in the bucket and are subject to [data storage](../pricing.md#prices-storage) charges.

    {% endnote %}

## Use cases {#examples}

* [{#T}](../tutorials/server-logs.md)
* [{#T}](../tutorials/server-logs-yq.md)
* [{#T}](../tutorials/bucket-to-bucket.md)

### See also {#see-also}

* [{#T}](../security/overview.md)
