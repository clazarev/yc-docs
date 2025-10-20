# Требования к шифрованию данных и управлению ключами и секретами

## 4. Шифрование данных и управление ключами {#data-encryption-and-key-management}


{{ yandex-cloud }} предоставляет встроенные функции шифрования при использовании ряда сервисов. В зоне ответственности клиента находится включение шифрования в этих сервисах, а также самостоятельная реализация шифрования в других компонентах обработки критичных данных. Для шифрования данных и управления ключами шифрования предназначен сервис [{{ kms-name }}](../../../kms/) ({{ kms-short-name }}).

API сервисов {{ yandex-cloud }} поддерживают наборы алгоритмов (cipher suits) и версии протокола TLS, отвечающие требованиям стандарта PCI DSS и другим стандартам.

### Шифрование в состоянии покоя (at rest) {#at-rest}

По умолчанию все пользовательские данные в состоянии покоя (at rest) зашифрованы на уровне {{ yandex-cloud }}. Шифрование на уровне {{ yandex-cloud }} является реализацией одной из лучших практик по защите данных пользователей и выполняется на ключах {{ yandex-cloud }}.

Если ваша корпоративная политика информационной безопасности предъявляет требования к длине ключа или частоте [ротации ключей](../../../kms/operations/key.md#rotate), вы можете шифровать данные собственными ключами. Для этого можно использовать сервис {{ kms-short-name }} и его интеграцию с другими сервисами {{ yandex-cloud }}, либо реализовать шифрование на уровне Data plane полностью самостоятельно.

{{ yandex-cloud }} предоставляет функции шифрования в состоянии покоя (at rest) для следующих сервисов:
* {{ compute-name }} (шифрование дисков ВМ);
* {{ objstorage-name }} (шифрование бакетов);
* {{ managed-k8s-name }} (шифрование секретов).

#### 4.1 Включено шифрование дисков в {{ compute-full-name }} {#compute-encryption}

| ID требования | Критичность |
| --- | --- |
| CRYPT17 | Высокая |

**Поиск зашифрованных дисков ВМ:**

{% list tabs group=instructions %}

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для поиска зашифрованных дисков:

      {% cut "**Bash**" %}

      ```bash
      export ORG_ID=<ID_организации>
      CLOUDS=$(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id')

      echo "Encrypted disks:"
      for CLOUD_ID in $CLOUDS
        do
        FOLDERS=$(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id')
        for FOLDER_ID in $FOLDERS
        do
          DISKS=$(yc compute disk list --folder-id $FOLDER_ID --format=json | jq -r '.[] | select(.kms_key.key_id)' | jq -r '.id')

          if [[ -n "$DISKS" ]]; then
            for DISK in $DISKS
            do
              DISKDATA=$(yc compute disk get --id $DISK --folder-id $FOLDER_ID --format=json)
              VM_ID=$(echo $DISKDATA| jq -r '.instance_ids[]')

              VMDATA=""

              if [[ -n "$VM_ID" ]]; then
                VMDATA=$(yc compute instance get --id $VM_ID --folder-id $FOLDER_ID --format=json)
              fi

              echo "------------"
              echo "CLOUD_ID:" $CLOUD_ID
              echo "FOLDER_ID:" $FOLDER_ID
              echo "DISK_ID: "$(echo $DISKDATA | jq -r '.id')
              echo "DISK_NAME: "$(echo $DISKDATA | jq -r '.name')
              echo "DISK_TYPE: "$(echo $DISKDATA | jq -r '.type_id')
              echo "DISK_ZONE: "$(echo $DISKDATA | jq -r '.zone_id')
              echo "DISK_SIZE: "$(echo $DISKDATA | jq -r '.size')
              echo "DISK_KEY: "$(echo $DISKDATA | jq -r '.kms_key')

              if [[ -n "$VMDATA" ]]; then
                echo "VM_ID: "$(echo $VMDATA | jq -r '.id')
                echo "VM_NAME: "$(echo $VMDATA | jq -r '.name')
                echo "VM_STATUS: "$(echo $VMDATA | jq -r '.status')
              fi
              echo "------------"
            done
          fi
        done
      done
      ```

      {% endcut %}

      {% cut "**PowerShell**" %}

      ```powershell
      $ORG_ID = "<ID_организации>"

      $Clouds = yc resource-manager cloud list --organization-id=$ORG_ID --format=json | ConvertFrom-Json | Select @{n="CloudID";e={$_.id}}, created_at, @{n="CloudName";e={$_.name}}, organization_id

      $EncryptedVMs = @()
      $VMDisks = @()

      foreach ($Cloud in $Clouds) {
        $Folders = yc resource-manager folder list --cloud-id $Cloud.CloudID --format=json | ConvertFrom-Json

        foreach($Folder in $Folders) {
          $FolderDiskList = yc compute disk list --folder-id $Folder.id --format=json | ConvertFrom-Json | where{$_.kms_key}

          foreach($Disk in $FolderDiskList) {
            $VMData = $null

            if($Disk.instance_ids) {
              $VMData = yc compute instance get --id $Disk.instance_ids --folder-id $Folder.id --format=json | ConvertFrom-Json
            }

            $EncryptedVMs += $Disk | Select @{n="CloudID";e={$Cloud.CloudID}}, @{n="CloudName";e={$Cloud.CloudName}}, @{n="FolderID";e={$Folder.id}}, @{n="FolderName";e={$Folder.name}}, @{n="DiskID";e={$_.id}}, @{n="DiskName";e={$_.name}}, @{n="DiskType";e={$_.type_id}}, zone_id, @{n="DiskSize";e={$_.size/1GB}}, kms_key, @{n="VMID";e={$VMData.id}}, @{n="VMName";e={$VMData.name}}, @{n="VMStatus";e={$VMData.status}}
          }
        }
      }

      $EncryptedVMs
      ```

      {% endcut %}

{% endlist %}

Проверьте список возвращенных зашифрованных дисков. Если список соответствует вашей модели угроз, то дополнительных действий не требуется. Если какие-либо диски отсутствуют в списке, то выполните следующие действия:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором находится диск.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. На панели слева выберите ![image](../../../_assets/console-icons/hard-drive.svg) **{{ ui-key.yacloud.compute.disks_ddfdb }}** и найдете в списке диск, который требуется зашифровать.

      Если диск присоединен к ВМ и ВМ включена, рекомендуется выключить ее.
  1. [Создайте](../../../compute/operations/disk-control/create-snapshot.md) снимок диска.
  1. Создайте из полученного снимка новый зашифрованный диск:

      1. Нажмите кнопку **{{ ui-key.yacloud.compute.disks.button_create }}**.
      1. В открывшейся форме:
          1. В поле **{{ ui-key.yacloud.compute.instances.create-disk.field_name }}** задайте имя диска.
          1. В поле **{{ ui-key.yacloud.compute.disk-form.field_zone }}** укажите нужную [зону доступности](../../../overview/concepts/geo-scope.md).
          1. В поле **{{ ui-key.yacloud.compute.instances.create-disk.field_source }}** выберите `{{ ui-key.yacloud.compute.instances.create-disk.value_source-snapshot }}` и выберите созданный ранее снимок.
          1. В поле **{{ ui-key.yacloud.compute.disk-form.field_type }}** задайте необходимый [тип диска](../../../compute/concepts/disk.md#disks-types).
          1. В блоке **{{ ui-key.yacloud.compute.disk-form.section_encryption }}** включите опцию **{{ ui-key.yacloud.compute.disk-form.label_disk-encryption }}** и выберите или создайте [ключ шифрования](../../../kms/concepts/key.md) {{ kms-short-name }}.
          1. Нажмите кнопку **{{ ui-key.yacloud.compute.disks.create.button_create }}**.
  1. После создания зашифрованного диска присоедините его к нужной ВМ вместо незашифрованного.

{% endlist %}

#### 4.2 В {{ objstorage-full-name }} включено шифрование данных at rest с ключом {{ kms-short-name }} {#storage-kms}

Для защиты критичных данных в {{ objstorage-full-name }} рекомендуется использовать шифрование бакета на стороне сервера с помощью ключей {{ kms-full-name }} (server-side encryption). Такое шифрование защищает от случайной или намеренной публикации содержимого бакета в интернете. Подробнее см. в разделе [Шифрование](../../../storage/concepts/encryption.md) документации {{ objstorage-name }}.

| ID требования | Критичность |
| --- | --- |
| CRYPT1 | Средняя |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить бакеты.
  1. В списке сервисов выберите **{{ objstorage-name }}**.
  1. Перейдите в настройки бакета.
  1. Перейдите на вкладку **Шифрование**.
  1. Убедитесь, что шифрование включено и указан {{ kms-short-name }} ключ шифрования.
  1. Если шифрование включено, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. [Настройте](../../../storage/tools/aws-cli.md) AWS CLI на работу с облаком.
  1. Выполните команду, чтобы проверить, что шифрование включено:

     ```bash
     aws --endpoint-url=https://{{ s3-storage-host }}/ \
     s3api get-bucket-encryption \
     --bucket <имя бакета>
     ```

  1. Если шифрование включено, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Настройте шифрование бакета согласно [инструкции](../../../storage/operations/buckets/encrypt.md).

### Шифрование в состоянии передачи (in transit) {#in-transit}

В большинстве случаев соединение с сервисами {{ yandex-cloud }} возможно только с использованием HTTPS. Однако в некоторых сценариях data plane доступ к сервисам может быть осуществлен и по HTTP, без шифрования соединения на прикладном уровне. Во всех таких сценариях у пользователя есть возможность выбрать в настройках сервиса, какой протокол использовать при data plane-операциях: HTTP или HTTPS, а в случае выбора HTTPS указать собственный TLS-сертификат.

{% note info %}

Убедитесь, что используете для работы (или соединения) с API сервисов {{ yandex-cloud }} протокол TLS версии 1.2 и выше, так как более ранние версии подвержены уязвимостям.

Например, использование gRPC-интерфейсов {{ yandex-cloud }} гарантирует работу по TLS 1.2 и выше, так как протокол HTTP/2, на основе которого работает gRPC, устанавливает TLS 1.2 в качестве минимальной поддерживаемой версии протокола TLS.

Поддержка устаревших протоколов TLS в сервисах {{ yandex-cloud }} [будет постепенно прекращена](../../../security/security-bulletins/index.md).

{% endnote %}

{{ yandex-cloud }} предоставляет возможность использования собственных TLS-сертификатов для следующих сервисов:
* {{ objstorage-name }};
* {{ alb-name }};
* {{ api-gw-name }};
* {{ cdn-name }}.

#### 4.3 В {{ objstorage-full-name }} включено HTTPS для хостинга статического сайта {#storage-https}

[{{ objstorage-name }}](../../../storage/) поддерживает безопасное подключение по протоколу HTTPS. Вы можете загрузить собственный сертификат безопасности, если к сайту в {{ objstorage-name }} требуется доступ по протоколу HTTPS. Также доступна интеграция с сервисом [{{ certificate-manager-name }}](../../../certificate-manager/). См. инструкции в документации {{ objstorage-name }}:
* [Настройка HTTPS](../../../storage/operations/hosting/certificate.md)
* [Бакет](../../../storage/concepts/bucket.md)

При работе с сервисом [{{ objstorage-name }}](../../../storage/) необходимо убедиться, что в клиенте отключена поддержка протоколов TLS ниже версии 1.2. При помощи политики (bucket policy) [`aws:securetransport`](../../../storage/s3/api-ref/policy/conditions.md) необходимо проверить, что для бакета настроен запрет на работу без протокола TLS.

| ID требования | Критичность |
| --- | --- |
| CRYPT2 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В [консоли управления]({{ link-console-main }}) в списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** и перейдите в нужный бакет.
  1. На панели слева выберите ![image](../../../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}**.
  1. Выберите вкладку **{{ ui-key.yacloud.storage.bucket.switch_https }}**.
  1. Убедитесь, что доступ по протоколу HTTPS включен и указан TLS-сертификат.
  1. Если доступ по HTTPS включен, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  Выполните команду, указав имя нужного бакета:

  ```bash
  yc storage bucket get-https <имя_бакета>
  ```

  Если в поле `certificate_id` команда вернула идентификатор сертификата, значит доступ по протоколу HTTPS включен и рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

[Включите](../../../storage/operations/hosting/certificate.md) доступ по HTTPS, если бакет используется для хостинга статического сайта.

#### 4.4 В {{ alb-full-name }} используется HTTPS {#alb-https}

Сервис [{{ alb-name }}](../../../application-load-balancer/) поддерживает HTTPS-обработчик с загрузкой [сертификата](../../../certificate-manager/concepts/imported-certificate.md) из {{ certificate-manager-name }}. См. [описание настройки обработчика](../../../application-load-balancer/concepts/application-load-balancer.md#listener) в документации {{ alb-full-name }}.

| ID требования | Критичность |
| --- | --- |
| CRYPT3 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}
 
  1. В консоли управления выберите облако или каталог, в которых необходимо проверить балансировщики.
  1. В списке сервисов выберите **{{ alb-name }}**.
  1. Перейдите в настройки балансировщика.
  1. Убедитесь, что у обработчика указан протокол **HTTPS**.
  1. Если указан HTTPS, рекомендация выполняется. Если нет, перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех балансировщиков без https:

      {% cut "**Bash**" %}

      ```bash
      export ORG_ID=<ID_организации>
      CLOUDS=$(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id')
      for CLOUD_ID in $CLOUDS
      do
        FOLDERS=$(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id')
        for FOLDER_ID in $FOLDERS
        do
          yc application-load-balancer load-balancer list --folder-id $FOLDER_ID --format=json | jq -r '.[] | select(.listeners[0].tls | not)' | jq -r '.'
        done
      done
      ```

      {% endcut %}

      {% cut "**PowerShell**" %}

      ```powershell
      $ORG_ID = "<ID_организации>"

      $Clouds = yc resource-manager cloud list --organization-id $ORG_ID --format=json | ConvertFrom-Json | Select @{n="CloudID";e={$_.id}}, created_at, @{n="CloudName";e={$_.name}}, organization_id

      $ALBWithoutTLS = @()

      foreach ($Cloud in $Clouds) {
        $Folders = yc resource-manager folder list --cloud-id $Cloud.CloudID --format=json | ConvertFrom-Json

        foreach($Folder in $Folders) {
          $ALBWithoutTLS += yc application-load-balancer load-balancer list --folder-id $Folder.id --format=json | ConvertFrom-Json | where{!$_.listeners.tls}
        }
      }

      $ALBWithoutTLS
      ```

      {% endcut %}

  1. Если выведен пустой список, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Включите HTTPS обработчик согласно [инструкции](../../../application-load-balancer/tutorials/tls-termination/index.md).

#### 4.5 В {{ api-gw-full-name }} используется HTTPS и собственный домен {#api-gateway-https}

[{{ api-gw-name }}](../../../api-gateway/) обеспечивает безопасное подключение по протоколу HTTPS. Вы можете привязать собственный домен и загрузить собственный сертификат безопасности для доступа к вашему [API-шлюзу](../../../api-gateway/concepts/index.md) по протоколу HTTPS.

| ID требования | Критичность |
| --- | --- |
| CRYPT4 | Средняя |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить шлюзы.
  1. В списке сервисов выберите **{{ api-gw-name }} → Настройки шлюза → Домены**.
  1. Убедитесь, что домен и сертификат подключены.
  1. Если домен и сертификат активны, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех api gateway без подключенных доменов и сертификатов:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for APIGW in $(yc serverless api-gateway list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc serverless api-gateway get --id $APIGW --format json | jq -r '. | select(.attached_domains[0].certificate_id | not)' | jq -r '.id'
     done;
     done;
     done
     ```

  1. Если выведен пустой список, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

1. В консоли управления выберите облако или каталог, в которых необходимо подключить домены и сертификаты.
1. В списке сервисов выберите **{{ api-gw-name }} → Настройки шлюза → Домены**.
1. Подключите домены и сертификаты.

#### 4.6 В {{ cdn-full-name }} используется HTTPS и собственный SSL-сертификат {#cdn-https}

[{{ cdn-name }}](../../../cdn/) поддерживает безопасное подключение по протоколу HTTPS к источникам. Также вы можете загрузить собственный сертификат безопасности для доступа к вашему [CDN-ресурсу](../../../cdn/concepts/resource.md) по протоколу HTTPS.

| ID требования | Критичность |
| --- | --- |
| CRYPT5 | Низкая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить ресурсы.
  1. В списке сервисов выберите **{{ cdn-name }}**.
  1. Перейдите в настройки ресурса, на вкладку **Дополнительно**.
  1. Убедитесь, что в поле **Протокол для источников** указан протокол **HTTPS**.
  1. Убедитесь, что в поле **Сертификат** указан собственный сертификат либо **Let’s encrypt**.
  1. Если указан HTTPS и собственный сертификат, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех ресурсов без подключенных сертификатов и HTTPS до источников:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for CDN in $(yc cdn resource list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc cdn resource get --id $CDN --format json | jq -r '. | select(.origin_protocol=="HTTPS" and .ssl_certificate.type=="CM" | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. Если выведен пустой список, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

[Подключите](../../../cdn/operations/resources/configure-basics.md) сертификат и HTTPS согласно инструкции.

### Самостоятельное шифрование {#self-encryption}

**При использовании сервисов, которые не имеют встроенных функций шифрования, шифрование критичных данных является ответственностью клиента.**

#### 4.7 Используется шифрование данных на уровне приложения {#self-data-app}

Для шифрования данных на уровне приложения (client-side encryption) перед их отправкой в бакет {{ objstorage-full-name }} вы можете использовать следующие подходы:
* Интеграция {{ objstorage-name }} с сервисом {{ kms-name }} для шифрования данных на уровне приложения (client-side encryption). Подробнее смотрите в разделе «Рекомендуемые криптографические библиотеки».
* Шифрование данных на уровне приложения перед отправкой их в {{ objstorage-name }} с помощью сторонних библиотек. При использовании сторонних библиотек и собственных способов управления ключами следует убедиться, что схема работы, используемые алгоритмы и длины ключей соответствуют требованиям регуляторов.

Для шифрования данных на уровне приложения (client-side encryption) рекомендуется использовать следующие библиотеки:
* AWS Encryption SDK и его [интеграцию с {{ kms-short-name }}](../../../kms/tutorials/encrypt/aws-encryption-sdk.md);
* Google Tink и ее [интеграцию с {{ kms-short-name }}](../../../kms/tutorials/encrypt/google-tink.md);
* [SDK {{ yandex-cloud }}](../../../kms/tutorials/encrypt/sdk.md) вместе с любой другой криптографической библиотекой, совместимой с PCI DSS или другими стандартами, применяемыми в вашей компании.

Сравнение библиотек представлено в разделе [Какой способ шифрования выбрать](../../../kms/tutorials/encrypt/) документации {{ kms-short-name }}.

| ID требования | Критичность |
| --- | --- |
| CRYPT7 | Высокая |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Убедитесь, что данные хранятся в зашифрованном виде.

{% endlist %}

#### 4.8 Используется шифрование дисков и снимков виртуальных машин {#managed-vm-kms}

По умолчанию все данные на дисках {{ compute-full-name }} шифруются на уровне базы данных хранилища с помощью системного ключа. Это позволяет защитить данные от компрометации в случае физической кражи дисков из дата-центров {{ yandex-cloud }}. 

Рекомендуем также использовать шифрование дисков и [снимков дисков](../../../compute/concepts/snapshot.md) с помощью пользовательских [симметричных ключей](../../../kms/concepts/key.md) {{ kms-full-name }}. Такой подход позволяет:
* Защищаться от потенциальных угроз нарушения изоляции и компрометации данных на уровне виртуальной инфраструктуры.
* Контролировать шифрование и жизненный цикл ключей {{ kms-short-name }}, а также управлять ими. Подробнее см. в разделе [{#T}](../../../kms/operations/key.md).
* Повысить уровень контроля доступа к данным на диске за счет необходимости прав на ключ {{ kms-short-name }}. Подробнее см. в разделе [{#T}](../../../kms/operations/key-access.md).
* Отслеживать операции шифрования и расшифрования вашим ключом {{ kms-short-name }} с помощью сервиса {{ at-full-name }}. Подробнее см. в разделе [{#T}](../../../kms/concepts/index.md#keys-audit).

Вы можете зашифровать диски следующих типов:
* Сетевой SSD-диск (`network-ssd`).
* Сетевой HDD-диск (`network-hdd`).
* Нереплицируемый SSD-диск (`network-ssd-nonreplicated`).
* Сверхбыстрое сетевое хранилище с тремя репликами (SSD) (`network-ssd-io-m3`).

| ID требования | Критичность |
| --- | --- |
| CRYPT8 | Средняя |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  При [создании диска](../../../compute/operations/disk-create/empty.md) убедитесь, что включена опция **{{ ui-key.yacloud.compute.disk-form.label_disk-encryption }}**.

{% endlist %}

**Инструкции и решения по выполнению:**

[Зашифруйте](../../../compute/operations/disk-control/disk-encrypt.md) диск виртуальной машины {{ compute-full-name }}.

### Управление ключами {#keys}

Для шифрования данных и управления ключами рекомендуется использовать [{{ kms-name }}](../../../kms/tutorials/encrypt/sdk.md). {{ kms-short-name }} предназначен для защиты данных в инфраструктуре {{ yandex-cloud }}, а также подходит для шифрования и расшифровки любых ваших данных.

{{ kms-short-name }} использует схему шифрования AES-GCM. Вы можете выбрать длину ключа: 128, 192 или 256 — и настроить период ротации ключей в зависимости от своих потребностей.

#### 4.9 Ключи {{ kms-name }} хранятся в аппаратном модуле безопасности (HSM) {#keys-hsm}

В продакшн-среде рекомендуется использовать отдельные ключи, все крипто-операции с которыми будут выполняться только внутри специализированного аппаратного устройства. Подробнее см. статью [Аппаратный модуль безопасности (HSM)](../../../kms/concepts/hsm.md).

Чтобы использовать HSM, при создании ключа выберите тип алгоритма AES-256 HSM. Все операции с этим ключом будут выполняться внутри HSM, дополнительные действия не требуются.

Рекомендуется использовать HSM для ключей {{ kms-short-name }}, это увеличивает уровень безопасности.

| ID требования | Критичность |
| --- | --- |
| CRYPT9 | Информационная |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить ключи.
  1. В списке сервисов выберите **{{ kms-name }}**.
  1. Перейдите на вкладку **Ключи**.
  1. Убедитесь, что в поле **Алгоритм шифрования** указан **AES-256 HSM**.
  1. Если указан AES-256 HSM, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех ключей {{ kms-short-name }} организации и их алгоритмов шифрования:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do yc kms symmetric-key list --folder-id=$FOLDER_ID --format json | jq -r '.[] | "KEY_ID " + .id + "FOLDER_ID " + .folder_id + "ALGORITM_ID " + .default_algorithm' 
     done;
     done
     ```

  1. Если алгоритм шифрования содержит AES-256 HSM, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

[Установите](../../../kms/operations/symmetric-encryption.md) алгоритм шифрования для ключей {{ kms-short-name }} «AES-256 HSM».

#### 4.10 Права на управление ключами в {{ kms-short-name }} выданы контролируемым пользователям {#keys-controlled-users}

Для доступа к сервису {{ kms-short-name }} необходимо использовать [IAM-токен](../../../iam/concepts/authorization/iam-token.md).

В случае автоматизации работы с {{ kms-short-name }} рекомендуется создать [сервисный аккаунт](../../../iam/concepts/users/service-accounts.md) и выполнять команды и скрипты от его имени. Если вы используете виртуальные машины, получите IAM-токен для сервисного аккаунта через механизм [назначения сервисного аккаунта](../../../compute/operations/vm-connect/auth-inside-vm.md) виртуальной машине. Другие способы получения IAM-токена для сервисного аккаунта приведены в статье [Получение IAM-токена для сервисного аккаунта](../../../iam/operations/iam-token/create-for-sa.md) документации {{ iam-short-name }}.

Рекомендуется выдавать пользователям и сервисным аккаунтам гранулярные доступы на конкретные ключи сервиса {{ kms-short-name }}. Подробнее см. статью [Управление доступом в {{ kms-name }}](../../../kms/security/) документации {{ kms-short-name }}.

Подробнее о мерах безопасности при управлении доступом читайте в статье [Аутентификация и управление доступом](../../../security/standard/authentication.md).

Для того чтобы проверить права доступа к ключу {{ kms-short-name }}, необходимо проверить, у кого есть права:
* на организацию, облако, каталоги с правами: `admin`, `editor`, `kms.admin`, `kms.editor`, `kms.keys.encrypterDecrypter`;
* на ключи: `kms.keys.encrypterDecrypter` и `kms.editor`.

| ID требования | Критичность |
| --- | --- |
| CRYPT10 | Средняя |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}
 
  1. В консоли управления выберите облако или каталог, в которых необходимо проверить права на ключ.
  1. Перейдите на вкладку **Права доступа**.
  1. Убедитесь, что роли `admin`, `editor`, `kms.admin`, `kms.editor`, `kms.keys.encrypterDecrypter` имеют только контролируемые пользователи.
  1. Проверить права доступа к самим ключам возможно только через CLI.

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для поиска учетных записей на уровне организации:

     ```bash
     export ORG_ID=<ID организации>
     yc organization-manager organization list-access-bindings --id=${ORG_ID} --format=json | jq -r '.[] | select(.role_id=="admin" or .role_id=="editor" or .role_id=="kms.admin" or .role_id=="kms.editor" or .role_id=="kms.keys.encrypterDecrypter")'
     ```

  1. Если в списке отсутствуют учетные записи, рекомендация выполнена. В противном случае перейдите к п. «Инструкции и решения по выполнению».

  1. Найдите учетные записи с назначенными ролями на уровне облаков:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do yc resource-manager cloud list-access-bindings --id=$CLOUD_ID --format=json | jq -r '.[] | select(.role_id=="admin" or .role_id=="editor" or .role_id=="kms.admin" or .role_id=="kms.editor" or .role_id=="kms.keys.encrypterDecrypter")'
     done
     ```

  1. Если в списке отсутствуют учетные записи, рекомендация выполнена. В противном случае перейдите к п. «Инструкции и решения по выполнению».

  1. Выполните команду для поиска учетных записей с назначенными примитивными ролями на уровне всех каталогов в ваших облаках:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
     do yc resource-manager folder list-access-bindings --id=$FOLDER_ID --format=json | jq -r '.[] | select(.role_id=="admin" or .role_id=="editor" or .role_id=="kms.admin" or .role_id=="kms.editor" or .role_id=="kms.keys.encrypterDecrypter")' && echo $FOLDER_ID
     done;
     done
     ```

  1. Если в списке отсутствуют учетные записи, рекомендация выполнена. В противном случае перейдите к п. «Инструкции и решения по выполнению».

  1. Найдите учетные записи с назначенными ролями на уровне ключей:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for KEY in $(yc kms symmetric-key list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc kms symmetric-key list-access-bindings --id $KEY --format json 
     done;
     done;
     done
     ```

{% endlist %}

**Инструкции и решения по выполнению:**

Проконтролируйте, кому предоставлен доступ к ключам {{ kms-short-name }}.

#### 4.11 Для {{ kms-short-name }} ключей включена ротация {#keys-rotation}

Для повышения безопасности инфраструктуры рекомендуется разделить ключи шифрования на две группы:
* Ключи для сервисов, которые обрабатывают критичные данные, но не хранят их. Например, {{ message-queue-name }}, {{ sf-name }}.
* Ключи для сервисов, которые хранят критичные данные. Например, Managed Services for Databases.

Для первой группы рекомендуется настроить автоматическую ротацию с периодом ротации больше, чем срок обработки данных в этих сервисах. По истечении периода ротации старые версии должны быть удалены. При автоматической ротации и удалении старых версий ключей ранее обработанные данные не могут быть восстановлены и расшифрованы.

Для сервисов хранения данных рекомендуется использовать либо ручные процедуры ротации, либо автоматическую ротацию ключей в зависимости от внутренних процедур обработки критичных данных.

Безопасным значением для AES-GCM является шифрование 4 294 967 296 (= 2<sup>32</sup>) блоков. После достижения этого количества шифрованных блоков необходимо создать новую версию ключа шифрования данных. Подробнее про режим работы AES-GCM см. в [материалах NIST](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf).

{% note info %}

Удаление какой-либо версии ключа равносильно уничтожению всех данных, зашифрованных с ее помощью. Ключ можно защитить от удаления с помощью установки параметра deletionProtection, однако этот параметр не защищает от удаления отдельных версий.	 

{% endnote %}

Подробнее о ротации ключей см. в разделе [Версия ключа](../../../kms/concepts/version.md) документации {{ kms-short-name }}.

| ID требования | Критичность |
| --- | --- |
| CRYPT11 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить ключи.
  1. В списке сервисов выберите **{{ kms-name }}**.
  1. Перейдите в настройки ключа.
  1. Найдите параметр **Период ротации**.
  1. Если в параметре указано любое значение, отличное от **Нет ротации**, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех ключей {{ kms-short-name }} организации и их алгоритмов шифрования:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do yc kms symmetric-key list --folder-id=$FOLDER_ID --format=json | jq -r '.[] | select(.rotation_period | not)' | jq -r '.id' 
     done;
     done
     ```

  1. Если выведен пустой список, то рекомендация выполняется. Если нет, перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Установите период ротации для ключей.

#### 4.12 Для ключей {{ kms-short-name }} включена защита от удаления {#keys-deletion-protection}

Удаление {{ kms-short-name }} ключа приводит к гарантированному удалению данных, поэтому необходимо защищать ключи от непреднамеренного удаления. В {{ kms-short-name }} существует соответствующая функция.

| ID требования | Критичность |
| --- | --- |
| CRYPT12 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить ключи.
  1. В списке сервисов выберите **{{ kms-name }}**.
  1. Перейдите в настройки ключа.
  1. Найдите параметр **Защита от удаления**.
  1. Если в параметре указано **Да**, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для вывода списка всех ключей KMS без защиты от удаления:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do yc kms symmetric-key list --folder-id=$FOLDER_ID --format=json | jq -r '.[] | select(.deletion_protection | not)' | jq -r '.id' 
     done;
     done
     ```

  1. Если выведен пустой список, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Установите защиту от удаления.

### Управление секретами {#secrets}

Критичные данные и секреты для доступа к данным (токены аутентификации, API-ключи, ключи шифрования и т. п.) не следует использовать в открытом виде в коде, в названиях и описаниях объектов облака, в метаданных виртуальных машин и т. д. Вместо этого используйте сервисы для хранения секретов, такие как {{ lockbox-short-name }} или HashiCorp Vault.

#### 4.13 В организации используется {{ lockbox-full-name }} для безопасного хранения секретов {#secrets-lockbox}

Критичные данные и секреты для доступа к данным (токены аутентификации, API-ключи, ключи шифрования и т. п.) не следует использовать в открытом виде в коде, в названиях и описаниях объектов облака, в метаданных виртуальных машин и т. д. Вместо этого используйте сервисы для хранения секретов, такие как {{ lockbox-short-name }}.

Сервис {{ lockbox-short-name }} обеспечивает безопасное хранение секретов только в зашифрованном виде. Шифрование выполняется с помощью {{ kms-short-name }}. Для разграничения доступа к секретам используйте сервисные роли.

Инструкции по работе с сервисом см. в [документации](../../../lockbox/) {{ lockbox-short-name }}.

{% note info %}

При работе в {{ TF }} рекомендуем [заполнять]({{ tf-provider-resources-link }}/lockbox_secret_version) содержимое секрета скриптом. В таком случае содержимое не останется в файле `.tfstate`.

{% endnote %}

| ID требования | Критичность |
| --- | --- |
| CRYPT13 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить секреты.
  1. В списке сервисов выберите **{{ lockbox-short-name }}**.
  1. Убедитесь, что используется как минимум один секрет {{ lockbox-short-name }}.
  1. Если используется {{ lockbox-short-name }}, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Посмотрите доступные вам организации и зафиксируйте необходимый ID:

     ```bash
     yc organization-manager organization list
     ```

  1. Выполните команду для поиска как минимум одного секрета {{ lockbox-short-name }}:
 
     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do yc lockbox secret list --folder-id=$FOLDER_ID --format=json 
     done;
     done
     ```

  1. Если выведен пустой список, рекомендация выполняется. Если нет, перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Храните секреты в {{ lockbox-short-name }}.

#### 4.14 Для {{ serverless-containers-name }} и {{ sf-name }} используются секреты {{ lockbox-short-name }} {#secrets-serverless-functions}

При работе с {{ serverless-containers-name }} или {{ sf-name }} часто возникает необходимость использовать секрет (токен, пароль и т.д.).

Если указать секретную информацию в переменных окружения, она может быть доступна для просмотра любому пользователю облака с правами на просмотр и использование функции и влечет за собой риски ИБ.

Рекомендуется использовать для этих целей интеграцию Serverless с {{ lockbox-short-name }}. Вы можете указать конкретный секрет из сервиса {{ lockbox-full-name }} и сервисный аккаунт с правами на данный секрет для использования его в функции или контейнере. 

Рекомендуется убедиться, что секреты используются именно таким образом. 

| ID требования | Критичность |
| --- | --- |
| CRYPT14 | Средняя |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить функции.
  1. В списке сервисов выберите **{{ sf-name }}**.
  1. Перейдите в настройки функции, на вкладку **Редактор**.
  1. Найдите параметр **Секреты {{ lockbox-short-name }}**.
  1. Если в параметрах каждого объекта указано **Секреты {{ lockbox-short-name }}** или отсутствуют env с секретными данными, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

- Проверка через CLI {#cli}

  1. Выполните команду для поиска всех облачных функций, которые не используют секреты {{ lockbox-short-name }} и убедитесь, что в данных функциях не используются секретные данные в env:

     ```bash
     export ORG_ID=<ID организации>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for VER in $(yc serverless function version list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc serverless function version get $VER --format=json | jq -r '. | select(.secrets | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. Если выведен пустой список, рекомендация выполняется. В противном случае перейдите к п. «Инструкции и решения по выполнению».

{% endlist %}

**Инструкции и решения по выполнению:**

Удалите секретные данные из env и воспользуйтесь функционалом интеграции с {{ lockbox-short-name }}:
* [{#T}](../../../serverless-containers/operations/lockbox-secret-transmit.md).
* [{#T}](../../../functions/operations/function/lockbox-secret-transmit.md).

#### 4.15 При работе {{ coi }} используется шифрование секретов {#secrets-coi}

{{ kms-short-name }} предоставляет возможность шифрования секретов, используемых в конфигурации {{ TF }}, в частности, для передачи секретов на виртуальную машину в зашифрованном виде. См. инструкцию в разделе [Шифрование секретов в {{ TF-full }}](../../../kms/tutorials/terraform-secret.md) документации {{ kms-short-name }}. Передача секретов через переменные окружения в открытом виде небезопасна, поскольку они отображаются в свойствах ВМ.

| ID требования | Критичность |
| --- | --- |
| CRYPT15 | Высокая |

**Инструкции и решения по выполнению:**

[Шифрование секретов в {{ TF }} для передачи на ВМ с {{ coi }}](https://github.com/yandex-cloud-examples/yc-encrypt-coi-secrets).

Другие рекомендации по безопасному использованию {{ TF }} см. в статье [Безопасная конфигурация: {{ TF }}](../../../security/standard/virtualenv-safe-config.md#tf-using).

#### 4.16 Администратор облака имеет инструкцию по действиям в случае компрометации секретов его облака {#secrets-scanning}

В {{ yandex-cloud }} по умолчанию для всех включен [Secret Scanning Service](../../../security/operations/search-secrets.md).
Он обнаруживает облачные структурированные секреты в открытом доступе в следующих источниках:

* в публичных репозиториях Github;
* в поисковом индексе Яндекса;
* в Helm-чартах {{ k8s }} маркетплейса.

Список облачных секретов для обнаружения:

* {{ yandex-cloud }} Session Cookie;
* {{ yandex-cloud }} {{ iam-short-name }} token;
* {{ yandex-cloud }} API Key;
* Yandex Passport OAuth token;
* {{ yandex-cloud }} AWS API compatible Access Secret;
* {{ yandex-cloud }} {{ captcha-name }} Server Key;
* {{ lockbox-short-name }} структурированные секреты.

Сервис автоматически уведомляет клиента о найденном секрете, который относится к его инфраструктуре:

* по электронной почте;
* с помощью [событий](../../../audit-trails/concepts/events.md) {{ at-full-name }}.

| ID требования | Критичность |
| --- | --- |
| CRYPT16 | Информационная |

**Инструкции и решения по выполнению:**

Убедитесь, что:

* [Контактные данные ответственного за организацию актуальны](../../../security/standard/authentication.md#org-contacts).
* [Включен сервис {{ at-full-name }} на уровне организации](../../../security/standard/audit-logs.md#audit-trails).
* Администратор ознакомлен с [инструкцией](../../../security/operations/search-secrets.md#secret-is-leaked) по действиям при компрометации секретов.
