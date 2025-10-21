---
title: Как создать пустой диск в {{ compute-full-name }}
description: Следуя данной инструкции, вы сможете создать пустой диск заданного размера.
---

# Создать пустой диск

Вы можете создать пустой диск заданного размера.

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором нужно создать диск.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. На панели слева выберите ![image](../../../_assets/console-icons/hard-drive.svg) **{{ ui-key.yacloud.compute.disks_ddfdb }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.compute.storage.button_create-disk }}**.
  1. Введите имя диска. Требования к имени:

      {% include [name-format](../../../_includes/name-format.md) %}

  1. Если требуется, добавьте произвольное описание диска.
  1. Выберите [зону доступности](../../../overview/concepts/geo-scope.md), в которой будет находиться диск.
  1. Выберите нужный тип диска: `{{ ui-key.yacloud.compute.value_disk-type-network-hdd_cw9XD }}`, `{{ ui-key.yacloud.compute.value_disk-type-network-ssd_4Mmub }}`, `{{ ui-key.yacloud.compute.value_disk-type-network-ssd-io-m3_pPfnG }}` или `{{ ui-key.yacloud.compute.value_disk-type-network-ssd-nonreplicated_d7W56 }}`.


  1. Выберите нужный размер блока (минимальный объем хранения информации на диске). По умолчанию размер блоков всех создаваемых дисков равен 4 КБ, однако для дисков больше 8 ТБ этого недостаточно.
  1. Укажите нужный размер диска. Максимальный размер диска зависит от заданного размера блока.
  1. {% include [encryption-section](../../../_includes/compute/encryption-section.md) %}
  1. Если требуется, выберите [расписание](../../concepts/snapshot-schedule.md), по которому для диска будут автоматически создаваться [снимки](../../concepts/snapshot.md), или создайте его. Подробнее о настройках расписания см. в [инструкции](../snapshot-control/create-schedule.md).

     {% include [snapshot-disk-types](../../../_includes/compute/snapshot-disk-types.md) %}

     При создании диска для него можно выбрать только одно расписание снимков. Если требуется, после создания диска вы можете добавить к нему еще несколько расписаний по [инструкции](../disk-control/configure-schedule.md#add-schedule).

  1. Нажмите кнопку **{{ ui-key.yacloud.compute.disks.create.button_create }}**.

- CLI {#cli}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команд CLI для создания дисков:

      ```bash
      yc compute disk create --help
      ```

  1. Создайте диск в каталоге по умолчанию:

      ```bash
      yc compute disk create \
        --name first-disk \
        --size 10 \
        --description "my first disk via yc" \
        --kms-key-id <идентификатор_ключа>
      ```

      Где:

      * `--name` — имя диска. Требования к имени диска:

          {% include [name-format](../../../_includes/name-format.md) %}

      * `--size` — размер диска.
      * `--description` — описание диска.
      * `--kms-key-id` — идентификатор [симметричного ключа {{ kms-short-name }}](../../../kms/concepts/key.md) для создания зашифрованного диска. Необязательный параметр.

        {% include [encryption-role](../../../_includes/compute/encryption-role.md) %}
        
        {% include [encryption-disable-warning](../../../_includes/compute/encryption-disable-warning.md) %}

        {% include [encryption-keys-note](../../../_includes/compute/encryption-keys-note.md) %}

      Команда создаст диск размером 10 ГБ с именем `first-disk` и описанием `my first disk via yc`.

  1. Получите список дисков в каталоге по умолчанию:

      {% include [compute-disk-list](../../../_includes/compute/disk-list.md) %}

      Получите тот же список с большим количеством деталей в формате YAML:

      ```bash
      yc compute disk list --format yaml
      ```

      Результат:
      ```text
      - id: fhm4aq4hvq5g********
          folder_id: b1gm3og7ei7a********
          created_at: "2018-10-29T07:43:06Z"
          name: first-disk
          description: my first disk via yc
          type_id: network-hdd
          zone_id: {{ region-id }}-a
          size: "10737418240"
          status: READY
          disk_placement_policy: {}
          hardware_generation:
            legacy_features:
              pci_topology: PCI_TOPOLOGY_V1
          kms_key:
            key_id: abjbaqdga6hs********
            version_id: abj295dgqnlp********
      - id: fhmo6rdqg5fo********
          folder_id: b1gm3og7ei7a********
          created_at: "2018-10-29T07:33:04Z"
          name: second-disk
          description: my second disk via yc
          type_id: network-hdd
          zone_id: {{ region-id }}-a
          size: "8589934592"
          product_ids:
          - f2en2dtd08b5********
          status: READY
          source_image_id: fdvk34al8k5n********
          instance_ids:
          - fhm5b617fjnj********
          disk_placement_policy: {}
          hardware_generation:
            legacy_features:
              pci_topology: PCI_TOPOLOGY_V1
          kms_key:
            key_id: abjbaqdga6hs********
            version_id: abj295dgqnlp********
      ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  {% include [terraform-empty-disk-create](../../../_includes/compute/terraform-empty-disk-create.md) %}

- API {#api}

  Воспользуйтесь методом REST API [create](../../api-ref/Disk/create.md) для ресурса [Disk](../../api-ref/Disk/index.md) или вызовом gRPC API [DiskService/Create](../../api-ref/grpc/Disk/create.md).

{% endlist %}

#### См. также {#see-also}

* [{#T}](../snapshot-control/create-schedule.md)
