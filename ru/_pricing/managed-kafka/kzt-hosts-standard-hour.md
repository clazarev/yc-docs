
| Ресурс        | Цена за 1 час,<br>вкл. НДС                                     | Цена с CVoS на 6 месяцев,<br>вкл. НДС                                           | Цена с CVoS на 1 год,<br>вкл. НДС                                               |
|---------------|---------------------------------------------------:|--------------------------------------------------------------------------------:|--------------------------------------------------------------------------------:|
| **Intel Cascade Lake**                                                                                                                                                                                                                 |
| 50% vCPU      | {{ sku|KZT|mdb.cluster.kafka.v2.cpu.c50|string }}  | −                                                                               | −                                                                               |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.kafka.v2.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.cpu.c100.v2|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.cpu.c100.v2|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.kafka.v2.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.ram.v2|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.ram.v2|string }} (-22%)      |
| **Intel Ice Lake**                                                                                                                                                                                                                     |
| 50% vCPU      | {{ sku|KZT|mdb.cluster.kafka.v3.cpu.c50|string }}  | −                                                                               | −                                                                               |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.kafka.v3.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.cpu.c100.v3|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.cpu.c100.v3|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.kafka.v3.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.ram.v3|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.ram.v3|string }} (-22%)      |
| **Intel Ice Lake (Compute Optimized)** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.kafka.highfreq-v3.cpu.c100|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.kafka.highfreq-v3.ram|string }} | - | - |
| **AMD Zen 4** |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.kafka.v4a.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.cpu.c100.v4a|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.cpu.c100.v4a|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.kafka.v4a.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.kafka.ram.v4a|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.kafka.ram.v4a|string }} (-22%)      |
| **AMD Zen 4 HighFreq** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.kafka.highfreq-v4a.cpu.c100|string }} | - | - |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.kafka.highfreq-v4a.ram|string }} | - | - |


