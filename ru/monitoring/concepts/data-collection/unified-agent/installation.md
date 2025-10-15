---
title: Как установить и обновить {{ unified-agent-full-name }}
description: Из статьи вы узнаете, как установить и обновить {{ unified-agent-full-name }}.
sourcePath: ru/monitoring_includes/concepts/data-collection/unified-agent/installation.md
---

# Установка и обновление {{ unified-agent-full-name }}

{% include [agent-version](../../../../_includes/monitoring/agent-version.md) %}

Подробнее см. раздел [{#T}](../../../operations/prometheus/ingestion/prometheus-agent.md).

## Список поддерживаемых операционных систем {#supported-os}

Работа {{ unified-agent-short-name }} поддерживается на следующих операционных системах:

- Ubuntu 16.04 или выше (возможна [установка любым из способов](#setup));
- Debian 9 или выше (Docker-образ, deb-пакет или бинарный файл);
- CentOS 7 или выше (Docker-образ);
- Fedora 32 или выше (Docker-образ);
- Fedora CoreOS (Docker-образ).


<iframe width="640" height="360" src="https://runtime.strm.yandex.ru/player/video/vplvmbl3hthmrm47cpx2?autoplay=0&mute=0" allow="autoplay; fullscreen; picture-in-picture; encrypted-media" frameborder="0" scrolling="no"></iframe>

[Смотреть видео на YouTube](https://www.youtube.com/watch?v=EY6c_6YYF10).




## Подготовка к установке {#before-you-begin}

Перед установкой {{ unified-agent-full-name }} выполните следующие шаги:

1. Создайте виртуальную машину в {{ yandex-cloud }} или хост вне {{ yandex-cloud }} на одной из [поддерживаемых операционных систем](#supported-os), например, Ubuntu 16.04 или выше.

1. (Опционально) [Установите Docker](https://docs.docker.com/install/), если через него вы хотите запускать {{ unified-agent-short-name }}. Docker уже предустановлен в Fedora CoreOS.
   - Настройте публичный IPv4-адрес (рекомендуется).
   - [Настройте Docker для работы с IPv6](https://docs.docker.com/config/daemon/ipv6) и [включите сетевую трансляцию адресов](https://medium.com/@skleeschulte/how-to-enable-ipv6-for-docker-containers-on-ubuntu-18-04-c68394a219a2), если вы не можете использовать публичный IPv4-адрес.

1. [Создайте сервисный аккаунт](../../../../iam/operations/sa/create.md) в каталоге, куда будут записываться метрики, и [назначьте ему роль](../../../../iam/operations/sa/assign-role-for-sa.md) `{{ roles-monitoring-editor }}`.

1. Настройте авторизацию агента в {{ monitoring-full-name }} API:
   - Если агент установлен на виртуальную машину в {{ yandex-cloud }}, [привяжите созданный сервисный аккаунт](../../../../compute/operations/vm-connect/auth-inside-vm.md#link-sa-with-instance) к виртуальной машине. В этом случае агент будет автоматически получать IAM-токен сервисного аккаунта из сервиса-метаданных.
   - Если агент установлен на хосте вне {{ yandex-cloud }}, [создайте авторизованный ключ](../../../../iam/operations/authentication/manage-authorized-keys.md#create-authorized-key) для сервисного аккаунта. Подробнее про поставку метрик с хостов вне {{ yandex-cloud }} читайте в разделе [{#T}](../../../operations/unified-agent/non-yc.md).


## Установка {#setup}

Установите {{ unified-agent-short-name }} одним из способов:

{% list tabs group=unified_agent %}

- Docker-образ {#docker}

  Установите Docker Engine. Воспользуйтесь [инструкцией](https://docs.docker.com/engine/install/#supported-platforms) по установке и запуску Docker для вашей операционной системы.

  {{ unified-agent-short-name }} распространяется в виде Docker-образа. Образ опубликован в репозитории `{{ registry }}/yc` с названием `unified_agent` и тегом `latest`. Образ содержит бинарный файл с агентом и конфигурационный файл, настраивающий агент для [поставки системных метрик Linux](../../../operations/unified-agent/linux_metrics.md) в {{ monitoring-full-name }}.

  Чтобы загрузить Docker-образ, выполните команду:

  ```bash
  sudo docker pull {{ registry }}/yc/unified-agent:latest
  ```

- deb-пакет {#deb}

  {{ unified-agent-short-name }} распространяется в виде deb-пакета для операционных систем Ubuntu 16.04 и выше, Debian 9 и выше. Пакет содержит бинарный файл с агентом и конфигурационный файл, расположенный в `/etc/yandex/unified_agent/config.yml`.

  Чтобы установить {{ unified-agent-short-name }}:

  {% include [agent-setup-deb](../../../../_includes/monitoring/agent-setup-deb.md) %}


  {% include [agent-setup-actions](../../../../_includes/monitoring/agent-setup-actions.md) %}

- Бинарный файл {#binary}

  {{ unified-agent-short-name }} распространяется в виде бинарного файла, собранного под архитектуру x86-64 / amd64 для операционных систем Ubuntu 16.04 и выше.

  Чтобы скачать последнюю версию агента в виде бинарного файла, выполните команду:

  ```bash
  ua_version=$(curl --silent https://{{ s3-storage-host }}/yc-unified-agent/latest-version) \
  bash -c 'curl --silent --remote-name https://{{ s3-storage-host }}/yc-unified-agent/releases/$ua_version/unified_agent && chmod +x ./unified_agent'
  ```

- При создании ВМ {#vm}

  Вы можете установить {{ unified-agent-short-name }} при создании виртуальной машины в консоли управления, через CLI, API или {{ TF }}.

  Чтобы установить агент из [консоли управления]({{ link-console-main }}), в блоке **{{ ui-key.yacloud.compute.instances.create.section_monitoring }}** включите опцию **{{ ui-key.yacloud.compute.instances.create.unified-agent }}**.

  Чтобы установить агент через CLI, API или {{ TF }}, укажите в [пользовательских метаданных](../../../../compute/concepts/metadata/sending-metadata.md) (`user-data`) строку:

  
  ```text
  #cloud-config\nruncmd:\n  - wget -O - https://monitoring.{{ api-host }}/monitoring/v2/unifiedAgent/config/install.sh | bash
  ```




  Для установки агента и отправки метрик у виртуальной машины должен быть доступ в интернет.

  {% include [agent-setup-actions](../../../../_includes/monitoring/agent-setup-actions.md) %}


  После разворачивания ВМ {{ unified-agent-short-name }} запустится автоматически и начнет передавать базовые метрики ВМ в сервис {{ monitoring-full-name }}.

  Обновление агента выполняйте вручную через бинарный файл.

{% endlist %}


## Обновление {#update}

Обновите {{ unified-agent-short-name }} одним из способов:

{% list tabs group=unified_agent %}

- Docker-образ {#docker}

  1. Остановите контейнер `ua`:

      ```bash
      sudo docker stop ua
      ```

  1. Удалите контейнер:

      ```bash
      sudo docker rm ua
      ```

  1. [Запустите](./run-and-stop.md#run) контейнер.

- deb-пакет {#deb}

  Заново скачайте и [установите](#setup) deb-пакет последней версии.

- Бинарный файл {#binary}

  {% include [agent-update-binary](../../../../_includes/monitoring/agent-update-binary.md) %}

- При создании ВМ {#vm}

  {% include [agent-update-binary](../../../../_includes/monitoring/agent-update-binary.md) %}

{% endlist %}


#### Что дальше {#what-is-next}

- [Узнайте, как запустить и остановить {{ unified-agent-short-name }}](./run-and-stop.md)
- [Изучите концепции {{ unified-agent-short-name }}](./index.md)
- [Узнайте подробнее о конфигурировании {{ unified-agent-short-name }}](./configuration.md)
- [Ознакомьтесь с рекомендациями по эксплуатации {{ unified-agent-short-name }}](./best-practices.md)
