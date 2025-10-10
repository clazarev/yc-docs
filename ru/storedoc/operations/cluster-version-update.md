# Обновление версии {{ SD }}

Вы можете обновить кластер {{ mmg-name }} только на следующую версию относительно текущей, например, с версии 4.2 на 4.4. Обновление до более поздних версий производится поэтапно. Например, обновление версии {{ SD }} с 4.2 до 8.0 выполняется в такой последовательности: 4.2 → 4.4 → 5.0 → 6.0 → 7.0 → 8.0. Перед каждым этапом обновления кластера необходимо обновлять [версию совместимости кластера](#compatibility-update).


{% note alert %}

После обновления невозможно вернуть кластер к предыдущей версии.

{% endnote %}

## Перед обновлением версии {#before-update}

1. Убедитесь, что это не нарушит работу ваших приложений:

1. Посмотрите в истории изменений {{ MG }}, как обновления могут повлиять на работу ваших приложений.
1. Попробуйте обновить версию на тестовом кластере. Его можно [развернуть из резервной копии](cluster-backups.md#restore) основного кластера.
1. [Создайте резервную копию](cluster-backups.md#create-backup) основного кластера непосредственно перед обновлением версии.

1. Убедитесь, что [версия совместимости кластера](#compatibility-update) соответствует текущей версии кластера. При необходимости обновите версию совместимости кластера.

## Обновить версию кластера {#start-update}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **Yandex StoreDoc**.
  1. Выберите нужный кластер в списке и нажмите кнопку **{{ ui-key.yacloud.mdb.clusters.button_action-edit }}**.
  1. В поле **{{ ui-key.yacloud.mdb.forms.base_field_version }}** выберите номер новой версии.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

  После запуска обновления кластер переходит в статус **UPDATING**. Дождитесь окончания операции и проверьте версию кластера.

- CLI {#cli}

  1. Получите список ваших кластеров {{ SD }} командой:

     ```bash
     {{ yc-mdb-mg }} cluster list
     ```

  1. Получите информацию о нужном кластере и проверьте версию {{ SD }}, указанную в свойстве `config.version`:

     ```bash
     {{ yc-mdb-mg }} cluster get <имя_или_идентификатор_кластера>
     ```

  1. Запустите обновление {{ SD }}:

     ```bash
     {{ yc-mdb-mg }} cluster update <имя_или_идентификатор_кластера> \
        --mongodb-version=<номер_новой_версии>
     ```

     После запуска обновления кластер переходит в статус **UPDATING**. Дождитесь окончания операции и проверьте версию кластера.

  1. После обновления все возможности {{ SD }}, у которых нет обратной совместимости с прежней версией, выключаются. Чтобы снять это ограничение, выполните команду:

     ```bash
     {{ yc-mdb-mg }} cluster update <имя_или_идентификатор_кластера> \
        --feature-compatibility-version=<номер_новой_версии>
     ```


- {{ TF }} {#tf}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.
  
       О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).
  
    1. Добавьте к описанию кластера {{ mmg-name }} поле `version` или измените его значение, если оно уже существует:
  
       ```hcl
       resource "yandex_mdb_mongodb_cluster" "<имя_кластера>" {
         ...
         cluster_config {
           version = "<версия_Yandex_StoreDoc>"
         }
       }
       ```

    1. Проверьте корректность настроек.
  
         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}
  
    1. Подтвердите изменение ресурсов.
  
         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}
  
   Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_mongodb_cluster).

   {% include [Terraform timeouts](../../_includes/mdb/mmg/terraform/timeouts.md) %}

- REST API {#api}

   1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

   1. Воспользуйтесь методом [Cluster.Update](../api-ref/Cluster/update.md) и выполните запрос, например с помощью {{ api-examples.rest.tool }}:

      {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

      ```bash
      curl \
         --request PATCH \
         --header "Authorization: Bearer $IAM_TOKEN" \
         --header "Content-Type: application/json" \
         --url 'https://{{ api-host-mdb }}/managed-mongodb/v1/clusters/<идентификатор_кластера>' \
         --data '{
                  "updateMask": "configSpec.version",
                  "configSpec": {
                    "version": "<новая_версия_Yandex_StoreDoc>"
                  }
                }'
      ```

      Где:

      * `updateMask` — перечень изменяемых параметров в одну строку через запятую.

         В данном случае передается один параметр.

      * `configSpec.version` — новая версия {{ SD }}.

      Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

   1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/update.md#yandex.cloud.operation.Operation).

- gRPC API {#grpc-api}

    1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Воспользуйтесь вызовом [ClusterService.Update](../api-ref/grpc/Cluster/update.md) и выполните запрос, например с помощью {{ api-examples.grpc.tool }}:

        {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/mongodb/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<идентификатор_кластера>",
                  "update_mask": {
                    "paths": [ 
                      "config_spec.version"
                    ]
                  },  
                  "config_spec": {
                    "version": "<версия_Yandex_StoreDoc>"
                  }
               }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.mongodb.v1.ClusterService.Update
        ```

        Где:

        * `update_mask` — перечень изменяемых параметров в виде массива строк `paths[]`.

          В данном случае передается один параметр.

        * `version` — новая версия {{ SD }}.

        Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

    1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/Cluster/update.md#yandex.cloud.operation.Operation).

{% endlist %}

{% note info %}

После обновления все возможности {{ SD }}, у которых нет обратной совместимости с прежней версией, выключаются. Чтобы снять это ограничение, [обновите версию совместимости кластера](#compatibility-update).

{% endnote %}

## Обновить версию совместимости {#compatibility-update}

Обновление версии совместимости кластера требуется, если вам нужно:
* обновить кластер до новой версии, при этом версия совместимости не соответствует текущей версии кластера;
* сделать доступными новые возможности {{ SD }} после обновления кластера.

Подробнее об обратной совместимости читайте в [документации {{ MG }}](https://docs.mongodb.com/manual/reference/command/setFeatureCompatibilityVersion/).

{% note alert %}

После обновления версии совместимости невозможно вернуть кластер к предыдущей версии совместимости.

{% endnote %}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **Yandex StoreDoc**.
  1. Выберите нужный кластер в списке и нажмите кнопку **{{ ui-key.yacloud.mdb.clusters.button_action-edit }}**.
  1. В поле **{{ ui-key.yacloud.mdb.forms.base_field_fcv }}** выберите номер нужной версии.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

  1. Получите список ваших кластеров {{ SD }} командой:

     ```bash
     {{ yc-mdb-mg }} cluster list
     ```

  1. Получите информацию о нужном кластере и проверьте версию {{ SD }}, указанную в свойстве `config.feature_compatibility_version`:

     ```bash
     {{ yc-mdb-mg }} cluster get <имя_или_идентификатор_кластера>
     ```

  1. Выполните команду изменения кластера, указав новую версию совместимости:

     ```bash
     {{ yc-mdb-mg }} cluster update <имя_или_идентификатор_кластера> \
        --feature-compatibility-version=<версия_совместимости_Yandex_StoreDoc>
     ```

- {{ TF }} {#tf}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.
  
       О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).
  
    1. Добавьте к описанию кластера {{ mmg-name }} поле `feature_compatibility_version` или измените его значение, если оно уже существует:
  
       ```hcl
       resource "yandex_mdb_mongodb_cluster" "<имя_кластера>" {
         ...
         cluster_config {
           feature_compatibility_version = "<версия_совместимости_Yandex_StoreDoc>"
         }
       }
       ```

    1. Проверьте корректность настроек.
  
         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}
  
    1. Подтвердите изменение ресурсов.
  
         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}
  
   Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_mongodb_cluster).

   {% include [Terraform timeouts](../../_includes/mdb/mmg/terraform/timeouts.md) %}

- REST API {#api}

   1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

   1. Воспользуйтесь методом [Cluster.Update](../api-ref/Cluster/update.md) и выполните запрос, например с помощью {{ api-examples.rest.tool }}:

      {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

      ```bash
      curl \
         --request PATCH \
         --header "Authorization: Bearer $IAM_TOKEN" \
         --header "Content-Type: application/json" \
         --url 'https://{{ api-host-mdb }}/managed-mongodb/v1/clusters/<идентификатор_кластера>' \
         --data '{
                  "updateMask": "configSpec.featureCompatibilityVersion",
                  "configSpec": {
                    "featureCompatibilityVersion": "<версия_совместимости_Yandex_StoreDoc>"
                  }
                }'
      ```

      Где:

      * `updateMask` — перечень изменяемых параметров в одну строку через запятую.

         В данном случае передается один параметр.

      * `configSpec.featureCompatibilityVersion` — новая версия совместимости {{ SD }}.

      Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

   1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/update.md#yandex.cloud.operation.Operation).

- gRPC API {#grpc-api}

    1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Воспользуйтесь вызовом [ClusterService.Update](../api-ref/grpc/Cluster/update.md) и выполните запрос, например с помощью {{ api-examples.grpc.tool }}:

        {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/mongodb/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<идентификатор_кластера>",
                  "update_mask": {
                    "paths": [ 
                      "config_spec.feature_compatibility_version"
                    ]
                  },  
                  "config_spec": {
                    "feature_compatibility_version": "<версия_совместимости_Yandex_StoreDoc>"
                  }
               }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.mongodb.v1.ClusterService.Update
        ```

        Где:

        * `update_mask` — перечень изменяемых параметров в виде массива строк `paths[]`.

          В данном случае передается один параметр.

        * `feature_compatibility_version` — новая версия совместимости {{ SD }}.

        Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

    1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/Cluster/update.md#yandex.cloud.operation.Operation).

{% endlist %}

## Примеры {#examples}

Допустим, нужно обновить кластер с версии 5.0 до версии 6.0.

{% list tabs group=instructions %}

- CLI {#cli}

   1. Чтобы узнать идентификатор кластера, получите список всех кластеров в каталоге:

      ```bash
      {{ yc-mdb-mg }} cluster list
      ```

      Результат:

      ```text
      +----------------------+---------------+---------------------+--------+---------+
      |          ID          |     NAME      |     CREATED AT      | HEALTH | STATUS  |
      +----------------------+---------------+---------------------+--------+---------+
      | c9q8p8j2gaih******** |   storedoc406  | 2019-04-23 12:44:17 | ALIVE  | RUNNING |
      +----------------------+---------------+---------------------+--------+---------+
      ```

   1. Чтобы получить информацию о кластере `c9qut3k64b2o********`, выполните команду:

      ```bash
      {{ yc-mdb-mg }} cluster get c9qut3k64b2o********
      ```

      Результат:

      ```text
      id: c9qut3k64b2o********
      folder_id: b1g0itj57rbj********
      created_at: "2019-07-16T09:43:50.393231Z"
      name: storedoc406
      environment: PRODUCTION
      monitoring:
      - name: Console
        description: Console charts
        link: {{ link-console-main }}/folders/b1g0itj57rbj********/managed-mongodb/cluster/c9qut3k64b2o********?section=monitoring
      config:
        version: "5.0"
        feature_compatibility_version: "5.0"
        ...
      ```

   1. Чтобы обновить кластер `c9qutgkd4b2o********` до версии 6.0, выполните команду:

      ```bash
      {{ yc-mdb-mg }} cluster update c9qutgkd4b2o******** \
          --mongodb-version=6.0
      ```

   1. Чтобы включить все возможности версии 6.0 в кластере `c9qutgkd4b2o********`, выполните команду:

      ```bash
      {{ yc-mdb-mg }} cluster update c9qutgkd4b2o******** \
          --feature-compatibility-version=6.0
      ```

{% endlist %}
