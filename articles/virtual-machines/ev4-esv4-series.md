---
title: Ev4 ve Esv4 serisi-Azure sanal makineleri
description: Ev4 ve Esv4 serisi VM 'Ler için Özellikler.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 677158c69ef15508ff9fc00e83ff87678cf9f983
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443978"
---
# <a name="ev4-and-esv4-series"></a>Ev4 ve Esv4 serisi

Ev4 ve Esv4 serisi, &reg; &reg; hiper iş parçacıklı bir yapılandırmadaki Intel Xeon Platinum 8272CL (Cascade Lake) işlemcileri üzerinde çalışır, bellek açısından yoğun çeşitli kurumsal uygulamalar için Idealdir ve 50 GB 'A kadar RAM özelliğine sahiptir. 3,4 GHz 'nin tüm çekirdek Turbo saat hızına sahiptir.

> [!NOTE]
> Sık sorulan sorular için,  [Yerel geçici disk olmadan Azure VM boyutlarına](azure-vms-no-temp-disk.md)bakın.

## <a name="ev4-series"></a>Ev4 serisi

Ev4 serisi boyutlar Intel Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Ev4 serisi örnekler, bellek açısından yoğun kurumsal uygulamalar için idealdir. Ev4 serisi VM 'Ler özelliği Intel &reg; Hyper-Threading Technology.

Uzak veri diski depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium Depolama disklerini kullanmak için Esv4 boyutlarını kullanın. Esv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Ev4 serisi ile aynıdır.

[Acu](acu.md): 195-210<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Yalnızca uzak depolama | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Yalnızca uzak depolama | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Yalnızca uzak depolama | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Yalnızca uzak depolama | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Yalnızca uzak depolama | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Yalnızca uzak depolama | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Yalnızca uzak depolama | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Yalnızca uzak depolama | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 serisi

Esv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Esv4 serisi örnekler, bellek açısından yoğun kurumsal uygulamalar için idealdir. Evs4 serisi VM 'Ler özelliği Intel &reg; Hyper-Threading Technology. Uzak veri diski depolaması, sanal makinelerden ayrı olarak faturalandırılır.

[Acu](acu.md): 195-210<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Yalnızca uzak depolama | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Yalnızca uzak depolama | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Yalnızca uzak depolama | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Yalnızca uzak depolama | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Yalnızca uzak depolama | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Yalnızca uzak depolama | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Yalnızca uzak depolama | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Yalnızca uzak depolama | 32 | 80000/1200 | 8|30000 |
| Standard_E80is_v4 <sup>2</sup> | 80 | 504 | Yalnızca uzak depolama | 32 | 80000/1500 | 8|30000 |

<sup>1</sup> [kısıtlı çekirdek boyutları kullanılabilir)](./constrained-vcpu.md).

<sup>2</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

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
