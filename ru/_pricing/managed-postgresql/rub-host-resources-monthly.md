
| Ресурс        | Цена за 1 месяц,<br>вкл. НДС                          | Цена с CVoS на 6 месяцев,<br>вкл. НДС                                              | Цена с CVoS на 1 год,<br>вкл. НДС                                                  |
|---------------|------------------------------------------------------:|-----------------------------------------------------------------------------------:|-----------------------------------------------------------------------------------:|
| **Intel Broadwell**                                                                                                                                                                                                                             |
| 5% vCPU       | {{ sku|RUB|mdb.cluster.pg.v1.cpu.c5|month|string }}   | −                                                                                  | −                                                                                  |
| 20% vCPU      | {{ sku|RUB|mdb.cluster.pg.v1.cpu.c20|month|string }}  | −                                                                                  | −                                                                                  |
| 50% vCPU      | {{ sku|RUB|mdb.cluster.pg.v1.cpu.c50|month|string }}  | −                                                                                  | −                                                                                  |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.pg.v1.cpu.c100|month|string }} | −                                                                                  | −                                                                                  |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.v1.ram|month|string }}      | −                                                                                  | −                                                                                  |
| **Intel Cascade Lake**                                                                                                                                                                                                                          |
| 5% vCPU       | {{ sku|RUB|mdb.cluster.pg.v2.cpu.c5|month|string }}   | −                                                                                  | −                                                                                  |
| 20% vCPU      | {{ sku|RUB|mdb.cluster.pg.v2.cpu.c20|month|string }}  | −                                                                                  | −                                                                                  |
| 50% vCPU      | {{ sku|RUB|mdb.cluster.pg.v2.cpu.c50|month|string }}  | −                                                                                  | −                                                                                  |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.pg.v2.cpu.c100|month|string }} | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.cpu.c100.v2|month|string }} (-15%) | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.cpu.c100.v2|month|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.v2.ram|month|string }}      | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.ram.v2|month|string }} (-15%)      | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.ram.v2|month|string }} (-22%)      |
| **Intel Ice Lake**                                                                                                                                                                                                                              |
| 50% vCPU      | {{ sku|RUB|mdb.cluster.pg.v3.cpu.c50|month|string }}  | −                                                                                  | −                                                                                  |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.pg.v3.cpu.c100|month|string }} | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.cpu.c100.v3|month|string }} (-15%) | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.cpu.c100.v3|month|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.v3.ram|month|string }}      | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.ram.v3|month|string }} (-15%)      | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.ram.v3|month|string }} (-22%)      |
| **Intel Ice Lake (Compute Optimized)** |
| 100% vCPU | {{ sku|RUB|mdb.cluster.pg.highfreq-v3.cpu.c100|month|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.highfreq-v3.ram|month|string }} | - | - |
| **AMD Zen 4** |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.pg.v4a.cpu.c100|month|string }} | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.cpu.c100.v4a|month|string }} (-15%) | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.cpu.c100.v4a|month|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.v4a.ram|month|string }}      | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.pg.ram.v4a|month|string }} (-15%)      | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.pg.ram.v4a|month|string }} (-22%)      |
| **AMD Zen 4 HighFreq** |
| 100% vCPU | {{ sku|RUB|mdb.cluster.pg.highfreq-v4a.cpu.c100|month|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.pg.highfreq-v4a.ram|month|string }} | - | - |


