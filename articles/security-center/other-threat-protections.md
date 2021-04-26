---
title: Azure Güvenlik Merkezi 'nde ek tehdit koruması
description: Azure Defender 'ın ötesinde Azure Güvenlik Merkezi 'nden kullanılabilir tehdit koruması hakkında bilgi edinin
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754343"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde ek tehdit korumaları
Azure Güvenlik Merkezi, yerleşik [Azure Defender korumalarının](azure-defender.md)yanı sıra aşağıdaki tehdit koruması yeteneklerini de sunmaktadır.

> [!TIP]
> Güvenlik Merkezi 'nin tehdit koruması yeteneklerini etkinleştirmek için, ilgili iş yüklerini içeren abonelikte Azure Defender 'ı etkinleştirmeniz gerekir.
>
> **MariaDB/MySQL/PostgreSQL Için Azure veritabanı** için tehdit korumasını yalnızca kaynak düzeyinde etkinleştirebilirsiniz.


## <a name="threat-protection-for-azure-network-layer"></a>Azure ağ katmanı için tehdit koruması <a name="network-layer"></a>
Güvenlik Merkezi ağ katmanı analizi, Azure çekirdek yönlendiricileri tarafından toplanan paket üstbilgileri olan örnek [ıpdüzeltme verilerini](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)temel alır. Güvenlik Merkezi, bu veri akışına bağlı olarak kötü amaçlı trafik etkinliklerini belirlemek ve işaretlemek için makine öğrenimi modellerini kullanır. Ayrıca, Güvenlik Merkezi, IP adreslerini zenginleştirmek için Microsoft Threat Intelligence veritabanını kullanır.

Bazı ağ yapılandırmalarında, güvenlik merkezi 'nin şüpheli ağ etkinliğinde uyarı üretmesini kısıtlıyor. Güvenlik Merkezi 'nin ağ uyarıları oluşturması için şunları doğrulayın:
- Sanal makineniz genel bir IP adresine sahiptir (veya genel IP adresine sahip bir yük dengeleyicide bulunur).
- Sanal makinenizin ağ çıkış trafiği bir dış KIMLIK çözümü tarafından engellenmiyor.

Azure ağ katmanı uyarılarının bir listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-azurenetlayer)bakın.


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Azure Resource Manager için tehdit koruması (Önizleme)<a name ="management-layer"></a>
Güvenlik Merkezi 'nin Azure Resource Manager tabanlı koruma katmanı Şu anda önizleme aşamasındadır.

Güvenlik Merkezi, Azure için denetim düzlemi olarak kabul edilen Azure Resource Manager olaylarını kullanarak ek bir koruma katmanı sunar. Güvenlik Merkezi, Azure Resource Manager kayıtlarını analiz ederek Azure abonelik ortamında olağandışı veya potansiyel olarak zararlı işlemleri algılar.

Azure Defender Kaynak Yöneticisi uyarıları listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-resourcemanager)bakın.


>[!NOTE]
> Yukarıdaki analizler Microsoft Cloud App Security tarafından desteklenmektedir. Bu analizlerden yararlanmak için bir Cloud App Security lisansını etkinleştirmeniz gerekir. Cloud App Security lisansınız varsa, bu uyarılar varsayılan olarak etkinleştirilir. Uyarıları devre dışı bırakmak için:
>
> 1. Güvenlik Merkezi 'nin menüsünde **fiyatlandırma & ayarları**' nı seçin.
> 1. Değiştirmek istediğiniz aboneliği seçin.
> 1. **Tehdit algılamayı** seçin.
> 1. **Verilerinize erişmek için Microsoft Cloud App Security Izin ver**' i temizleyin ve **Kaydet**' i seçin.


>[!NOTE]
>Güvenlik Merkezi, güvenlikle ilgili müşteri verilerini kaynakla aynı coğrafi bölgede depolar. Microsoft, kaynak coğrafi bölgede henüz Güvenlik Merkezi dağıtmamışsa, verileri Birleşik Devletler depolar. Cloud App Security etkinleştirildiğinde, bu bilgiler Cloud App Security coğrafi konum kurallarına uygun olarak depolanır. Daha fazla bilgi için bkz. [bölgesel olmayan hizmetler Için veri depolama](https://azuredatacentermap.azurewebsites.net/).

1. Aracıyı yüklemekte olduğunuz çalışma alanını ayarlayın. Çalışma alanının, güvenlik merkezi 'nde kullandığınız abonelikte olduğundan ve çalışma alanında okuma/yazma izinlerine sahip olduğunuzdan emin olun.

1. **Azure Defender**'ı etkinleştirin ve **Kaydet**' i seçin.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB için tehdit koruması (Önizleme)<a name="cosmos-db"></a>

Azure Cosmos DB uyarıları, Azure Cosmos DB hesaplarına erişmek veya bu hesaplara yararlanmaya yönelik olağan dışı ve potansiyel olarak zararlı denemelere göre oluşturulur.

Daha fazla bilgi için bkz.

* [Azure Cosmos DB için Gelişmiş tehdit koruması (Önizleme)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB için tehdit koruması uyarıları listesi (Önizleme)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Diğer Microsoft Hizmetleri için tehdit koruması <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF için tehdit koruması <a name="azure-waf"></a>

Azure Application Gateway, web uygulamalarınız için açıklardan yararlanmaya ve güvenlik açıklarına karşı merkezi koruma sağlayan bir web uygulaması güvenlik duvarı (WAF) sunar.

Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. WAF Application Gateway, açık Web uygulaması güvenlik projesinden 3,0 veya 2.2.9 temel kural kümesini temel alır. WAF, yeni güvenlik açıklarına karşı korumak için otomatik olarak güncelleştirilir. 

Azure WAF lisansına sahipseniz, WAF uyarılarınız ek bir yapılandırma gerekmeden Güvenlik Merkezi 'ne akışla kaydedilir. WAF tarafından oluşturulan uyarılar hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı Ayrıntılar kural grupları ve kuralları](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS koruması için tehdit koruması <a name="azure-ddos"></a>

Dağıtılmış hizmet reddi (DDoS) saldırılarına kolayca çalıştırılabilir. Özellikle de uygulamalarınızı buluta taşıyorsanız harika bir güvenlik sorunu haline gelmiştir. 

DDoS saldırısı, uygulamanın kaynaklarını tüketmeye çalışır ve uygulamayı meşru kullanıcılar için kullanılamaz hale getirir. DDoS saldırıları Internet üzerinden erişilebilecek tüm uç noktaları hedefleyebilir.

DDoS saldırılarına karşı savunmak için, Azure DDoS koruması için bir lisans satın alın ve en iyi uygulama tasarımı uygulamalarından olduğunuzdan emin olun. DDoS koruması farklı hizmet katmanları sağlar. Daha fazla bilgi için bkz. [Azure DDoS korumasına genel bakış](../ddos-protection/ddos-protection-overview.md).

Azure DDoS koruması uyarılarının bir listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-azureddos)bakın.


## <a name="next-steps"></a>Sonraki adımlar
Bu tehdit koruması özelliklerinden güvenlik uyarıları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Tüm Azure Güvenlik Merkezi uyarıları için başvuru tablosu](alerts-reference.md)
* [Azure Güvenlik Merkezi'nde güvenlik uyarıları](security-center-alerts-overview.md)
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)
* [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)