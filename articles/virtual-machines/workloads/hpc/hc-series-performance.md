---
title: HC Serisi VM boyutu performansı
description: Azure 'da HC Serisi VM boyutları için performans testi sonuçları hakkında bilgi edinin.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 177a58090303a70491d9a9226eca40d0bb371764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722268"
---
# <a name="hc-series-virtual-machine-sizes"></a>HC Serisi sanal makine boyutları

Birkaç performans testi, HC Serisi boyutlarda çalıştırıldı. Bu performans testinin bazı sonuçları aşağıda verilmiştir.

| İş Yükü                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 190 GB/sn (Intel MLC AVX-512)  |
| High-Performance linpack (HPL)                  | 3520 GigaFLOPS (Rtepe), 2970 GigaFLOPS (Rmax) |
| RDMA gecikme & bant genişliği                        | 1,05 mikrosaniye, 96,8 GB/sn   |
| Yerel NVMe SSD üzerinde FIO                           | 1,3 GB/sn okuma, 900 MB/s yazma |  
| 4 Azure Premium SSD (P30 yönetilen diskler, RAID0) * *  | 780 MB/s okuma, 780 MB/yazma |

## <a name="mpi-latency"></a>MPı gecikmesi

OSU mikro kıyaslama paketinden MPı gecikme süresi testi çalıştırılır. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 'da

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Azure HC üzerinde MPı gecikmesi.":::

## <a name="mpi-bandwidth"></a>MPı bant genişliği

OSU mikro kıyaslama paketinden MPı bant genişliği sınaması çalıştırılır. Örnek betikler [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) 'da

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Azure HC üzerinde MPı bant genişliği.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest paketinin](https://community.mellanox.com/s/article/perftest-package) gecikme süresi (ib_send_lat) ve bant genişliği (ib_send_bw) gibi birçok InfiniBand testi vardır. Örnek bir komut aşağıda verilmiştir.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- HPC iş yüklerini çalıştırmanın daha üst düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
