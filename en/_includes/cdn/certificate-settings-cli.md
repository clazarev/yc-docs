To configure a [TLS certificate](../../cdn/concepts/clients-to-servers-tls.md) for a CDN resource, use these parameters:

* `--dont-use-ssl-cert`: Do not use a certificate. The resource will be available only over HTTP.
* `--cert-manager-ssl-cert-id`: Certificate ID. The resource will be available over HTTP and HTTPS.

  {% include [certificate-usage](certificate-usage.md) %}