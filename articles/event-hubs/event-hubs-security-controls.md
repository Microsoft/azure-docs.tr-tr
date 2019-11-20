---
title: Azure Event Hubs için güvenlik denetimleri
description: Azure Event Hubs değerlendirmek için güvenlik denetimlerinin denetim listesi
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219399"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure Event Hubs için güvenlik denetimleri

Bu makale, Azure Event Hubs yerleşik olarak bulunan güvenlik denetimlerini belgeler.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Hizmet uç noktası desteği| Evet |  |  |
| VNet ekleme desteği| Hayır | |  |
| Ağ yalıtımı ve güvenlik duvarı desteği| Evet |  |  |
| Zorlamalı tünel desteği| Hayır |  |  |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Evet | |  |
| Denetim ve yönetim düzlemi günlüğü ve denetimi| Evet |  |  |
| Veri düzlemi günlüğü ve denetimi| Evet |   |  |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Authentication| Evet | | [Azure Event Hubs erişimi yetkilendirme](authorize-access-event-hubs.md), [Azure Active Directory kullanarak Event Hubs kaynaklarına](authorize-access-azure-active-directory.md)erişimi yetkilendirme, [paylaşılan erişim imzalarını kullanarak Event Hubs kaynaklarına erişimi yetkilendirme](authorize-access-shared-access-signature.md) |
| Authorization|  Evet | | [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md), [Event Hubs kaynaklarına erişmek için Azure Active Directory ile bir uygulamanın kimliğini](authenticate-application.md)doğrulama, [kullanarak Event Hubs kaynaklara erişimi kimlik doğrulama paylaşılan erişim imzaları (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Bekleyen sunucu tarafı şifrelemesi: Microsoft tarafından yönetilen anahtarlar |  Evet | |  |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Hayır |  |  |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Yok | |  |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Evet | |  |
| Şifrelenmiş API çağrıları| Evet |  |  |

## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Yapılandırma yönetimi desteği (yapılandırmanın sürümü oluşturma, vb.)| Evet | |  |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.
