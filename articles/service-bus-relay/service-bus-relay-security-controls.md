---
title: Azure Service Bus geçişi için güvenlik denetimleri
description: Azure Service Bus geçişini değerlendirmek için güvenlik denetimlerinin denetim listesi
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219975"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure Service Bus geçişi için güvenlik denetimleri

Bu makale Azure Service Bus geçişine yerleşik olarak bulunan güvenlik denetimlerini belgeler.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Hizmet uç noktası desteği| Hayır |  |   |
| Ağ yalıtımı ve güvenlik duvarı desteği| Hayır |  |   |
| Zorlamalı tünel desteği| Yok | Geçiş, TLS tünelidir  |   |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Evet | |   |
| Denetim ve yönetim düzlemi günlüğü ve denetimi| Evet | [Azure Resource Manager](../azure-resource-manager/index.yml)üzerinden. |   |
| Veri düzlemi günlüğü ve denetimi| Evet | Bağlantı başarısı/hatası ve hataları ve günlüğe kaydedildi.  |   |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Authentication| Evet | SAS aracılığıyla. | [Azure Relay kimlik doğrulaması ve yetkilendirme](relay-authentication-and-authorization.md) |
| Authorization|  Evet | SAS aracılığıyla. | [Azure Relay kimlik doğrulaması ve yetkilendirme](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Bekleyen sunucu tarafı şifrelemesi: Microsoft tarafından yönetilen anahtarlar |  Yok | Geçiş bir Web soketi ve verileri kalıcı yapmaz. |   |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Hayır | Yalnızca Microsoft TLS sertifikalarını kullanır.  |   |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Yok | |   |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Evet | Hizmet için TLS gerekiyor. |   |
| Şifrelenmiş API çağrıları| Evet | 'DİR. |


## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Yapılandırma yönetimi desteği (yapılandırmanın sürümü oluşturma, vb.)| Evet | [Azure Resource Manager](../azure-resource-manager/index.yml)üzerinden.|   |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.