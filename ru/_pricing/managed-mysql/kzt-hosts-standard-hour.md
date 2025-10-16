
| Ресурс        | Цена за 1 час,<br>вкл. НДС                         | Цена с CVoS на 6 месяцев,<br>вкл. НДС                                           | Цена с CVoS на 1 год,<br>вкл. НДС                                               |
|---------------|---------------------------------------------------:|--------------------------------------------------------------------------------:|--------------------------------------------------------------------------------:|
| **Intel Broadwell**                                                                                                                                                                                                                    |
| 5% vCPU       | {{ sku|KZT|mdb.cluster.mysql.v1.cpu.c5|string }}   | −                                                                               | −                                                                               |
| 20% vCPU      | {{ sku|KZT|mdb.cluster.mysql.v1.cpu.c20|string }}  | −                                                                               | −                                                                               |
| 50% vCPU      | {{ sku|KZT|mdb.cluster.mysql.v1.cpu.c50|string }}  | −                                                                               | −                                                                               |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.mysql.v1.cpu.c100|string }} | −                                                                               | −                                                                               |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.v1.ram|string }}      | −                                                                               | −                                                                               |
| **Intel Cascade Lake**                                                                                                                                                                                                                 |
| 5% vCPU       | {{ sku|KZT|mdb.cluster.mysql.v2.cpu.c5|string }}   | −                                                                               | −                                                                               |
| 20% vCPU      | {{ sku|KZT|mdb.cluster.mysql.v2.cpu.c20|string }}  | −                                                                               | −                                                                               |
| 50% vCPU      | {{ sku|KZT|mdb.cluster.mysql.v2.cpu.c50|string }}  | −                                                                               | −                                                                               |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.mysql.v2.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v2|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v2|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.v2.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v2|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v2|string }} (-22%)      |
| **Intel Ice Lake**                                                                                                                                                                                                                     |
| 50% vCPU      | {{ sku|KZT|mdb.cluster.mysql.v3.cpu.c50|string }}  | −                                                                               | −                                                                               |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.mysql.v3.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v3|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v3|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.v3.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v3|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v3|string }} (-22%)      |
| **Intel Ice Lake (Compute Optimized)** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.mysql.highfreq-v3.cpu.c100|string }} | − | − |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.highfreq-v3.ram|string }} | − | − |
| **AMD Zen 4** |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.mysql.v4a.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v4a|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v4a|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.v4a.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v4a|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v4a|string }} (-22%)      |
| **AMD Zen 4 HighFreq** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.mysql.highfreq-v4a.cpu.c100|string }} | − | − |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.mysql.highfreq-v4a.ram|string }} | − | − |


