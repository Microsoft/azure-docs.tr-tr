---
title: Azure ağ mimarisi belgeleri
description: Azure ağ hizmetleri için sunulan başvuru mimarisi belgeleri hakkında bilgi edinin.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484804"
---
# <a name="azure-networking-architecture-documentation"></a>Azure ağ mimarisi belgeleri

Bu makalede, Azure 'da uygulamalarınızı oluşturmak için kullanabileceğiniz farklı ağ hizmetlerini keşfetmenize yardımcı olabilecek mimari kılavuzlarıyla ilgili bilgiler sağlanmaktadır.

## <a name="networking-overview"></a>Ağlara genel bakış

Aşağıdaki tabloda, Azure 'daki bir sanal veri merkezine ve hub ve bağlı bileşen topolojisine yönelik bir ağa genel bakış sağlayan makaleler yer almaktadır.

|Başlık |Açıklama  |
|---------|---------|
|[Sanal Veri Merkezleri](/azure/architecture/vdc/networking-virtual-datacenter)   | Azure 'da bir sanal veri merkezinin ağ perspektifini sağlar.       |
|[Merkez-uç topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Abonelik sınırları ve birden çok hub hakkında bilgi ile birlikte Azure 'daki hub ve bağlı bileşen ağ topolojisine genel bir bakış sağlar.          |

## <a name="connect-to-azure-resources"></a>Azure kaynaklarına bağlanma

Aşağıdaki tabloda Azure kaynakları arasında bağlantı sağlayan Azure Ağ Hizmetleri, şirket içi bir ağdan Azure kaynaklarına bağlantı ve Azure 'daki şube bağlantısına dallanma hakkında makaleler yer almaktadır.

|Başlık |Açıklama  |
|---------|---------|
|[Eşlenen sanal ağlara IP adresi alanları ekleme](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Eşlenmiş sanal ağlara IP adresi alanları eklemeye yardımcı olan betikler sağlar.        |
|[Azure Ağ Bağdaştırıcısı’nı kullanarak tek başına sunucuları bağlama](/azure/architecture/hybrid/azure-network-adapter)   | Windows Yönetim Merkezi aracılığıyla dağıttığınız Azure ağ bağdaştırıcısını kullanarak, şirket içi tek başına sunucunun Microsoft Azure sanal ağlara nasıl bağlanacağını gösterir.        |
|[Sanal ağ eşlemesi ve VPN ağ geçitleri arasında seçim yapma](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Azure 'daki sanal ağları bağlamak için iki yolu karşılaştırır: sanal ağ eşlemesi ve VPN ağ geçitleri.        |
|[Bir şirket içi ağı Azure’a bağlamaBir şirket içi ağı Azure’a bağlama](/azure/architecture/reference-architectures/hybrid-networking/)  | Şirket içi bir ağı bir Azure sanal ağına (VNet) bağlama seçeneklerini karşılaştırır. Her seçenek için, daha ayrıntılı bir başvuru mimarisi sağlanır.        |
|[SD-Azure sanal WAN ile WAN bağlantısı mimarisi](../../virtual-wan/sd-wan-connectivity-architecture.md)|Azure sanal WAN ile özel bir yazılım tanımlı WAN (SD-WAN) bağlama için farklı bağlantı seçeneklerini açıklar.|

## <a name="deploy-highly-available-applications"></a>Yüksek oranda kullanılabilir uygulamalar dağıtma

Aşağıdaki tablo, Azure ağ hizmetleri 'nin bir birleşimini kullanarak uygulamalarınızın yüksek kullanılabilirlik için nasıl dağıtılacağını anlatan makaleler içerir.

|Başlık |Açıklama  |
|---------|---------|
|[Çok bölgeli N katmanlı uygulama](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Gelen istekleri birincil bölgeye yönlendirmek için Traffic Manager kullanan çok bölgeli N katmanlı bir uygulamayı açıklar ve bu bölge kullanılamaz duruma gelirse, Traffic Manager ikincil bölgeye yük devreder.      |
| [Azure'da çok kiracılı SaaS](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   , Ön kapı ve Application Gateway birleşimini içeren bir çok kiracılı çözüm kullanır.  Ön kapı, bölgeler arasında trafiğin dengelenmesi ve yolların Application Gateway yollarla Şirket içindeki trafiği, istemci iş ihtiyaçlarını karşılayan çeşitli hizmetlere yük dengelemeye yardımcı olur.  |
| [Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için oluşturulmuş çok katmanlı Web uygulaması ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için derlenmiş dayanıklı çok katmanlı uygulamalar dağıtır. Birincil bölge kullanılamaz duruma gelirse, Traffic Manager ikincil bölgeye devreder.  |
|[IaaS: ilişkisel veritabanıyla Web uygulaması](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Bir hizmet olarak altyapı (IaaS) Web uygulaması ve SQL Server veritabanı barındırmak için yüksek oranda kullanılabilir bir mimari sağlamak üzere birden çok bölge arasında yayılan kaynakların nasıl kullanılacağını açıklar.     |
|[Düşük maliyetli sunucusuz Azure hizmetlerini kullanarak gerçek zamanlı konum paylaşımı](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   , Uygulamalarınızın tek bir bölgeye dağıtılmasından daha yüksek kullanılabilirlik sağlamak için Azure ön kapısını kullanır. Birinci bölge bölgesel bir kesintiden etkileniyorsa, ikinci bölgeye yük devretmek için Front Door kullanabilirsiniz.      |
|[Yüksek oranda kullanılabilir ağ sanal gereçleri](/azure/architecture/reference-architectures/dmz/nva-ha)     | Azure 'da yüksek kullanılabilirlik için bir dizi ağ sanal gereçlerini (NVA 'lar) nasıl dağıtacağınızı gösterir.        |
|[Traffic Manager ve Application Gateway ile çok bölgeli Yük Dengeleme](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Kullanılabilirlik ve sağlam olağanüstü durum kurtarma altyapısına ulaşmak için birden fazla Azure bölgesinde dayanıklı çok katmanlı uygulamaların nasıl dağıtılacağını açıklar.        |

## <a name="secure-your-network-resources"></a>Ağ kaynaklarınızın güvenliğini sağlama

Aşağıdaki tabloda, Azure ağ hizmetleri 'ni kullanarak ağ kaynaklarınızı nasıl koruyabileceğiniz makaleler yer almaktadır.

|Başlık |Açıklama  |
|---------|---------|
|[Ağ güvenliği için en iyi uygulamalar](../../security/fundamentals/network-best-practices.md) |Ağ güvenlerinizi iyileştirmek için en iyi Azure uygulamaları koleksiyonunu açıklar.         |
[Azure Güvenlik Duvarı Mimari Kılavuzu](/azure/architecture/example-scenario/firewalls/) | , Üçüncü taraf sanal gereçler kullanarak Azure 'da yüksek oranda kullanılabilir güvenlik duvarları tasarlamak için yapılandırılmış bir yaklaşım sağlar.        |
|[Güvenli hibrit ağ uygulama](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Şirket içi ağ ile Azure sanal ağı arasında çevre ağı olarak da adlandırılan bir DMZ uygulayan bir mimari tanımlar. Tüm gelen ve giden trafik Azure güvenlik duvarından geçer.        |
|[Ağ düzeyinde bölümlemeye sahip iş yüklerinin güvenliğini sağlama ve bu iş yüklerini yönetme](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Azure 'da iş yüklerini bir ağ perspektifinden düzenlemek için kullanılan üç ortak deseni açıklar.   Bu desenlerin her biri farklı bir yalıtım ve bağlantı türü sağlar.      |
|[Sanal ağlar için Güvenlik Duvarı ve Uygulama Ağ Geçidi](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Azure Güvenlik Duvarı ve Azure Application Gateway gibi Azure sanal ağ güvenlik hizmetlerini, her bir hizmetin ne zaman kullanılacağını ve her ikisini de birleştiren ağ tasarımı seçeneklerini açıklar.      |

## <a name="next-steps"></a>Sonraki adımlar

[Azure sanal ağ](../../virtual-network/virtual-networks-overview.md)hakkında bilgi edinin.
