---
title: Azure Cosmos DB için güvenlik denetimleri
description: Azure Cosmos DB değerlendirmek için güvenlik denetimlerinin denetim listesi
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886784"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Azure Cosmos DB için güvenlik denetimleri

Bu makalede, Azure Cosmos DB yerleşik olarak bulunan güvenlik denetimleri Belgelenebilir.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Ağ

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Hizmet uç noktası desteği| Evet |  |
| VNet ekleme desteği| Evet | VNet hizmet uç noktası ile, bir Azure Cosmos DB hesabını yalnızca bir sanal ağın (VNet) belirli bir alt ağından erişime izin verecek şekilde yapılandırabilirsiniz. Ayrıca, VNet erişimini güvenlik duvarı kurallarıyla birleştirebilirsiniz.  Bkz. [sanal ağlardan erişim Azure Cosmos DB](VNet-service-endpoint.md). |
| Ağ yalıtımı ve güvenlik duvarı desteği| Evet | Güvenlik Duvarı desteğiyle, Azure Cosmos hesabınızı yalnızca onaylanan bir IP adresi kümesinden, IP adresi aralığına ve/veya bulut hizmetlerinden erişime izin verecek şekilde yapılandırabilirsiniz. Bkz. [Azure Cosmos DB IP güvenlik duvarını yapılandırma](how-to-configure-firewall.md).|
| Zorlamalı tünel desteği| Evet | , Sanal makinelerin bulunduğu VNet üzerinde istemci tarafında yapılandırılabilir.   |

## <a name="monitoring--logging"></a>& Günlüğü izleme

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Azure izleme desteği (Log Analytics, App Insights vb.)| Evet | Azure Cosmos DB gönderilen tüm istekler günlüğe kaydedilir. Azure [izleme](../azure-monitor/overview.md), Azure ölçümleri, Azure denetim günlüğü desteklenir.  Veri düzlemi isteklerine karşılık gelen bilgileri, sorgu çalışma zamanı istatistiklerini, sorgu metnini, MongoDB isteklerini günlüğe kaydedebilirsiniz. Ayrıca, uyarıları da ayarlayabilirsiniz. |
| Denetim ve yönetim düzlemi günlüğü ve denetimi| Evet | Güvenlik duvarları, VNET 'ler, anahtar erişimi ve ıAM gibi hesap düzeyindeki işlemler için Azure etkinlik günlüğü. |
| Veri düzlemi günlüğü ve denetimi | Evet | Kapsayıcı oluşturma, sağlama işleme, dizin oluşturma ilkeleri ve belgelerde CRUD işlemleri gibi kapsayıcı düzeyindeki işlemler için Tanılama izleme günlüğü. |

## <a name="identity"></a>Kimlik

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Authentication| Evet | Evet, veritabanı hesabı düzeyinde; veri düzlemi düzeyinde Cosmos DB, kaynak belirteçlerini ve anahtar erişimini kullanır. |
| Authorization| Evet | Azure Cosmos hesabında ana anahtarlar (birincil ve ikincil) ve kaynak belirteçleri ile desteklenir. Ana anahtarlarla verilere okuma/yazma veya salt okuma erişimi alabilirsiniz. Kaynak belirteçleri, belgeler ve kapsayıcılar gibi kaynaklara sınırlı zamanlı erişime izin verir. |

## <a name="data-protection"></a>Veri koruma

| Güvenlik denetimi | Evet/Hayır | Notlar |
|---|---|--|
| Bekleyen sunucu tarafı şifrelemesi: Microsoft tarafından yönetilen anahtarlar | Evet | Tüm Cosmos veritabanları ve yedeklemeleri varsayılan olarak şifrelenir; [Azure Cosmos DB veri şifrelemeyi](database-encryption-at-rest.md)inceleyin. Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme desteklenmez. |
| Bekleyen sunucu tarafı şifreleme: müşteri tarafından yönetilen anahtarlar (BYOK) | Hayır |  |
| Sütun düzeyinde şifreleme (Azure veri Hizmetleri)| Evet | Yalnızca API Premium tablolarında. Tüm API 'Ler bu özelliği desteklemez. Bkz [. Azure Cosmos DB giriş: Tablo API'si](table-introduction.md). |
| Aktarım sırasında şifreleme (ExpressRoute şifrelemesi, VNet şifreleme ve VNet-VNet şifreleme gibi)| Evet | Tüm Azure Cosmos DB verileri aktarım sırasında şifrelenir. |
| Şifrelenmiş API çağrıları| Evet | Azure Cosmos DB tüm bağlantıları HTTPS 'yi destekler. Azure Cosmos DB TLS 1,2 bağlantılarını da destekler, ancak bu henüz zorlanmaz. Müşteriler, son ucunda daha düşük düzey TLS 'yi devre dışı bırakır. Cosmos DB bağlandıklarından emin olabilirler.  |

## <a name="configuration-management"></a>Yapılandırma yönetimi

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Yapılandırma yönetimi desteği (yapılandırmanın sürümü oluşturma, vb.)| Hayır  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Cosmos DB için ek güvenlik denetimleri

| Güvenlik denetimi | Evet/Hayır | Notlar|
|---|---|--|
| Çapraz kaynak kaynak paylaşımı (CORS) | Evet | Bkz. [çıkış noktaları arası kaynak paylaşımı 'nı (CORS) yapılandırma](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure hizmetleri genelinde yerleşik güvenlik denetimleri](../security/fundamentals/security-controls.md)hakkında daha fazla bilgi edinin.