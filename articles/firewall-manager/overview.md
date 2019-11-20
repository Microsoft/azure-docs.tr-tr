---
title: Azure Güvenlik Duvarı Yöneticisi önizlemesi nedir?
description: Azure Güvenlik Duvarı Yöneticisi özellikleri hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: df649eab1f0e2946078f8efd3cdd6ab68c3b7938
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580202"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Azure Güvenlik Duvarı Yöneticisi önizlemesi nedir?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Güvenlik Duvarı Yöneticisi önizlemesi, bulut tabanlı güvenlik perimeters için merkezi güvenlik ilkesi ve rota yönetimi sağlayan bir güvenlik yönetim hizmetidir. Hub ve bağlı bileşen mimarilerini kolayca oluşturmanıza olanak tanıyan, Microsoft tarafından yönetilen bir kaynak olan [Azure sanal WAN hub](../virtual-wan/virtual-wan-about.md#resources)ile birlikte çalışmaktadır. Güvenlik ve yönlendirme ilkeleri böyle bir hub ile ilişkilendirildiğinde, bu, *[güvenli bir sanal hub](secured-virtual-hub.md)* olarak adlandırılır. 

![Güvenlik Duvarı-yönetici](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Azure Güvenlik Duvarı Yöneticisi Önizleme özellikleri

Azure Güvenlik Duvarı Yöneticisi önizlemesi aşağıdaki özellikleri sunar:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Merkezi Azure Güvenlik Duvarı dağıtımı ve yapılandırması

Farklı Azure bölgelerine ve aboneliklerine yayılan birden çok Azure Güvenlik Duvarı örneğini merkezi olarak dağıtabilir ve yapılandırabilirsiniz. 

### <a name="hierarchical-policies-global-and-local"></a>Hiyerarşik ilkeler (genel ve yerel)

Azure Güvenlik Duvarı ilkelerini birden çok güvenli sanal hub genelinde merkezi olarak yönetmek için Azure Güvenlik Duvarı Yöneticisi Önizlemesi ' ni kullanabilirsiniz. Merkezi BT ekipleriniz, ekipler genelinde kurumsal çapta güvenlik duvarı ilkesini zorlamak için küresel güvenlik duvarı ilkelerini yazabilir. Yerel olarak yazılmış güvenlik duvarı ilkeleri, daha iyi çeviklik için bir DevOps self servis modeline izin verir.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Gelişmiş güvenlik için üçüncü taraf hizmet olarak güvenlik ile tümleşik

Azure Güvenlik Duvarı 'na ek olarak, VNet ve şube Internet bağlantılarınız için ek ağ koruması sağlamak üzere üçüncü taraf güvenlik hizmeti (SECaaS) sağlayıcılarını tümleştirebilirsiniz.

- VNet 'Ten Internet 'e (V2I) trafik filtreleme

   - Tercih ettiğiniz üçüncü taraf güvenlik sağlayıcınızla giden sanal ağ trafiğini filtreleyin.
   - Azure üzerinde çalışan bulut iş yükleriniz için Gelişmiş Kullanıcı tarafından uyumlu Internet korumasından yararlanın.

- Internet 'Ten dallan (B2I) trafik filtreleme

   Internet senaryolarına şube için üçüncü taraf filtrelemesini kolayca eklemek üzere Azure bağlantınız ve küresel dağıtım özelliğinden yararlanın.

Güvenilen güvenlik sağlayıcıları hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi güvenilen güvenlik iş ortakları (Önizleme) nedir?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Merkezi yol yönetimi

Bağlı olan sanal ağlarda Kullanıcı tanımlı yolları (UDR) el ile ayarlama gereksinimi olmadan, filtreleme ve günlüğe kaydetme için güvenli hub 'ınıza trafiği kolayca yönlendirin. Şube için üçüncü taraf sağlayıcıları (B2I) trafik filtrelemesini, VNet 'Ten sanal ağa (B2V), VNET 'Ten VNET 'e (V2V) ve VNET 'Ten Internet 'e (V2I) yönelik Azure Güvenlik Duvarı ile yan yana kullanabilirsiniz. V2I trafik filtreleme için üçüncü taraf sağlayıcıları, B2V veya V2V için Azure Güvenlik Duvarı gerekmediği sürece de kullanabilirsiniz. 

## <a name="region-availability"></a>Bölge kullanılabilirliği

Genel önizleme için aşağıdaki bölgeler desteklenir:

- Batı Avrupa, Kuzey Avrupa, Fransa Orta, Fransa Güney, UK Güney, UK Batı
- Avustralya Doğu, Avustralya Orta, Avustralya Orta 2, Avustralya Güneydoğu
- Kanada Orta
- Doğu ABD, Batı ABD, Doğu ABD 2, Orta Güney ABD, Batı ABD 2, Orta ABD, Orta Kuzey ABD, Orta Batı ABD

Azure Güvenlik Duvarı Ilkeleri yalnızca bu bölgelerde oluşturulabilir, ancak bölgeler arasında kullanılabilirler. Örneğin, Batı ABD bir ilke oluşturabilir ve Doğu ABD ' de kullanabilirsiniz. 

## <a name="known-issues"></a>Bilinen sorunlar

Azure Güvenlik Duvarı Yöneticisi önizlemesi aşağıdaki bilinen sorunlara sahiptir:

|Sorun  |Açıklama  |Risk azaltma  |
|---------|---------|---------|
|El ile oluşturulan Merkezi sanal ağlar desteklenmez|Şu anda Azure Güvenlik Duvarı Yöneticisi, sanal hub 'larla oluşturulan ağları destekler. Kendi el ile oluşturulmuş hub VNet 'in kullanılması henüz desteklenmiyor.|Şimdilik, Azure Güvenlik Duvarı Yöneticisi 'Ni sanal hub 'larla oluşturulan hub ve bağlı bileşen ağları ile birlikte kullanın.<br>Şu anda araştırma.
|Üçüncü taraf filtreleme sınırlamaları|V2I Azure Firewall B2V ve V2V ile üçüncü taraf sağlayıcılarla trafik filtrelemesi desteklenmez.|Şu anda araştırma.|
|Trafik bölme şu anda desteklenmiyor|Office 365 ve Azure genel PaaS trafik bölünmesi Şu anda desteklenmiyor. Bu nedenle, V2I veya B2I için bir üçüncü taraf sağlayıcı seçilirse ortak hizmet aracılığıyla tüm Azure genel PaaS ve Office 365 trafiği de gönderilir.|Şu anda hub 'da trafik bölmeyi araştırıyoruz.
|Bölge başına bir hub|Bölge başına birden fazla hub 'ınız olamaz|Bir bölgede birden çok sanal WAN oluşturun.|
|Temel ilkeler yerel ilkeyle aynı bölgede olmalıdır|Tüm yerel ilkelerinizi temel ilkeyle aynı bölgede oluşturun. Başka bir bölgeden güvenli bir hub 'daki tek bir bölgede oluşturulmuş bir ilkeyi yine de uygulayabilirsiniz.|Şu anda araştırma.|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Yöneticisi önizleme dağıtımına genel bakış](deployment-overview.md)
- [Güvenli sanal hub 'lar](secured-virtual-hub.md)hakkında bilgi edinin.