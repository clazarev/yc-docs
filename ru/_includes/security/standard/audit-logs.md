# Требования к сбору, мониторингу и анализу аудитных логов

## 5. Сбор, мониторинг и анализ аудитных логов {#audit-logs}


Аудитные логи (журналы аудита) — это записи обо всех событиях в системе, включая доступ к ней и выполненные операции. Сбор и проверка аудитных логов позволяют контролировать соблюдение установленных процедур и стандартов безопасности и выявить изъяны в механизмах безопасности.

События в аудитных логах относятся к различным уровням:
* [уровень {{ yandex-cloud }}](#audit-trails) — события, происходящие с ресурсами {{ yandex-cloud }};
* [уровень ОС](#os-level);
* [уровень приложений](#app-level);
* [уровень сети](#network-level) (Flow Logs).

{% note info %}

О событиях {{ k8s }} читайте в разделе [Сбор, мониторинг и анализ аудитных логов в {{ managed-k8s-full-name }}](../../../security/domains/kubernetes.md#collection-monitoring-analysis-audit-logs).

{% endnote %}

### Общее {#general}

#### 5.1 Включен сервис {{ at-full-name }} на уровне организации {#audit-trails}

Основным инструментом сбора логов уровня {{ yandex-cloud }} является сервис [{{ at-full-name }}](../../../audit-trails/concepts/index.md). Сервис позволяет собирать аудитные логи о происходящих с ресурсами {{ yandex-cloud }} событиях и загружать эти логи в бакет {{ objstorage-full-name }} или лог-группу {{ cloud-logging-name }} для дальнейшего анализа или экспорта. См. [инструкцию](../../../audit-trails/quickstart.md), как запустить сбор логов.

Аудитные логи {{ at-name }} могут содержать два разных типа событий: [события уровня конфигурации](../../../audit-trails/concepts/events.md) и [события уровня сервисов](../../../audit-trails/concepts/events-data-plane.md).

К [событиям уровня конфигурации](../../../audit-trails/concepts/format.md) относятся действия, связанные с конфигурированием ресурсов {{ yandex-cloud }}, такие как создание, изменение или удаление компонентов инфраструктуры, пользователей или политик. К [событиям уровня сервисов](../../../audit-trails/concepts/format-data-plane.md) относятся изменения и действия, которые происходят с данными и ресурсами внутри сервисов {{ yandex-cloud }}. По умолчанию {{ at-name }} не регистрирует события уровня сервисов. [Включать](../../../audit-trails/quickstart.md#the-trail-creation) сбор аудитных логов уровня сервисов нужно отдельно для каждого из поддерживаемых сервисов.

Подробнее см. в разделе [{#T}](../../../audit-trails/concepts/control-plane-vs-data-plane.md).

 Для сбора метрик, анализа некоторых событий уровня {{ yandex-cloud }} и настройки оповещений рекомендуется использовать сервис [{{ monitoring-full-name }}](../../../monitoring/).  С его помощью возможно отслеживать, например, резкое возрастание нагрузки на {{ compute-name }}, RPS сервиса {{ alb-name }}, значительные изменения в статистике событий сервиса {{ iam-name }}.

Кроме того, {{ monitoring-name }} можно применять для мониторинга работоспособности самого сервиса {{ at-name }} и мониторинга событий безопасности. Выгрузка метрик в SIEM-систему возможна через [API](../../../glossary/rest-api.md), см. [инструкцию](../../../monitoring/operations/metric/get.md).

[Решение: Мониторинг {{ at-name }} и событий безопасности с помощью {{ monitoring-name }}](https://github.com/yandex-cloud-examples/yc-audit-trails-monitoring)

Аудитные логи возможно экспортировать в лог-группу [{{ cloud-logging-name }}](../../../logging/) или {{ yds-name }} и в SIEM-систему клиента для анализа информации о событиях и инцидентах.

Список важных событий уровня {{ yandex-cloud }} для поиска в аудитных логах:

[Решение: поиск важных событий безопасности в аудитных логах](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/_use_cases_and_searches)

{{ at-full-name }} возможно включить на уровне каталога, облака и организации. Рекомендуется включать {{ at-full-name }} на уровне всей организации — это позволит централизованно собирать аудитные логи, например, в отдельное облако безопасности.

| ID требования | Критичность |
| --- | --- |
| AUDIT1 | Высокая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В консоли управления выберите облако или каталог, в которых необходимо проверить функции.
  1. В списке сервисов выберите {{ at-full-name }}.
  1. Убедитесь, что в параметре Фильтр находится значение Организация.
  1. Дополнительно убедитесь, что назначение логов: bucket {{ objstorage-full-name }}, лог-группа [{{ cloud-logging-name }}](../../../logging/), {{ yds-name }} в рабочем состоянии и логи доступны для дальнейшего анализа.

{% endlist %}

#### 5.2 События {{ at-full-name }} экспортируются в SIEM-системы {#events}

Решения для экспорта аудитных логов {{ yandex-cloud }} подготовлены для следующих SIEM-систем:


* ArcSight — [Сбор, мониторинг и анализ аудитных логов во SIEM ArcSight](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-arcsight)

* Splunk — [Сбор, мониторинг и анализ аудитных логов в SIEM Splunk](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-splunk)

* MaxPatrol SIEM — [Сбор, мониторинг и анализ аудитных логов в MaxPatrol SIEM](../../../audit-trails/tutorials/maxpatrol.md)

* Wazuh — [Сбор, мониторинг и анализ аудитных логов в Wazuh](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-wazuh/blob/main/README.md)

* KUMA — [Сбор, мониторинг и анализ аудитных логов в KUMA](../../../tutorials/security/audit-trails-events-to-kuma/index.md)

Вы можете подробнее ознакомиться с MaxPatrol в [разделе](../../../audit-trails/tutorials/maxpatrol.md).

Для настройки экспорта в любые SIEM подходят утилиты [GeeseFS](../../../storage/tools/geesefs.md) или [s3fs](../../../storage/tools/s3fs.md). Они позволяют смонтировать бакет {{ objstorage-full-name }} как локальный диск виртуальной машины. Далее на ВМ необходимо установить коннектор для SIEM и настроить вычитывание JSON-файлов из бакета. Либо утилиты совместимые с AWS Kinesis datastreams в случае, если вы направляете аудитные логи в {{ yds-full-name }}.


Вы также можете анализировать аудитные логи вручную, если у вас отсутствует SIEM-система, с помощью [поиска](../../../audit-trails/tutorials/search-events-audit-logs/index.md) событий {{ yandex-cloud }} в {{ yq-full-name }}, {{ cloud-logging-name }} или {{ objstorage-name }}.


| ID требования | Критичность |
| --- | --- |
| AUDIT2 | Информационная |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Убедитесь, что аудитные логи из {{ at-full-name }} экспортируются для анализа в SIEM-систему либо анализируются в облаке одним из способов.

{% endlist %}

#### 5.3 Настроено реагирование на события {{ at-full-name }} {#reaction}

Вы можете реагировать на события {{ at-full-name }} средствами вашей SIEM-системы либо вручную. Либо вы можете использовать автоматическое реагирование.

C помощью {{ sf-full-name }} можно настроить оповещения о событиях {{ at-name }}, а так же автоматическое реагирование на вредоносные действия, например  удаление опасных правил или прав доступа.

[Решение: уведомления и реагирование на события ИБ {{ at-name }} с помощью {{ iam-short-name }} / {{ sf-name }} + Telegram](https://github.com/yandex-cloud-examples/yc-audit-trails-automatic-response)

| ID требования | Критичность |
| --- | --- |
| AUDIT3 | Средняя |

#### 5.4 Выполнен hardering бакета Object Storage, где хранятся аудитные логи {{ at-full-name }} {#hardering}

Убедитесь, что в случае записи аудитных логов {{ at-full-name }} в bucket {{ objstorage-full-name }} сам бакет настроен в соответствии с лучшими практиками безопасности:

* [Отсутствует публичный доступ к бакету {{ objstorage-full-name }}](../../../security/standard/virtualenv-safe-config.md#bucket-access).
* [В {{ objstorage-full-name }} используются политики доступа (Bucket Policy)](../../../security/standard/virtualenv-safe-config.md#bucket-policy).
* [В {{ objstorage-full-name }} включена функция **Блокировка версии объекта** (object lock)](../../../security/standard/virtualenv-safe-config.md#object-lock).
* [В {{ objstorage-full-name }} включен механизм логирования действий с бакетом](../../../security/standard/virtualenv-safe-config.md#bucket-logs).
* [В {{ objstorage-full-name }} включено шифрование данных at rest с помощью ключа KMS](../../../security/standard/encryption.md#storage-kms).

Вы можете воспользоваться решением для настройки безопасного бакета {{ objstorage-full-name }} с помощью {{ TF }}.

| ID требования | Критичность |
| --- | --- |
| AUDIT4 | Средняя |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Выполните проверку вручную.

{% endlist %}

#### 5.5 Выполняется сбор аудитных логов с уровня ОС {#os-level}

При использовании облачных сервисов по модели [IaaS](/blog/posts/2022/01/iaas) и использовании групп узлов {{ k8s }} клиент отвечает за безопасность ОС и выполняет сбор событий уровня ОС самостоятельно. Для сбора стандартных событий, которые генерирует ОС, и их экспорта в SIEM-систему клиента существуют бесплатные инструменты, такие как:
  * [Osquery](https://osquery.io/);
  * [Filebeat (ELK)](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-system.html);
  * [Wazuh](https://documentation.wazuh.com/current/getting-started/use_cases/log_analysis.html).

Дополнительные опции генерации событий возможно реализовать с помощью утилиты Auditd для Linux, Sysmon для Windows.

Системные метрики Linux (процессор, память, диск) можно собирать с помощью компонента [{{ unified-agent-short-name }}](../../../monitoring/concepts/data-collection/unified-agent/index.md) сервиса {{ monitoring-name }}.

Также события ОС возможно экспортировать в {{ cloud-logging-name }} с  помощью [плагина Fluent bit](https://github.com/yandex-cloud/fluent-bit-plugin-yandex) либо в {{ yds-name }}.

Для описания событий, которые нужно искать в логах, рекомендуем использовать формат [Sigma](https://github.com/SigmaHQ/sigma), поддерживаемый популярными SIEM-системами. Репозиторий Sigma содержит [библиотеку событий](https://github.com/SigmaHQ/sigma/tree/master/rules), описанных в этом формате.

Чтобы получать точную хронологию событий уровня ОС и приложений, настройте синхронизацию часов по [инструкции](../../../compute/tutorials/ntp.md).

Дополнительно рекомендуется повысить уровень логирования внутри виртуальных машин как минимум до [`VERBOSE`](https://en.wikipedia.org/wiki/Verbose_mode).

| ID требования | Критичность |
| --- | --- |
| AUDIT5 | Высокая |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Выполните проверку вручную.

{% endlist %}

#### 5.6 Выполняется сбор аудитных логов с уровня приложений {#app-level}

Сбор событий уровня приложений, развернутых на ресурсах {{ compute-short-name }}, клиент может выполнять самостоятельно. Например, записывать логи приложения в файл и передавать их в SIEM-систему с помощью инструментов, перечисленных в подразделе выше.

Включите сбор аудитных логов в используемых неуправляемых СУБД:

* Включите протоколирование всех действий аутентификации (успешных и неудачных).
* Активируйте логирование операций изменения данных (`INSERT`, `UPDATE`, `DELETE`).
* Настройте регистрацию операций изменения схемы (`ALTER`, `CREATE`, `DROP`).
* Фиксируйте изменения разрешений и привилегий.
* Настройте события для отслеживания запросов.

| ID требования | Критичность |
| --- | --- |
| AUDIT6 | Средняя |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Выполните проверку вручную.

{% endlist %}

#### 5.7 Выполняется сбор логов с уровня сети {#network-level}

Запись событий о сетевом трафике {{ vpc-short-name }} (Flow Logs) на текущий момент может выполняться только средствами клиента. Для сбора и передачи событий могут использоваться решения из {{ marketplace-full-name }} (например, [NGFW](/marketplace?tab=software&search=NGFW), [IDS/IPS](/marketplace?tab=software&search=IDS%2FIPS), [сетевые продукты](/marketplace?categories=network)) либо бесплатное ПО. Также сбор логов уровня сети возможно выполнять с помощью различных агентов — HIDS и др.

| ID требования | Критичность |
| --- | --- |
| AUDIT7 | Средняя |

{% list tabs group=instructions %}

- Ручная проверка {#manual}

  Выполните проверку вручную.

{% endlist %}

#### 5.8 Отслеживаются события уровня сервисов {#data-plane-events}

[Аудитный лог событий уровня сервисов](../../../audit-trails/concepts/format-data-plane.md) — это запись о событиях, которые произошли с ресурсами {{ yandex-cloud }}, в форме JSON-объекта. Благодаря отслеживанию событий уровня сервисов вам будет проще собирать дополнительные события с облачных сервисов, что позволит эффективнее реагировать на инциденты безопасности в облаках. Кроме того, отслеживание событий уровня сервисов поможет обеспечить соответствие вашей облачной инфраструктуры нормативным правовым актам и отраслевым стандартам. Например, вы можете отслеживать получение сотрудниками доступа к конфиденциальным данным, хранящимся в [бакетах](../../../storage/concepts/bucket.md).

Включать сбор аудитных логов уровня сервисов нужно отдельно для каждого из [поддерживаемых сервисов](../../../audit-trails/concepts/control-plane-vs-data-plane.md#data-plane-events).

Рекомендуется включать **все события** для сервисов [{{ iam-full-name }}](../../../audit-trails/concepts/events-data-plane.md#iam) и [{{ dns-full-name }}](../../../audit-trails/concepts/events-data-plane.md#dns), а также **все события** для следующих сервисов, если эти сервисы используются:

* [{{ certificate-manager-full-name }}](../../../audit-trails/concepts/events-data-plane.md#certificate-manager)
* [{{ compute-full-name }}](../../../audit-trails/concepts/events-data-plane.md#compute)
* [{{ kms-full-name }}](../../../audit-trails/concepts/events-data-plane.md#kms)
* [{{ lockbox-full-name }}](../../../audit-trails/concepts/events-data-plane.md#lockbox)
* [{{ mch-full-name }}](../../../audit-trails/concepts/events-data-plane.md#mch)
* [{{ managed-k8s-full-name }}](../../../audit-trails/concepts/events-data-plane.md#managed-service-for-kubernetes)
* [{{ mmg-full-name }}](../../../audit-trails/concepts/events-data-plane.md#mmg)
* [{{ mmy-full-name }}](../../../audit-trails/concepts/events-data-plane.md#mmy)
* [{{ mpg-full-name }}](../../../audit-trails/concepts/events-data-plane.md#mpg)
* [{{ mrd-full-name }}](../../../audit-trails/concepts/events-data-plane.md#mrd)
* [{{ objstorage-full-name }}](../../../audit-trails/concepts/events-data-plane.md#objstorage)
* [{{ sws-full-name }}](../../../audit-trails/concepts/events-data-plane.md#sws)
* [{{ websql-full-name }}](../../../audit-trails/concepts/events-data-plane.md#websql)

| ID требования | Критичность |
| --- | --- |
| AUDIT8 | Средняя |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../../resource-manager/concepts/resources-hierarchy.md#folder), в котором расположен [трейл](../../../audit-trails/concepts/trail.md).
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_audit-trails }}**.
  1. Выберите нужный трейл.
  1. Убедитесь, что на странице с информацией о трейле в блоке **{{ ui-key.yacloud.audit-trails.label_event-filter-section }}** указаны все сервисы, для которых вы хотите собирать логи уровня сервисов, и для каждого указанного сервиса задана нужная [область сбора](../../../audit-trails/concepts/trail.md#collecting-area) аудитных логов.

      Список поддерживаемых сервисов см. в [{#T}](../../../audit-trails/concepts/events-data-plane.md).

{% endlist %}

#### 5.9 Включен модуль {{ sd-name }} {{ atr-name }} для проверки действий, произведенных сотрудниками {{ yandex-cloud }} с инфраструктурой {#access-transparency-enabled}

Все действия сотрудников {{ yandex-cloud }} фиксируются и контролируются с помощью [бастионных хостов](../../../tutorials/routing/bastion.md), на которых записываются операции с ресурсами, обрабатывающими пользовательские данные.

Модуль [{{ atr-name }}](../../../security-deck/concepts/access-transparency.md) позволяет проверить, для каких целей сотрудники провайдера получили доступ к инфраструктуре. Например, для выполнения дополнительной диагностики IT‑систем инженерами службы поддержки или обновления ПО. [ML‑модели](../../../glossary/ml-models.md) анализируют эти действия. {{ yagpt-name }}, встроенный в {{ atr-name }}, создает сводки о событиях доступа для повышения прозрачности. Подозрительные сессии автоматически передаются на рассмотрение команде безопасности {{ yandex-cloud }}.

| ID требования | Критичность |
| --- | --- |
| AUDIT9 | Низкая |

{% list tabs group=instructions %}

- Проверка в консоли управления {#console}

  1. Перейдите в сервис [{{ sd-full-name }}]({{ link-sd-main }}).
  1. На панели слева выберите ![CloudCheck](../../../_assets/console-icons/cloud-check.svg) **{{ atr-name }}**.
  1. Если вы видите предложение о подключении модуля {{ atr-name }}, то этот модуль у вас еще не активирован: перейдите к п. <q>Инструкции и решения по выполнению</q>.

{% endlist %}

**Инструкции и решения по выполнению:**

Нажмите кнопку **Подключить**, чтобы активировать модуль `{{ atr-name }}`.
