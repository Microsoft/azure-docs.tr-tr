---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751143"
---
|  | **Özel Eşleme** | **Microsoft Eşlemesi** |  **Ortak eşleme** (yeni devreler için kullanım dışı) |
| --- | --- | --- | --- |
| **Maksimum. # eşleme başına desteklenen ön ekler** |4000 varsayılan olarak, ExpressRoute Premium ile 10.000 |200 |200 |
| **Desteklenen IP adresi aralıkları** |WAN 'ağınız içindeki geçerli herhangi bir IP adresi. |Size veya bağlantı sağlayıcınıza ait genel IP adresleri. |Size veya bağlantı sağlayıcınıza ait genel IP adresleri. |
| **Sayı gereksinimleri olarak** |Özel ve ortak AS numaraları. Birini kullanmayı seçerseniz ortak AS numarasına sahip olmanız gerekir. |Özel ve ortak AS numaraları. Ancak, genel IP adreslerinin sahipliğini kanıtlamanız gerekir. |Özel ve ortak AS numaraları. Ancak, genel IP adreslerinin sahipliğini kanıtlamanız gerekir. |
| **Desteklenen IP protokolleri**| IPv4, IPv6 (Önizleme) |  IPv4, IPv6 | IPv4 |
| **Yönlendirme arabirimi IP adresleri** |RFC1918 ve genel IP adresleri |Yönlendirme kayıt defterlerinde size kayıtlı genel IP adresleri. |Yönlendirme kayıt defterlerinde size kayıtlı genel IP adresleri. |
| **MD5 karma desteği** |Yes |Yes |Yes |