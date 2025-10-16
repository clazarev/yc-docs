---
title: Как настроить эндпоинт-источник {{ ydb-full-name }} в {{ data-transfer-full-name }}
description: Из статьи вы узнаете, как задать настройки при создании или изменении эндпоинта-источника {{ ydb-full-name }} в {{ data-transfer-full-name }}.
---

# Передача данных из эндпоинта-источника {{ ydb-name }}


С помощью сервиса {{ data-transfer-full-name }} вы можете переносить данные из базы {{ ydb-name }} и реализовывать различные сценарии переноса, обработки и трансформации данных. Для реализации трансфера:

1. [Ознакомьтесь с возможными сценариями передачи данных](#scenarios).
1. [Подготовьте базу данных {{ ydb-name }}](#prepare) к трансферу.
1. [Настройте эндпоинт-источник](#endpoint-settings) в {{ data-transfer-full-name }}.
1. [Настройте один из поддерживаемых приемников данных](#supported-targets).
1. [Создайте](../../transfer.md#create) и [запустите](../../transfer.md#activate) трансфер.
1. Выполняйте необходимые действия по работе с базой и [контролируйте трансфер](../../monitoring.md).
1. При возникновении проблем, [воспользуйтесь готовыми решениями](#troubleshooting) по их устранению.

## Сценарии передачи данных из {{ ydb-name }} {#scenarios}

1. {% include [cdc](../../../../_includes/data-transfer/scenario-captions/cdc.md) %}

    * [Захват изменений из {{ PG }} и поставка в {{ DS }}](../../../tutorials/ydb-to-yds.md);
    * [Захват изменений из {{ PG }} и поставка в {{ KF }}](../../../tutorials/cdc-ydb.md).

1. {% include [data-mart](../../../../_includes/data-transfer/scenario-captions/data-mart.md) %}

    * [Загрузка данных из {{ ydb-short-name }} в витрину {{ CH }}](../../../tutorials/ydb-to-clickhouse.md).

1. {% include [storage](../../../../_includes/data-transfer/scenario-captions/storage.md) %}

    * [Загрузка данных из {{ ydb-short-name }} в {{ objstorage-name }}](../../../tutorials/ydb-to-object-storage.md).

Подробное описание возможных сценариев передачи данных в {{ data-transfer-full-name }} см. в разделе [Практические руководства](../../../tutorials/index.md).

## Подготовка базы данных источника {#prepare}

{% include [prepare db](../../../../_includes/data-transfer/endpoints/sources/ydb-prepare.md) %}

## Настройка эндпоинта-источника {{ ydb-name }} {#endpoint-settings}

При [создании](../index.md#create) или [изменении](../index.md#update) эндпоинта вы можете задать:
* Настройки подключения к базе данных {{ ydb-full-name }}. Эти параметры обязательные.
* Список путей для переноса (таблицы, директории).


{% note warning %}

Для создания или редактирования эндпоинта управляемой базы данных вам потребуется [роль `ydb.viewer`](../../../../ydb/security/index.md#ydb-viewer) или примитивная [роль `viewer`](../../../../iam/roles-reference.md#viewer), выданная на каталог кластера этой управляемой базы данных.

{% endnote %}


{% list tabs group=instructions %}

- Консоль управления {#console}

  * {% include [YDB UI](../../../../_includes/data-transfer/fields/ydb/ui/database-name.md) %}

  
  * {% include [YDB UI](../../../../_includes/data-transfer/fields/ydb/ui/service-account.md) %}

  * {% include [YDB UI](../../../../_includes/data-transfer/fields/ydb/ui/security-groups.md) %}


  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbSource.paths.title }}** — добавьте пути к таблицам или директориям {{ ydb-name }}, которые необходимо перенести.

      Имена таблиц и директорий должны соответствовать правилам именования идентификаторов в {{ ydb-short-name }}. Экранирование двойных кавычек не требуется. В пути в качестве разделителя используется символ `/`. Подробнее читайте в [документации {{ ydb-short-name }}]({{ ydb.docs }}/concepts/connect#database).

      Добавление новых путей при редактировании эндпоинта, использующегося в трансферах типа {{ dt-type-copy-repl }} в статусе {{ dt-status-repl }}, не приведет к загрузке истории данных таблиц. Чтобы добавить таблицу с ее историческими данными, используйте поле **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.Transfer.data_objects.title }}** в [параметрах трансфера](../../transfer.md#update).

      Для трансферов типа {{ dt-type-copy }} можно не указывать пути, в таком случае будут перенесены все таблицы.

      Для трансферов типа {{ dt-type-repl }} или {{ dt-type-copy-repl }} обязательно указывать пути, в том числе и при репликации всех таблиц.

  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbCustomFeedSettings.changefeed_custom_name.title }}** — укажите название потока изменений, если он уже создан. В противном случае оставьте это поле пустым.

  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbCustomFeedSettings.changefeed_custom_consumer_name.title }}** — укажите потребителя данных (consumer), которого вы создали для потока изменений. По умолчанию используется потребитель с именем `__data_transfer_consumer`.


      
      {% note info %}

      Если указан потребитель данных, то для подключения трансфера к {{ ydb-short-name }} сервисному аккаунту, указанному в настройках эндпоинта, достаточно роли `ydb.viewer`. Если потребитель данных не указан, сервисному аккаунту требуется роль `ydb.editor`, чтобы создать потребителя по умолчанию.

      {% endnote %}
      

  * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbSource.YdbSourceAdvancedSettings.sharded_snapshot.title }}**:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbShardedSnapshotSettings.is_snapshot_sharded.title }}** — включите, чтобы ускорить трансфер с помощью шардированного снапшота.

      Во время стадии копирования таблицы делятся на партиции. Копирование будет выполняться быстрее, если количество воркеров, умноженное на количество потоков внутри воркера, будет пропорционально количеству партиций.

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbShardedSnapshotSettings.copy_folder.title }}** — укажите имя папки, где во время стадии копирования будут храниться копии переносимых таблиц. Папка создается в корневой директории базы-источника и по умолчанию называется `data-transfer`. Копии таблиц в папке содержат только метаданные, поэтому занимают незначительный объем памяти. Когда стадия копирования завершится, папка будет удалена.
    
    
    Для управления параллельным копированием у сервисного аккаунта, указанного в настройках эндпоинта, должна быть [роль `ydb.editor`](../../../../ydb/security/index.md#ydb-editor).


    Чтобы добиться максимальной скорости при параллельном копировании:

      * В параметрах эндпоинта-источника включите опцию **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbShardedSnapshotSettings.is_snapshot_sharded.title }}**.

      
      * В параметрах трансфера, в блоке **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.SnapshotSettings.parallel_settings.title }}**, задайте число воркеров и потоков в каждом воркере так, чтобы общее число потоков было равно числу партиций. Например, чтобы быстрее скопировать таблицу, разбитую на 6 партиций, можно задать 3 воркера и 2 потока внутри каждого воркера.
      

    [Подробнее о параллельном копировании](../../../concepts/sharded.md).

- {{ TF }} {#tf}

  * Тип эндпоинта — `ydb_source`.

  {% include [Managed YDB {{ TF }}](../../../../_includes/data-transfer/necessary-settings/terraform/managed-ydb-source.md) %}

  Пример структуры конфигурационного файла (приведены не все параметры):

  
  ```hcl
  resource "yandex_datatransfer_endpoint" "ydb-source" { 
    name = "<имя_эндпоинта>"
    settings {
      ydb_source {
        database               = "<имя_БД_YDB>"
        service_account_id     = "<идентификатор_сервисного_аккаунта>"
        paths                  = ["<список_путей_к_переносимым_объектам_YDB>"]
        changefeed_custom_name = "<имя_потока_изменений>"
      }
    }
  }
  ```


  Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-dt-endpoint }}).

{% endlist %}


## Настройка приемника данных {#supported-targets}

Настройте один из поддерживаемых приемников данных:

* [{{ CH }}](../target/clickhouse.md);
* [{{ objstorage-full-name }}](../target/object-storage.md);
* [{{ KF }}](../target/kafka.md);
* [{{ DS }}](../target/data-streams.md);
* [{{ ytsaurus-name }}](../source/yt.md);
* [{{ ydb-full-name }}](../target/yandex-database.md).

Полный список поддерживаемых источников и приемников в {{ data-transfer-full-name }} см. в разделе [Доступные трансферы](../../../transfer-matrix.md).

После настройки источника и приемника данных [создайте и запустите трансфер](../../transfer.md#create).

## Решение проблем, возникающих при переносе данных {#troubleshooting}

Известные проблемы, связанные с использованием эндпоинта {{ ydb-name }}:

{% include [overloaded](../../../../_includes/data-transfer/troubles/overloaded.md) %}

См. полный список рекомендаций в разделе [Решение проблем](../../../troubleshooting/index.md).
