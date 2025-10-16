Роль `managed-mongodb.restorer` позволяет восстанавливать кластеры Yandex StoreDoc из резервных копий, просматривать информацию о кластерах, хостах, шардах, базах данных и пользователях Yandex StoreDoc, логи работы кластеров, а также данные о квотах и операциях с ресурсами сервиса.

Пользователи с этой ролью могут:
* просматривать информацию о резервных копиях [кластеров Yandex StoreDoc](../../storedoc/concepts/index.md) и восстанавливать кластеры из [резервных копий](../../storedoc/concepts/backup.md);
* просматривать информацию о кластерах Yandex StoreDoc;
* просматривать информацию о [хостах](../../storedoc/concepts/instance-types.md) кластеров Yandex StoreDoc;
* просматривать информацию о [шардах](../../storedoc/concepts/sharding.md) кластеров Yandex StoreDoc;
* просматривать информацию о базах данных Yandex StoreDoc;
* просматривать информацию о [пользователях](../../storedoc/concepts/users-and-roles.md) Yandex StoreDoc;
* просматривать информацию об алертах Yandex StoreDoc;
* просматривать [логи](../../storedoc/operations/cluster-logs.md) работы кластеров Yandex StoreDoc;
* просматривать информацию о результатах диагностики производительности кластеров Yandex StoreDoc;
* просматривать информацию о [квотах](../../storedoc/concepts/limits.md#mmg-quotas) сервиса Yandex StoreDoc;
* просматривать информацию об операциях с ресурсами сервиса Yandex StoreDoc.

Включает разрешения, предоставляемые ролью `managed-mongodb.viewer`.