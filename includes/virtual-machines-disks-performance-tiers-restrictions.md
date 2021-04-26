---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750164"
---
- Bu özellik şu anda yalnızca Premium SSD 'Ler için destekleniyor.
- Diskin katmanını değiştirmeden önce VM 'nizi serbest bırakabilir veya diski çalışan bir VM 'den ayırmanız gerekir.
- P60, P70 ve P80 performans katmanları yalnızca 4.096 GiB 'den büyük diskler tarafından kullanılabilir.
- Diskin performans katmanı 12 saatte bir yalnızca bir kez indirgenir.

## <a name="change-performance-tier-without-downtime-preview"></a>Kesinti olmadan performans katmanını değiştirme (Önizleme)

Normalde, VM 'nizi serbest bırakmak veya performans katmanınızı değiştirmek için diskinizin bağlantısını ayırmanız gerekir. Ancak bu önizleme özelliğini etkinleştirirseniz, sanal makineyi serbest bırakmak veya katmanı değiştirmek için diskinizi ayırmanız gerekmez. Önizlemeye [buradan](https://aka.ms/liveperftiersignup)kaydolabilirsiniz.

Önizleme aşağıdaki sınırlamalara sahiptir:
- Yalnızca EastUS2EUAP bölgesinde kullanılabilir.
- Şu anda paylaşılan diskler için kullanılamıyor
- `2020-12-01`Performans katmanlarını kapalı kalma süresi olmadan değiştirmek için API ile birlikte Azure Resource Manager şablonları kullanılmalıdır.