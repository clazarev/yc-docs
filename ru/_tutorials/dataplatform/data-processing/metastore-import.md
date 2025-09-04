# Перенос метаданных между кластерами {{ dataproc-name }} с помощью {{ metastore-name }}


Вы можете перенести метаданные между [кластерами {{ dataproc-full-name }}](../../../data-proc/concepts/index.md) с СУБД Hive. Сначала метаданные экспортируются из одного кластера, затем импортируются в другой с помощью [{{ metastore-full-name }}](../../../metadata-hub/concepts/metastore.md).

Чтобы перенести метаданные между кластерами {{ dataproc-name }}:

1. [Создайте тестовую таблицу](#create-table).
1. [Экспортируйте данные](#export-data).
1. [Подключите {{ dataproc-name }} к {{ metastore-name }}](#connect).
1. [Импортируйте данные](#import-data).
1. [Проверьте результат](#check-result).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).

{% note warning %}

{% include [connect-metastore-to-s3-with-policy](../../_tutorials_includes/metastore-to-s3-with-policy.md) %}

{% endnote %}

{% note info %}

{{ metastore-name }} находится на стадии [Preview](../../../overview/concepts/launch-stages.md).

{% endnote %}

## Необходимые платные ресурсы {#paid-resources}

В стоимость поддержки инфраструктуры входит:

* плата за вычислительные ресурсы кластера {{ dataproc-name }} и объем хранилища (см. [тарифы {{ dataproc-name }}](../../../data-proc/pricing.md));
* плата за хранение данных и операции с данными в бакете (см. [тарифы {{ objstorage-full-name }}](../../../storage/pricing.md));
* плата за использование NAT-шлюза и исходящий через шлюз трафик (см. [тарифы {{ vpc-full-name }}](../../../vpc/pricing.md#nat-gateways)).

## Перед началом работы {#before-you-begin}

Подготовьте инфраструктуру:

{% list tabs group=instructions %}

- Вручную {#manual}

    1. [Создайте сервисный аккаунт](../../../iam/operations/sa/create.md) `dataproc-s3-sa` и назначьте ему роли `dataproc.agent`, `dataproc.provisioner`, `managed-metastore.integrationProvider` и `storage.uploader`.
    1. В {{ objstorage-full-name }} [создайте бакет](../../../storage/operations/buckets/create.md) `dataproc-bucket` и предоставьте сервисному аккаунту разрешение `READ и WRITE` для этого бакета.
    1. [Создайте облачную сеть](../../../vpc/operations/network-create.md) `dataproc-network`.
    1. В этой сети [создайте подсеть](../../../vpc/operations/subnet-create.md) `dataproc-subnet`.
    1. [Настройте NAT-шлюз](../../../vpc/operations/create-nat-gateway.md) для созданной подсети.
    1. [Создайте группу безопасности](../../../vpc/operations/security-group-create.md) `dataproc-security-group` со следующими правилами:

        {% cut "Правила группы безопасности" %}

        #|
        || **Для какого сервиса нужно правило** | **Зачем нужно правило** | **Настройки правила** ||
        || {{ dataproc-name }} | Для входящего служебного трафика.
        |
        * Диапазон портов — `{{ port-any }}`.
        * Протокол — `Любой` (`Any`).
        * Источник — `Группа безопасности`.
        * Группа безопасности — `Текущая` (`Self`). ||
        || {{ dataproc-name }} | Для входящего трафика, чтобы разрешить доступ к NTP-серверам для синхронизации времени.
        |
        * Диапазон портов — `123`.
        * Протокол — `UDP`.
        * Источник — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        || {{ dataproc-name }} | Для входящего трафика, чтобы из интернета подключаться по SSH к хостам подкластеров с публичным доступом.
        |
        * Диапазон портов — `22`.
        * Протокол — `TCP`.
        * Источник — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        || {{ metastore-name }} | Для входящего трафика от клиентов.
        |
        * Диапазон портов — `30000-32767`.
        * Протокол — `Любой` (`Any`).
        * Источник — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        || {{ metastore-name }} | Для входящего трафика от балансировщика.
        |
        * Диапазон портов — `10256`.
        * Протокол — `Любой` (`Any`).
        * Источник — `Проверки состояния балансировщика`. ||
        || {{ dataproc-name }} | Для исходящего служебного трафика.
        |
        * Диапазон портов — `{{ port-any }}`.
        * Протокол — `Любой` (`Any`).
        * Источник — `Группа безопасности`.
        * Группа безопасности — `Текущая` (`Self`). ||
        || {{ dataproc-name }} | Для исходящего HTTPS-трафика.
        |
        * Диапазон портов — `{{ port-https }}`.
        * Протокол — `TCP`.
        * Назначение — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        || {{ dataproc-name }} | Для исходящего трафика, чтобы разрешить доступ к NTP-серверам для синхронизации времени.
        |
        * Диапазон портов — `123`.
        * Протокол — `UDP`.
        * Источник — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        || {{ dataproc-name }} | Для исходящего трафика, чтобы разрешить подключение кластера {{ dataproc-name }} к {{ metastore-name }}.
        |
        * Диапазон портов — `{{ port-metastore }}`.
        * Протокол — `Любой` (`Any`).
        * Источник — `CIDR`.
        * CIDR блоки — `0.0.0.0/0`. ||
        |#

        {% endcut %}

    1. [Создайте два кластера {{ dataproc-name }}](../../../data-proc/operations/cluster-create.md) с именами `dataproc-source` и `dataproc-target` и следующими настройками:

        * **{{ ui-key.yacloud.mdb.forms.base_field_environment }}** — `PRODUCTION`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_services }}**:

            * `HDFS`
            * `HIVE`
            * `SPARK`
            * `YARN`
            * `ZEPPELIN`

        * **{{ ui-key.yacloud.mdb.forms.base_field_service-account }}** — `dataproc-s3-sa`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_zone }}** — та, в которой находится подсеть `dataproc-subnet`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_properties }}** — `spark:spark.sql.hive.metastore.sharedPrefixes` со значением `com.amazonaws,ru.yandex.cloud`. Нужно для выполнения заданий PySpark и для интеграции с {{ metastore-name }}.
        * **{{ ui-key.yacloud.mdb.forms.config_field_bucket }}** — `dataproc-bucket`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_network }}** — `dataproc-network`.
        * **{{ ui-key.yacloud.mdb.forms.field_security-group }}** — `dataproc-security-group`.
        * **{{ ui-key.yacloud.mdb.forms.config_field_ui_proxy }}** — включен.
        * **{{ ui-key.yacloud.mdb.forms.host_column_subnetwork }}** у подкластеров {{ dataproc-name }} — `dataproc-subnet`.
        * **{{ ui-key.yacloud.mdb.forms.field_assign-public-ip }}** у хоста-мастера — включен.

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}
    1. Скачайте в ту же рабочую директорию файл конфигурации [metastore-import.tf](https://github.com/yandex-cloud-examples/yc-data-proc-metadata-import/blob/main/metastore-import.tf).

        В этом файле описаны:

        * [сеть](../../../vpc/concepts/network.md#network);
        * [NAT-шлюз](../../../vpc/concepts/gateways.md) и таблица маршрутизации, необходимые для работы {{ dataproc-name }};
        * [подсеть](../../../vpc/concepts/network.md#subnet);
        * [группа безопасности](../../../vpc/concepts/security-groups.md) для {{ dataproc-name }} и {{ metastore-name }};
        * [сервисный аккаунт](../../../iam/concepts/users/service-accounts.md), необходимый для работы кластера {{ dataproc-name }};
        * сервисный аккаунт, необходимый для создания бакета {{ objstorage-name }};
        * [статический ключ доступа](../../../iam/concepts/authorization/access-key.md) для создания [бакета {{ objstorage-full-name }}](../../../storage/concepts/bucket.md);
        * бакет;
        * два кластера {{ dataproc-name }}.

    1. Укажите в файле `metastore-import.tf`:

        * `folder_id` — идентификатор облачного каталога (такой же, как в настройках провайдера).
        * `dp_ssh_key` — абсолютный путь к публичному ключу для кластеров {{ dataproc-name }}. [Подробнее о подключении к хосту {{ dataproc-name }} по SSH](../../../data-proc/operations/connect-ssh.md).

    1. Проверьте корректность файлов конфигурации {{ TF }} с помощью команды:

        ```bash
        terraform validate
        ```

        Если в файлах конфигурации есть ошибки, {{ TF }} на них укажет.

    1. Создайте необходимую инфраструктуру:

        {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Создайте тестовую таблицу {#create-table}

В кластере `dataproc-source` создайте тестовую таблицу `countries`:

1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-proc }}**.
1. Откройте страницу кластера `dataproc-source`.
1. Перейдите по ссылке **Zeppelin Web UI** в разделе **{{ ui-key.yacloud.mdb.cluster.overview.section_ui-proxy }}**.
1. Выберите **Notebook**, затем ![image](../../../_assets/console-icons/plus.svg) **Create new note**.
1. В появившемся окне укажите название записи и нажмите кнопку **Create**.
1. Чтобы выполнить PySpark-задание, вставьте скрипт Python в строку ввода:

    ```python
    %pyspark

    from pyspark.sql.types import *

    schema = StructType([StructField('Name', StringType(), True),
    StructField('Capital', StringType(), True),
    StructField('Area', IntegerType(), True),
    StructField('Population', IntegerType(), True)])

    df = spark.createDataFrame([('Австралия', 'Канберра', 7686850, 19731984), ('Австрия', 'Вена', 83855, 7700000)], schema)
    df.write.mode("overwrite").option("path","s3a://dataproc-bucket/countries").saveAsTable("countries")
    ```

1. Нажмите кнопку ![image](../../../_assets/console-icons/play.svg) **Run all paragraphs** и дождитесь завершения задания.
1. Замените в строке ввода Python-код на SQL-запрос:

    ```sql
    %sql

    SELECT * FROM countries;
    ```

1. Нажмите кнопку ![image](../../../_assets/console-icons/play.svg) **Run all paragraphs**.

    Результат:

    ```text
    |   Name    |  Capital |  Area   | Population |
    | --------- | -------- | ------- | ---------- |
    | Австралия | Канберра | 7686850 | 19731984   |
    | Австрия   | Вена     | 83855   | 7700000    |
    ```

## Экспортируйте данные {#export-data}

Чтобы перенести данные из одного кластера {{ dataproc-name }} в другой, создайте резервную копию данных в кластере `dataproc-source` с помощью утилиты `pg_dump`:

1. [Подключитесь по SSH](../../../data-proc/operations/connect-ssh.md) к хосту-мастеру кластера `dataproc-source`:

    ```bash
    ssh ubuntu@<FQDN_хоста-мастера>
    ```

    [Узнайте, как получить FQDN](../../../data-proc/operations/fqdn.md).

1. Создайте резервную копию и сохраните ее в файле `metastore_dump.sql`:

    ```bash
    pg_dump --data-only --schema public postgres://hive:hive-p2ssw0rd@localhost/metastore > metastore_dump.sql
    ```

1. Отключитесь от хоста-мастера.
1. Скачайте файл `metastore_dump.sql` на локальную машину, в текущую директорию:

    ```bash
    scp ubuntu@<FQDN_хоста-мастера>:metastore_dump.sql .
    ```

1. [Загрузите файл](../../../storage/operations/objects/upload.md#simple) `metastore_dump.sql` в бакет `dataproc-bucket`.

## Подключите {{ dataproc-name }} к {{ metastore-name }} {#connect}

1. [Создайте кластер {{ metastore-name }}](../../../metadata-hub/operations/metastore/cluster-create.md) с параметрами:

    * **{{ ui-key.yacloud.mdb.forms.base_field_service-account }}** — `dataproc-s3-sa`.
    * **{{ ui-key.yacloud.mdb.forms.label_network }}** — `dataproc-network`.
    * **{{ ui-key.yacloud.mdb.forms.network_field_subnetwork }}** — `dataproc-subnet`.
    * **{{ ui-key.yacloud.mdb.forms.field_security-group }}** — `dataproc-security-group`.

1. [Добавьте в настройки кластера](../../../data-proc/operations/cluster-update.md) `dataproc-target` свойство `spark:spark.hive.metastore.uris` со значением `thrift://<IP-адрес_кластера_{{ metastore-name }}>:{{ port-metastore }}`.

    Чтобы узнать IP-адрес кластера {{ metastore-name }}, в консоли управления выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}** и на левой панели выберите страницу ![image](../../../_assets/console-icons/database.svg) **{{ ui-key.yacloud.metastore.label_metastore }}**. Для нужного кластера скопируйте значение из колонки **{{ ui-key.yacloud.metastore.field_metastore-endpoint-ip }}**.

## Импортируйте данные {#import-data}

1. Откройте страницу кластера {{ metastore-name }}.
1. Нажмите кнопку ![image](../../../_assets/console-icons/arrow-down-to-square.svg) **{{ ui-key.yacloud.metastore.action_import }}**.
1. В открывшемся окне укажите бакет `dataproc-bucket` и файл `metastore_dump.sql`.
1. Нажмите кнопку **{{ ui-key.yacloud.metastore.dialog.import-export.action_import }}**.
1. Дождитесь, когда импорт завершится. Статус импорта можно проверить на странице кластера {{ metastore-name }}, в разделе ![image](../../../_assets/console-icons/list-check.svg) **{{ ui-key.yacloud.dataproc.switch_operations }}**.

## Проверьте результат {#check-result}

1. Откройте страницу кластера `dataproc-target`.
1. Перейдите по ссылке **Zeppelin Web UI** в разделе **{{ ui-key.yacloud.mdb.cluster.overview.section_ui-proxy }}**.
1. Выберите **Notebook**, затем ![image](../../../_assets/console-icons/plus.svg) **Create new note**.
1. В появившемся окне укажите название записи и нажмите кнопку **Create**.
1. Отправьте SQL-запрос:

    ```sql
    %sql

    SELECT * FROM countries;
    ```

1. Нажмите кнопку ![image](../../../_assets/console-icons/play.svg) **Run all paragraphs**.

    Результат:

    ```text
    |   Name    |  Capital |  Area   | Population |
    | --------- | -------- | ------- | ---------- |
    | Австралия | Канберра | 7686850 | 19731984   |
    | Австрия   | Вена     | 83855   | 7700000    |
    ```

В итоге в кластер `dataproc-target` были импортированы метаданные из кластера `dataproc-source`.

## Удалите созданные ресурсы {#clear-out}

Некоторые ресурсы платные. Удалите ресурсы, которые вы больше не будете использовать, чтобы не платить за них:

1. [Удалите кластер {{ metastore-name }}](../../../metadata-hub/operations/metastore/cluster-delete.md).
1. [Удалите объекты](../../../storage/operations/objects/delete.md) из бакета.
1. Удалите остальные ресурсы в зависимости от способа их создания:

    {% list tabs group=instructions %}

    - Вручную {#manual}

        1. [Кластеры {{ dataproc-name }}](../../../data-proc/operations/cluster-delete.md).
        1. [Бакет {{ objstorage-name }}](../../../storage/operations/buckets/delete.md).
        1. [Таблицу маршрутизации](../../../vpc/operations/delete-route-table.md).
        1. [NAT-шлюз](../../../vpc/operations/delete-nat-gateway.md).
        1. [Группу безопасности](../../../vpc/operations/security-group-delete.md).
        1. [Подсеть](../../../vpc/operations/subnet-delete.md).
        1. [Сеть](../../../vpc/operations/network-delete.md).
        1. [Сервисный аккаунт](../../../iam/operations/sa/delete.md).

    - {{ TF }} {#tf}

        {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

    {% endlist %}
