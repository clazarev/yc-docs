1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором нужно создать [кластер {{ managed-k8s-name }}](../../managed-kubernetes/concepts/index.md#kubernetes-cluster).
1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}**.
1. Нажмите кнопку **{{ ui-key.yacloud.k8s.clusters.button_create }}**.
1. Введите имя и описание кластера {{ managed-k8s-name }}. Имя кластера {{ managed-k8s-name }} должно быть уникальным в рамках {{ yandex-cloud }}.
1. Укажите **{{ ui-key.yacloud.k8s.clusters.create.field_service-account }}**, который будет использоваться для создания ресурсов.
1. Укажите **{{ ui-key.yacloud.k8s.clusters.create.field_node-service-account }}**, который будет использоваться [узлами {{ managed-k8s-name }}](../../managed-kubernetes/concepts/index.md#node-group) для доступа к [реестру Docker-образов](../../container-registry/concepts/registry.md) [{{ container-registry-full-name }}](../../container-registry/).
1. (Опционально) Укажите **{{ ui-key.yacloud.k8s.clusters.create.field_kms-key }}**, который будет использоваться для [шифрования секретов](../../managed-kubernetes/concepts/encryption.md).

   {% include [write-once-settings](write-once-setting.md) %}

1. Укажите [релизный канал](../../managed-kubernetes/concepts/release-channels-and-updates.md).

   {% include [write-once-settings](write-once-setting.md) %}

1. В поле **{{ ui-key.yacloud.component.label-set.label_labels }}** добавьте [облачные метки](../../managed-kubernetes/concepts/index.md#cluster-labels).

1. В блоке **{{ ui-key.yacloud.k8s.clusters.create.section_main-cluster }}**:
   * (Опционально) Раскройте секцию **Вычислительные ресурсы** и выберите [конфигурацию ресурсов](../../managed-kubernetes/concepts/index.md#master-resources) для мастера.

     {% include [master-autoscale](master-autoscale.md) %}

     {% include [master-default-config](master-default-config.md) %}

   * В поле **{{ ui-key.yacloud.k8s.clusters.create.field_master-version }}** выберите версию {{ k8s }}, которая будет установлена на [мастере {{ managed-k8s-name }}](../../managed-kubernetes/concepts/index.md#master).
   * В поле **{{ ui-key.yacloud.k8s.clusters.create.field_address-type }}** выберите способ назначения [IP-адреса](../../vpc/concepts/address.md):
     * `{{ ui-key.yacloud.k8s.clusters.create.switch_auto }}` — чтобы назначить случайный IP-адрес из пула IP-адресов {{ yandex-cloud }}.
     * `{{ ui-key.yacloud.k8s.clusters.create.switch_none }}` — чтобы не назначать публичный IP-адрес.

     {% include [nat-instance-restriction](nat-instance-restriction.md) %}

     {% include [write-once-settings](write-once-setting.md) %}

   * В поле **{{ ui-key.yacloud.k8s.clusters.create.field_master-type }}** выберите тип мастера {{ managed-k8s-name }}:
     * `Базовый` — содержит один хост мастера в одной зоне доступности. Такой мастер дешевле, но он не является отказоустойчивым. Прежнее название — _зональный_.

       {% note warning %}

       {% include [base-zonal-pricing](../../_includes/managed-kubernetes/base-zonal-pricing.md) %}

       {% endnote %}

     * `Высокодоступный` — содержит три хоста мастера. Прежнее название — _региональный_.

       {% note warning %}

       {% include [ha-regional-pricing](../../_includes/managed-kubernetes/ha-regional-pricing.md) %}

       {% endnote %}

   * В поле **{{ ui-key.yacloud.k8s.clusters.create.field_network }}** выберите [сеть](../../vpc/concepts/network.md#network), в которой будет создан мастер {{ managed-k8s-name }}. Если сети нет, [создайте ее](../../vpc/operations/network-create.md).

      {% include [note-another-catalog-network](note-another-catalog-network.md) %}

   * Для высокодоступного мастера в поле **Распределение мастеров по зонам доступности** выберите, как должны быть размещены хосты мастера:
     * `Одна зона` — в одной зоне доступности и одной подсети. Такой мастер подойдет, если вы хотите обеспечить высокую доступность кластера и уменьшить сетевую задержку внутри него.
     * `Разные зоны` — в трех разных зонах доступности. Такой мастер обеспечивает наибольшую отказоустойчивость: при недоступности одной зоны мастер остается работоспособным.

   * В зависимости от выбранного типа мастера:
     * Для базового или высокодоступного мастера в одной зоне укажите зону доступности и подсеть. 
     * Для высокодоступного мастера в разных зонах укажите подсети в каждой зоне. 

     Если подсетей нет, [создайте](../../vpc/operations/subnet-create.md) их.

     {% note warning %}

     Тип мастера и его размещение нельзя изменить после создания кластера.

     {% endnote %}

   * Выберите [группы безопасности](../../vpc/concepts/security-groups.md) для сетевого трафика кластера {{ managed-k8s-name }}.

     {% include [security-groups-alert](security-groups-alert.md) %}

1. В блоке **{{ ui-key.yacloud.k8s.clusters.create.section_maintenance }}**:
   * В поле **{{ ui-key.yacloud.k8s.clusters.create.field_maintenance-window }}** настройте окно для обновлений:
     * `{{ ui-key.yacloud.k8s.clusters.create.value_maintenance-disabled }}` — отключение автоматических обновлений.
     * `{{ ui-key.yacloud.k8s.clusters.create.value_maintenance-anytime }}` — обновления разрешены в любое время.
     * `{{ ui-key.yacloud.k8s.clusters.create.value_maintenance-daily }}` — обновления будут происходить во временном интервале, указанном в поле **{{ ui-key.yacloud.k8s.clusters.create.field_maintenance-daily }}**.
     * `{{ ui-key.yacloud.k8s.clusters.create.value_maintenance-weekly }}` — обновления будут происходить во временном интервале, указанном в поле **{{ ui-key.yacloud.k8s.clusters.create.label_maintenance-weekly }}**.
1. В блоке **{{ ui-key.yacloud.k8s.clusters.create.section_allocation }}**:
   * (Опционально) Выберите [контроллер сетевых политик](../../managed-kubernetes/concepts/network-policy.md#policy-controllers):

     {% include [write-once-setting](write-once-setting.md) %}

     {% include [calico-cilium-mutual-exclusion](calico-cilium-mutual-exclusion.md) %}

     * **{{ ui-key.yacloud.k8s.clusters.create.field_network-policy }}**, чтобы задействовать Calico.
     * **{{ ui-key.yacloud.k8s.clusters.create.field_tunnel-mode }}**, чтобы задействовать Cilium.


   * Укажите **{{ ui-key.yacloud.k8s.clusters.create.field_cluster-cidr }}** — диапазон IP-адресов, из которого будут выделяться IP-адреса для [подов](../../managed-kubernetes/concepts/index.md#pod).
   * Укажите **{{ ui-key.yacloud.k8s.clusters.create.field_service-cidr }}** — диапазон IP-адресов, из которого будут выделяться IP-адреса для [сервисов](../../managed-kubernetes/concepts/index.md#service).
   * Задайте маску подсети узлов {{ managed-k8s-name }} и максимальное количество подов в узле.
1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.