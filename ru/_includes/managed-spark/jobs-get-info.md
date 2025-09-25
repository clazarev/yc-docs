{% list tabs group=instructions %}

- Консоль управления {#console}

    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-spark }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **{{ ui-key.yacloud.mdb.cluster.switch_jobs }}**.
    1. Нажмите на имя нужного задания.

- CLI {#cli}
    
    {% include [cli-install](../../_includes/cli-install.md) %}
    
    {% include [default-catalogue](../../_includes/default-catalogue.md) %}
    
    Чтобы получить информацию о задании:

    1. Посмотрите описание команды CLI для получения информации о задании:

        ```bash
        yc managed-spark job get --help
        ```
    
    1. Получите информацию о задании, выполнив команду:

        ```bash
        yc managed-spark job get <идентификатор_задания> \
          --cluster-id <идентификатор_кластера>
        ```

        Идентификатор кластера можно получить со [списком кластеров в каталоге](../../managed-spark/operations/cluster-list.md#list-clusters).

        Идентификатор задания можно получить со [списком заданий в кластере](#list).

- gRPC API {#grpc-api}

    1. [Получите IAM-токен для аутентификации в API](../../managed-spark/api-ref/authentication.md) и поместите токен в переменную среды окружения:

       {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Воспользуйтесь вызовом [JobService.Get](../../managed-spark/api-ref/grpc/Job/get.md) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/spark/v1/job_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                   "cluster_id": "<идентификатор_кластера>",
                   "job_id": "<идентификатор_задания>"
               }' \
            {{ api-host-spark }}:{{ port-https }} \
            yandex.cloud.spark.v1.JobService.Get
        ```

        Идентификатор кластера можно получить со [списком кластеров в каталоге](../../managed-spark/operations/cluster-list.md#list-clusters), идентификатор задания — со [списком заданий в кластере](#list).

    1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../../managed-spark/api-ref/grpc/Job/get.md#yandex.cloud.spark.v1.Job).

{% endlist %}
