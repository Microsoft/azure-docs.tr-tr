---
title: HBv3 serisi VM boyutlarının performansı ve ölçeklenebilirliği
description: Azure 'da HBv3 serisi VM boyutlarının performansı ve ölçeklenebilirliği hakkında bilgi edinin.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604779"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 serisi sanal makine performansı

Yaygın HPC mikro kıyaslamaları kullanılarak performans beklentileri şunlardır:

| İş Yükü                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/sn (NUMA başına yaklaşık 82-86 GB/sn)                                     |
| High-Performance linpack (HPL)                  | 120-çekirdek VM boyutu için 4 TF (Rtepe, FP64), 8 TF (Rtepe, FP32)               |
| RDMA gecikme & bant genişliği                        | 1,2 mikrosaniye (1-bayt), 192 GB/s (tek yönlü)                                        |
| Yerel NVMe SDS üzerinde FIO (RAID0)                  | 7 GB/sn okuma, 3 GB/s yazma; 186k ıOPS okuma, 201k ıOPS yazma |

## <a name="process-pinning"></a>İşlem sabitleme

[İşlem sabitleme](compiling-scaling-applications.md#process-pinning) , HBv3 serisi VM 'lerde düzgün çalışarak, temel bir Silicon as 'YI Konuk VM 'de kullanıma sunduk. En iyi performans ve tutarlılık için işlem sabitlenmesini önemle öneririz.

## <a name="mpi-latency"></a>MPı gecikmesi

OSU mikro kıyaslama paketinden MPı gecikme süresi testi aşağıdaki şekilde yürütülebilir. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)' da bulunur.

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPı bant genişliği
OSU mikro kıyaslama paketinden MPı bant genişliği testi, aşağıda her şekilde yürütülebilir. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)' da bulunur.
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
[Mellanox Perftest paketinin](https://community.mellanox.com/s/article/perftest-package) gecikme süresi (ib_send_lat) ve bant genişliği (ib_send_bw) gibi birçok InfiniBand testi vardır. Örnek bir komut aşağıda verilmiştir.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Sonraki adımlar
- [MPI uygulamalarını ölçeklendirme](compiling-scaling-applications.md)hakkında bilgi edinin.
- [Techcommunity makalesindeki](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843)HBv3 VM 'lerinde HPC uygulamalarının performans ve ölçeklenebilirlik sonuçlarını gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- HPC iş yüklerini çalıştırmanın daha üst düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
