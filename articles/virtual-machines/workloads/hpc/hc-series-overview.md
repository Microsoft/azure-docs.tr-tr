---
title: HC serisi VM genel bakış - Azure sanal makineler | Microsoft Docs
description: Azure'da HC serisi VM boyutu için Önizleme desteği hakkında daha fazla bilgi edinin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797543"
---
# <a name="hc-series-virtual-machine-overview"></a>HC serisi sanal makineye genel bakış

Intel Xeon ölçeklenebilir işlemcilerde HPC uygulama performansını en üst düzeye, bu yeni mimariyi yerleştirme işlem için özenli bir yaklaşım gerektirir. Burada, bunu kararlılığımızın HPC uygulamaları için Azure HC serisi vm'lerde özetler. Bir fiziksel NUMA etki alanı ve sanal NUMA'yı etki alanına başvurmak için "vNUMA" başvurmak için "pNUMA" terimini kullanacağız. Benzer şekilde, fiziksel CPU çekirdekleri için başvurmak için "pCore" terimini kullanacağız ve CPU çekirdeği başvurmak için "sanal çekirdek" sanallaştırılmış.

Fiziksel bir HC sunucusu 2 olan * 24 çekirdekli Intel Xeon Platinum 8168 CPU toplam 48 fiziksel çekirdek. Her CPU tek pNUMA etki alanı ve altı kanalının bir DRAM erişimi birleşik. Büyük L2 önbelleği x 4'te de karşılaştırma için önceki Intel CPU'yu L3 Önbellek azaltırken önceki nesil (1 MB/çekirdek 256 KB/çekirdek->), bir Intel Xeon Platinum CPU özelliği (2,5 MB/core 1.375 MB/çekirdek->).

Yukarıdaki topolojisi HC serisi hiper yönetici yapılandırması için de yaşamanız sağlanır. Azure hiper yönetici VM ile engellemeden çalışılacak yer sağlamak için pCores 0-1 ve 24-25 (diğer bir deyişle, her yuva ilk 2 pCores) tutarız. Biz ardından pNUMA etki alanları tüm kalan çekirdek VM'ye atayın. Bu nedenle, VM görürsünüz:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` VM başına çekirdek

Sanal makine pCores 0-1 ve 25 24 verilen olmayan, bilgi içeriyor. Bu nedenle, bunu yerel olarak 22 çekirdek varmış gibi her vNUMA kullanıma sunar.

Sıra Intel Xeon Platinum, altın ve Gümüş CPU bir-zar 2B kafes ağ içinde iletişim kurmak ve dış CPU yuvaya uygular. İşlemi en iyi performans ve tutarlılık için sabitleme kesinlikle öneririz. İşlem sabitleme çalışır HC serisi VM'ler üzerinde temel alınan Silikon olarak açığa çıkarıldığı-Konuk VM. Daha fazla bilgi için bkz. [Intel Xeon SP mimarisi](https://bit.ly/2RCYkiE).

Aşağıdaki diyagramda çekirdek ayrımı ayrılmış Azure hiper Yöneticisi ve HC serisi VM için gösterilmektedir.

![Azure hiper Yöneticisi ve HC serisi VM için ayrılan çekirdek ayrımı](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Donanım belirtimleri

| Donanım belirtimleri          | HC serisi VM                     |
|----------------------------------|----------------------------------|
| Çekirdek                            | 40 (HT devre dışı)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| CPU frekansını (AVX olmayan)          | 3.7 GHz (tek çekirdek) 2.7 3.4 GHz (tüm çekirdek) |
| Bellek                           | 8 GB/çekirdek (toplam 352)            |
| Yerel Disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Ağ                          | 50 Gb Ethernet (40 Gb kullanılabilir) Azure ikinci nesil SmartNIC *** |

## <a name="software-specifications"></a>Yazılım özellikleri

| Yazılım özellikleri     | HC serisi VM          |
|-----------------------------|-----------------------|
| En fazla MPI iş boyutu            | 4400 çekirdek (100 sanal makine ölçek kümeleri), 8800 çekirdekler (200 sanal makine ölçek kümeleri) |
| MPI desteği                 | MVAPICH2, OpenMPI MPICH, Platform MPI, Intel MPI  |
| Ek çerçeveler       | Birleşik iletişim X, libfabric, PGAS |
| Azure depolama desteği       | Standart ve Premium (en fazla 4 disk) |
| SRLOV RDMA için işletim sistemi desteği   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud desteği    | Evet                         |
| Azure Batch desteği         | Evet                         |

## <a name="next-steps"></a>Sonraki adımlar

* HPC VM boyutları hakkında daha fazla bilgi [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) ve [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) azure'da.

* Daha fazla bilgi edinin [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) azure'da.
