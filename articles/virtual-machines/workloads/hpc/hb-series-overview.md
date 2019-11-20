---
title: HB serisi VM genel bakış - Azure sanal makineler | Microsoft Docs
description: Azure'da HB serisi VM boyutu için Önizleme desteği hakkında daha fazla bilgi edinin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707768"
---
# <a name="hb-series-virtual-machines-overview"></a>HB serisi sanal makineler genel bakış

AMD EPYC üzerinde yüksek performanslı bilgi işlem (HPC) uygulama performansını en üst düzeye özenli bir yaklaşım bellek konumu ve işlem yerleştirme gerektirir. Aşağıda AMD EPYC mimarisi ve kararlılığımızın, azure'da HPC uygulamaları için özetler. Bir fiziksel NUMA etki alanı ve sanal NUMA'yı etki alanına başvurmak için "vNUMA" başvurmak için "pNUMA" terimini kullanacağız.

Fiziksel olarak HB serisi 2. * 32 çekirdekli EPYC 7551 CPU'lar 64 fiziksel çekirdek toplam. 64 bu çekirdek her biri dört çekirdek olduğu 16 pNUMA etki alanlarına (yuva başına 8), ayrılmış ve bir "CPU karmaşık" (veya "CCX" olarak). Bir işletim sistemi pNUMA/vNUMA sınır nasıl görürsünüz olan kendi L3 önbellek her CCX sahiptir. Bitişik CCXs çifti fiziksel DRAM (DRAM HB serisi sunuculara 32 GB), iki kanallara erişim paylaşır.

Azure hiper yönetici VM ile engellemeden çalışılacak yer sağlamak için fiziksel pNUMA etki alanı 0 (ilk CCX) tutarız. Biz sonra VM için pNUMA etki alanları 1-15 (kalan CCX birimleri) atayın. VM görürsünüz:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` VM başına çekirdek

VM, kendisi için 0'ı pNUMA sağlanmamış bilmez. VM pNUMA 1-15 vNUMA-14, 0 ile 7 vNUMA vSocket 0 ile 8 vNUMA vSocket 1 üzerinde şirket olarak anlar. Asimetrik olsa da, işletim sistemi önyükleme ve normal şekilde çalışır. Bu kılavuz size en iyi şekilde nasıl bu asimetrik NUMA Düzen MPI uygulamalarını çalıştırmak için yönlendirir.

İşlem sabitleme çalışır HB serisi VM'ler üzerinde temel alınan Silikon olarak kullanıma sunuyoruz çünkü-Konuk VM. İşlemi en iyi performans ve tutarlılık için sabitleme kesinlikle öneririz.

Daha fazla bakın [AMD EPYC mimarisi](https://bit.ly/2Epv3kC) ve [çok yonga mimarisi](https://bit.ly/2GpQIMb) LinkedIn'de. Daha ayrıntılı bilgi için bkz. [HPC ayarlama Kılavuzu AMD EPYC işlemciler](https://bit.ly/2T3AWZ9).

Aşağıdaki diyagramda çekirdek ayrımı ayrılmış Azure hiper Yöneticisi ve HB serisi VM için gösterilmektedir.

![Azure hiper Yöneticisi ve HB serisi VM için ayrılan çekirdek ayrımı](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Donanım belirtimleri

| Donanım belirtimleri                | HB serisi VM                     |
|----------------------------------|----------------------------------|
| Çekirdek                            | 60 (SMT devre dışı)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU frekansını (AVX olmayan)          | ~2.55 GHz (tek + tüm çekirdek)   |
| Bellek                           | 4 GB/çekirdek (toplam 240)            |
| Yerel Disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Ağ                          | 50 Gb Ethernet (40 Gb kullanılabilir) Azure ikinci nesil SmartNIC *** |

## <a name="software-specifications"></a>Yazılım özellikleri

| Yazılım özellikleri           |HB serisi VM           |
|-----------------------------|-----------------------|
| En fazla MPI iş boyutu            | 6000 çekirdek (100 sanal makine ölçek kümeleri) 12000 çekirdek (200 sanal makine ölçek kümeleri)  |
| MPI desteği                 | MVAPICH2, OpenMPI MPICH, Platform MPI, Intel MPI  |
| Ek çerçeveler       | Birleşik iletişim X, libfabric, PGAS |
| Azure depolama desteği       | Standart ve Premium (en fazla 4 disk) |
| SRLOV RDMA için işletim sistemi desteği   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud desteği    | Evet                         |
| Azure Batch desteği         | Evet                         |

## <a name="next-steps"></a>Sonraki adımlar

* HPC VM boyutları hakkında daha fazla bilgi [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) ve [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) azure'da.

* Daha fazla bilgi edinin [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) azure'da.
