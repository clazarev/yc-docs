
| Ресурс        | Цена за 1 месяц,<br>вкл. НДС                                  | Цена с CVoS на 6 месяцев,<br>вкл. НДС                                                | Цена с CVoS на 1 год,<br>вкл. НДС                                                                |
|---------------|--------------------------------------------------------------:|-------------------------------------------------------------------------------------:|-------------------------------------------------------------------------------------------------:|
| **Intel Cascade Lake**                                                                                                                                                                                                                                                  |
| 50% vCPU      | {{ sku|RUB|mdb.cluster.opensearch.v2.cpu.c50|month|string }}  | —                                                                                    | —                                                                                                |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.opensearch.v2.cpu.c100|month|string }} | —                                                                                    | —                                                                                                |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.opensearch.v2.ram|month|string }}      | —                                                                                    | —                                                                                                |
| **Intel Ice Lake**                                                                                                                                                                                                                                                      |
| 50% vCPU      | {{ sku|RUB|mdb.cluster.opensearch.v3.cpu.c50|month|string }}  | —                                                                                    | —                                                                                                |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.opensearch.v3.cpu.c100|month|string }} | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.opensearch.cpu.c100.v3|month|string }} (-15%) | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.opensearch.cpu.c100.v3|month|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.opensearch.v3.ram|month|string }}      | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.opensearch.ram.v3|month|string }} (-15%)      | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.opensearch.ram.v3|month|string }} (-22%)      |
| **Intel Ice Lake (Compute Optimized)** |
| 100% vCPU | {{ sku|RUB|mdb.cluster.opensearch.highfreq-v3.cpu.c100|month|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.opensearch.highfreq-v3.ram|month|string }} | - | - |
| **AMD Zen 4** |
| 100% vCPU     | {{ sku|RUB|mdb.cluster.opensearch.v4a.cpu.c100|month|string }} | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.opensearch.cpu.c100.v4a|month|string }} (-15%) | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.opensearch.cpu.c100.v4a|month|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.opensearch.v4a.ram|month|string }}      | {{ sku|RUB|v1.commitment.selfcheckout.m6.mdb.opensearch.ram.v4a|month|string }} (-15%)      | {{ sku|RUB|v1.commitment.selfcheckout.y1.mdb.opensearch.ram.v4a|month|string }} (-22%)      |
| **AMD Zen 4 HighFreq** |
| 100% vCPU | {{ sku|RUB|mdb.cluster.opensearch.highfreq-v4a.cpu.c100|month|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|RUB|mdb.cluster.opensearch.highfreq-v4a.ram|month|string }} | - | - |


