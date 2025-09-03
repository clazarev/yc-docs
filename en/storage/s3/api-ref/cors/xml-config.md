# CORS configuration for buckets

{{ objstorage-name }} allows you to manage bucket [CORS configurations](../../../concepts/cors.md). To upload a CORS configuration to {{ objstorage-name }}, you need to create an XML document as described in this section. You can get a document in this format by downloading an existing configuration.

The general configuration format is as follows:

```xml
<CORSConfiguration>
    <CORSRule>
        <AllowedOrigin>URL</AllowedOrigin>
        <AllowedMethod>HTTP_Method</AllowedMethod>
        <AllowedHeader>Header_Name</AllowedHeader>
        ...
    </CORSRule>
    ...
</CORSConfiguration>
```

A configuration may contain up to 100 rules.

## Elements {#elements}

Element | Description
----- | -----
`CORSConfiguration` | Root element of a CORS configuration. It may contain a maximum of 100 `CORSRule` elements.<br/><br/>Path: `/CORSConfiguration`.
`CORSRule` | Rule for filtering incoming requests to the resource. Each rule must contain at least one `AllowedMethod` and `AllowedOrigin` element.<br/><br/>Path: `/CORSConfiguration/CORSRule`.
`ID` | Unique rule ID (up to 255 characters).<br/><br/>This is an optional parameter. It can be used to search for a rule in a file.<br/><br/>Path: `/CORSConfiguration/CORSRule/ID`.
`AllowedMethod` | HTTP method (`PUT`, `GET`, `HEAD`, `POST`, or `DELETE`) that can be used in a cross-domain request. Each method should be specified in a separate element. Specify at least one method.<br/><br/>Path: `/CORSConfiguration/CORSRule/AllowedMethod`.
`AllowedOrigin` | Website that allows sending CORS requests to the bucket. Specify at least one `AllowedOrigin` element.<br/><br/>May not contain more than one `*` character. Examples: `http://*.example.com`, `*`.<br/><br/>Path: `/CORSConfiguration/CORSRule/AllowedOrigin`.
`AllowedHeader` | Header allowed in a request to an object. If multiple headers are allowed, specify each one in a separate `AllowedHeader` element. You can use a single `*` character in the header name to define a template. For example, `<AllowedHeader>*</AllowedHeader>` means that all headers are allowed.<br/><br/>An [options](../object/options.md) request contains the `Access-Control-Request-Headers` header. {{ objstorage-name }} maps the headers provided to `Access-Control-Request-Headers` against the `AllowedHeader` set and returns a list of allowed headers in response to the `options` request.<br/><br/>Path: `/CORSConfiguration/CORSRule/AllowedHeader`.
`MaxAgeSeconds` | Time in seconds during which the result of the [options](../object/options.md) request to the object remains cached in the browser.<br/><br/>Path: `/CORSConfiguration/CORSRule/MaxAgeSeconds`.
`ExposeHeader` | Header that can be exposed to browser JavaScript apps. If multiple headers are allowed, specify each one in a separate element.<br/><br/>When requesting an object, the JavaScript client can only use the headers specified in `ExposeHeader` elements.<br/><br/>Path: `/CORSConfiguration/CORSRule/ExposeHeader`.


## Example {#example}

The following configuration allows you to send CORS requests from the `http://www.example.com` website using the `PUT`, `POST`, and `DELETE` methods without any header restrictions.

```xml
<CORSConfiguration>
    <CORSRule>
        <AllowedOrigin>http://www.example.com</AllowedOrigin>
        <AllowedMethod>PUT</AllowedMethod>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedMethod>DELETE</AllowedMethod>
        <AllowedHeader>*</AllowedHeader>
    </CORSRule>
</CORSConfiguration>
```

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}

* [{#T}](../../../operations/buckets/cors.md)