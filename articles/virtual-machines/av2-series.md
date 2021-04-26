---
title: Av2 Serisi
description: AV2 serisi VM 'Ler için Özellikler.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 680ffe7964104f157debc64e44ac2f004ddac86d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565521"
---
# <a name="av2-series"></a>Av2 Serisi

AV2 serisi VM 'Ler, çeşitli donanım türlerinde ve işlemcilerde dağıtılabilir. AV2 serisi VM 'Lerde geliştirme ve test gibi giriş düzeyi iş yükleri için en uygun CPU performansı ve bellek yapılandırması vardır. Bu boyut, dağıtılan donanımdan bağımsız olarak, çalışan örnek için tutarlı işlemci performansı sunacak şekilde kısıtlanıyor. Bu boyutun dağıtıldığı fiziksel donanımı belirlemek için Sanal Makinenin içinden sanal donanımı sorgulayın. Bazı örnek kullanım örnekleri arasında geliştirme ve test sunucuları, düşük trafikli web sunucuları, küçük ve orta ölçekli veritabanları, kavram kanıtı ve kod depoları bulunur.

[Acu](acu.md): 100<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor <br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor <br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor <br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir <br>
[VM oluşturma desteği](generation-2.md): 1. nesil <br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenmez<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal Çekirdek | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla geçici depolama aktarım hızı: ıOPS/okuma MBps/yazma MBps | Maksimum veri diski/aktarım hızı: ıOPS | En fazla NIC | Beklenen ağ bant genişliği (MB/sn)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Diğer boyutlar ve bilgiler

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

Fiyatlandırma Hesaplayıcı: [Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/)

Disk türleri hakkında daha fazla bilgi: [disk türleri](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
