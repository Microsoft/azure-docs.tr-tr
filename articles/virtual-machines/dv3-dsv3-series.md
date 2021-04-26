---
title: Dv3 ve Dsv3 serisi
description: Dv3 ve Dsv3 serisi VM 'Ler için Özellikler.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 22400164ac82df3ec0e0e24daf6d823eae219837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566439"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 ve Dsv3 serisi

Dv3-Series, Intel® Xeon® Platinum 8272CL (Cascade Lake) üzerinde çalışır. bir hiper iş yükleri için daha iyi bir değer teklifi sağlayan Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri. Bellek ve ağ sınırları, hiper iş parçacığına geçme ile hizalanmak üzere bir çekirdek temelinde ayarlanırken, bellek (~ 3,5 GiB/vCPU 'dan 4 GiB/vCPU 'ya) genişletildi. Dv3 serisi artık D/dv2-Series ' in yüksek bellek sanal makine boyutlarına sahiptir; bunlar bellek için iyileştirilmiş [Ev3 ve Esv3-serisine](ev3-esv3-series.md)taşınmıştır.

Örnek D Serisi Kullanım örnekleri arasında kurumsal düzeyde uygulamalar, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz vardır.

## <a name="dv3-series"></a>Dv3 serisi

Dv3 serisi boyutlar Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)Ile ıntel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır. Dv3 serisi boyutları, üretim iş yüklerinin çoğu için uygun bir vCPU, bellek ve geçici depolama kombinasyonu sunar.

Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium depolama disklerini kullanmak için Dsv3 boyutlarını kullanın. Dsv3 boyutları için fiyatlandırma ve faturalandırma oranları Dv3 serisi ile aynıdır.

Dv3 serisi VM 'Ler özelliği Intel® Hyper-Threading teknolojisini.

[Acu](acu.md): 160-190<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | En fazla geçici depolama aktarım hızı: ıOPS/okuma MBps/yazma MBps | En fazla NIC/ağ bant genişliği |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 serisi

Dsv3 serisi boyutlar Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (Alswell), Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) Ile ıntel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır ve Premium depolama kullanır. Dsv3 serisi boyutları, üretim iş yüklerinin çoğu için uygun bir vCPU, bellek ve geçici depolama kombinasyonu sunar.

Dsv3 serisi VM 'Ler özelliği Intel® Hyper-Threading teknolojisini.

[Acu](acu.md): 160-190<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps (GiB 'de önbellek boyutu) | Önbelleğe alınan en fazla patlama ve geçici depolama aktarım hızı: ıOPS/MBps<sup>1</sup> | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | Önbelleğe alınmamış maksimum patlama disk işleme: ıOPS/MBps<sup>1</sup> | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> Dsv3 serisi VM 'ler, disk performansını zaman alabilir ve aynı anda en fazla 30 dakika boyunca ücretsiz [olarak alabilir.](./disk-bursting.md)

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