# Использование групп узлов c GPU без предустановленных драйверов


Вы можете использовать группы узлов {{ managed-k8s-name }} для рабочих нагрузок на видеопроцессорах ([GPU](../../compute/concepts/gpus.md)) без предустановленных драйверов. Приложение [GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/overview.html) позволит вам самостоятельно выбрать подходящую версию драйвера.

Чтобы подготовить кластер и группу узлов {{ managed-k8s-name }} без предустановленных драйверов к запуску рабочих нагрузок:

1. [Установите GPU Operator](#install-gpu-operator).
1. [Проверьте правильность установки драйверов](#check-install).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).


## Необходимые платные ресурсы {#paid-resources}

В стоимость поддержки описываемого решения входят:

* Плата за кластер {{ managed-k8s-name }}: использование мастера и исходящий трафик (см. [тарифы {{ managed-k8s-name }}](../../managed-kubernetes/pricing.md)).
* Плата за узлы кластера (ВМ): использование вычислительных ресурсов, операционной системы и хранилища (см. [тарифы {{ compute-name }}](../../compute/pricing.md)).
* Плата за публичный IP-адрес, если он назначен узлам кластера (см. [тарифы {{ vpc-name }}](../../vpc/pricing.md#prices-public-ip)).


## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

1. {% include [configure-sg-manual](../../_includes/managed-kubernetes/security-groups/configure-sg-manual-lvl3.md) %}

    {% include [sg-common-warning](../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

1. [Создайте кластер {{ managed-k8s-name }}](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create.md) любой подходящей конфигурации. При создании укажите группы безопасности, подготовленные ранее.

1. [Создайте группу узлов {{ managed-k8s-name }}](../../managed-kubernetes/operations/node-group/node-group-create.md) с настройками:
   * **{{ ui-key.yacloud.compute.instances.create.section_platform }}** — перейдите на вкладку **GPU** и выберите подходящую платформу.
   * **{{ ui-key.yacloud.k8s.node-groups.create.field_driverless-gpu }}** — выберите опцию.
   * **{{ ui-key.yacloud.mdb.forms.field_security-group }}** — выберите созданные ранее группы безопасности.
   * **{{ ui-key.yacloud.k8s.node-groups.create.field_node-taints }}** — укажите [taint-политику](../../managed-kubernetes/concepts/index.md#taints-tolerations) `nvidia.com/gpu=true:NoSchedule`.


1. {% include [Настройка kubectl](../../_includes/managed-kubernetes/kubectl-install.md) %}

## Требования к версиям компонентов {#version-requirements}

Начиная с версии {{ k8s }} 1.30 для корректной работы группы узлов с GPU без предустановленных драйверов требуются:

* GPU Operator версии `24.9.0` или выше.
* Драйвер NVIDIA версии `550.144.03` или выше.

При использовании компонентов более старых версий могут возникнуть ошибки компиляции драйверов.

## Установите GPU Operator {#install-gpu-operator}

1. {% include [Установка Helm](../../_includes/managed-kubernetes/helm-install.md) %}

1. Установите GPU Operator:

    ```bash
    helm repo add nvidia https://helm.ngc.nvidia.com/nvidia && \
    helm repo update && \
    helm install \
      --namespace gpu-operator \
      --create-namespace \
      --set driver.version=<версия_драйвера> \
      gpu-operator nvidia/gpu-operator
    ```

    Где `driver.version` — версия драйвера NVIDIA®. Вы можете не указывать параметр с версией драйвера, в этом случае будет установлена версия [по умолчанию](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/platform-support.html#gpu-operator-component-matrix).

    {% note info %}

    Рекомендуемые версии драйверов:

    * Для групп узлов версии {{ k8s }} 1.30 или выше — [версия `550.144.03`](https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-550-144-03/index.html) или выше.

    * Для групп узлов на платформе {{ a100-epyc }} (`gpu-standard-v3`) — [версия `515.48.07`](https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-515-48-07/index.html).

    {% endnote %}

    GPU Operator будет установлен с параметрами по умолчанию. Подробнее о параметрах см. в [официальной документации](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html#common-chart-customization-options).

    {% note tip %}

    Вы можете посмотреть значения параметров в конфигурационном файле `values.yaml` Helm-чарта. Для этого скачайте архив Helm-чарта командой `helm pull --untar nvidia/gpu-operator`.

    {% endnote %}

## Проверьте правильность установки драйверов {#check-install}

Получите логи пода `nvidia-driver-daemonset`:

```bash
DRIVERS_POD_NAME="$(kubectl get pods --namespace gpu-operator | grep nvidia-driver-daemonset | awk '{print $1}')" && \
kubectl --namespace gpu-operator logs "${DRIVERS_POD_NAME}"
```

Они должны содержать сообщение об успешной установке драйвера, например:

```text
Defaulted container "nvidia-driver-ctr" out of: nvidia-driver-ctr, k8s-driver-manager (init)
DRIVER_ARCH is x86_64
Creating directory NVIDIA-Linux-x86_64-<версия_драйвера>
Verifying archive integrity... OK
Uncompressing NVIDIA Accelerated Graphics Driver for Linux-x86_64 <версия_драйвера>

...

Loading NVIDIA driver kernel modules...
+ modprobe nvidia
+ modprobe nvidia-uvm
+ modprobe nvidia-modeset

...

Done, now waiting for signal
```

Теперь вы можете запускать рабочие нагрузки с GPU согласно руководству [Запуск рабочих нагрузок с GPU](../../managed-kubernetes/tutorials/running-pod-gpu.md).

## Решение проблем {#troubleshooting}

### Ошибки компиляции драйверов {#compilation-error}

Если при установке драйверов возникают ошибки компиляции:

1. Убедитесь, что установлен GPU Operator версии 24.9.0 или выше:

    ```bash
    helm list -n gpu-operator
    ```

1. Используйте предварительно скомпилированные драйверы:

    ```bash
    helm upgrade gpu-operator nvidia/gpu-operator \
      --namespace gpu-operator \
      --set driver.usePrecompiled=true \
      --set driver.version=550.144.03
    ```

## Удалите созданные ресурсы {#clear-out}

Некоторые ресурсы платные. Удалите ресурсы, которые вы больше не будете использовать, во избежание списания средств за них:

1. [Удалите кластер {{ k8s }}](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-delete.md).
1. Если вы создавали сервисные аккаунты, [удалите их](../../iam/operations/sa/delete.md).
