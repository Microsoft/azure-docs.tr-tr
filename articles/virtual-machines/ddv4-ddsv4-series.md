---
title: Ddv4 ve Ddsv4 serisi
description: Dv4, Ddv4, Dsv4 ve Ddsv4 serisi VM 'Ler için Özellikler.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: feabdcef9298c93f0cba93d3eeb9ebb0a32d6ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560353"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 ve Ddsv4 serisi

Ddv4 ve Ddsv4 serisi, &reg; &reg; bir hiper iş yükleri için daha iyi bir değer teklifi sağlayan, bir hiper iş parçacığı yapılandırmasındaki Intel Xeon Platinum 8272CL (Cascade Lake) işlemcilerde çalışır. 3,4 GHz, [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ve [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)tüm Core Turbo saat hızına sahiptir. Ayrıca [Intel &reg; derin öğrenme artışı](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)de desteklenir. Bu yeni VM boyutları %50 daha büyük yerel depolamaya sahip olacak ve [Gen2 VM 'leri](./generation-2.md)ile [Dv3/Dsv3](./dv3-dsv3-series.md) boyutlarına kıyasla hem okuma hem de yazma için daha iyi yerel disk IOPS olacaktır.

D Serisi Kullanım örnekleri arasında kurumsal düzeyde uygulamalar, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz bulunur.

## <a name="ddv4-series"></a>Ddv4 serisi

Ddv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Ddv4-Series, çoğu üretim iş yükleri için vCPU, bellek ve geçici disk birleşimini sunmaktadır.

Yeni Ddv4 VM boyutları, hızlı, daha büyük yerel SSD depolama (2.400 GiB 'ye kadar) içerir ve düşük gecikme süresi, geçici depolama alanına hızlı okuma/yazma veya önbellek ya da geçici dosyalar için geçici depolamaya ihtiyaç duyulan uygulamalar gibi, yüksek hızlı yerel depolama alanı için tasarlanmıştır. Ddv4 VM 'lerine standart SSD 'ler ve standart HDD depolama alanı ekleyebilirsiniz. Uzak veri diski depolaması, sanal makinelerden ayrı olarak faturalandırılır.

[Acu](acu.md): 195-210<br>
[Premium Depolama](premium-storage-performance.md): desteklenmiyor<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): destekleniyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenir<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (*en az 4 vCPU gerektirir*)<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br> 

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | <sup>**</sup> Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup>Bu IOPS değerleri [Gen2 VM 'leri](generation-2.md) kullanılarak garanti edilebilir

## <a name="ddsv4-series"></a>Ddsv4 serisi

Ddsv4-Series, Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Ddsv4-Series, çoğu üretim iş yükleri için vCPU, bellek ve geçici disk birleşimini sunmaktadır.

Yeni Ddsv4 VM boyutları, hızlı, daha büyük yerel SSD depolama (2.400 GiB 'ye kadar) içerir ve düşük gecikme süresi, geçici depolama alanına hızlı okuma/yazma veya önbellek ya da geçici dosyalar için geçici depolamaya ihtiyaç duyulan uygulamalar gibi, yüksek hızlı yerel depolama alanı için tasarlanmıştır. 

 > [!NOTE]
 >Ddsv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Ddv4 serisi ile aynıdır.

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
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>**</sup>Bu IOPS değerleri [Gen2 VM 'leri](generation-2.md) kullanılarak garanti edilebilir

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
