---
title: App Service Ortamı v1 'ye giriş-Azure
description: Tüm uygulamalarınızı çalıştırmak için güvenli, VNet 'e katılmış ve ayrılmış ölçek birimleri sağlayan App Service Ortamı v1 özelliği hakkında bilgi edinin.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 32450d0c5fbb5599b286921b9653ae68faf40ecf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070148"
---
# <a name="introduction-to-app-service-environment-v1"></a>App Service Ortamı v1 'ye giriş

> [!NOTE]
> Bu makale App Service Ortamı v1 ile ilgilidir.  Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.

## <a name="overview"></a>Genel Bakış

App Service Ortamı, Web Apps, Mobile Apps ve API Apps dahil olmak üzere yüksek ölçekte Azure App Service uygulamaları güvenli bir şekilde çalıştırmak için tam olarak yalıtılmış ve ayrılmış bir ortam sağlayan [Azure App Service](../overview.md) [Premium][PremiumTier] hizmet planı seçeneğidir.  

App Service ortamlar, gereken uygulama iş yükleri için idealdir:

* Çok yüksek ölçekli
* Yalıtım ve güvenli ağ erişimi

Müşteriler tek bir Azure bölgesi içinde ve birden çok Azure bölgesinde birden çok App Service ortamı oluşturabilir.  Bu, yüksek RPS iş yüklerini desteklemek üzere durum açısından daha az uygulama katmanlarını yatay olarak ölçeklendirmeye yönelik App Service ortamlarını ideal hale getirir.

App Service ortamlar yalnızca tek bir müşterinin uygulamalarını çalıştırmak üzere yalıtılmıştır ve her zaman bir sanal ağa dağıtılır.  Müşteriler hem gelen hem de giden uygulama ağ trafiği üzerinde ayrıntılı denetime sahiptir ve uygulamalar, sanal ağlar üzerinde şirket içi kurumsal kaynaklara yüksek hızlı güvenli bağlantılar kurabilir.

App Service ortamların yüksek ölçekli ve güvenli ağ erişimini etkinleştirme konusunda genel bir bakış için, App Service ortamlarında [AzureCon derin][AzureConDeepDive] inceleyin!

Birden çok App Service ortamı kullanarak yatay ölçeklendirmeyle ilgili ayrıntılı bilgi için [coğrafi olarak dağıtılmış uygulama parmak izini][GeodistributedAppFootprint]ayarlama makalesine bakın.

AzureCon derin ve içinde gösterilen güvenlik mimarisinin nasıl yapılandırıldığını görmek için App Service ortamları ile [katmanlı güvenlik mimarisi](app-service-app-service-environment-layered-security.md) uygulama makalesine bakın.

App Service ortamlarında çalışan uygulamalar, Web uygulaması güvenlik duvarları (WAF) gibi yukarı akış cihazları tarafından geçitli erişime sahip olabilir.  [App Service ortamları için WAF yapılandırma](app-service-app-service-environment-web-application-firewall.md) makalesinde bu senaryo ele alınmaktadır.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Adanmış Işlem kaynakları

Bir App Service Ortamı tüm işlem kaynakları yalnızca tek bir aboneliğe ayrılmıştır ve bir App Service Ortamı tek bir uygulama tarafından özel kullanım için 50 (50) bilgi işlem kaynaklarıyla yapılandırılabilir.

Bir App Service Ortamı, bir ön uç bilgi işlem kaynak havuzundan ve bunlardan biri üç çalışan işlem kaynak havuzlarından oluşur.

Ön uç havuzu, SSL sonlandırmasından sorumlu işlem kaynaklarını ve bir App Service Ortamı içindeki uygulama isteklerinin otomatik yük dengelemesini içerir.

Her çalışan havuzu [App Service planlarına][AppServicePlan]ayrılan işlem kaynaklarını içerir, bu da bir veya daha fazla Azure App Service uygulaması içerir.  App Service Ortamı en fazla üç farklı çalışan havuzu kullanılabilir olduğundan, her bir çalışan havuzu için farklı işlem kaynakları seçme esnekliği vardır.  

Örneğin, bu, geliştirme veya test uygulamaları için tasarlanan App Service planları için daha az güçlü işlem kaynaklarıyla bir çalışan havuzu oluşturmanızı sağlar.  İkinci (veya hatta üçüncü) çalışan havuzu, üretim uygulamaları çalıştıran App Service planlarına yönelik daha güçlü işlem kaynakları kullanabilir.

Ön uç ve çalışan havuzlarının kullanabileceği işlem kaynakları miktarı hakkında daha fazla bilgi için bkz. [nasıl yapılandırılır App Service ortamı][HowToConfigureanAppServiceEnvironment].  

App Service Ortamı desteklenen kullanılabilir işlem kaynağı boyutlarına ilişkin ayrıntılar için [App Service fiyatlandırma][AppServicePricing] sayfasına bakın ve Premium fiyatlandırma katmanındaki App Service ortamları için kullanılabilir seçenekleri gözden geçirin.

## <a name="virtual-network-support"></a>Sanal ağ desteği

App Service Ortamı bir Azure Resource Manager Sanal ağda **veya** klasik dağıtım modeli sanal ağında ([sanal ağlarda daha fazla bilgi][MoreInfoOnVirtualNetworks]) oluşturulabilir.  Bir sanal ağda App Service Ortamı her zaman ve bir sanal ağın alt ağı içinde daha kesin bir şekilde bulunduğundan, hem gelen hem de giden ağ iletişimini denetlemek için sanal ağların güvenlik özelliklerinden yararlanabilirsiniz.  

Bir App Service Ortamı, bir genel IP adresi veya yalnızca bir Azure Iç Load Balancer (ıLB) adresiyle birlikte sunulan bir Internet ile karşılıklı olabilir.

[Ağ güvenlik gruplarını][NetworkSecurityGroups] , bir App Service ortamı bulunduğu alt ağla gelen ağ iletişimini kısıtlamak için kullanabilirsiniz.  Bu sayede, Web uygulaması güvenlik duvarları ve ağ SaaS sağlayıcıları gibi yukarı akış cihazlarının ve hizmetlerinin arkasındaki uygulamalar çalıştırabilirsiniz.

Uygulamalar, iç veritabanları ve web hizmetleri gibi şirket kaynaklarına da sıklıkla erişmelidir.  Yaygın bir yaklaşım, bu uç noktaların yalnızca bir Azure sanal ağı içinde akan iç ağ trafiği için kullanılabilir hale getirme amaçlıdır.  App Service Ortamı, iç hizmetlerle aynı sanal ağa katıldığında, ortamda çalışan uygulamalar, [siteden siteye][SiteToSite] ve [Azure ExpressRoute][ExpressRoute] bağlantıları aracılığıyla erişilebilen uç noktalar dahil olmak üzere bunlara erişebilir.

App Service ortamlarının sanal ağlarla ve şirket içi ağlarda nasıl çalıştığı hakkında daha fazla bilgi için, [ağ mimarisi][NetworkArchitectureOverview]üzerinde aşağıdaki makalelere bakın, [gelen trafiği kontrol][ControllingInboundTraffic]edin ve arka [uçlara güvenli][SecurelyConnectingToBackends]bir şekilde bağlanma. 

## <a name="getting-started"></a>Başlarken

App Service ortamları ile çalışmaya başlamak için bkz. [nasıl App Service ortamı oluşturma][HowToCreateAnAppServiceEnvironment]

App Service Ortamı ağ mimarisine genel bakış için bkz. [ağ mimarisine genel bakış][NetworkArchitectureOverview] makalesi.

ExpressRoute ile App Service Ortamı kullanma hakkında ayrıntılı bilgi için [Express Route ve App Service ortamları][NetworkConfigDetailsForExpressRoute]hakkında aşağıdaki makaleye bakın.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->