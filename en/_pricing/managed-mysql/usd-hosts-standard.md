{% list tabs %}

- Prices per hour

   | Resource | Price per hour,<br>without VAT | With CVoS for 6 months,<br>without VAT | With CVoS for 1 year,<br>without VAT |
   |----------------|---------------------------------------------------:|--------------------------------------------------------------------------------:|--------------------------------------------------------------------------------:|
   | **Intel Broadwell** |
   | 5% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c5|string }} | − | − |
   | 20% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c20|string }} | − | − |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c50|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c100|string }} | − | − |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v1.ram|string }} | − | − |
   | **Intel Cascade Lake** |
   | 5% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c5|string }} | − | − |
   | 20% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c20|string }} | − | − |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c50|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v2|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v2|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v2.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v2|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v2|string }} (-22%) |
   | **Intel Ice Lake** |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v3.cpu.c50|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v3.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v3|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v3|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v3.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v3|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v3|string }} (-22%) |
   | **Intel Ice Lake (Compute Optimized)** |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.highfreq-v3.cpu.c100|string }} | − | − |
   | RAM (for 1 GB)| {{ sku|USD|mdb.cluster.mysql.highfreq-v3.ram|string }} | − | − |
   | **AMD Zen 4** |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v4a.cpu.c100|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v4a|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v4a|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v4a.ram|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v4a|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v4a|string }} (-22%) |
   | **AMD Zen 4 HighFreq** |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.highfreq-v4a.cpu.c100|string }} | − | − |
   | RAM (for 1 GB)| {{ sku|USD|mdb.cluster.mysql.highfreq-v4a.ram|string }} | − | − |

- Prices per month

   | Resource | Price per month,<br>without VAT | With CVoS for 6 months,<br>without VAT | With CVoS for 1 year,<br>without VAT |
   |----------------|---------------------------------------------------------:|--------------------------------------------------------------------------------:|--------------------------------------------------------------------------------------------:|
   | **Intel Broadwell** |
   | 5% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c5|month|string }} | − | − |
   | 20% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c20|month|string }} | − | − |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c50|month|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v1.cpu.c100|month|string }} | − | − |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v1.ram|month|string }} | − | − |
   | **Intel Cascade Lake** |
   | 5% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c5|month|string }} | − | − |
   | 20% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c20|month|string }} | − | − |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c50|month|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v2.cpu.c100|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v2|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v2|month|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v2.ram|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v2|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v2|month|string }} (-22%) |
   | **Intel Ice Lake** |
   | 50% vCPU | {{ sku|USD|mdb.cluster.mysql.v3.cpu.c50|month|string }} | − | − |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v3.cpu.c100|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v3|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v3|month|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v3.ram|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v3|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v3|month|string }} (-22%) |
   | **Intel Ice Lake (Compute Optimized)** |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.highfreq-v3.cpu.c100|month|string }} | − | − |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.highfreq-v3.ram|month|string }} | − | − |
   | **AMD Zen 4**  |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.v4a.cpu.c100|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.cpu.c100.v4a|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.cpu.c100.v4a|month|string }} (-22%) |
   | RAM (for 1 GB) | {{ sku|USD|mdb.cluster.mysql.v4a.ram|month|string }} | {{ sku|USD|v1.commitment.selfcheckout.m6.mdb.mysql.ram.v4a|month|string }} (-15%) | {{ sku|USD|v1.commitment.selfcheckout.y1.mdb.mysql.ram.v4a|month|string }} (-22%) |
   | **AMD Zen 4 HighFreq** |
   | 100% vCPU | {{ sku|USD|mdb.cluster.mysql.highfreq-v4a.cpu.c100|month|string }} | − | − |
   | RAM (for 1 GB)| {{ sku|USD|mdb.cluster.mysql.highfreq-v4a.ram|month|string }} | − | − |

{% endlist %}
