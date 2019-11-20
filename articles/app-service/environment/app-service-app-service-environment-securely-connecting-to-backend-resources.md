---
title: App Service ortamından Azure ile arka uç kaynaklarına güvenli bağlanma-Azure
description: App Service Ortamı arka uç kaynaklarına güvenli bir şekilde bağlanma hakkında bilgi edinin.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: adb7c246a9f8c8d202d45b58f4d22eeb8d51a773
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069973"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>App Service ortamından arka uç kaynaklarına güvenli bir şekilde bağlanma
## <a name="overview"></a>Genel Bakış
Bir App Service Ortamı her zaman bir Azure Resource Manager sanal ağda **veya** klasik dağıtım modeli [sanal ağında][virtualnetwork]oluşturulduğundan, bir App Service ortamı giden bağlantıları diğer arka uç kaynaklarına sanal ağ üzerinden özel olarak akabilir.  Haziran 2016 ' de yapılan son değişikliklerle birlikte, ASE 'ler ortak adres aralıkları veya RFC1918 adres alanları (örn. özel adresler) kullanan sanal ağlara da dağıtılabilir.  

Örneğin, 1433 numaralı bağlantı noktasına sahip bir sanal makine kümesinde çalışan bir SQL Server olabilir.  Uç nokta, yalnızca aynı sanal ağdaki diğer kaynaklardan erişime izin vermek için ACLd olabilir.  

Diğer bir örnek olarak, hassas uç noktalar şirket içinde çalışabilir ve Azure 'a [siteden siteye][SiteToSite] veya [Azure ExpressRoute][ExpressRoute] bağlantıları aracılığıyla bağlı olabilir.  Sonuç olarak, yalnızca siteden siteye veya ExpressRoute tünellerini bağlı sanal ağlardaki kaynaklar şirket içi uç noktalara erişebilir.

Bu senaryoların tümünde, bir App Service Ortamı çalışan uygulamalar çeşitli sunuculara ve kaynaklara güvenli bir şekilde bağlanabiliyor.  Aynı sanal ağdaki (ya da aynı sanal ağa bağlı) bir App Service Ortamı çalıştıran uygulamalardan giden trafik, yalnızca sanal ağ üzerinden akacaktır.  Özel uç noktalara giden trafik, genel Internet üzerinden akmaz.

Bir desteklenmediği uyarısıyla, bir sanal ağ içindeki uç noktalara App Service Ortamı giden trafiğe uygulanır.  App Service ortamlar, App Service Ortamı **aynı** alt ağda bulunan sanal makinelerin uç noktalarına ulaşamaz.  Bu, App Service ortamların yalnızca App Service Ortamı tarafından özel kullanım için ayrılmış bir alt ağa dağıtıldığı sürece normalde bir sorun olmaması gerekir.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Giden bağlantı ve DNS gereksinimleri
App Service Ortamı düzgün bir şekilde çalışması için, çeşitli uç noktalara giden erişim gerekir. AX tarafından kullanılan dış uç noktaların tam listesi [ExpressRoute Için ağ yapılandırması](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) makalesinin "gerekli ağ bağlantısı" bölümünde bulunur.

App Service ortamları, sanal ağ için yapılandırılmış geçerli bir DNS altyapısı gerektirir.  Bir App Service Ortamı oluşturulduktan sonra DNS yapılandırması değiştirilirse, geliştiriciler bir App Service Ortamı yeni DNS yapılandırmasını çekmeye zorlayabilir.  Portalda App Service Ortamı Yönetim dikey penceresinin en üstünde bulunan "yeniden Başlat" simgesi kullanılarak sıralı bir ortamı yeniden başlatmanın tetiklenmesi, ortamın yeni DNS yapılandırmasını başlatmasına neden olur.

Ayrıca, VNET üzerindeki özel DNS sunucularının, bir App Service Ortamı oluşturmadan önce bir süre önce kurulumunu da öneririz.  Bir App Service Ortamı oluşturulurken bir sanal ağın DNS yapılandırması değiştirilirse, bu, App Service Ortamı oluşturma işleminin başarısız olmasına neden olur.  Benzer bir Vein içinde, bir VPN ağ geçidinin diğer ucunda özel bir DNS sunucusu varsa ve DNS sunucusu ulaşılamaz durumdaysa veya kullanılamıyorsa, App Service Ortamı oluşturma işlemi de başarısız olur.

## <a name="connecting-to-a-sql-server"></a>SQL Server bağlanma
Ortak bir SQL Server yapılandırmasında 1433 numaralı bağlantı noktasında dinleme yapan bir uç nokta vardır:

![SQL Server uç noktası][SqlServerEndpoint]

Bu uç noktayla trafiği kısıtlamak için iki yaklaşım vardır:

* [Ağ Access Control listeleri][NetworkAccessControlLists] (Ağ ACL 'Leri)
* [Ağ güvenlik grupları][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Ağ ACL 'SI Ile erişimi kısıtlama
Bağlantı noktası 1433, bir ağ erişim denetimi listesi kullanılarak güvenli hale getirilir.  Aşağıdaki örnek, bir sanal ağın içinden gelen istemci adreslerini beyaz listeler ve diğer tüm istemcilere erişimi engeller.

![Ağ Access Control listesi örneği][NetworkAccessControlListExample]

SQL Server ile aynı sanal ağda App Service Ortamı çalıştıran uygulamalar, SQL Server sanal makine için **VNET iç** IP adresini kullanarak SQL Server örneğine bağlanabilir.  

Aşağıdaki örnek bağlantı dizesi, kendi özel IP adresini kullanarak SQL Server başvurur.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Sanal makinede ortak bir uç nokta de olsa da, ağ ACL 'si nedeniyle genel IP adresini kullanan bağlantı girişimleri reddedilir. 

## <a name="restricting-access-with-a-network-security-group"></a>Ağ güvenlik grubuyla erişimi kısıtlama
Erişimin güvenliğini sağlamaya yönelik alternatif bir yaklaşım da ağ güvenlik grubudur.  Ağ güvenlik grupları ayrı sanal makinelere veya sanal makineler içeren bir alt ağa uygulanabilir.

İlk olarak bir ağ güvenlik grubunun oluşturulması gerekir:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Yalnızca VNet iç trafiğine erişimi kısıtlamak, bir ağ güvenlik grubuyla çok basittir.  Bir ağ güvenlik grubundaki varsayılan kurallar yalnızca aynı sanal ağdaki diğer ağ istemcilerinden erişime izin verir.

Sonuç olarak SQL Server erişimi, SQL Server çalıştıran sanal makinelere ya da sanal makineleri içeren alt ağa varsayılan kuralları ile bir ağ güvenlik grubu uygulamak kadar basittir.

Aşağıdaki örnek, kapsayan alt ağa bir ağ güvenlik grubu uygular:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Nihai sonuç, dış erişimi engelleyen bir güvenlik kuralları kümesidir ve VNet iç erişimine izin verir:

![Varsayılan ağ güvenlik kuralları][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Başlarken
App Service ortamlarıyla çalışmaya başlamak için bkz. [giriş App Service ortamı][IntroToAppServiceEnvironment]

App Service Ortamı gelen trafiği denetleme hakkında daha fazla bilgi için bkz. [bir App Service ortamı gelen trafiği denetleme][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
