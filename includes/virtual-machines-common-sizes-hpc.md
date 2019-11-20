---
title: include dosyası
description: include dosyası
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116725"
---
Azure HPC iyileştirilmiş sanal makineler (VM 'Ler), çeşitli gerçek dünyada uygulamalar için liderlik sınıfı performans, MPı ölçeklenebilirliği ve maliyet verimliliği sağlamak üzere tasarlanmıştır.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>Büyük ölçekli HPC için InfiniBand ağı
HBv2 VM 'Ler özelliği 200 GB/sn Mellanox HDR InfiniBand, her ikisi de HB ve HC VM 'Leri özelliği 100 GB/sn Mellanox EDR InfiniBand. Bu sanal makine türlerinin her biri, iyileştirilmiş ve tutarlı RDMA performansı için engelleyici olmayan bir FAT ağacına bağlanır.

HBv2 VM 'Leri, uyarlamalı yönlendirmeyi ve dinamik bağlı taşımayı (Standart RC ve UD aktarımlarında ek olarak) destekler. Bu özellikler uygulama performansı, ölçeklenebilirliği ve tutarlılığı geliştirir ve bunların kullanımı kesinlikle önerilir.  

HBv2, HB ve HC VM 'Leri, standart Mellanox/OFED sürücüleri destekler. Bu nedenle, tüm RDMA fiilleri ve MPı türleri desteklenir. Bu sanal makine türlerinin her biri, daha fazla uygulama performansı için MPı topluluğu donanım tabanlı yük boşaltma 'yı da destekler.
 
Özgün H serisi VM 'Ler özelliği 56 GB/sn Mellanox FDR InfiniBand. H serisi üzerinde InfiniBand arabiriminden yararlanmak için, müşterilerin Azure Marketi 'nden bu sanal makine türüne özgü resmi olarak desteklenen görüntüleri kullanarak dağıtılması gerekir. 


## <a name="hbv2-series"></a>HBv2 serisi
HBv2 serisi VM 'Ler, akıcı Dynamics, sınırlı öğe analizi ve rezervoır benzetimi gibi bellek bant genişliğine göre çalışan uygulamalar için iyileştirilmiştir. HBv2 VM 'Ler özelliği 120 AMD EPIC 7742 işlemci çekirdekleri, CPU çekirdeği başına 4 GB RAM ve eşzamanlı çoklu iş parçacığı yok. Her HBv2 VM, en fazla 340 GB/sn bellek bant genişliği ve en fazla 4 işlem FP64 işlem sağlar. 

Premium Depolama: desteklenir

| Boyut | Sanal işlemci | İşlemci | Bellek (GB) | Bellek bant genişliği GB/sn | Taban CPU sıklığı (GHz) | Tüm çekirdekler sıklığı (GHz, tepe) | Tek çekirdekli sıklık (GHz, tepe) | RDMA performansı (GB/sn) | MPı desteği | Geçici depolama (GB) | Maksimum veri diskleri | En fazla Ethernet NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYıC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Tümü | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB serisi
HB serisi VM’ler sıvı dinamiği, belirtik sonlu öğe analizi ve hava durumu modelleme gibi bellek bant genişliğine dayalı uygulamalar için iyileştirilmiştir. HB VM 'Ler özelliği 60 AMD EPIC 7551 işlemci çekirdekleri, CPU çekirdeği başına 4 GB RAM ve eşzamanlı çoklu iş parçacığı yok. Bir HB VM, en fazla 260 GB/sn bellek bant genişliği sağlar.  

ACU: 199-216

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

| Boyut | Sanal işlemci | İşlemci | Bellek (GB) | Bellek bant genişliği GB/sn | Taban CPU sıklığı (GHz) | Tüm çekirdekler sıklığı (GHz, tepe) | Tek çekirdekli sıklık (GHz, tepe) | RDMA performansı (GB/sn) | MPı desteği | Geçici depolama (GB) | Maksimum veri diskleri | En fazla Ethernet NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYıC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tümü | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC serisi
HC Serisi VM 'Ler, örtük sınırlı öğe analizi, molesel Dynamics ve hesaplama Chemistry gibi yoğun hesaplama tarafından yönetilen uygulamalar için iyileştirilmiştir. HC VM’ler, 44 Intel Xeon Platinum 8168 işlemci çekirdeği ve CPU çekirdeği başına 8 GB RAM içerir ve hyperthreading yoktur. Intel Xeon Platinum platformu Intel Math çekirdek kitaplığı gibi yazılım araçlarının zengin ekosistemini destekler. 

ACU: 297-315

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir


| Boyut | Sanal işlemci | İşlemci | Bellek (GB) | Bellek bant genişliği GB/sn | Taban CPU sıklığı (GHz) | Tüm çekirdekler sıklığı (GHz, tepe) | Tek çekirdekli sıklık (GHz, tepe) | RDMA performansı (GB/sn) | MPı desteği | Geçici depolama (GB) | Maksimum veri diskleri | En fazla Ethernet NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Tümü | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H Serisi
H serisi VM 'Ler, yüksek CPU sıklıklarca veya çekirdek gereksinimlerine göre büyük bellek kullanan uygulamalar için iyileştirilmiştir. H serisi VM 'Ler özelliği 8 veya 16 Intel Xeon E5 2667 v3 işlemci çekirdekleri, CPU çekirdeği başına en fazla 14 GB RAM ve hiper iş parçacığı yok. Bir H serisi VM, en fazla 80 GB/sn bellek bant genişliği sağlar.

ACU: 290-300

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

| Boyut | Sanal işlemci | İşlemci | Bellek (GB) | Bellek bant genişliği GB/sn | Taban CPU sıklığı (GHz) | Tüm çekirdekler sıklığı (GHz, tepe) | Tek çekirdekli sıklık (GHz, tepe) | RDMA performansı (GB/sn) | MPı desteği | Geçici depolama (GB) | Maksimum veri diskleri | En fazla Ethernet NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPı | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 |  - | Intel 5. x, MS-MPı | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPı | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPı | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPı | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPı | 2000 | 64 | 4 |

<sup>1</sup> MPI uygulamaları için, adanmış RDMA arka uç ağı, FDR InfiniBand ağı tarafından etkinleştirilir.

<br>
