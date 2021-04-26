---
title: include dosyası
description: include dosyası
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98147348"
---
Özel IPSec ilkeleriyle çalışırken aşağıdaki gereksinimleri göz önünde bulundurun:

* **Ike** -Ike IÇIN, Ike şifrelemesi ' nden herhangi bir PARAMETREYI ve Ike bütünlüğünden herhangi bir PARAMETREYI ve DH grubundan herhangi bir parametreyi seçebilirsiniz.
* **IPSec** -IPSec Için IPSec şifrelemesi ' nden herhangi bir parametreyi ve IPSec bütünlüğünden herhangi bir PARAMETREYI ve PFS ' yi seçebilirsiniz. IPSec şifrelemesi veya IPSec bütünlüğü için parametrelerden herhangi biri GCM ise, her iki ayar için parametreler GCM olmalıdır.

>[!NOTE]
> Özel IPSec ilkeleriyle, Yanıtlayıcı ve Başlatıcı (varsayılan IPSec ilkelerinin aksine) kavramı yoktur. Her iki taraf (Şirket içi ve Azure VPN Gateway), ıKE Aşama 1 ve ıKE aşama 2 için aynı ayarları kullanacaktır. Hem IKEv1 hem de IKEv2 protokolleri desteklenir. Azure için yalnızca Yanıtlayıcı olarak destek yoktur.
>

**Kullanılabilir ayarlar ve parametreler**

| Ayar | Parametreler |
|--- |--- |
| IKE şifrelemesi | GCMAES256, GCMAES128, AES256, AES128 |
| IKE bütünlüğü | SHA384, SHA256 |
| DH Grubu | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec Şifrelemesi | GCMAES256, GCMAES128, AES256, AES128, None |
| IPsec Bütünlüğü | GCMAES256, GCMAES128, SHA256 |
| PFS Grubu | ECP384, ECP256, PFS24, PFS14, None |
| SA Yaşam Süresi |gir en az 300/varsayılan 27000 saniye |
