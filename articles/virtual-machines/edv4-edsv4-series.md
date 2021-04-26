---
title: Edv4 ve Edsv4 serisi
description: Ev4, Edv4, Esv4 ve Edsv4 serisi VM 'Ler için Özellikler.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 407d208ef0385c795d086bb495ed51a020395cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443995"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 ve Edsv4 serisi

Edv4 ve Edsv4 serisi, &reg; &reg; hiper iş parçacıklı bir yapılandırmadaki Intel Xeon Platinum 8272CL (Cascade Lake) işlemcilerde çalışır ve 504 gib 'A kadar RAM, [Intel &reg; Turbo Boost technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ve [Intel &reg; Gelişmiş vektör Uzantıları 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)için idealdir. Ayrıca [Intel &reg; derin öğrenme artışı](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)de desteklenir. Bu yeni VM boyutları %50 daha büyük yerel depolamaya sahip olacak ve [Gen2 VM 'leri](./generation-2.md)ile [Ev3/Esv3](./ev3-esv3-series.md) boyutlarına kıyasla hem okuma hem de yazma için daha iyi yerel disk IOPS olacaktır. 3,4 GHz 'nin tüm çekirdek Turbo saat hızına sahiptir. 

## <a name="edv4-series"></a>Edv4 serisi

Edv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) işlemcileri üzerinde çalışır. Edv4 sanal makinesi, hızlı ve büyük yerel SSD depolamaya (2.400 GiB 'ye kadar) ek olarak 504 GiB 'a kadar RAM özelliğine sahiptir. Bu sanal makineler, düşük gecikme süresi, yüksek hızlı yerel depolama 'dan faydalanabilecek bellek yoğunluklu kurumsal uygulamalar ve uygulamalar için idealdir. Edv4 VM 'lerine standart SSD 'ler ve standart HDD disk depolama alanı ekleyebilirsiniz. 

[Acu](acu.md): 195-210<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | <sup>**</sup> Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup>Bu IOPS değerleri [Gen2 VM 'leri](generation-2.md) kullanılarak garanti edilebilir

## <a name="edsv4-series"></a>Edsv4 serisi

Edsv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) işlemcileri üzerinde çalışır. Edsv4 sanal makinesi, hızlı ve büyük yerel SSD depolamaya (2.400 GiB 'ye kadar) ek olarak 504 GiB 'a kadar RAM özelliğine sahiptir. Bu sanal makineler, düşük gecikme süresi, yüksek hızlı yerel depolama 'dan faydalanabilecek bellek yoğunluklu kurumsal uygulamalar ve uygulamalar için idealdir.

[Acu](acu.md): 195-210<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | <sup>**</sup> Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps (GiB 'de önbellek boyutu) | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |
| Standard_E80ids_v4 <sup>2</sup> | 80 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1500 | 8|30000 |

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
