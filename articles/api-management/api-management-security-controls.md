---
title: Azure API Management için güvenlik denetimleri
description: API Management değerlendirmek için güvenlik denetimlerinin denetim listesi
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 7f5fe404c93b7db22444b9dad97a0d3474c33a16
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257081"
---
# <a name="security-controls-for-api-management"></a>API Management için güvenlik denetimleri

Bu makalede, API Management yerleşik olarak bulunan güvenlik denetimleri Belgelenebilir.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Hizmet uç noktası desteği| Hayır | |  |
| VNet ekleme desteği| Evet | |  |
| Ağ yalıtımı ve güvenlik duvarı desteği| Evet | Sırasıyla ağ güvenlik grupları (NSG) ve Azure Application Gateway (veya diğer yazılım gereci) kullanma. |  |
| Zorlamalı tünel desteği| Evet | Azure ağı Zorlamalı tünel sağlar. |  |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Evet | | |
| Denetim ve yönetim düzlemi günlüğü ve denetimi| Evet | [Azure Izleyici etkinlik günlükleri](../azure-monitor/platform/activity-logs-overview.md) | |
| Veri düzlemi günlüğü ve denetimi| Evet | [Azure izleyici tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md) ve (isteğe bağlı olarak) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Authentication| Evet | |  |
| Authorization| Evet | |  |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar | Belgeler |
|---|---|--|--|
| Bekleyen sunucu tarafı şifrelemesi: Microsoft tarafından yönetilen anahtarlar | Evet | Sertifikalar, anahtarlar ve gizli-adlandırılmış değerler gibi hassas veriler hizmet tarafından yönetilen hizmet örneği anahtarları ile şifrelenir. |  |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Hayır | Tüm şifreleme anahtarları hizmet örneği başına alınır ve hizmet yönetilir. |  |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Yok | |  |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Evet | [Express Route](../expressroute/index.yml) ve VNET şifrelemesi, [Azure ağı](../virtual-network/index.yml)tarafından sağlanır. |  |
| Şifrelenmiş API çağrıları| Evet | Yönetim düzlemi çağrıları TLS üzerinden [Azure Resource Manager](../azure-resource-manager/index.yml) üzerinden yapılır. Geçerli bir JSON Web belirteci (JWT) gerekiyor.  Veri düzlemi çağrıları TLS ve desteklenen kimlik doğrulama mekanizmalarından biri (örneğin, istemci sertifikası veya JWT) ile güvenli hale getirilmiş olabilir. |   |
 |

## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar| Belgeler |
|---|---|--|--|
| Yapılandırma yönetimi desteği (yapılandırmanın sürümü oluşturma, vb.)| Evet | [Azure API Management DevOps kaynak setini](https://aka.ms/apimdevops) kullanma |  |

## <a name="vulnerability-scans-false-positives"></a>Güvenlik açığı yanlış pozitifleri tarar

Bu bölüm, Azure API Management etkilemeyen yaygın güvenlik açıklarını belgelemektedir.

| Güvenlik açığı               | Açıklama                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bilet taşma payı (CVE-2016-9244) | Bilet taşma payı, bazı F5 ürünlerinde bulunan TLS Sessionbilet uzantısının uygulanmasında güvenlik açığıdır. Başlatılmamış bellekten 31 baytlık verilerin sızıntısını ("bleden") sağlar. Bunun nedeni, istemciden geçirilen bir oturum kimliği olan bir oturum kimliğini doldurmasından kaynaklanır ve bu da BT 32 bit uzunluğunda hale gelir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.