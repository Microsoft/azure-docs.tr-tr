---
title: HBv3-Series-Azure sanal makineleri
description: HBv3 serisi VM 'Ler için Özellikler.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 14c5484268940a927965acf798310c3bdccf17d3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309722"
---
# <a name="hbv3-series"></a>HBv3 serisi

HBv3 serisi VM 'Ler, sıvı Dynamics, açık ve örtük sınırlı öğe analizi, hava durumu modelleme, seismik işleme, rezervoır benzetimi ve RTL simülasyonu gibi HPC uygulamaları için iyileştirilmiştir. HBv3 VM 'Leri 120 AMD EPI™ 7003-serisi (MILAN) CPU çekirdekleri, 448 GB RAM ve hiper iş parçacığı oluşturma özelliğine sahiptir. HBv3 serisi VM 'Ler ayrıca 350 GB/sn bellek bant genişliği, çekirdek başına en fazla 32 MB L3 önbellek, blok cihaz SSD performansı ve saat sıklıklarının 3,675 GHz 'ye kadar olan GB/sn sağlar. 

Tüm HBv3 serisi VM 'Ler, NVıDıA ağ üzerinden 200 GB/sn HDR InfiniBand özelliği, süper bilgisayar ölçekli MPı iş yüklerini etkinleştirir. Bu VM 'Ler, iyileştirilmiş ve tutarlı RDMA performansı için engelleyici olmayan bir FAT ağacına bağlanır. HDR InfiniBand yapısı, uyarlamalı yönlendirmeyi ve dinamik bağlı taşımayı (Standart RC ve UD aktarımlarına ek olarak) destekler. Bu özellikler uygulama performansını, ölçeklenebilirliği ve tutarlılığı geliştirir ve kullanımları kesinlikle önerilir.

[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Ultra diskler](disks-types.md#ultra-disk): desteklenir (kullanılabilirlik, kullanım ve performans hakkında[daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) ) <br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): çok yakında<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

|Boyut |Sanal işlemci |İşlemci |Bellek (GiB) |Bellek bant genişliği GB/sn |Taban CPU sıklığı (GHz) |Tüm çekirdekler sıklığı (GHz, tepe) |Tek çekirdekli sıklık (GHz, tepe) |RDMA performansı (GB/sn) |MPı desteği |Geçici depolama (GiB) |Maksimum veri diskleri |En fazla Ethernet vNIC |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYıC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Tümü |2 * 960 |32 |8 |
|Standard_HB120 96rs_v3 |96  |AMD EPYıC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Tümü |2 * 960 |32 |8 |
|Standard_HB120 64rs_v3 |64  |AMD EPYıC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Tümü |2 * 960 |32 |8 |
|Standard_HB120 32rs_v3 |32  |AMD EPYıC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Tümü |2 * 960 |32 |8 |
|Standard_HB120 16rs_v3 |16  |AMD EPYıC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Tümü |2 * 960 |32 |8 |

Hakkında daha fazla bilgi edinin:
- [Mimari ve VM topolojisi](./workloads/hpc/hbv3-series-overview.md)
- Desteklenen işletim sistemi dahil desteklenen [yazılım yığını](./workloads/hpc/hbv3-series-overview.md#software-specifications)
- HBv3 serisi VM 'nin beklenen [performansı](./workloads/hpc/hbv3-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
- Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
