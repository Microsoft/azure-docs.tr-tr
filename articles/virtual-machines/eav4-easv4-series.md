---
title: Eav4-Series ve Easv4 serisi
description: Eav4 ve Easv4 serisi VM 'Ler için Özellikler.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ec320d7eeebbe5eab14061cd5c194f15acf5638a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557939"
---
# <a name="eav4-and-easv4-series"></a>Eav4 ve Easv4 serisi

Eav4-Series ve Easv4 serisi, en fazla 256 MB boyutlu önbellek kullanan çok iş parçacıklı bir yapılandırmada AMD 'nin 2.35 GHz EPYıC<sup>TM</sup> 7452 işlemcisini kullanır ve en fazla bellek için iyileştirilmiş iş yüklerini çalıştırmaya yönelik seçenekleri artırır. Eav4-Series ve Easv4 serisi, Ev3 & Esv3 serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

## <a name="eav4-series"></a>Eav4 serisi

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

Eav4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını elde etmeye yönelik 2.35 GHz AMD EPıC<sup>TM</sup> 7452 işlemcisini temel alır. Eav4 serisi Boyutlar, bellek açısından yoğun kurumsal uygulamalar için idealdir. Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium SSD 'yi kullanmak için Easv4-Series boyutlarını kullanın. Easv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Eav3 serisi ile aynıdır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standart \_ E2a \_ v4|2|16|50|4|3000/46/23|2 | 800 |
| Standart \_ E4a \_ v4|4|32|100|8|6000/93/46|2 | 1600 |
| Standart \_ E8a \_ v4|8|64|200|16|12000/187/93|4 | 3200 |
| Standart \_ E16a \_ v4|16|128|400|32|24000/375/187|8 | 6400 |
| Standart \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 8000 |
| Standart \_ E32a \_ v4|32|256|800|32|48000/750/375|8 | 12800 |
| Standart \_ E48a \_ v4|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| Standart \_ E64a \_ v4|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| Standart \_ E96a \_ v4|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Easv4 serisi

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

Easv4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını ve Premium SSD 'yi kullanmayı sağlayan 2.35 GHz AMD EPIC<sup>TM</sup> 7452 işlemcisini temel alır. Easv4 serisi Boyutlar, bellek açısından yoğun kurumsal uygulamalar için idealdir.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

<sup>1</sup> [kısıtlı çekirdek boyutu var](./constrained-vcpu.md).

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
