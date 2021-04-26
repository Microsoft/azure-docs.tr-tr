---
title: Dv4 ve Dsv4 serisi-Azure sanal makineleri
description: Dv4 ve Dsv4 serisi VM 'Ler için Özellikler.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: cc6ce30855d17f45636e0df04978fed88dcecff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549150"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 ve Dsv4 serisi

Dv4 ve Dsv4 serisi, &reg; &reg; bir hiper iş yükleri için daha iyi bir değer teklifi sağlayan, bir hiper iş parçacığı yapılandırmasındaki Intel Xeon Platinum 8272CL (Cascade Lake) işlemcilerde çalışır. 3,4 GHz 'nin tüm çekirdek Turbo saat hızına sahiptir. 

> [!NOTE]
> Sık sorulan sorular için,  [Yerel geçici disk olmadan Azure VM boyutlarına](azure-vms-no-temp-disk.md)bakın.
## <a name="dv4-series"></a>Dv4 serisi

Dv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Dv4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve uzak depolama seçeneklerinin bir birleşimini sunar. Dv4 serisi VM 'Ler özelliği [ıntel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Uzak veri diski depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium Depolama disklerini kullanmak için Dsv4 boyutlarını kullanın. Dsv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri dv4 serisi ile aynıdır.

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
| Standard_D2_v4 | 2 | 8 | Yalnızca uzak depolama | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Yalnızca uzak depolama | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Yalnızca uzak depolama | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Yalnızca uzak depolama | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Yalnızca uzak depolama | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Yalnızca uzak depolama | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Yalnızca uzak depolama | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 serisi

Dsv4 serisi boyutlar Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) üzerinde çalışır. Dv4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve uzak depolama seçeneklerinin bir birleşimini sunar. Dsv4 serisi VM 'Ler özelliği [ıntel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Uzak veri diski depolaması, sanal makinelerden ayrı olarak faturalandırılır.

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
| Standard_D2s_v4 | 2 | 8  | Yalnızca uzak depolama | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Yalnızca uzak depolama | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Yalnızca uzak depolama | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Yalnızca uzak depolama | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Yalnızca uzak depolama | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Yalnızca uzak depolama | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Yalnızca uzak depolama | 32 | 80000/1200 | 8|30000 |
