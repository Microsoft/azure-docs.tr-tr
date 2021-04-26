---
title: Kısıtlanmış vCPU boyutları
description: Kısıtlanmış vCPU sayısına sahip olmayan VM boyutlarını listeler.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557089"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Kısıtlanmış vCPU özellikli VM boyutları

SQL Server veya Oracle gibi bazı veritabanı iş yükleri yüksek bellek, depolama ve g/ç bant genişliği gerektirir, ancak yüksek çekirdekli bir sayı değildir. Birçok veritabanı iş yükü CPU yoğunluklu değildir. Azure, aynı bellek, depolama ve g/ç bant genişliğini koruyarak, VM vCPU sayısını, yazılım lisanslama maliyetini azaltmak üzere kısıtlamak için belirli VM boyutları sunar.

VCPU sayısı, özgün VM boyutunun bir yarısı veya bir çeyrekte kısıtlanabilir. Bu yeni VM boyutları, tanımanıza daha kolay hale getirmek için etkin vCPU sayısını belirten bir soneke sahiptir.

Örneğin, geçerli VM boyutu Standard_GS5, 32 vCPU, 448 GB RAM, 64 disk (256 TB 'a kadar) ve 80.000 IOPS veya 2 GB/s g/ç bant genişliği ile gelir. Standard_GS5-16 ve Standard_GS5-8 yeni VM boyutları, sırasıyla 16 ve 8 etkin vCPU ile birlikte gelir; bu arada bellek, depolama ve g/ç bant genişliği için Standard_GS5 özelliklerinin geri kalanını sürdürirken.

SQL Server veya Oracle için ücretlendirilen lisanslama ücretleri yeni vCPU sayısı ile sınırlıdır ve diğer ürünlerin yeni vCPU sayısı temelinde ücretlendirilmelidir. Bu, VM özelliklerinin etkin (Faturalanabilir) vCPU 'Lara oranını artırdığı için %50 ile %75 arasında bir sonuç verir. Bu yeni VM boyutları, müşteri iş yüklerinin, yazılım lisanslama maliyetini iyileştirirken aynı bellek, depolama ve g/ç bant genişliğini kullanmasına izin verir. Şu anda, işletim maliyeti, işletim sistemi lisansı içeren işlem maliyeti, özgün boyutuyla aynı kalır. Daha fazla bilgi için bkz. [Azure VM boyutları daha fazla maliyetli veritabanı iş yükleri için](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name                | Sanal işlemci | Özellikler           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | M8ms ile aynı    |
| Standard_M8-4ms     | 4    | M8ms ile aynı    |
| Standard_M16-4ms    | 4    | M16ms ile aynı   |
| Standard_M16-8ms    | 8    | M16ms ile aynı   |
| Standard_M32-8ms    | 8    | M32ms ile aynı   |
| Standard_M32-16ms   | 16   | M32ms ile aynı   |
| Standard_M64-32ms   | 32   | M64ms ile aynı   |
| Standard_M64-16ms   | 16   | M64ms ile aynı   |
| Standard_M128-64ms  | 64   | M128ms ile aynı  |
| Standard_M128-32ms  | 32   | M128ms ile aynı  |
| Standard_E4 2s_v3   | 2    | E4s_v3 ile aynı  |
| Standard_E8 4s_v3   | 4    | E8s_v3 ile aynı  |
| Standard_E8 2s_v3   | 2    | E8s_v3 ile aynı  |
| Standard_E16 8s_v3  | 8    | E16s_v3 ile aynı |
| Standard_E16 4s_v3  | 4    | E16s_v3 ile aynı |
| Standard_E32 16s_v3 | 16   | E32s_v3 ile aynı |
| Standard_E32 8s_v3  | 8    | E32s_v3 ile aynı |
| Standard_E64 32s_v3 | 32   | E64s_v3 ile aynı |
| Standard_E64 16s_v3 | 16   | E64s_v3 ile aynı |
| Standard_E4 2s_v4   | 2    | E4s_v4 ile aynı  |
| Standard_E8 4s_v4   | 4    | E8s_v4 ile aynı  |
| Standard_E8 2s_v4   | 2    | E8s_v4 ile aynı  |
| Standard_E16 8s_v4  | 8    | E16s_v4 ile aynı |
| Standard_E16 4s_v4  | 4    | E16s_v4 ile aynı |
| Standard_E32 16s_v4 | 16   | E32s_v4 ile aynı |
| Standard_E32 8s_v4  | 8    | E32s_v4 ile aynı |
| Standard_E64 32s_v4 | 32   | E64s_v4 ile aynı |
| Standard_E64 16s_v4 | 16   | E64s_v4 ile aynı |
| Standard_E4 2ds_v4  | 2    | E4ds_v4 ile aynı |
| Standard_E8 4ds_v4  | 4    | E8ds_v4 ile aynı |
| Standard_E8 2ds_v4  | 2    | E8ds_v4 ile aynı |
| Standard_E16 8ds_v4 | 8    | E16ds_v4 ile aynı|
| Standard_E16 4ds_v4 | 4    | E16ds_v4 ile aynı|
| Standard_E32 16ds_v4| 16   | E32ds_v4 ile aynı|
| Standard_E32 8ds_v4 | 8    | E32ds_v4 ile aynı|
| Standard_E64 32ds_v4| 32   | E64ds_v4 ile aynı|
| Standard_E64 16ds_v4| 16   | E64ds_v4 ile aynı|
| Standard_E4 2as_v4  | 2    | E4as_v4 ile aynı |
| Standard_E8 4as_v4  | 4    | E8as_v4 ile aynı |
| Standard_E8 2as_v4  | 2    | E8as_v4 ile aynı |
| Standard_E16 8as_v4 | 8    | E16as_v4 ile aynı|
| Standard_E16 4as_v4 | 4    | E16as_v4 ile aynı|
| Standard_E32 16as_v4| 16   | E32as_v4 ile aynı|
| Standard_E32 8as_v4 | 8    | E32as_v4 ile aynı|
| Standard_E64 32as_v4| 32   | E64as_v4 ile aynı|
| Standard_E64 16as_v4| 16   | E64as_v4 ile aynı|
| Standard_E96 48as_v4| 48   | E96as_v4 ile aynı|
| Standard_E96 24as_v4| 24   | E96as_v4 ile aynı|
| Standard_GS4-8      | 8    | GS4 ile aynı     |
| Standard_GS4-4      | 4    | GS4 ile aynı     |
| Standard_GS5-16     | 16   | GS5 ile aynı     |
| Standard_GS5-8      | 8    | GS5 ile aynı     |
| Standard_DS11 1_v2  | 1    | DS11_v2 ile aynı |
| Standard_DS12 2_v2  | 2    | DS12_v2 ile aynı |
| Standard_DS12 1_v2  | 1    | DS12_v2 ile aynı |
| Standard_DS13 4_v2  | 4    | DS13_v2 ile aynı |
| Standard_DS13 2_v2  | 2    | DS13_v2 ile aynı |
| Standard_DS14 8_v2  | 8    | DS14_v2 ile aynı |
| Standard_DS14 4_v2  | 4    | DS14_v2 ile aynı |
| Standard_M416 208s_v2 | 208    | M416s_v2 ile aynı|
| Standard_M416 208ms_v2 | 208    | M416ms_v2 ile aynı |

## <a name="other-sizes"></a>Diğer boyutlar
- [İşlem için iyileştirilmiş](./sizes-compute.md)
- [Bellek için iyileştirilmiş](./sizes-memory.md)
- [Depolama için iyileştirilmiş](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [Yüksek performanslı işlem](./sizes-hpc.md)

## <a name="next-steps"></a>Sonraki adımlar
Azure [işlem birimlerinin (ACU)](./acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
