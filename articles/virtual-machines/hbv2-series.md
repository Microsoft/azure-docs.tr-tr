---
title: HBv2-Series-Azure sanal makineleri
description: HBv2 serisi VM 'Ler için Özellikler.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 25f0933c2d0b8b3c8ec227ce52c974a50a671043
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309739"
---
# <a name="hbv2-series"></a>HBv2 serisi

HBv2 serisi VM 'Ler, akıcı Dynamics, sınırlı öğe analizi ve rezervoır benzetimi gibi bellek bant genişliğine göre çalışan uygulamalar için iyileştirilmiştir. HBv2 VM 'Ler özelliği 120 AMD EPIC 7742 işlemci çekirdekleri, CPU çekirdeği başına 4 GB RAM ve eşzamanlı çoklu iş parçacığı yok. Her HBv2 VM, en fazla 340 GB/sn bellek bant genişliği ve en fazla 4 işlem FP64 işlem sağlar.

HBv2 serisi VM 'Ler özelliği 200 GB/sn Mellanox HDR InfiniBand. Bu VM 'Ler, iyileştirilmiş ve tutarlı RDMA performansı için engelleyici olmayan bir FAT ağacına bağlanır. Bu VM 'Ler, uyarlamalı yönlendirmeyi ve dinamik bağlı taşımayı (Standart RC ve UD aktarımlara ek olarak DCT) destekler. Bu özellikler uygulama performansını, ölçeklenebilirliği ve tutarlılığı geliştirir ve kullanımları önerilir.

[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Ultra diskler](disks-types.md#ultra-disk): desteklenir (kullanılabilirlik, kullanım ve performans hakkında[daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) ) <br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (performans ve olası sorunlar hakkında[daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) ) <br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | İşlemci | Bellek (GiB) | Bellek bant genişliği GB/sn | Taban CPU sıklığı (GHz) | Tüm çekirdekler sıklığı (GHz, tepe) | Tek çekirdekli sıklık (GHz, tepe) | RDMA performansı (GB/sn) | MPı desteği | Geçici depolama (GiB) | Maksimum veri diskleri | En fazla Ethernet vNIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYıC 7V12 | 456 | 350 | 2.45 | 3,1 | 3.3 | 200 | Tümü | 480 + 960 | 8 | 8 |

Hakkında daha fazla bilgi edinin:
- [Mimari ve VM topolojisi](./workloads/hpc/hbv2-series-overview.md)
- Desteklenen işletim sistemi dahil desteklenen [yazılım yığını](./workloads/hpc/hbv2-series-overview.md#software-specifications)
- HBv2 serisi VM 'nin beklenen [performansı](./workloads/hpc/hbv2-performance.md)

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
