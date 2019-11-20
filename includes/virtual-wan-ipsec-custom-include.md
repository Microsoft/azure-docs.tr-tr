---
title: dosya ekle
description: dosya ekle
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168368"
---
Özel IPSec ilkeleriyle çalışırken aşağıdaki gereksinimleri göz önünde bulundurun:

* **Ike** -Ike IÇIN, Ike şifrelemesi ' nden herhangi bir PARAMETREYI ve Ike bütünlüğünden herhangi bir PARAMETREYI ve DH grubundan herhangi bir parametreyi seçebilirsiniz.
* **IPSec** -IPSec Için IPSec şifrelemesi ' nden herhangi bir parametreyi ve IPSec bütünlüğünden herhangi bir PARAMETREYI ve PFS ' yi seçebilirsiniz. IPSec şifrelemesi veya IPSec bütünlüğü için parametrelerden herhangi biri GCM ise, her iki ayar için parametreler GCM olmalıdır.

>[!NOTE]
> Özel IPSec ilkeleriyle, Yanıtlayıcı ve Başlatıcı (varsayılan IPSec ilkelerinin aksine) kavramı yoktur. Her iki taraf (Şirket içi ve Azure VPN Gateway), ıKE Aşama 1 ve ıKE aşama 2 için aynı ayarları kullanacaktır. Hem IKEv1 hem de IKEv2 protokolleri desteklenir. Azure için yalnızca Yanıtlayıcı olarak destek yoktur.
>

**Kullanılabilir ayarlar ve parametreler**

| Ayar | Parametrelere |
|--- |--- |
| IKE şifrelemesi | AES256, AES192, AES128 |
| IKE bütünlüğü | SHA384, SHA256, SHA1 |
| DH grubu | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPSec şifrelemesi | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPSec bütünlüğü | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS Grubu | PFS24, ECP384, ECP256, PFS2048, PFS2 |
