| Resource       | Price for 1 hour,<br>without VAT   | Price with CVoS for 6 months,<br>without VAT | Price with CVoS for 1 year,<br>without VAT |
|----------------|---------------------------------------------------:|---------------------------------:|----------------------------------:|
| **Intel Cascade Lake**                                     |
| 50% vCPU      | {{ sku|USD|mdb.kafka.kraft.v2.cpu.c50|string }} | – | – | 
| 100% vCPU     | {{ sku|USD|mdb.kafka.kraft.v2.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.cpu.c100.v2|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.cpu.c100.v2|string }} (-15%) |
| RAM (for 1 GB) | {{ sku|USD|mdb.kafka.kraft.v2.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.ram.v2|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.ram.v2|string }} (-15%) |
| **Intel Ice Lake**                                         |
| 50% vCPU      | {{ sku|USD|mdb.kafka.kraft.v3.cpu.c50|string }} | – | – |
| 100% vCPU     | {{ sku|USD|mdb.kafka.kraft.v3.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.cpu.c100.v3|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.cpu.c100.v3|string }} (-15%) |
| RAM (for 1 GB) | {{ sku|USD|mdb.kafka.kraft.v3.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.ram.v3|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.ram.v3|string }} (-15%) |
| **Intel Ice Lake (Compute Optimized)**                     |
| 100% vCPU | {{ sku|USD|mdb.kafka.kraft.highfreq-v3.cpu.c100|string }} | – | – | 
| RAM (for 1 GB) | {{ sku|USD|mdb.kafka.kraft.highfreq-v3.ram|string }} | – | – |
| **AMD Zen 4**                                         |
| 100% vCPU     | {{ sku|USD|mdb.kafka.kraft.v4a.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.cpu.c100.v4a|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.cpu.c100.v4a|string }} (-15%) |
| RAM (for 1 GB) | {{ sku|USD|mdb.kafka.kraft.v4a.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.kafka.kraft.ram.v4a|string }} (-10%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.kafka.kraft.ram.v4a|string }} (-15%) |
| **AMD Zen 4 HighFreq**                     |
| 100% vCPU | {{ sku|USD|mdb.kafka.kraft.highfreq-v4a.cpu.c100|string }} | – | – | 
| RAM (for 1 GB) | {{ sku|USD|mdb.kafka.kraft.highfreq-v4a.ram|string }} | – | – |
