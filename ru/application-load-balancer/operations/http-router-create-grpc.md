---
title: Как создать HTTP-роутер для gRPC-трафика в {{ alb-full-name }}
description: Следуя данной инструкции, вы сможете создать HTTP-роутер для gRPC-трафика.
---

# Создать HTTP-роутер для gRPC-трафика

Чтобы создать [HTTP-роутер](../concepts/http-router.md) и добавить в него [маршрут](../concepts/http-router.md#routes):

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором будет создаваться HTTP-роутер.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. На панели слева выберите ![image](../../_assets/console-icons/route.svg) **{{ ui-key.yacloud.alb.label_http-routers }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.alb.button_http-router-create }}**.
  1. Введите имя HTTP-роутера.
  1. В блоке **{{ ui-key.yacloud.alb.label_virtual-hosts }}** нажмите кнопку **{{ ui-key.yacloud.alb.button_virtual-host-add }}**.
  1. {% include [console-name-vh](../../_includes/application-load-balancer/instruction-steps/console-name-vh.md) %}
  1. (Опционально) В поле **{{ ui-key.yacloud.alb.label_authority }}** введите: `*` или [IP-адрес](../../vpc/concepts/address.md) балансировщика.

      {% include [console-no-authority-notice](../../_includes/application-load-balancer/instruction-steps/console-no-authority-notice.md) %}
  1. {% include [console-vh-form-sws-step](../../_includes/application-load-balancer/instruction-steps/console-vh-form-sws-step.md) %}
  1. {% include [console-vh-form-albnative-rl-step](../../_includes/application-load-balancer/instruction-steps/console-vh-form-albnative-rl-step.md) %}
  1. {% include [console-vh-form-header-mods-step](../../_includes/application-load-balancer/instruction-steps/console-vh-form-header-mods-step.md) %}
  1. Нажмите кнопку **{{ ui-key.yacloud.alb.button_add-route }}** и в открывшейся форме **{{ ui-key.yacloud.alb.label_new-route }}**:

      1. {% include [console-update-http-route-naming-step](../../_includes/application-load-balancer/instruction-steps/console-update-http-route-naming-step.md) %}
      1. {% include [console-update-grpc-route-ss1](../../_includes/application-load-balancer/instruction-steps/console-update-grpc-route-ss1.md) %}
      1. {% include [console-update-grpc-route-ss2](../../_includes/application-load-balancer/instruction-steps/console-update-grpc-route-ss2.md) %}
      1. {% include [console-update-grpc-route-ss3](../../_includes/application-load-balancer/instruction-steps/console-update-grpc-route-ss3.md) %}

  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды [CLI](../../cli/) для создания HTTP-роутера:

      ```bash
      yc alb http-router create --help
      ```
  1. Выполните команду:

      ```bash
      yc alb http-router create <имя_HTTP-роутера>
      ```

      Результат:

      ```text
      id: a5dcsselagj4********
      name: <имя_HTTP-роутера>
      folder_id: aoerb349v3h4********
      created_at: "2022-06-16T21:04:59.438292069Z"
      ```
  1. {% include [cli-vh-create-help](../../_includes/application-load-balancer/instruction-steps/cli-vh-create-help.md) %}
  1. {% include [cli-vh-create](../../_includes/application-load-balancer/instruction-steps/cli-vh-create.md) %}
  1. {% include [cli-vh-append-grpc-route-help](../../_includes/application-load-balancer/instruction-steps/cli-vh-append-grpc-route-help.md) %}
  1. {% include [cli-vh-append-grpc-route-code](../../_includes/application-load-balancer/instruction-steps/cli-vh-append-grpc-route-code.md) %}

      Где:

      {% include [cli-grpc-route-creation-legend](../../_includes/application-load-balancer/instruction-steps/cli-grpc-route-creation-legend.md) %}

      {% include [cli-vh-append-grpc-route-output](../../_includes/application-load-balancer/instruction-steps/cli-vh-append-grpc-route-output.md) %}

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Опишите в конфигурационном файле параметры HTTP-роутера и виртуального хоста:

      ```hcl
      resource "yandex_alb_http_router" "tf-router" {
        name          = "<имя_HTTP-роутера>"
        labels        = {
          tf-label    = "tf-label-value"
          empty-label = ""
        }
      }

      resource "yandex_alb_virtual_host" "my-virtual-host" {
        name           = "<имя_виртуального_хоста>"
        http_router_id = yandex_alb_http_router.tf-router.id

        rate_limit {
          all_requests {
            per_second = <количество_запросов_в_секунду>
            # или per_minute = <количество_запросов_в_минуту>
          }
          requests_per_ip {
            per_second = <количество_запросов_в_секунду>
            # или per_minute = <количество_запросов_в_минуту>
          }
        }

        route {
          name                      = "<имя_маршрута>"
          disable_security_profile  = true|false

          grpc_route {
            grpc_match {
              fqmn {
                prefix = "/<префикс_FQMN_запроса>"
                # или exact = "<FQMN_запроса>"
                # или regex = "<регулярное_выражение>"
              }
            }

            grpc_route_action {
              backend_group_id  = "<идентификатор_группы_бэкендов>"
              host_rewrite      = "<значение_заголовка_Host>"
              max_timeout       = "<таймаут_соединения>s"
              idle_timeout      = "<таймаут-простоя>s"
              rate_limit {
                all_requests {
                  per_second = <количество_запросов_в_секунду>
                  # или per_minute = <количество_запросов_в_минуту>
                }
                requests_per_ip {
                  per_second = <количество_запросов_в_секунду>
                  # или per_minute = <количество_запросов_в_минуту>
                }
              }
            }
          }
        }

        authority        = ["<домен_1>","<домен_2>",...,"<домен_n>"]

        modify_request_headers {
          name           = "<имя_заголовка>"
          append         = "<добавляемая_в_содержимое_заголовка_строка>"
          # или replace  = "<новое_содержимое_заголовка>"
          # или remove   = true|false
        }

        modify_response_headers {
          name           = "<имя_заголовка>"
          append         = "<добавляемая_в_содержимое_заголовка_строка>"
          # или replace  = "<новое_содержимое_заголовка>"
          # или remove   = true|false
        }

        route_options {
          security_profile_id = "<идентификатор_профиля_безопасности>"
        }
      }
      ```

      Где:
      * `yandex_alb_http_router` — описание HTTP-роутера:
          * `name` — имя HTTP-роутера. Формат имени:

              {% include [name-format](../../_includes/name-format.md) %}

          * `labels` — [метки](../../resource-manager/concepts/labels.md) для HTTP-роутера. Укажите пару ключ — значение.
      * `yandex_alb_virtual_host` — описание виртуального хоста:

          {% include [tf-vh-create-legend-part1](../../_includes/application-load-balancer/instruction-steps/tf-vh-create-legend-part1.md) %}

          * `route` — описание маршрута виртуального хоста:

              {% include [tf-vh-create-legend-route-general](../../_includes/application-load-balancer/instruction-steps/tf-vh-create-legend-route-general.md) %}

              {% include [tf-vh-create-legend-route-grpc](../../_includes/application-load-balancer/instruction-steps/tf-vh-create-legend-route-grpc.md) %}

          {% include [tf-vh-create-legend-last-part](../../_includes/application-load-balancer/instruction-steps/tf-vh-create-legend-last-part.md) %}

      Более подробную информацию о параметрах используемых ресурсов в {{ TF }} см. в документации провайдера:
      * Ресурс [yandex_alb_http_router]({{ tf-provider-resources-link }}/alb_http_router).
      * Ресурс [yandex_alb_virtual_host]({{ tf-provider-resources-link }}/alb_virtual_host).
  1. Создайте ресурсы

      {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

      {{ TF }} создаст все требуемые ресурсы. Проверить появление ресурсов и их настройки можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../cli/):

      ```bash
      yc alb http-router get <имя_HTTP-роутера>
      ```

      {% include [Terraform timeouts](../../_includes/application-load-balancer/terraform-timeout-router-and-host.md) %}

- API {#api}

  Воспользуйтесь методом REST API [create](../api-ref/HttpRouter/create.md) для ресурса [HttpRouter](../api-ref/HttpRouter/index.md) или вызовом gRPC API [HttpRouterService/Create](../api-ref/grpc/HttpRouter/create.md).

{% endlist %}