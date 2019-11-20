---
title: 'Azure ExpressRoute: güvenlik denetimleri'
description: Azure ExpressRoute değerlendirme için güvenlik denetimlerinin denetim listesi
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079923"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure ExpressRoute için güvenlik denetimleri

Bu makale, Azure ExpressRoute 'da yerleşik olarak bulunan güvenlik denetimlerini belgeler.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Hizmet uç noktası desteği| Yok |  |
| VNet ekleme desteği| Yok | |
| Ağ yalıtımı ve güvenlik duvarı desteği| Yes | Her müşteri kendi yönlendirme etki alanında bulunur ve kendi VNet 'ine tünel |
| Zorlamalı tünel desteği| Yok | Sınır Ağ Geçidi Protokolü (BGP) üzerinden. |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Yes | Bkz. [ExpressRoute izleme, ölçümler ve uyarılar](expressroute-monitoring-metrics-alerts.md).|
| Denetim ve yönetim düzlemi günlüğü ve denetimi| Yes |  |
| Veri düzlemi günlüğü ve denetimi| Hayır |   |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Kimlik Doğrulaması| Yes | Microsoft için ağ geçidi için hizmet hesabı (GWM) (denetleyici); Geliştirme ve OP için tam zamanında (JıT) erişim. |
| Yetkilendirme|  Yes |Microsoft için ağ geçidi için hizmet hesabı (GWM) (denetleyici); Geliştirme ve OP için tam zamanında (JıT) erişim. |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Rest 'te sunucu tarafı şifreleme: Microsoft tarafından yönetilen anahtarlar |  Yok | ExpressRoute müşteri verilerini depolamaz. |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Yok |  |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Yok | |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Hayır | |
| Şifrelenmiş API çağrıları| Yes | [Azure Resource Manager](../azure-resource-manager/index.yml) ve https üzerinden. |


## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Yapılandırma yönetimi desteği (yapılandırmanın sürümü oluşturma, vb.)| Yes | Ağ kaynak sağlayıcısı (NRP) aracılığıyla. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.