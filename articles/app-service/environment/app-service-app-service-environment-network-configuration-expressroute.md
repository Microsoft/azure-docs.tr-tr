---
title: Azure ExpressRoute için ağ yapılandırması ayrıntıları-App Service
description: Azure ExpressRoute bağlantı hattına bağlı sanal ağlarda PowerApps için App Service Ortamı ağ yapılandırması ayrıntıları.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b10bd15538ecca7934a397ca63db1150a0bfc32c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070033"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Azure ExpressRoute ile PowerApps için App Service Ortamı ağ yapılandırması ayrıntıları

Müşteriler, şirket içi ağınızı Azure 'a genişletmek için bir [Azure ExpressRoute][ExpressRoute] devresini sanal ağ altyapısına bağlanabilir. App Service Ortamı, [sanal ağ][virtualnetwork] altyapısının bir alt ağında oluşturulur. App Service Ortamı üzerinde çalışan uygulamalar, yalnızca ExpressRoute bağlantısı üzerinden erişilebilen arka uç kaynaklarına güvenli bağlantılar kurar.  

App Service Ortamı, bu senaryolarda oluşturulabilir:
- Sanal ağları Azure Resource Manager.
- Klasik dağıtım modeli sanal ağları.
- Ortak adres aralıkları veya RFC1918 adres alanları (yani, özel adresler) kullanan sanal ağlar. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Gerekli ağ bağlantısı

App Service Ortamı, başlangıçta ExpressRoute 'a bağlı bir sanal ağda karşılanmamış olabilecek ağ bağlantısı gereksinimlerine sahiptir.

App Service Ortamı aşağıdaki ağ bağlantısı ayarlarının düzgün şekilde çalışmasını gerektirir:

* 80 numaralı bağlantı noktasında ve 443 numaralı bağlantı noktasında dünya çapındaki Azure depolama uç noktalarına giden ağ bağlantısı. Bu uç noktalar App Service Ortamı aynı bölgede ve ayrıca diğer Azure bölgeleriyle bulunur. Azure depolama uç noktaları şu DNS etki alanları altında çözümlenir: table.core.windows.net, blob.core.windows.net, queue.core.windows.net ve file.core.windows.net.  

* 445 numaralı bağlantı noktasında Azure Files hizmetine giden ağ bağlantısı.

* App Service Ortamı ile aynı bölgede bulunan Azure SQL veritabanı uç noktalarına giden ağ bağlantısı. SQL veritabanı uç noktaları, 1433, 11000-11999 ve 14000-14999 bağlantı noktalarına açık erişim gerektiren database.windows.net etki alanı altında çözümlenmektedir. SQL Database V12 bağlantı noktası kullanımı hakkında daha fazla bilgi için bkz. [ADO.NET 4,5 için 1433 üzerindeki bağlantı noktaları](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Azure Yönetim düzlemi uç noktalarına giden ağ bağlantısı (hem Azure klasik dağıtım modeli hem de Azure Resource Manager uç noktaları). Bu uç noktalara yönelik bağlantı management.core.windows.net ve management.azure.com etki alanlarını içerir. 

* Ocsp.msocsp.com, mscrl.microsoft.com ve crl.microsoft.com etki alanlarına giden ağ bağlantısı. SSL işlevselliğini desteklemek için bu etki alanlarına yönelik bağlantı gereklidir.

* Sanal ağın DNS yapılandırması, bu makalede bahsedilen tüm uç noktaları ve etki alanlarını çözümleyebilmelidir. Uç noktalar çözümlenemiyorsa App Service Ortamı oluşturma başarısız olur. Mevcut App Service Ortamı, sağlıksız olarak işaretlenir.

* DNS sunucularıyla iletişim için 53 numaralı bağlantı noktası üzerinden giden erişim gereklidir.

* Bir VPN ağ geçidinin diğer ucunda özel bir DNS sunucusu varsa, DNS sunucusuna App Service Ortamı içeren alt ağdan erişilebilir olması gerekir. 

* Giden ağ yolu, iç kurumsal proxy 'lerde gezinemiyorum ve şirket içi tünellere zorlamalı olarak ayarlanamaz. Bu eylemler giden ağ trafiğinin etkin NAT adresini App Service Ortamı olarak değiştirir. Giden ağ trafiği App Service Ortamı NAT adresinde yapılan değişiklikler bağlantı hatalarının çok sayıda uç nokta olmasına neden olur. App Service Ortamı oluşturma başarısız oluyor. Mevcut App Service Ortamı, sağlıksız olarak işaretlenir.

* App Service Ortamı için gerekli bağlantı noktalarına gelen ağ erişimine izin verilmelidir. Ayrıntılar için bkz. [App Service ortamı gelen trafiği denetleme][requiredports].

DNS gereksinimlerini karşılamak için, sanal ağ için geçerli bir DNS altyapısının yapılandırıldığından ve korunduğundan emin olun. App Service Ortamı oluşturulduktan sonra DNS yapılandırması değiştirilirse, geliştiriciler App Service Ortamı yeni DNS yapılandırmasını çekmeye zorlayabilir. [Azure portal][NewPortal]App Service ortamı yönetimi altında **Yeniden Başlat** simgesini kullanarak bir sıralı ortam yeniden başlatması tetikleyebilirsiniz. Yeniden başlatma, ortamın yeni DNS yapılandırmasını almasına neden olur.

Gelen ağ erişim gereksinimlerini yerine getirmek için App Service Ortamı alt ağında bir [ağ güvenlik grubu (NSG)][NetworkSecurityGroups] yapılandırın. NSG, [App Service ortamı gelen trafiği denetlemek için][requiredports]gerekli erişime izin verir.

## <a name="outbound-network-connectivity"></a>Giden ağ bağlantısı

Varsayılan olarak, yeni oluşturulan bir ExpressRoute devresi, giden internet bağlantısına izin veren bir varsayılan yol tanıtır. App Service Ortamı, bu yapılandırmayı diğer Azure uç noktalarına bağlanmak için kullanabilir.

Ortak bir müşteri yapılandırması, giden internet trafiğini şirket içi akışa zorlayan kendi varsayılan yolunu (0.0.0.0/0) tanımlamaktır. Bu trafik, App Service Ortamı bağımsız olarak akış ayırır. Giden trafik, şirket içi veya NAT, artık çeşitli Azure uç noktalarıyla çalışmayan tanınmayan bir adres kümesine engellenir.

Çözüm, App Service Ortamı içeren alt ağda bir (veya daha fazla) Kullanıcı tanımlı yollar (UDRs) tanımlamaktır. UDR, varsayılan yol yerine, alt ağa özgü yolları tanımlar.

Mümkünse, aşağıdaki yapılandırmayı kullanın:

* ExpressRoute yapılandırması 0.0.0.0/0 tanıtır. Varsayılan olarak, yapılandırma zorlaması Şirket içindeki tüm giden trafiği tüneller.
* App Service Ortamı içeren alt ağa uygulanan UDR, Internet 'in bir sonraki atlama türüyle 0.0.0.0/0 tanımlıyor. Bu yapılandırmanın bir örneği, bu makalenin ilerleyen kısımlarında açıklanmıştır.

Bu yapılandırmanın Birleşik etkisi, alt ağ düzeyi UDR 'nin ExpressRoute zorlamalı tünelinin üzerinde öncelikli hale gelir. App Service Ortamı giden Internet erişimi garanti edilir.

> [!IMPORTANT]
> Bir UDR 'de tanımlanan yolların, ExpressRoute yapılandırması tarafından tanıtılan tüm rotalardan öncelikli olması için yeterince özel olması gerekir. Sonraki bölümde açıklanan örnek, geniş 0.0.0.0/0 adres aralığını kullanır. Bu Aralık, daha belirli adres aralıklarını kullanan rota tanıtımlarında yanlışlıkla geçersiz kılınabilir.
> 
> App Service Ortamı, genel eşleme yolundan özel eşleme yoluna giden yolları çapraz duyuran ExpressRoute yapılandırmalarında desteklenmez. Ortak eşlemeye sahip ExpressRoute yapılandırmalarında, büyük bir Microsoft Azure IP adresi aralığı kümesi için Microsoft 'tan yol reklamları alın. Bu adres aralıkları özel eşleme yolunda çapraz tanıtılırsa, App Service Ortamı alt ağdan gelen tüm giden ağ paketleri müşterinin Şirket içi ağ altyapısına tünel uygulamaya zorlanır. Bu ağ akışı şu anda App Service Ortamı ile desteklenmiyor. Bir çözüm, genel eşleme yolundan özel eşleme yoluna yönelik çapraz reklam yollarını durdurmaktır.
> 
> 

Kullanıcı tanımlı rotalar hakkında arka plan bilgileri için bkz. [sanal ağ trafiği yönlendirme][UDROverview].  

Kullanıcı tanımlı yollar oluşturma ve yapılandırma hakkında bilgi edinmek için bkz. [PowerShell kullanarak ağ trafiğini yönlendirme tablosuyla yönlendirme][UDRHowTo].

## <a name="udr-configuration"></a>UDR yapılandırması

Bu bölümde App Service Ortamı için bir örnek UDR yapılandırması gösterilmektedir.

### <a name="prerequisites"></a>Önkoşullar

* [Azure İndirmeleri sayfasından][AzureDownloads]Azure PowerShell ' i yükler. Haziran 2015 veya üzeri bir tarih içeren bir indirme seçin. En son PowerShell cmdlet 'lerini yüklemek için **komut satırı araçları** > **Windows PowerShell**altında, **yüklemek** ' ı seçin.

* App Service Ortamı tarafından özel kullanım için benzersiz bir alt ağ oluşturun. Benzersiz alt ağ, alt ağa uygulanan UDRs 'nin yalnızca App Service Ortamı için giden trafiği açmasını sağlar.

> [!IMPORTANT]
> Yapılandırma adımlarını tamamladıktan sonra yalnızca App Service Ortamı dağıtın. Adımları App Service Ortamı dağıtmayı denemeden önce giden ağ bağlantısının kullanılabilir olmasını sağlar.

### <a name="step-1-create-a-route-table"></a>1\. adım: Yönlendirme tablosu oluşturma

Batı ABD Azure bölgesinde, bu kod parçacığında gösterildiği gibi **Directınternetroutetable** adlı bir yol tablosu oluşturun:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>2\. adım: Tabloda rotalar oluşturma

Giden internet erişimini etkinleştirmek için yol tablosuna yollar ekleyin.  

İnternet 'e giden erişimi yapılandırın. 0\.0.0.0/0 için bu kod parçacığında gösterildiği gibi bir yol tanımlayın:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0, geniş bir adres aralığıdır. Aralık, daha belirgin olan ExpressRoute tarafından tanıtılan adres aralıkları tarafından geçersiz kılınır. 0\.0.0.0/0 rotasına sahip bir UDR yalnızca 0.0.0.0/0 tanıtan bir ExpressRoute yapılandırması ile birlikte kullanılmalıdır. 

Alternatif olarak, Azure tarafından kullanılan geçerli ve kapsamlı bir CıDR aralığı listesini indirin. Tüm Azure IP adresi aralıklarına yönelik XML dosyası, [Microsoft Indirme merkezi][DownloadCenterAddressRanges]' nden kullanılabilir.  

> [!NOTE]
>
> Azure IP adresi aralıkları zaman içinde değişir. Kullanıcı tanımlı yolların eşitlenmiş halde tutulması için düzenli el ile güncelleştirmeleri olması gerekir.
>
> Tek bir UDR 'nin varsayılan 100 rotasıyla üst limiti vardır. 100 yol sınırına sığacak şekilde Azure IP adresi aralıklarını "özetleyin". UDR tanımlı yolların, ExpressRoute bağlantınızın tanıtıldığı rotalardan daha belirgin olması gerekir.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>3\. adım: Tabloyu alt ağla ilişkilendir

Yol tablosunu App Service Ortamı dağıtmayı planladığınız alt ağ ile ilişkilendirin. Bu komut **Directınternetroutetable** tablosunu App Service ortamı Içerecek **asesubnet** alt ağıyla ilişkilendirir.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>4\. Adım: Rotayı test edin ve onaylayın

Yol tablosu alt ağa bağlandıktan sonra, yolu test edin ve onaylayın.

Bir sanal makineyi alt ağa dağıtın ve şu koşulları onaylayın:

* Bu makalede açıklanan Azure ve Azure dışı uç noktalara giden trafik, ExpressRoute bağlantı hattını göstermez. Alt ağdan giden trafiğe Zorlamalı tünel uygulandığında App Service Ortamı oluşturma işlemi her zaman başarısız olur.
* Bu makalede açıklanan uç noktalar için DNS aramaları düzgün şekilde çözümlenir. 

Yapılandırma adımlarını tamamladıktan ve yolu doğruladıktan sonra, sanal makineyi silin. App Service Ortamı oluşturulduğunda alt ağın "boş" olması gerekir.

Artık App Service Ortamı dağıtmaya hazırsınız!

## <a name="next-steps"></a>Sonraki adımlar

PowerApps için App Service Ortamı kullanmaya başlamak için bkz. [App Service ortamı giriş][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
