---
title: Ev3-Series ve Esv3 serisi
description: Ev3 ve Esv3 serisi VM 'Ler için Özellikler.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: mimckitt
ms.openlocfilehash: 878ca249a02d3b53d0085052b2ff1caf590e3ce3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557871"
---
# <a name="ev3-and-esv3-series"></a>Ev3 ve Esv3 serisi

Ev3 ve Esv3 serisi, Intel® Xeon® Platinum 8272CL (Cascade Lake) üzerinde çalışır. ya da Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya bir hiper iş yükleri için Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) işlemcisi, çoğu genel amaçlı iş yükleri için daha iyi bir değer teklifi sağlar ve Ev3 ' yi diğer bulutların genel amaçlı VM 'Leri ile hizalı hale getiriyor.  Bellek ve ağ sınırları, hiper iş parçacığına geçme ile hizalanmak üzere bir çekirdek temelinde ayarlandığı sürece (7 GiB/vCPU 'dan 8 GiB/vCPU 'ya kadar) bellek genişletilir. Ev3, D/dv2 ailelerinin yüksek bellek sanal makine boyutlarına göre takip edilir.

## <a name="ev3-series"></a>Ev3 serisi

Ev3 serisi örnekler Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) işlemcileri ve Feature Intel Turbo Boost Technology 2,0 üzerinde çalışır. Ev3 serisi örnekleri, yoğun bellek kullanımlı kurumsal uygulamalar için idealdir.

Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium depolama disklerini kullanmak için ESv3 boyutlarını kullanın. ESv3 boyutları için fiyatlandırma ve faturalandırma oranları Ev3 serisi ile aynıdır.

Ev3 serisi VM 'nin özelliği Intel® Hyper-Threading teknolojisi.

[Acu](acu.md): 160-190<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum NIC/Ağ bant genişliği |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> kısıtlı çekirdek boyutu var.

<sup>2</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

## <a name="esv3-series"></a>Esv3 serisi

Esv3 serisi örnekler Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) işlemci, özellik Intel Turbo Boost Technology 2,0 ve Premium Storage kullanma üzerinde çalışır. Esv3 serisi örnekler, bellek açısından yoğun kurumsal uygulamalar için idealdir.

Esv3 serisi VM 'nin özelliği Intel® Hyper-Threading teknolojisi.

[Acu](acu.md): 160-190<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps (GiB 'de önbellek boyutu) | Önbelleğe alınan veri bloğu ve geçici depolama aktarım hızı: ıOPS/MBps<sup>3</sup> | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps |  Önbelleğe alınmamış disk aktarım hızı: ıOPS/MBps<sup>3</sup>| En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> kısıtlı çekirdek boyutu var.

<sup>2</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

<sup>3</sup> Esv3 serisi VM 'ler, disk performansını zaman alabilir ve aynı anda en fazla 30 dakika boyunca ücretsiz [olarak alabilir.](./disk-bursting.md)

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Diğer boyutlar ve bilgiler

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)
- [Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/)
- Disk türleri hakkında daha fazla bilgi için bkz. [Azure 'Da hangi disk türleri mevcuttur?](disks-types.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.