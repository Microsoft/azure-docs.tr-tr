---
title: HBv2 serisi VM boyutu performansı
description: Azure 'da HBv2 serisi VM boyutları için performans testi sonuçları hakkında bilgi edinin.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6edaed1011b867cac9920a19be6bb5fb7157e16f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721227"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 serisi sanal makine boyutları

[HBv2 serisi](../../hbv2-series.md) boyut sanal makinelerinde çeşitli performans testleri çalıştırıldı. Bu performans testinin bazı sonuçları aşağıda verilmiştir.


| İş Yükü                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 350 GB/sn (CCX başına 21-23 GB/sn)                                     |
| High-Performance linpack (HPL)                  | 4 TeraFLOPS (Rtepe, FP64), 8 TeraFLOPS (Rmax, FP32)               |
| RDMA gecikme & bant genişliği                        | 1,2 mikrosaniye, 190 GB/sn                                        |
| Yerel NVMe SSD üzerinde FIO                           | 2,7 GB/sn okuma, 1,1 GB/s yazma; 102k ıOPS okuma, 115 ıOPS yazma |
| On 8 * Azure Premium SSD (P40 yönetilen diskler, RAID0) * *  | 1,3 GB/sn okuma, 2,5 GB/yazma; 101k ıOPS okuma, 105k ıOPS yazma |


## <a name="mpi-latency"></a>MPı gecikmesi

OSU mikro kıyaslama paketinden MPı gecikme süresi testi çalıştırılır. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)' da bulunur.


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Azure HB üzerinde MPı gecikmesi.":::


## <a name="mpi-bandwidth"></a>MPı bant genişliği

OSU mikro kıyaslama paketinden MPı bant genişliği sınaması çalıştırılır. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)' da bulunur.


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Azure HB üzerinde MPı bant genişliği.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest paketinin](https://community.mellanox.com/s/article/perftest-package) gecikme süresi (ib_send_lat) ve bant genişliği (ib_send_bw) gibi birçok InfiniBand testi vardır. Örnek bir komut aşağıda verilmiştir. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- HPC iş yüklerini çalıştırmanın daha üst düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).