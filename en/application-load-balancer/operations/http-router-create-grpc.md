---
title: How to create an HTTP router for gRPC traffic in {{ alb-full-name }}
description: In this tutorial, you will learn how to create an HTTP router for gRPC traffic.
---

# Creating an HTTP router for gRPC traffic

To create an [HTTP router](../concepts/http-router.md) and add a [route](../concepts/http-router.md#routes) to it:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to create your HTTP router.
  1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/route.svg) **{{ ui-key.yacloud.alb.label_http-routers }}**.
  1. Click **{{ ui-key.yacloud.alb.button_http-router-create }}**.
  1. Specify your HTTP router name.
  1. Under **{{ ui-key.yacloud.alb.label_virtual-hosts }}**, click **{{ ui-key.yacloud.alb.button_virtual-host-add }}**.
  1. Specify **{{ ui-key.yacloud.common.name }}**.
  1. In the **{{ ui-key.yacloud.alb.label_authority }}** field, enter: `*` or the [IP address](../../vpc/concepts/address.md) of the load balancer.
  1. Optionally, in the **{{ ui-key.yacloud.alb.label_security-profile-id }}** field, select the [{{ sws-full-name }}](../../smartwebsecurity/) [security profile](../../smartwebsecurity/concepts/profiles.md). A security profile allows you to enable WAF and filter incoming requests, limiting their number for protection against malicious attacks. For more information, see [{#T}](../../smartwebsecurity/concepts/profiles.md).


  1. Optionally, under **{{ ui-key.yacloud.alb.label_modifications }}**, click **{{ ui-key.yacloud.alb.button_add-modification }}** and configure the [HTTP header](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields).
     * In the **{{ ui-key.yacloud.alb.label_modification-type }}** field, select:
       * `{{ ui-key.yacloud.alb.label_header-request }}`: To modify the incoming request header, from client to load balancer.
       * `{{ ui-key.yacloud.alb.label_header-response }}`: To modify outgoing response header, from backend to external client.
     * In the **{{ ui-key.yacloud.alb.label_modification-header }}** field, give the header a name, e.g., `Host`, `User-Agent`, `X-Forwarded-For`, or `Strict-Transport-Security`.
     * In the **{{ ui-key.yacloud.alb.label_modification-operation }}** field, select:
       * `append`: To add a specified string to the header value.
       * `replace`: To replace the header value with a specified string.
       * `remove`: To remove the header. Both the header value and the header will be removed.
       * `rename`: To rename the header. The header value will not change.
     * Enter a string to modify the header value or a new header name.
  1. Click **{{ ui-key.yacloud.alb.button_add-route }}** and select **{{ ui-key.yacloud.alb.label_route-type }}**: `{{ ui-key.yacloud.alb.label_proto-grpc }}`.
     1. Specify **{{ ui-key.yacloud.common.name }}**.
     1. In the **{{ ui-key.yacloud.alb.label_fqmn }}** field, select one of the options:
        * `{{ ui-key.yacloud.alb.label_match-prefix }}`: Route all requests starting with a specific FQMN. In the input field, specify `/<first_word_in_service_name>`, e.g., `/helloworld`.
        * `{{ ui-key.yacloud.alb.label_match-exact }}`: Route all requests matching the specified FQMN.
        * `{{ ui-key.yacloud.alb.label_match-regex }}`: Route all requests matching the [RE2](https://github.com/google/re2/wiki/Syntax) [regular expression](https://en.wikipedia.org/wiki/Regular_expression).

        {% note warning %}

        The FQMN must start with a slash "`/`" and contain a part of the service name where your procedure call is redirected.

        {% endnote %}

     1. In the **{{ ui-key.yacloud.alb.label_route-action }}** field, select one of the options: `{{ ui-key.yacloud.alb.label_route-action-route }}` or `{{ ui-key.yacloud.alb.label_route-action-statusResponse }}`. Depending on the selected option:
        * `{{ ui-key.yacloud.alb.label_route-action-route }}`:
          * In the **{{ ui-key.yacloud.alb.label_backend-group }}** list, select a [backend group](../concepts/backend-group.md) from your HTTP router folder.
          * Optionally, in the **{{ ui-key.yacloud.alb.label_host-rewrite }}** field, select one of these options:
            * `none`: No rewriting.
            * `rewrite`: Rewrite to the specified value.
            * `auto`: Rewrite to the target [VM](../../compute/concepts/vm.md) address.
          * Optionally, in the **{{ ui-key.yacloud_billing.alb.label_max-timeout }}** field, specify the maximum connection time. You can specify a shorter timeout in the `grpc-timeout` request HTTP header.
          * Optionally, in the **{{ ui-key.yacloud.alb.label_idle-timeout }}** field, specify the connection inactivity timeout.
        * `{{ ui-key.yacloud.alb.label_route-action-statusResponse }}`:
          * In the **{{ ui-key.yacloud.alb.label_grpc-status-code }}** field, select the gRPC response code.
  1. Click **{{ ui-key.yacloud.common.create }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the [CLI](../../cli/) command for creating an HTTP router:

     ```bash
     yc alb http-router create --help
     ```

  1. Run this command:

     ```bash
     yc alb http-router create <HTTP_router_name>
     ```

     Result:

     ```text
     id: a5dcsselagj4********
     name: <HTTP_router_name>
     folder_id: aoerb349v3h4********
     created_at: "2022-06-16T21:04:59.438292069Z"
     ```

  1. See the description of the CLI command for creating a virtual host:

     ```bash
     yc alb virtual-host create --help
     ```

  1. To create a virtual host, run this command with the HTTP router name and virtual host settings specified:

     ```bash
     yc alb virtual-host create <virtual_host_name> \
       --http-router-name <HTTP_router_name> \
       --authority * \
       --rate-limit rps=100,all-requests \
       --security-profile-id <security_profile_ID>
     ```

     Where:
     * `--http-router-name`: HTTP router name.
     * `--authority`: `:authority` header domains associated with this virtual host. You can use wildcards, e.g., `*.foo.com` or `*-bar.foo.com`.
     * `--rate-limit`: Request rate limit. This is an optional setting.
       * `rps` or `rpm`: Number of allowed incoming requests per second or minute.
       * `all-requests`: Limits all incoming requests.
       * `requests-per-ip`: Limits the total number of requests per IP address. That is, for each IP address, only the specified number of requests is allowed per unit of time.
     * `--security-profile-id`: [{{ sws-full-name }}](../../smartwebsecurity/) [security profile](../../smartwebsecurity/concepts/profiles.md) ID. This is an optional setting. A security profile allows you to enable WAF and filter incoming requests, limiting their number for protection against malicious attacks. For more information, see [{#T}](../../smartwebsecurity/concepts/profiles.md).


     Result:

     ```text
     done (1s)
     name: <virtual_host_name>
     authority:
       - *
     rate_limit:
       all_requests:
         per_second: "100"
     ```

  1. See the description of the CLI command for adding a route:

     ```bash
     yc alb virtual-host append-grpc-route --help
     ```

  1. To add a route, run this command with the HTTP router ID / name and routing settings specified:

     ```bash
     yc alb virtual-host append-grpc-route <route_name> \
       --virtual-host-name <virtual_host_name> \
       --http-router-name <HTTP_router_name> \
       --prefix-fqmn-match / \
       --backend-group-name <backend_group_name> \
       --request-max-timeout 60s \
       --rate-limit rps=50,requests-per-ip \
       --disable-security-profile=<disable_security_profile>
     ```

     Where:
     * `--virtual-host-name`: Virtual host name.
     * `--http-router-name`: HTTP router name.
     * `--prefix-fqmn-match`: Route all requests with a specific prefix. Add `FQMN` `/` after this option.

       You can also use these options for specifying routing conditions:
       * `--exact-fqmn-match`: Route all requests matching the specified FQMN. Add `/<FQMN>/` after this option.
       * `--regex-fqmn-match`: Route all requests matching the [RE2](https://github.com/google/re2/wiki/Syntax) [regular expression](https://en.wikipedia.org/wiki/Regular_expression). Add `/<regular_expression>` after this option.
     * `--backend-group-name`: [Backend group](../concepts/backend-group.md) name.
     * `--rate-limit`: Request rate limit.
     * `--request-max-timeout`: Maximum request timeout in seconds. You can specify a shorter timeout in the `grpc-timeout` request HTTP header.
     * `--disable-security-profile`: Flag to disable the security profile. The default value is `false`. This is an optional parameter.

     For more information about `yc alb virtual-host append-grpc-route` options, see this [CLI reference](../../cli/cli-ref/application-load-balancer/cli-ref/virtual-host/append-grpc-route.md).

     Result:

     ```text
     done (1s)
     name: <virtual_host_name>
     authority:
     - *
     routes:
     - name: <route_name>
       grpc:
        match:
          fqmn:
           prefix_match: /helloworld
        route:
          backend_group_id: ds7snban2dvn********
          max_timeout: 60s
       disable_security_profile: true
     ```

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. In the configuration file, specify your HTTP router and virtual host settings:

     ```hcl
     resource "yandex_alb_http_router" "tf-router" {
       name          = "<HTTP_router_name>"
       labels        = {
         tf-label    = "tf-label-value"
         empty-label = ""
       }
     }

     resource "yandex_alb_virtual_host" "my-virtual-host" {
       name           = "<virtual_host_name>"
       http_router_id = yandex_alb_http_router.tf-router.id
       
       rate_limit {
         all_requests {
           per_second = <requests_per_second>
           # or per_minute = <requests_per_minute>
         }
         requests_per_ip {
           per_second = <requests_per_second>
           # or per_minute = <requests_per_minute>
         }
       }
       
       route {
         name                     = "<route_name>"
         disable_security_profile = <disable_security_profile>
         grpc_route {
           grpc_route_action {
             backend_group_id = "<backend_group_ID>"
             max_timeout      = "<timeout>"
           }
         }
       }
       
       route_options {
         security_profile_id = "<security_profile_ID>"
       }
     }
     ```

     Where:
     * `yandex_alb_http_router`: HTTP router description.
       * `name`: HTTP router name. Follow these naming requirements:

         {% include [name-format](../../_includes/name-format.md) %}

       * `labels`: HTTP router [labels](../../resource-manager/concepts/labels.md). Specify a key-value pair.
     * `yandex_alb_virtual_host`: Virtual host description:
       * `name`: Virtual host name. Follow these naming requirements:

         {% include [name-format](../../_includes/name-format.md) %}

       * `http_router_id`: HTTP router ID.
       * `rate_limit`: Optionally, rate limit for the whole virtual host.
         * `all_requests`: Optionally, limit on all requests per second or per minute:
           * `per_second`: Per second.
           * `per_minute`: Per minute.
         * `requests_per_ip`: Optionally, additionally limits requests for each IP address per second or per minute:
           * `per_second`: Per second.
           * `per_minute`: Per minute.
       * `route`: Route description:
         * `name`: Route name.
         * `disable_security_profile`: Optional flag to disable the [security profile](../../smartwebsecurity/concepts/profiles.md) in [{{ sws-full-name }}](../../smartwebsecurity/). The default value is `false`.
         * `grpc_route`: Route description for gRPC traffic:
           * `grpc_route_action`: Action applied to gRPC traffic.
             * `backend_group_id`: [Backend group](../concepts/backend-group.md) ID.
             * `max_timeout`: Maximum request timeout in seconds. You can specify a shorter timeout in the `grpc-timeout` request HTTP header.
       * `route_options`: Additional virtual host parameters (optional):
         * `security_profile_id`: Security profile ID. A security profile allows you to enable WAF and filter incoming requests, limiting their number for protection against malicious attacks. For more information, see [{#T}](../../smartwebsecurity/concepts/profiles.md).


     Learn more about the properties of {{ TF }} resources in the relevant {{ TF }} guides:
     * [yandex_alb_http_router]({{ tf-provider-resources-link }}/alb_http_router) resource
     * [yandex_alb_virtual_host]({{ tf-provider-resources-link }}/alb_virtual_host) resource
  1. Create the resources

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     {{ TF }} will create all the required resources. You can check your new resources and their settings in the [management console]({{ link-console-main }}) or using this [CLI](../../cli/) command:

     ```bash
     yc alb http-router get <HTTP_router_name>
     ```

- API {#api}

  Use the [create](../api-ref/HttpRouter/create.md) REST API method for the [HttpRouter](../api-ref/HttpRouter/index.md) resource or the [HttpRouterService/Create](../api-ref/grpc/HttpRouter/create.md) gRPC API call.

{% endlist %}