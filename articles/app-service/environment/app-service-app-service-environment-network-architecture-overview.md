---
title: App Service ortamlara yönelik ağ mimarisine genel bakış-Azure
description: Uygulama hizmeti ortamlarının ağ topolojisine yönelik mimari genel bakış.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 98eb4d7440126bedb3d2e1de5711141eaac8b07a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070060"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>App Service Ortamlarının Ağ Mimarisine Genel Bakış
## <a name="introduction"></a>Giriş
App Service ortamları her zaman bir [sanal ağ][virtualnetwork] alt ağı içinde oluşturulur; bir App Service ortamı çalıştıran uygulamalar aynı sanal ağ topolojisinde bulunan özel uç noktalarla iletişim kurabilir.  Müşteriler sanal ağ altyapısının parçalarını kilitleyebileceğinizden, bir App Service Ortamı oluşan ağ iletişim akışlarının türlerini anlamak önemlidir.

## <a name="general-network-flow"></a>Genel ağ akışı
Bir App Service Ortamı (Ao), uygulamalar için ortak sanal IP adresi (VIP) kullandığında, tüm gelen trafik bu genel VIP 'ye ulaşır.  Bu, uygulamalar için HTTP ve HTTPS trafiği, FTP, uzaktan hata ayıklama işlevselliği ve Azure yönetim işlemleri için de ek trafik içerir.  Genel VIP 'de bulunan belirli bağlantı noktalarının (hem zorunlu hem de isteğe bağlı) tam listesi için, [gelen trafiği][controllinginboundtraffic] bir App Service ortamı denetleme makalesine bakın. 

App Service ortamlar, yalnızca bir sanal ağ iç adresine bağlanan, ıLB (iç yük dengeleyici) adresi olarak da adlandırılan uygulamaları çalıştırmayı da destekler.  ILB etkin bir ASE 'de, uygulamalar için HTTP ve HTTPS trafiğinin yanı sıra uzaktan hata ayıklama çağrıları da ıLB adresine ulaşır.  Çoğu ortak ıLB-Ao yapılandırması için, FTP/FTPS trafiği de ıLB adresine ulaşır.  Ancak Azure yönetim işlemleri yine de ıLB etkin bir AO 'nun genel VIP 'sine 454/455 bağlantı noktasına akacaktır.

Aşağıdaki diyagramda, uygulamaların ortak bir sanal IP adresine bağlandığı bir App Service Ortamı için çeşitli gelen ve giden ağ akışlarına ilişkin genel bir bakış gösterilmektedir:

![Genel ağ akışları][GeneralNetworkFlows]

Bir App Service Ortamı, çeşitli özel müşteri uç noktalarıyla iletişim kurabilir.  Örneğin, App Service Ortamı çalıştıran uygulamalar aynı sanal ağ topolojisinde IaaS sanal makinelerinde çalışan veritabanı sunucusuna bağlanabilir.

> [!IMPORTANT]
> Ağ diyagramına baktığınızda, "diğer Işlem kaynakları" App Service Ortamı farklı bir alt ağda dağıtılır. Kaynakları Ale ile aynı alt ağda dağıtmak, Ao kaynaklarına yönelik bağlantıyı (belirli bir iç site içi yönlendirme hariç) engeller. Yerine farklı bir alt ağa dağıtın (aynı VNET 'te). App Service Ortamı daha sonra bağlantı kurabiliyor. Ek yapılandırma gerekmez.
> 
> 

App Service ortamlar ayrıca, App Service Ortamı yönetmek ve çalıştırmak için gereken SQL DB ve Azure depolama kaynaklarıyla iletişim kurar.  App Service Ortamı ile iletişim kuran SQL ve depolama kaynaklarından bazıları App Service Ortamı aynı bölgede bulunur, diğerleri ise uzak Azure bölgelerinde bulunur.  Sonuç olarak, App Service Ortamı düzgün çalışması için Internet 'e giden bağlantı her zaman gereklidir. 

Bir alt ağda App Service Ortamı dağıtıldığı için ağ güvenlik grupları alt ağa gelen trafiği denetlemek için kullanılabilir.  Bir App Service Ortamı gelen trafiği denetleme hakkında daha fazla bilgi için aşağıdaki [makaleye][controllinginboundtraffic]bakın.

App Service Ortamı giden Internet bağlantısına izin verme hakkında daha fazla bilgi için bkz. [Express Route][ExpressRoute]ile çalışma hakkında aşağıdaki makaleye bakın.  Makalede açıklanan yaklaşım, siteden siteye bağlantı ile çalışırken ve Zorlamalı tünel kullanılırken geçerlidir.

## <a name="outbound-network-addresses"></a>Giden ağ adresleri
App Service Ortamı giden çağrılar yaptığında, her zaman giden çağrılarla bir IP adresi ilişkilendirilir.  Kullanılan belirli IP adresi, Çağrılmakta olan bitiş noktasının sanal ağ topolojisi içinde veya sanal ağ topolojisinin dışında olup olmamasına bağlıdır.

Çağrılmakta olan bitiş noktası sanal ağ topolojisinin **dışındaysa** , kullanılan giden adres (giden NAT adresi) App SERVICE ORTAMı genel VIP 'dir.  Bu adres, Özellikler dikey penceresindeki App Service Ortamı Portal Kullanıcı arabiriminde bulunabilir.

![Giden IP adresi][OutboundIPAddress]

Bu adres, yalnızca App Service Ortamı bir uygulama oluşturarak ve sonra uygulamanın adresinde bir *nslookup* işlemini gerçekleştirerek ortak bir VIP 'Nin bulunduğu ASE 'ler için de belirlenebilir. Sonuç IP adresi hem genel VIP hem de App Service Ortamı giden NAT adresidir.

Çağrılmakta olan bitiş noktası sanal ağ topolojisinin **içindeyse** , çağıran uygulamanın giden adresi, uygulamayı çalıştıran bireysel işlem KAYNAĞıNıN iç IP adresi olacaktır.  Ancak, sanal ağ iç IP adreslerinin uygulamalara kalıcı bir şekilde eşlenmesi değildir.  Uygulamalar farklı işlem kaynakları arasında hareket edebilir ve bir App Service Ortamı kullanılabilir işlem kaynakları havuzu ölçeklendirme işlemleri nedeniyle değişebilir.

Ancak, App Service Ortamı her zaman bir alt ağ içinde bulunduğundan, uygulamayı çalıştıran bir işlem kaynağının iç IP adresinin her zaman alt ağın CıDR aralığında yer aldığı garanti edilir.  Sonuç olarak, sanal ağ içindeki diğer uç noktalara erişimi güvenli hale getirmek için hassas ACL 'Ler veya ağ güvenlik grupları kullanıldığında, App Service Ortamı içeren alt ağ aralığına erişim verilmesi gerekir.

Aşağıdaki diyagramda bu kavramlar daha ayrıntılı gösterilmektedir:

![Giden ağ adresleri][OutboundNetworkAddresses]

Yukarıdaki diyagramda:

* App Service Ortamı genel VIP 'si 192.23.1.2 olduğundan, bu, "Internet" uç noktalarına çağrı yaparken kullanılan giden IP adresidir.
* App Service Ortamı için kapsayan alt ağın CıDR aralığı 10.0.1.0/26 ' dır.  Aynı sanal ağ altyapısının içindeki diğer uç noktalar, bu adres aralığındaki bir yerde bulunan uygulamalardan gelen çağrıları görür.

## <a name="calls-between-app-service-environments"></a>App Service ortamları arasındaki çağrılar
Aynı sanal ağda birden çok App Service ortamı dağıtırsanız ve bir App Service Ortamı giden çağrıları başka bir App Service Ortamı yaparsanız daha karmaşık bir senaryo meydana gelebilir.  Bu tür çapraz App Service Ortamı aramalar Ayrıca "Internet" çağrıları olarak kabul edilir.

Aşağıdaki diyagramda, tek bir App Service Ortamı uygulamalarla katmanlı mimari bir örnek gösterilmektedir (ör. Uygulamaları ikinci bir App Service Ortamı arayan "ön kapı" Web uygulamaları) (örneğin, Internet 'ten erişilebilmesi amaçlanan iç arka uç API Apps). 

![App Service ortamları arasındaki çağrılar][CallsBetweenAppServiceEnvironments] 

Yukarıdaki örnekte, "Ao One" App Service Ortamı, 192.23.1.2 giden bir IP adresine sahiptir.  Bu App Service Ortamı çalışan bir uygulama, aynı sanal ağda bulunan ikinci bir App Service Ortamı ("AKEN Iki") üzerinde çalışan bir uygulamaya giden bir çağrı yapıyorsa, giden çağrı "Internet" çağrısı olarak kabul edilir.  Sonuç olarak, ikinci App Service Ortamı gelen ağ trafiği, 192.23.1.2 'tan kaynaklanan (yani ilk App Service Ortamı alt ağ adres aralığı değil) görünür.

Farklı App Service ortamları arasındaki çağrılar "Internet" çağrıları olarak kabul edilir, ancak her iki App Service ortamı da aynı Azure bölgesinde bulunuyorsa, ağ trafiği bölgesel Azure ağında kalır ve bu, fiziksel olarak genel Internet.  Sonuç olarak, ikinci App Service Ortamı alt ağında bir ağ güvenlik grubu kullanarak yalnızca ilk App Service Ortamı gelen çağrılara (giden IP adresi 192.23.1.2) izin verebilir, böylece uygulama arasında güvenli iletişim sağlayabilirsiniz Hizmet ortamları.

## <a name="additional-links-and-information"></a>Ek bağlantılar ve bilgiler
App Service ortamları tarafından kullanılan ve gelen trafiği denetlemek için ağ güvenlik gruplarını kullanan gelen bağlantı noktaları hakkındaki ayrıntılar [burada][controllinginboundtraffic]bulunabilir.

App Service ortamlara giden Internet erişimi sağlamak için Kullanıcı tanımlı yolların kullanımıyla ilgili ayrıntılar bu [makalede][ExpressRoute]sunulmaktadır. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

