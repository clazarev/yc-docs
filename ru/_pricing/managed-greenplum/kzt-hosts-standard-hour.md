
| Ресурс        | Цена за 1 час,<br>вкл. НДС                             | Цена с CVoS на 6 месяцев,<br>вкл. НДС                                               | Цена с CVoS на 1 год,<br>вкл. НДС                                                   |
|---------------|-------------------------------------------------------:|------------------------------------------------------------------------------------:|------------------------------------------------------------------------------------:|
| **Intel Cascade Lake**                                                                                                                                                                                                                             |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.greenplum.v2.cpu.c100|string }} | —                                                                                   | —                                                                                   |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.greenplum.v2.ram|string }}      | —                                                                                   | —                                                                                   |
| **Intel Ice Lake**                                                                                                                                                                                                                                 |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.greenplum.v3.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.greenplum.cpu.c100.v3|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.greenplum.cpu.c100.v3|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.greenplum.v3.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.greenplum.ram.v3|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.greenplum.ram.v3|string }} (-22%)      |
| **Intel Ice Lake (Compute Optimized)** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.greenplum.highfreq-v3.cpu.c100|string }} | — | — |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.greenplum.highfreq-v3.ram|string }} | — | — |
| **AMD Zen 4** |
| 100% vCPU     | {{ sku|KZT|mdb.cluster.greenplum.v4a.cpu.c100|string }} | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.greenplum.cpu.c100.v4a|string }} (-15%) | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.greenplum.cpu.c100.v4a|string }} (-22%) |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.greenplum.v4a.ram|string }}      | {{ sku|KZT|v1.commitment.selfcheckout.m6.mdb.greenplum.ram.v4a|string }} (-15%)      | {{ sku|KZT|v1.commitment.selfcheckout.y1.mdb.greenplum.ram.v4a|string }} (-22%)      |
| **AMD Zen 4 HighFreq** |
| 100% vCPU | {{ sku|KZT|mdb.cluster.greenplum.highfreq-v4a.cpu.c100|string }} | — | — |
| RAM (за 1 ГБ) | {{ sku|KZT|mdb.cluster.greenplum.highfreq-v4a.ram|string }} | — | — |


