# Configuring an identity federation

You can use [identity federation](../concepts/add-federation.md) to set up user authentication in the cloud. Identity federations are compatible with any identity provider (IdP) that supports [SAML 2.0](https://wiki.oasis-open.org/security/FrontPage).

To set up authentication through an identity federation, you need _at least_ the `organization-manager.federations.editor` [role](../../organization/security/index.md#organization-manager-federations-editor) for the [organization](../../organization/concepts/organization.md).

To set up federated authentication, follow these steps:

1. [Create an identity federation](#create-federation).
1. [Add the IdP server certificate to the federation](#add-certificate-fed).
1. [Add the federation certificate to the IdP server](#add-certificate-idp).
1. [Set up a SAML application in your IdP](#configure-sso).
1. [Configure user attribute mapping](#claims-mapping).
1. [Test authentication](#test-auth).

For IdP-specific examples, see our tutorials:

* [Active Directory](../tutorials/federations/integration-adfs.md).
* [Google Workspace](../tutorials/federations/integration-gworkspace.md).
* [{{ microsoft-idp.entra-id-full }}](../tutorials/federations/integration-azure.md).
* [Keycloak](../tutorials/federations/integration-keycloak.md).

## Creating an identity federation {#create-federation}

{% list tabs group=instructions %}

- {{ cloud-center }} UI {#cloud-center}

  1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Click ![Circles3Plus](../../_assets/console-icons/circles-3-plus.svg) **{{ ui-key.yacloud_org.form.federation.action.create }}**.

  1. Give your federation a name. It must be unique within the folder.

  1. (Optional) Add a description for your federation.

  1. In the **{{ ui-key.yacloud_org.entity.federation.field.cookieMaxAge }}** field, specify the time before the browser asks the user to re-authenticate.

  1. In the **{{ ui-key.yacloud_org.entity.federation.field.issuer }}** field, specify the IdP server ID to use for authentication. The format of the ID depends on the server type.

      To learn how to get the IdP server ID, consult the provider's documentation or contact their support.

  1. In the **{{ ui-key.yacloud_org.entity.federation.field.ssoBinding }}** field, select the redirect binding method. Most IdPs support the **POST** binding method.

  1. In the **{{ ui-key.yacloud_org.entity.federation.field.ssoUrl }}** field, specify the address of the page to which the browser redirects the user for authentication.

      To learn how to get the redirect page URL, consult your identity provider's documentation or contact their support.

      {% include [ssourl_protocol](../../_includes/organization/ssourl_protocol.md) %}

  1. In **Advanced**, specify the following parameters as required:

        * **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}**: If enabled, a federated user will be automatically added to your organization once they sign in. Otherwise, you will need to [manually add](./add-account.md#add-user-sso) your federated users.

            {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

        * **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**: Ensures authentication requests from {{ yandex-cloud }} contain a digital signature. You will need to install a {{ yandex-cloud }} SAML certificate on the IdP side.

            {% include [download-saml-cert-when-creating-fed](../../_includes/organization/download-saml-cert-when-creating-fed.md) %}

        * **{{ ui-key.yacloud_org.entity.federation.field.caseInsensitiveNameIds }}**: If enabled, federated user name IDs will be case-insensitive.
        * **{{ ui-key.yacloud_org.entity.federation.field.forceAuthn }}**: When the {{ yandex-cloud }} session expires, your IdP will prompt the user to re-authenticate.

  1. Click **{{ ui-key.yacloud_org.form.federation.create.action.create }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To create a federation:

    1. View the description of the create federation command:

        ```bash
        yc organization-manager federation saml create --help
        ```

    1. Specify the federation parameters in the create command:

        ```bash
        yc organization-manager federation saml create \
          --name <federation_name> \
          --organization-id <organization_ID> \
          --cookie-max-age <cookie_lifetime> \
          --issuer "<IdP_server_ID>" \
          --sso-binding <POST_or_REDIRECT> \
          --sso-url "<redirect_page_address>" \
          --encrypted-assertions \
          --auto-create-account-on-login \
          --case-insensitive-name-ids \
          --force-authn
        ```

        Where:

        * `--name`: Federation name. It must be unique within the folder.
        * `--organization-id`: Organization ID.
        * `--cookie-max-age`: Time that must elapse before the browser asks the user to re-authenticate, e.g., `12h`.
        * `--issuer`: IdP server ID to use for authentication.

            To learn how to get the IdP server ID, consult the provider's documentation or contact their support.

        * `--sso-binding`: Single sign-on binding type. The possible values are `POST` and `REDIRECT`. Most identity providers support the `POST` binding type.
        * `--sso-url`: URL of the page the browser redirects the user to for authentication.

            To learn how to get the redirect page URL, consult your identity provider's documentation or contact their support.

            {% include [ssourl_protocol](../../_includes/organization/ssourl_protocol.md) %}

        * `--encrypted-assertions`: Ensures authentication requests from {{ yandex-cloud }} contain a digital signature. You will need to install a {{ yandex-cloud }} certificate on the IdP side. This is an optional parameter.
        * `--auto-create-account-on-login`: If enabled, a federated user will be automatically added to your organization once they sign in. Otherwise, you will need to [manually add](./add-account.md#add-user-sso) your federated users. This is an optional parameter.

            {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

        * `--case-insensitive-name-ids`: If enabled, federated user name IDs will be case-insensitive. This is an optional parameter.
        * {% include [forceauthn-cli-enable](../../_includes/organization/forceauth-cli-enable.md) %}

- {{ TF }} {#tf}

    {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  1. {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Create a configuration file describing the federation.

      Configuration file structure example:

      ```hcl
      resource "yandex_organizationmanager_saml_federation" federation {
        name            = "my-federation"
        description     = "My new SAML federation"
        organization_id = "<organization_ID>"
        issuer          = "<IdP_server_ID>" 
        sso_url         = "<redirect_page_address>"
        sso_binding     = "<POST_or_REDIRECT>"
        cookie_max_age = <cookie_lifetime>
        auto_create_account_on_login = "<true_or_false>"
        case_insensitive_name_ids = "<true_or_false>"
        security_settings {
          encrypted_assertions = "<true_or_false>"
          force_authn          = "<true_or_false>"
        }
      }
      ```

      Where:

      * `name`: Federation name. It must be unique within the folder.
      * `description`: Federation description. This is an optional parameter.
      * `organization_id`: Organization ID.
      * `issuer`: IdP server ID to use for authentication.

          To learn how to get the IdP server ID, consult the provider's documentation or contact their support.

      * `sso_binding`: Single sign-on binding type. The possible values are `POST` and `REDIRECT`. Most identity providers support the `POST` binding type.
      * `sso_url`: URL of the page the browser redirects the user to for authentication.

          To learn how to get the redirect page URL, consult your identity provider's documentation or contact their support.

          {% include [ssourl_protocol](../../_includes/organization/ssourl_protocol.md) %}

      * `cookie_max_age`: Time, in seconds, before the browser prompts the user to re-authenticate. The default value is `28800` (8 hours).
      * `auto_create_account_on_login`: If `true`, a federated user will be automatically added to your organization once they sign in. Otherwise, you will need to [manually add](./add-account.md#add-user-sso) your federated users.

          {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

      * `case_insensitive_name_ids`: If `true`, federated user name IDs will be case-insensitive.
      * `security_settings`: Federation security settings:

          * `encrypted_assertions`: Ensures authentication requests from {{ yandex-cloud }} contain a digital signature. You will need to install a {{ yandex-cloud }} certificate on the IdP side.

          * {% include [force-authn-tf](../../_includes/organization/force-authn-tf.md) %}

      {% include [organizationmanager_saml_federation-tf](../../_includes/organization/organizationmanager_saml_federation-tf.md) %}

  1. Validate your {{ TF }} configuration files:

       {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a federation:

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  You can check the new federation and its settings in the [{{ ui-key.yacloud_org.pages.federations }}]({{ link-org-federations }}) section of your organization.

  {% note info %}

  You can also configure an [end-to-end Keycloack federation setup](https://github.com/yandex-cloud-examples/yc-iam-federation-with-keycloak-vm) using {{ TF }}.

  {% endnote %}

- API {#api}

  1. Create a file with the request body, e.g., `body.json`:

      ```json
      {
        "name": "my-federation",
        "description": "My new SAML federation",
        "organizationId": "<organization_ID>",
        "cookieMaxAge":"43200s",
        "issuer": "<IdP_server_ID>",
        "ssoUrl": "<redirect_page_address>",
        "ssoBinding": "<POST_or_REDIRECT>",
        "autoCreateAccountOnLogin": true,
        "caseInsensitiveNameIds": true,
        "securitySettings": {
          "encryptedAssertions": true,
          "forceAuthn": true
        }
      }
      ```

      Where:

      * `name`: Federation name. It must be unique within the folder.
      * `organizationId`: Organization ID.
      * `description`: Federation description. This is an optional parameter.
      * `cookieMaxAge`: Time, in seconds, before the browser prompts the user to re-authenticate. The default value is `28800` (8 hours).
      * `issuer`: IdP server ID to use for authentication.

            To learn how to get the IdP server ID, consult the provider's documentation or contact their support.

      * `ssoUrl`: URL of the page the browser redirects the user to for authentication.

          To learn how to get the redirect page URL, consult your identity provider's documentation or contact their support.

          {% include [ssourl_protocol](../../_includes/organization/ssourl_protocol.md) %}

      * `ssoBinding`: Single sign-on binding type. The possible values are `POST` and `REDIRECT`. Most identity providers support the `POST` binding type.
      * `autoCreateAccountOnLogin`: If `true`, a federated user will be automatically added to your organization once they sign in. Otherwise, you will need to [manually add](./add-account.md#add-user-sso) your federated users.

          {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

      * `caseInsensitiveNameIds`: If `true`, federated user name IDs will be case-insensitive.
      * `encryptedAssertions`: If `true`, authentication requests from {{ yandex-cloud }} will contain a digital signature. You will need to install a {{ yandex-cloud }} certificate on the IdP side.
      * {% include [forceauthn-api-enable](../../_includes/organization/forceauth-api-enable.md) %}

  1. {% include [include](../../_includes/iam/create-federation-curl.md) %}

{% endlist %}

## Adding an IdP server certificate to a federation {#add-certificate-fed}

When informing {{ org-full-name }} that a user has been authenticated, the IdP signs the message with its own certificate. To enable {{ org-name }} to verify this certificate, add it to your federation:

1. Get your identity provider certificate.

    To learn how to do this, consult the identity provider's documentation or contact their support.

    To prevent the browser from blocking the authentication page, make sure the subject name in the certificate contains the IdP server's FQDN, e.g., `fs.contoso.com`.

1. Convert the certificate file to the PEM format:

    ```text
    -----BEGIN CERTIFICATE-----
      <certificate value>
    -----END CERTIFICATE-----
    ```

1. Add the certificate to the federation:

    {% list tabs group=instructions %}

    - {{ cloud-center }} UI {#cloud-center}

      1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}) with an administrator or organization owner account.

      1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

      1. Click the row with the federation you want to add a certificate to.

      1. Click **{{ ui-key.yacloud_org.page.federation.section.certificates }}** under **{{ ui-key.yacloud_org.entity.certificate.action.add }}** at the bottom of the page.

      1. Enter certificate name and description.

      1. Choose how to add a certificate:

          * To add a certificate as a file, click **{{ ui-key.yacloud_portal.component.file-input.button_choose }}** and specify the path to it.
          * To paste the contents of a copied certificate, select the **{{ ui-key.yacloud_org.component.form-file-upload.method.manual }}** method.

      1. Click **{{ ui-key.yacloud_org.actions.add }}**.

    - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      1. View the description of the add certificate command:

          ```bash
          yc organization-manager federation saml certificate create --help
          ```

      1. Add a federation certificate by specifying the certificate file path:

          ```bash
          yc organization-manager federation saml certificate create --federation-name <federation_name> \
            --name "<certificate_name>" \
            --certificate-file <certificate_file_path>
          ```

    - API {#api}

      1. Create the `body.json` request body file and specify the contents of the certificate in the `data` property:

          ```json
          {
            "federationId": "<federation_ID>",
            "description": "<certificate_description>",
            "name": "<certificate_name>",
            "data": "<certificate_contents>"
          }
          ```

      1. Use the [create](../saml/api-ref/Certificate/create.md) REST API method for the [Certificate](../saml/api-ref/Certificate/index.md) resource or the [FederationService/Create](../saml/api-ref/grpc/Certificate/create.md) gRPC call and provide a file with the request parameters in your request.

      Sample cURL request:

      ```bash
      export IAM_TOKEN=CggaATEVAgA...
      curl \
        --request POST \
        --header "Content-Type: application/json" \
        --header "Authorization: Bearer ${IAM_TOKEN}" \
        --data '@body.json' \
        "https://organization-manager.{{ api-host }}/organization-manager/v1/saml/certificates"
      ```
  {% endlist %}

{% include [federation-certificates-note](../../_includes/organization/federation-certificates-note.md) %}

## Adding a federation certificate to an IdP server {#add-certificate-idp}

If you enabled **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** when creating the federation, all authentication requests from {{ yandex-cloud }} will contain a digital signature. The IdP server should be able to verify this signature. To enable this, you will need to add the {{ yandex-cloud }} certificate to the server:

1. If you did not download a {{ yandex-cloud }} SAML certificate when creating the identity federation, download it now:

    {% list tabs group=instructions %}

    - {{ cloud-center }} UI {#cloud-center}

      1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}) with an administrator or organization owner account.
      1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.
      1. In the list that opens, select the identity federation of interest. In the **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** field, click ![ArrowDownToLine](../../_assets/console-icons/arrow-down-to-line.svg) **{{ ui-key.yacloud_org.page.federation.action.download-cert }}**.

          If you see ![TriangleExclamation](../../_assets/console-icons/triangle-exclamation.svg) to the left of ![ArrowDownToLine](../../_assets/console-icons/arrow-down-to-line.svg) **{{ ui-key.yacloud_org.page.federation.action.download-cert }}**, your current {{ yandex-cloud }} SAML certificate has either expired or is about to expire.

          [Download a re-issued {{ yandex-cloud }} SAML certificate and install it](./renew-yc-certificate.md) in your identity federation.

          {% note tip %}

          In the top-right corner, click ![pencil](../../_assets/console-icons/pencil.svg) **Update** to see the expiration date of your current SAML certificate. The date is stated in the **SAML certificate** section, under **Advanced**.

          Save your SAML certificate expiration date to your calendar. A few months before that date, you will need to [download the re-issued {{ yandex-cloud }} SAML certificate and install it](../../organization/operations/renew-yc-certificate.md) in your federation and on the IdP server.

          {% endnote %}

    {% endlist %}

1. Provide the downloaded {{ yandex-cloud }} SAML certificate to the IdP server. To learn how to do this, consult the identity provider's documentation or contact their support.

## Setting up a SAML application in your IdP {#configure-sso}

The process for setting up a SAML application is IdP-specific and may vary. Here, you can find the general requirements to a SAML message that the IdP server will send to {{ yandex-cloud }} following user authentication.

{% cut "Sample SAML message" %}

```xml
<samlp:Response ID="_bcdf7b6b-ea42-4191-8d5e-ebd4274acec6" Version="2.0" IssueInstant="2019-07-30T13:24:25.488Z"
 Destination="https://{{ auth-host }}/federations/bfbrotp6l1b2avhe1spu" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified"
  InResponseTo="19fb953133b313a86a001f2d387160e47f3e7aa0" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://example.org/auth</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_90cd8dcc-6105-4300-9ae4-f2c8c5aeb1e5" IssueInstant="2019-07-30T13:24:25.488Z"
   Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://example.org/auth</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
        <ds:Reference URI="#_90cd8dcc-6105-4300-9ae4-f2c8c5aeb1e5">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
          <ds:DigestValue>phUQR...</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>VACd7O...</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7j...</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID>user@example.org</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="19fb953133b313a86a001f2d387160e47f3e7aa0" NotOnOrAfter="2019-07-30T13:29:25.488Z" Recipient="https://{{ auth-host }}/federations/bfbrotp6l1b2avhe1spu" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2019-07-30T13:24:25.482Z" NotOnOrAfter="2019-07-30T14:24:25.482Z">
      <AudienceRestriction>
        <Audience>https://{{ auth-host }}/federations/bfbrotp6l1b2avhe1spu</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress">
        <AttributeValue>user@example.org</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
        <AttributeValue>First Name</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
        <AttributeValue>Last Name</AttributeValue>
      </Attribute>
    </AttributeStatement>
  </Assertion>
</samlp:Response>
```

{% endcut %}

Set up the SAML application for the message to contain:

* The ID from the SAML authentication request sent by {{ yandex-cloud }} in the `Response` and `SubjectConfirmationData` elements of the `InResponseTo` attribute.
* ACS URL in the following elements:
  
  * In `Response` of the `Destination` attribute.
  * In `SubjectConfirmationData` of the `Recipient` attribute.
  * `Audience`.

  {% cut "How to get the federation ID" %}

  {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

  {% endcut %}

  
  {% cut "How to get the federation ACS URL" %}

  {% include [get-acs-url](../../_includes/organization/get-acs-url.md) %}

  {% endcut %}


* User's unique ID was specified in the `NameID` element. We recommend using the User Principal Name (UPN) or email address as the ID.
* Your IdP's redirect URL for user authentication was specified in the `Issuer` element.
* Signed message was specified in the `SignatureValue` element, and the certificate used to sign the message, in the `KeyInfo` element.
* Make sure to specify the username and email address in the `AttributeStatement` element; this will enable the user to contact {{ yandex-cloud }} support from the [management console]({{ link-console-support }}).

For more information on how to set up a SAML application, consult you IdP's documentation or contact their support. You can also find examples of IdP-specific setups in our tutorials:

* [Active Directory](../tutorials/federations/integration-adfs.md).
* [Google Workspace](../tutorials/federations/integration-gworkspace.md).
* [{{ microsoft-idp.entra-id-full }}](../tutorials/federations/integration-azure.md).
* [Keycloak](../tutorials/federations/integration-keycloak.md).

## Setting up user attribute mapping {#claims-mapping}

After a user gets authenticated, the IdP server will send a SAML message to {{ yandex-cloud }} confirming successful authentication. The message contains various user attributes, such as the ID, name, email address, etc.

To correctly provide user information to {{ org-full-name }}, you need to set up mapping between SAML message attributes and the personal data stored with your IdP.

User data | Comment | SAML message elements
------------------- | ----------- | ----------------------
Unique user ID | Required attribute. We recommend using the User Principal Name (UPN) or email address. | `<NameID>`
Surname | Displayed in {{ yandex-cloud }} services.<br> Value length limit: {{ saml-limit-last-name }}. | `<Attribute>` with the following parameter:<br>`Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname"`
Name | Displayed in {{ yandex-cloud }} services.<br> Value length limit: {{ saml-limit-first-name }}. | `<Attribute>` with the following parameter:<br>`Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"`
Full name | Displayed in {{ yandex-cloud }} services.<br>Example: Ivan Ivanov.<br> Value length limit: {{ saml-limit-display-name }}. | `<Attribute>` with the following parameter:<br>`Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"`
Email | Used to send notifications from {{ yandex-cloud }} services.<br>Example: `ivanov@example.com`.<br> Value length limit: {{ saml-limit-email }}. | `<Attribute>` with the following parameter:<br>`Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"`
Phone | Used to send notifications from {{ yandex-cloud }} services.<br>Example: +71234567890.<br> Value length limit: {{ saml-limit-phone }}. | `<Attribute>` with the following parameter:<br>`Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mobilephone"`
Profile image | Displayed in {{ yandex-cloud }} services.<br>Images are transmitted in [Base64]({{ link-base64 }}) encoding.<br> Value length limit: {{ saml-limit-thumbnail-photo }}. | `<Attribute>` with the following parameter:<br>`Name="thumbnailPhoto"`
Group membership | Used for dynamic mapping of group members. | `<Attribute>` with the following parameter:<br>`Name="member"`

{% note info %}

The `thumbnailPhoto` attribute value exceeding the length limit is ignored. Other attribute values exceeding the limit are truncated.

{% endnote %}

## Testing authentication {#test-auth}

Test federated user authentication:

1. If you disabled the **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}** option in your federation, [add](add-account.md#add-user-sso) the users manually.

1. Open the browser in guest or incognito mode for a clean new user simulation.

1. Follow the URL to log in to the management console:

   ```url
   {{ link-console-main }}/federations/<federation_ID>
   ```

   {% cut "How to get the federation ID" %}

   {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

   {% endcut %}

   The browser will forward you to your IdP's authentication page.

1. Enter your authentication data. By default, you must enter the UPN and password.
1. Click **Sign in**.

1. On successful authentication, the IdP server will redirect you to the ACS URL you specified in the server settings, and from there to the management console home page.

Make sure you are logged in to the console as a federated user.
