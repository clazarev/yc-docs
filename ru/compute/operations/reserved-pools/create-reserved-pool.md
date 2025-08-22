---
title: Создать пул резервов ВМ
description: Из статьи вы узнаете, как создать пул резервов ВМ {{ compute-full-name }} нужной конфигурации в нужной зоне доступности.
---

# Создание пула резервов виртуальных машин

{% include [reserved-pools-pricing-warning](../../../_includes/compute/reserved-pools-pricing-warning.md) %}

{% include [reserved-pools-preview-notice](../../../_includes/compute/reserved-pools-preview-notice.md) %}

Чтобы создать [пул резервов ВМ](../../concepts/reserved-pools.md):

{% list tabs group=instructions %}

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды [{{ yandex-cloud }} CLI](../../../cli/index.yaml) для создания пула резервов ВМ:

      ```bash
      yc compute reserved-instance-pool create --help
      ```
  1. Создайте пул резервов ВМ в каталоге по умолчанию:

      ```bash
      yc compute reserved-instance-pool create \
        --name <имя_пула> \
        --description <описание_пула> \
        --zone-id <идентификатор_зоны_доступности> \
        --size <размер_пула> \
        --platform <идентификатор_платформы> \
        --cores <количество_vCPU> \
        --memory <объем_RAM> \
        --gpus <количество_GPU> \
        --gpu-cluster-id <идентификатор_кластера_GPU> \
        --allow-oversubscription
      ```

      Где:
      * `--name` — имя создаваемого пула. Требования к имени:

          {% include [name-format](../../../_includes/name-format.md) %}

      * `--description` — описание создаваемого пула. Необязательный параметр.
      * `--zone-id` — идентификатор [зоны доступности](../../../overview/concepts/geo-scope.md), в которой резервируются [вычислительные ресурсы](../../concepts/vm.md#types).
      * `--size` — количество слотов в пуле. Необязательный параметр. Значение по умолчанию — `0`.
      * `--platform` — [платформа](../../concepts/vm-platforms.md), на которой резервируются вычислительные ресурсы. Необязательный параметр. Значение по умолчанию — `standard-v2` (`Intel Cascade Lake`).
      * `--cores` — количество vCPU виртуальных машин создаваемого пула. Необязательный параметр. Значение по умолчанию — `2`.
      * `--memory` — объем RAM виртуальных машин создаваемого пула. Необязательный параметр. Значение по умолчанию — `2 ГБ`.
      * `--gpus` — количество [GPU](../../concepts/gpus.md) виртуальных машин создаваемого пула. Необязательный параметр. Параметр доступен только при выборе платформы, [поддерживающей](../../concepts/gpus.md#config) GPU.
      * `--gpu-cluster-id` — идентификатор [кластера](../../concepts/gpus.md#gpu-clusters) GPU. Необязательный параметр.
      * `--allow-oversubscription` — параметр, включающий для пула [режим переподписки](../../concepts/reserved-pools.md#oversubscription), при котором к пулу можно привязывать ВМ в количестве, превышающем размер пула. Необязательный параметр. По умолчанию режим переподписки выключен.

      Результат:

      ```text
      done (8s)
      id: fv4vnl1ncbja********
      zone_id: {{ region-id }}-a
      cloud_id: b1gia87mbaom********
      folder_id: b1gt6g8ht345********
      name: test-pool
      description: sample description
      created_at: "2025-08-12T07:58:57Z"
      platform_id: standard-v2
      resources_spec:
        memory: "2147483648"
        cores: "2"
        core_fraction: "100"
      gpu_settings: {}
      network_settings:
        type: STANDARD
      size: "2"
      committed_size: "2"
      slot_stats:
        total: "2"
        unavailable: "2"
      instance_stats: {}
      ```

      Подробнее о команде `yc compute reserved-instance-pool create` читайте в [справочнике {{ yandex-cloud }} CLI](../../../cli/cli-ref/compute/cli-ref/reserved-instance-pool/create.md).

- API {#api}

  Воспользуйтесь методом REST API [create](../../api-ref/ReservedInstancePool/create.md) для ресурса [ReservedInstancePool](../../api-ref/ReservedInstancePool/index.md) или вызовом gRPC API [ReservedInstancePoolService/Create](../../api-ref/grpc/ReservedInstancePool/create.md).

{% endlist %}

#### См. также {#see-also}

* [{#T}](./manage-pool-vms.md)
* [{#T}](../../concepts/reserved-pools.md)