---
title: Azure yönetilen diskler için performans katmanları
description: Yönetilen diskler için performans katmanları hakkında bilgi edinin.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677312"
---
# <a name="performance-tiers-for-managed-disks"></a>Yönetilen diskler için performans katmanları

Azure yönetilen diskinizin performansı, disk oluşturduğunuzda, performans katmanı biçiminde ayarlanır. Performans katmanı, yönetilen diskinizin ıOPS ve aktarım hızını belirler. Diskinizin sağlanan boyutunu ayarladığınızda, bir performans katmanı otomatik olarak seçilir. Performans katmanı, dağıtım sırasında veya daha sonra disk boyutunu değiştirmeden değiştirilebilir.

Performans katmanını değiştirmek, diskinizin patlama özelliğini kullanmadan daha yüksek talebe göre hazırlanmanıza ve karşılamanızı sağlar. Ek performansın ne kadar süreyle gerekli olduğuna bağlı olarak, performans katmanınızı değiştirmek yerine, daha düşük maliyetli olabilir. Bu, tatil alışverişi, performans testi veya eğitim ortamı çalıştırma gibi düzenli olarak daha yüksek performans düzeyi gerektiren olaylar için idealdir. Bu olayları işlemek için, ihtiyacınız olduğu sürece daha yüksek bir performans katmanı kullanabilirsiniz. Daha sonra ek performansa ihtiyacınız kalmadığında Orijinal katmana geri dönebilirsiniz.

## <a name="how-it-works"></a>Nasıl çalışır?

Bir diski ilk kez dağıttığınızda veya sağladığınızda, bu diskin temel performans katmanı sağlanan disk boyutuna göre ayarlanır. Daha yüksek talebi karşılamak için bir performans katmanını orijinal taban çizgisinden daha yüksek kullanabilirsiniz. Artık bu performans düzeyine ihtiyacınız kalmadığında başlangıçtaki taban çizgisi performans katmanına geri dönebilirsiniz.

Performans katmanınız değiştikçe faturanızı değişiklikleriniz. Örneğin, bir P10 diski (128 GiB) sağlarsanız, taban çizgisi performans katmanınız P10 (500 ıOPS ve 100 MBps) olarak ayarlanır. P10 fiyatı üzerinden faturalandırılırsınız. Disk boyutunu arttırmadan katmanı, P50 (7.500 ıOPS ve 250 MBps) performansını eşleşecek şekilde yükseltebilirsiniz. Yükseltme sırasında, P50 fiyatı üzerinden faturalandırılırsınız. Artık daha yüksek performansa ihtiyacınız kalmadığında P10 katmanına geri dönebilirsiniz. Disk bir kez daha P10 ücret üzerinden faturalandırılır.

| Disk boyutu | Taban çizgisi performans katmanı | , Sürümüne yükseltilebilir |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Yok |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Yok |

Fatura bilgileri için bkz. [yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Sonraki adımlar

Performans katmanınızı nasıl değiştireceğinizi öğrenmek için bkz. [Portal](disks-performance-tiers-portal.md) veya [PowerShell/CLI](disks-performance-tiers.md) makaleleri.

