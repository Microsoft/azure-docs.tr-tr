---
title: Güvenlik denetimleri
description: Azure SQL veritabanı 'nı değerlendirmek için güvenlik denetimlerinin denetim listesi
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 58070cab1221b9d9585784d82cf1a48afcedb8af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802838"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL veritabanı için güvenlik denetimleri

Bu makale, Azure SQL veritabanı 'nda yerleşik olarak bulunan güvenlik denetimlerini belgeler.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL veritabanı hem [tek veritabanı](sql-database-single-index.yml) hem de [yönetilen örnek](sql-database-managed-instance.md)içerir. Aşağıdaki girişler, aksi belirtilmedikçe her iki teklife de uygulanır.

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Hizmet uç noktası desteği| Evet | Yalnızca [tek veritabanı](sql-database-single-index.yml) için geçerlidir. |
| Azure sanal ağ ekleme desteği| Evet | Yalnızca [yönetilen örnek](sql-database-managed-instance.md) için geçerlidir. |
| Ağ yalıtımı ve güvenlik duvarı desteği| Evet | Hem veritabanı düzeyinde hem de sunucu düzeyinde güvenlik duvarı. Ağ yalıtımı yalnızca [yönetilen örnek](sql-database-managed-instance.md) içindir. |
| Zorlamalı tünel desteği| Evet | [ExpressRoute](../expressroute/index.yml) VPN aracılığıyla [yönetilen örnek](sql-database-managed-instance.md) . |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Log Analytics veya Application Insights gibi Azure izleme desteği| Evet | EMVA 'dan SıEM çözümü olan SecureSphere, [SQL denetimi](sql-database-auditing.md)aracılığıyla [Azure Event Hubs](../event-hubs/index.yml) tümleştirme aracılığıyla da desteklenir. |
| Denetim düzlemi ve yönetimi-düzlem günlüğü ve denetimi| Evet | Yalnızca bazı olaylar için Evet |
| Veri düzlemi günlüğü ve denetimi | Evet | [SQL denetimi](sql-database-auditing.md) aracılığıyla |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Kimlik Doğrulaması| Evet | Azure Active Directory (Azure AD) |
| Yetkilendirme| Evet | None |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Rest 'te sunucu tarafı şifreleme: Microsoft tarafından yönetilen anahtarlar | Evet | [Always Encrypted](sql-database-always-encrypted.md)makalesinde açıklandığı gibi "kullanımda olan şifreleme" olarak adlandırılır. Sunucu tarafı şifreleme, [Saydam veri şifrelemesi](transparent-data-encryption-azure-sql.md)kullanır.|
| Aktarım sırasında şifreleme:<ul><li>Azure ExpressRoute şifrelemesi</li><li>Bir sanal ağda şifreleme</li><li>Sanal ağlar arasında şifreleme</ul>| Evet | HTTPS kullanma. |
| CMK veya BYOK gibi şifreleme anahtarı işleme| Evet | Hem hizmet tarafından yönetilen hem de müşteri tarafından yönetilen anahtar işleme sunulur. İkincisi [Azure Key Vault](../key-vault/index.yml)aracılığıyla sunulur. |
| Azure veri Hizmetleri tarafından sunulan sütun düzeyinde şifreleme| Evet | [Always Encrypted](sql-database-always-encrypted.md)üzerinden. |
| Şifrelenmiş API çağrıları| Evet | HTTPS/SSL kullanma. |

## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Yapılandırma sürümü oluşturma gibi yapılandırma yönetimi desteği| Hayır  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL veritabanı için ek güvenlik denetimleri

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Koruyucu: güvenlik açığı değerlendirmesi | Evet | Bkz. [SQL güvenlik açığı değerlendirmesi hizmeti, veritabanı güvenlik açıklarını belirlemenize yardımcı olur](sql-vulnerability-assessment.md). |
| Koruyucu: veri bulma ve sınıflandırma  | Evet | Bkz. [Azure SQL veritabanı ve SQL veri ambarı veri bulma & sınıflandırması](sql-database-data-discovery-and-classification.md). |
| Algılama: tehdit algılama | Evet | Bkz. [Azure SQL veritabanı Için Gelişmiş tehdit koruması](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.
