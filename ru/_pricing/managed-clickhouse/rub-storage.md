| Услуга                                                    | Цена за ГБ в месяц,<br>вкл. НДС                                               |
|-----------------------------------------------------------|------------------------------------------------------------------------------:|
| Хранилище на сетевых HDD-дисках                           | {{ sku|RUB|mdb.cluster.network-hdd.ch|month|string }}                         |
| Хранилище на нереплицируемых SSD-дисках                   | {{ sku|RUB|mdb.cluster.network-ssd-nonreplicated.ch|month|string }}           |
| Хранилище на сетевых SSD-дисках                           | {{ sku|RUB|mdb.cluster.network-nvme.ch|month|string }}                        |
| Сверхбыстрое сетевое хранилище с тремя репликами (SSD)    | {{ sku|RUB|mdb.cluster.network-ssd-io-m3.ch|month|string }}                   |
| Хранилище на локальных SSD-дисках                         | {{ sku|RUB|mdb.cluster.local-nvme.ch|month|string }}                          |
| Холодные данные гибридного хранилища и их резервные копии | {{ sku|RUB|storage.bucket.used_space.standard|pricingRate.720|month|string }} |
| Резервные копии сверх размера хранилища                   | {{ sku|RUB|mdb.cluster.clickhouse.backup|month|string }}                      |
