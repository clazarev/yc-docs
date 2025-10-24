# История изменений в {{ managed-k8s-full-name }}

Изменения появляются в [релизных каналах](./concepts/release-channels-and-updates.md) сервиса последовательно. Сначала обновления, содержащие новую функциональность и улучшения, появляются на канале `rapid`, через некоторое время они попадают в `regular` и уже после этого добавляются в `stable`.

## III квартал 2025 {#q3-2025}

### Новые возможности {#q3-2025-new-features}

* Добавлена возможность выбора [конфигурации мастера](./concepts/index.md#master-resources) с помощью [{{ TF }}](../terraform/) и [{{ yandex-cloud }} CLI](../cli/) при создании или изменении кластера. Подробнее на странице [{#T}](./operations/kubernetes-cluster/kubernetes-cluster-create.md).
* Реализован [доступ к API {{ yandex-cloud }} из кластера {{ managed-k8s-name }} с помощью федерации сервисных аккаунтов {{ iam-full-name }}](./tutorials/wlif-managed-k8s-integration.md). Теперь токены сервисного аккаунта {{ k8s }} можно обменивать на IAM-токены {{ yandex-cloud }} для простой аутентификации и авторизации в облаке из подов кластера.
* Поддержана возможность аутентификации в [{{ cloud-registry-full-name }}](../cloud-registry/) с помощью сервисного аккаунта группы узлов. Для доступа к реестрам {{ cloud-registry-name }} [назначьте](../iam/operations/sa/assign-role-for-sa.md) сервисному аккаунту группы узлов [роль](../cloud-registry/security/index.md#cloud-registry-artifacts-puller) `cloud-registry.artifacts.puller`.
* Добавлена поддержка одновременного [увеличения размера](./concepts/volume.md#volume-expansion) нескольких постоянных томов, смонтированных на одном узле. Подробнее на странице [{#T}](./operations/volumes/volume-expansion.md).

### Улучшения {#q3-2025-improvements}

* Поддержан {{ k8s }} версии [1.33](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/). Подробнее на странице [{#T}](./concepts/release-channels-and-updates.md).
* Среда запуска контейнеров [containerd](https://containerd.io/) обновлена до версии [1.7.27](https://github.com/containerd/containerd/releases/tag/v1.7.27) для кластеров с {{ k8s }} версии 1.30 и выше.
* Начиная с версии {{ k8s }} 1.30 операционная система узлов изменилась с Ubuntu 20.04 на Ubuntu 22.04. При обновлении групп узлов в этих версиях новые узлы автоматически создаются из образа виртуальной машины с Ubuntu 22.04. Подробнее на странице [{#T}](./concepts/node-os-update.md).
* В [Cluster Autoscaler](./concepts/autoscale.md#ca) добавлена проверка доступности [зоны](../overview/concepts/geo-scope.md) при выборе группы узлов для масштабирования. Теперь в недоступных зонах не будут выполняться попытки автоматического масштабирования групп узлов.

### Исправления {#q3-2025-problems-solved}

* Исправлена ошибка, в результате которой при изменении ресурсов мастера кластер переходил в статус `Running` до фактического завершения операции изменения.
* Устранена ошибка, которая приводила к нарушению связи мастера с узлами в кластерах с туннельным режимом при миграции мастера из одной подсети в другую. Данная проблема приводила к неработоспособности {{ k8s }} Webhook и Aggregated API на перенесенном мастере.

### Прочие изменения {#q3-2025-other-changes}

Убрана возможность отключить опцию автоматического увеличения ресурсов мастера при повышении нагрузки. Теперь опция включена для всех кластеров {{ managed-k8s-name }}. Из документа [Уровень обслуживания {{ managed-k8s-full-name }}®](https://yandex.ru/legal/cloud_sla_kb) удалено условие, что в настройках мастера должна быть включена указанная опция.

## II квартал 2025 {#q2-2025}

### Новые возможности {#q2-2025-new-features}

* Поддержан {{ k8s }} версии [1.32](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.32.md). Подробнее см. [{#T}](./concepts/release-channels-and-updates.md).
* Добавлена возможность указать одно и то же значение для минимального и максимального количества узлов в группе с [автоматическим масштабированием](./concepts/node-group/cluster-autoscaler.md). Таким образом можно, не меняя тип группы на фиксированный, добиться фиксированного размера и фактически отключить автоматическое масштабирование.
* Поддержаны [шифрованные диски](./concepts/volume.md#encrypted-disks) {{ compute-full-name }} для статической и динамической подготовки постоянных томов.
* Добавлена передача в {{ at-full-name }} [события уровня конфигурации](./at-ref.md#control-plane-events) `UpdateClusterCertificate` при обновлении [сертификата кластера](./concepts/release-channels-and-updates.md#certificates).
* Обновлен сетевой контроллер [Calico](./concepts/network-policy.md#calico) до версии [3.30](https://github.com/projectcalico/calico/blob/release-v3.30/release-notes/v3.30.0-release-notes.md).

### Улучшения {#q2-2025-improvements}

* Реализован механизм принудительного удаления узла в группе с [автоматическим масштабированием](./concepts/node-group/cluster-autoscaler.md), если он по каким-то причинам не смог подключиться к кластеру в течение 15 минут. После удаления узел автоматически пересоздается.
* В соответствии с [CIS {{ k8s }} Benchmarks](https://www.cisecurity.org/benchmark/kubernetes) отключен профайлинг на компонентах мастеров.
* В кластерах с [туннельным режимом](./concepts/network-policy.md#cilium) добавлена поддержка [Topology Aware Routing](https://kubernetes.io/docs/concepts/services-networking/topology-aware-routing/) для локализации трафика в одной [зоне доступности](../overview/concepts/geo-scope.md) с целью сокращения сетевых задержек.
* Улучшена безопасность регистрации узлов в кластере: теперь с помощью bootstrap-конфигурации можно выписать сертификат для узла только с самого этого узла, а не из любого другого узла или любого пода.

### Исправления {#q2-2025-problems-solved}

* Исправлена ошибка в сетевом контроллере [Cilium](./concepts/network-policy.md#cilium), которая приводила к недоступности сети кластера в случае отказа мастеров. Теперь при полном отказе мастеров сеть и приложения в кластере продолжают работать. Поддерживается только в кластерах с Cilium версии 1.15 и выше ({{ k8s }} версии 1.31).
* Исправлена ошибка, которая могла приводить к тому, что после окончания срока действия сертификата компоненты мастеров продолжали работать со старым сертификатом.
* Исправлена ошибка, которая могла приводить к невозможности [автоматического масштабирования](./concepts/node-group/cluster-autoscaler.md) групп узлов с количеством узлов более 80.
* Исправлена ошибка, которая могла приводить к невозможности обновления [целевых групп](../network-load-balancer/concepts/target-resources.md) {{ network-load-balancer-full-name }} для сервисов типа `LoadBalancer`.

## I квартал 2025 {#q1-2025}

### Новые возможности {#q1-2025-new-features}

* Реализована возможность настройки [вычислительных ресурсов](./concepts/index.md#master-resources) для мастеров, добавлены соответствующие [квоты](./concepts/limits.md#managed-k8s-quotas).
* Обновлены типы конфигураций мастеров: 
  * _Базовый_ — содержит один хост мастера в одной зоне доступности. Прежнее название — _зональный_.
  * _Высокодоступный в трех зонах доступности_ — содержит три хоста мастера в трех разных зонах доступности. Прежнее название — _региональный_.
  * _Высокодоступный в одной зоне доступности_ — содержит три хоста мастера в одной зоне доступности и одной подсети. Новая конфигурация.

  Подробнее см. в [описании мастера](./concepts/index.md#master).

### Исправления и улучшения {#q1-2025-problems-solved}

* Шифрование секретов кластеров в [etcd](https://kubernetes.io/docs/concepts/architecture/#etcd) переключено на [KMS v2](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/#kms-v2).
* Исправлена ошибка, в результате которой в некоторых случаях не удавалось создать кластер {{ managed-k8s-name }} с включенной записью логов.
* Изменена особенность, при которой наличие управляемых кластером {{ managed-k8s-name }} балансировщиков {{ network-load-balancer-name }} с включенной защитой от удаления блокировало удаление кластера. Теперь процесс удаления кластера не блокируется, а балансировщики остаются в каталоге пользователя.

## IV квартал 2024 {#q4-2024}

### Новые возможности {#q4-2024-new-features}

* Добавлена поддержка {{ k8s }} версии [1.31](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.31.md). Подробнее см. [{#T}](./concepts/release-channels-and-updates.md).
* Обновлен [Cilium](https://cilium.io/) с версии [1.12.9](https://github.com/cilium/cilium/releases/tag/v1.12.9) до [1.15.10](https://github.com/cilium/cilium/releases/tag/v1.15.10) для кластеров с {{ k8s }} версии 1.31 и выше.
* Обновлен [CoreDNS](https://coredns.io/) с версии [1.9.4](https://github.com/coredns/coredns/releases/tag/v1.9.4) до [1.11.3](https://github.com/coredns/coredns/releases/tag/v1.11.3) для всех поддерживаемых версий {{ k8s }}.

### Исправления и улучшения {#q4-2024-problems-solved}

* Добавлена предварительная проверка (_preflight check_) совместимости объектов или конфигураций с новой версией {{ k8s }} перед обновлением кластера.

  Если при проверке выявляются несовместимые объекты или конфигурации, обновление завершится ошибкой с указанием несовместимых ресурсов и описанием. 

  В текущей реализации проверка проводится только для [сетевых политик Cilium](./concepts/network-policy.md#cilium).
* Устранена одна из проблем, которая в некоторых случаях приводила к тому, что не удавалось подключить новый узел к кластеру, и узел навсегда оставался в статусе `NOT_CONNECTED`.

## III квартал 2024 {#q3-2024}

### Новые возможности {#q3-2024-new-features}

Добавлена поддержка миграции [мастеров](./concepts/index.md#master) между [подсетями](../vpc/concepts/network.md#subnet) внутри одной [зоны доступности](../overview/concepts/geo-scope.md).

### Исправления и улучшения {#q3-2024-problems-solved}

* Исправлена ошибка, в результате которой не сохранялись файлы аудитных логов кластера с записями размером более 128 КБ. Включена обрезка записей.
* Проведена ревизия [кластерных ролей](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) (ClusterRole) контроллера сетевых политик [Cilium](./concepts/network-policy.md#cilium). Оставлены только минимально необходимые права.
* Добавлена проверка полей `subnet-id` при обновлении [группы узлов](./concepts/index.md#node-group) с помощью CLI, {{ TF }} и API. Теперь если в запросе на обновление одновременно указаны параметры `network-interface` и `locations`, требуется, чтобы поля `subnet-id` в `locations` либо были все пустыми, либо полностью совпадали с набором `subnet-id` в `network-interface` (последовательность указания `subnet-id` может быть любой). Если в запросе больше одного элемента в массиве `network-interface`, поля `subnet-id` в `locations` должны быть пустыми.

## I полугодие 2024 {#h1-2024}

### Новые возможности {#h1-2024-new-features}

* Добавлена поддержка {{ k8s }} версий [1.28](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.28.md), [1.29](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.29.md) и [1.30](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.30.md). Подробнее см. [{#T}](./concepts/release-channels-and-updates.md).
* Обновлены лимиты [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) для поддержки дисков объемом более 200 ТБ.

### Исправления и улучшения {#h1-2024-problems-solved}

* Исправлена ошибка, в результате которой мог теряться размер [снапшота](https://kubernetes.io/docs/concepts/storage/volume-snapshots/) при большом размере [PersistentVolume](./concepts/volume.md#persistent-volume).
* Исправлена ошибка, в результате которой в некоторых случаях при обновлении [группы узлов](./concepts/index.md#node-group) не обновлялись маршруты до [podCIDR](https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/node-v1/#NodeSpec), из-за чего поды на узле могли быть недоступны.
* Устранен ряд уязвимостей в [runC](https://github.com/opencontainers/runc).
* Исправлена проблема c запуском [кластера](./concepts/index.md#kubernetes-cluster) после обновления сертификатов в остановленном состоянии.
* Исправлена ошибка, в результате которой в некоторых случаях узел при создании навсегда оставался в статусе `NOT_CONNECTED`.

## 2023 {#2023}

### Релиз 2023-6 {#2023-6}

В релизных каналах `rapid`, `regular` и `stable` доступны следующие изменения:
* Добавлена поддержка [сверхбыстрых сетевых хранилищ с тремя репликами (SSD)](../compute/concepts/disk.md#disks-types) для [классов хранилищ](./operations/volumes/manage-storage-class.md) и [постоянных томов](./concepts/volume.md#persistent-volume).
* Появилась возможность использовать группы узлов c [GPU](../compute/concepts/gpus.md) без предустановленных драйверов. Теперь вы можете самостоятельно выбирать подходящую версию драйвера с помощью приложения [GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/overview.html). Подробнее см. [{#T}](./tutorials/driverless-gpu.md).
* Убрано ограничение ресурса CPU у подов [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/coredns/) для предотвращения троттлинга.
* Добавлена поддержка [групп размещения нереплицируемых дисков](../compute/concepts/disk-placement-group.md) в CSI-драйвере {{ k8s }}. Параметры групп размещения доступны для [классов хранилищ](./operations/volumes/manage-storage-class.md).
* Исправлена ошибка игнорирования идентификатора [лог-группы](../logging/concepts/log-group.md) при обновлении параметра `master_logging` в кластере.
* Обновлен сетевой контроллер [Calico](./concepts/network-policy.md#calico) до версии [3.25](https://docs.tigera.io/archive/v3.25/release-notes/) для версий {{ k8s }} начиная с 1.24.

### Релиз 2023-5 {#2023-5}

В релизных каналах `rapid`, `regular` и `stable` доступны следующие изменения:
* Исправлена ошибка, при которой Guest Agent на узлах обращался к ресурсу за пределами кластера.
* Обновлена [патч-версия](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.27.md#changelog-since-v1273) для {{ k8s }} версии 1.27.
* Добавлена поддержка {{ k8s }} версии 1.26.
