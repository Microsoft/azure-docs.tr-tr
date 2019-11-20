---
title: Azure Key Vault için güvenlik denetimleri
description: Azure Key Vault değerlendirmek için güvenlik denetimlerinin denetim listesi
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886767"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure Key Vault için güvenlik denetimleri

Bu makalede, Azure Key Vault yerleşik olarak bulunan güvenlik denetimleri Belgelenebilir. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Hizmet uç noktası desteği| Evet | Sanal ağ (VNet) hizmet uç noktalarını kullanma. |
| VNet ekleme desteği| Hayır |  |
| Ağ yalıtımı ve güvenlik duvarı desteği| Evet | VNet güvenlik duvarı kurallarını kullanma. |
| Zorlamalı tünel desteği| Hayır |  |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Evet | Log Analytics kullanma. |
| Denetim/Yönetim düzlemi günlüğü ve denetimi| Evet | Log Analytics kullanma. |
| Veri düzlemi günlüğü ve denetimi| Evet | Log Analytics kullanma. |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Authentication| Evet | Kimlik doğrulaması Azure Active Directory. |
| Authorization| Evet | Key Vault erişim Ilkesini kullanma. |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Bekleyen sunucu tarafı şifrelemesi: Microsoft tarafından yönetilen anahtarlar | Evet | Tüm nesneler şifrelenir. |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Evet | Müşteri, Key Vault tüm anahtarları denetler. Donanım güvenlik modülü (HSM) tarafından desteklenen anahtarlar belirtildiğinde, FIPS düzey 2 HSM anahtarı, sertifikayı veya parolayı korur. |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Yok |  |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Evet | Tüm iletişimler şifreli API çağrıları aracılığıyla yapılır |
| Şifrelenmiş API çağrıları| Evet | HTTPS kullanma. |

## <a name="access-controls"></a>Erişim denetimleri

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Denetim/Yönetim düzlemi erişim denetimleri | Evet | Azure Resource Manager Rol Tabanlı Access Control (RBAC) |
| Veri düzlemi erişim denetimleri (her hizmet düzeyinde) | Evet | Key Vault erişim Ilkesi |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.