---
title: Dav4 ve Dasv4 serisi
description: Dav4 ve Dasv4 serisi VM 'Ler için Özellikler.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d68e5b7406b2cfa32b06f4731180684bae0c4334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554114"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 ve Dasv4 serisi

Dav4-Series ve Dasv4 serisi, çok iş parçacıklı bir yapılandırmada AMD 'nin 2.35 GHz EPıC<sup>TM</sup> 7452 işlemcisini kullanan yeni boyutlardır. her 8 çekirdeğe, bu L3 önbelleğin, genel amaçlı iş yüklerini çalıştırmaya yönelik müşteri seçeneklerini artıran 256 en az 8 Dav4-Series ve Dasv4 serisi, D & Dsv3 serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

## <a name="dav4-series"></a>Dav4 serisi

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

Dav4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını elde etmeye yönelik 2.35 GHz AMD EPıC<sup>TM</sup> 7452 işlemcisini temel alır. Dav4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve geçici depolama alanı birleşimini sunar. Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium SSD 'yi kullanmak için Dasv4 boyutlarını kullanın. Dasv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Dav4 serisi ile aynıdır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000/46/23   | 2 | 800 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2 | 1600 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4 | 3200 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8 | 6400 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8 | 12800 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 19200 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 25600 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 32000 |

## <a name="dasv4-series"></a>Dasv4 serisi

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

Dasv4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını ve Premium SSD 'yi kullanmayı sağlayan 2.35 GHz AMD EPIC<sup>TM</sup> 7452 işlemcisini temel alır. Dasv4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve geçici depolama alanı birleşimini sunar.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200|8 | 25600 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200|8 | 32000 |

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
