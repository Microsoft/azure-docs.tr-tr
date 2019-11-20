---
title: Ağ dağıtım özellikleri-Azure App Service | Microsoft Docs
description: Çeşitli App Service ağ özelliklerini kullanma
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 801692c53ef268f15edc60d31743aefa6a247a78
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928500"
---
# <a name="app-service-networking-features"></a>App Service ağ özellikleri

Azure App Service uygulamalar, birden çok şekilde dağıtılabilir. Varsayılan olarak, App Service barındırılan uygulamalar doğrudan internet erişimine açık ve yalnızca İnternet 'te barındırılan uç noktalara erişebilir. Bununla birlikte, birçok müşteri uygulamasının gelen ve giden ağ trafiğini kontrol etmeniz gerekir. Bu ihtiyaçları karşılamak için App Service çeşitli özellikler mevcuttur. Bu zorluk, belirli bir sorunu çözmek için hangi özelliğin kullanılması gerektiğini öğrenmektir. Bu belge, müşterilerin bazı örnek kullanım örneklerine göre hangi özelliğin kullanılması gerektiğini belirlemesine yardımcı olmaya yöneliktir.

Azure App Service için iki birincil dağıtım türü vardır. Ücretsiz, paylaşılan, temel, standart, Premium ve Premiumv2 fiyatlandırma SKU 'Larında App Service planlarını barındıran çok kiracılı ortak hizmet vardır. Ardından, yalıtılmış SKU App Service planlarının doğrudan Azure sanal ağınızda (VNet) barındırdığı tek kiracı App Service Ortamı (ASE) vardır. Kullandığınız özellikler, çok kiracılı bir hizmettir veya bir AO 'da olduğunuzda farklılık gösterecektir. 

## <a name="multi-tenant-app-service-networking-features"></a>Çok kiracılı App Service ağ özellikleri 

Azure App Service dağıtılmış bir sistemdir. Gelen HTTP/HTTPS isteklerini işleyen rollere ön uçlar denir. Müşteri iş yükünü barındıran rollere çalışanlar adı verilir. App Service dağıtımındaki tüm roller çok kiracılı bir ağda bulunur. Aynı App Service ölçek biriminde birçok farklı müşteri olduğundan, App Service ağını doğrudan ağınıza bağlanamazsınız. Ağları bağlamak yerine, uygulama iletişiminin farklı yönlerini işlemek için özelliklere ihtiyacımız var. Uygulamanızdaki istekleri işleyen özellikler, uygulamanızdan çağrı yaparken sorunları çözümlemek için kullanılamaz. Benzer şekilde, uygulamanızdaki çağrılara yönelik sorunları çözebilecek özellikler, uygulamanızdaki sorunları çözümlemek için kullanılamaz.  

| Gelen özellikler | Giden Özellikler |
|---------------------|-------------------|
| Uygulama tarafından atanan adres | Karma Bağlantılar |
| Erişim kısıtlamaları | Ağ Geçidi gerekli VNet tümleştirmesi |
| Hizmet Uç Noktaları | VNet tümleştirmesi (Önizleme) |

Aksi belirtilmedikçe tüm özellikler birlikte kullanılabilir. Çeşitli sorunlarınızı çözümlemek için özellikleri karıştırabilirsiniz.

## <a name="use-case-and-features"></a>Kullanım örneği ve Özellikler

Belirli bir kullanım durumu için, sorunu çözmenin birkaç yolu olabilir.  Kullanılacak doğru özellik bazen yalnızca kullanım durumunun kendisinden kaynaklanan nedenlerden kaynaklanır. Aşağıdaki gelen kullanım örnekleri, uygulamanıza giden trafiği denetleme sorunlarını çözmek için App Service ağ özelliklerini kullanmayı önerir. 
 
| Gelen kullanım örnekleri | Özellik |
|---------------------|-------------------|
| Uygulamanız için IP tabanlı SSL gereksinimlerini destekleme | Uygulama tarafından atanan adres |
| Paylaştırılmamış, uygulamanız için adanmış gelen adres | Uygulama tarafından atanan adres |
| Uygulamanıza erişimi iyi tanımlanmış bir adres kümesinden sınırlayın | Erişim kısıtlamaları |
| Uygulamamı sanal ağım içindeki özel IP 'lerde kullanıma sunun | ıLB ATıCı </br> Hizmet uç noktaları ile Application Gateway |
| VNet 'teki kaynaklardan uygulamama erişimi kısıtla | Hizmet Uç Noktaları </br> ıLB ATıCı |
| Uygulamamın sanal ağı 'nda özel bir IP 'de kullanıma sunulması | ıLB ATıCı </br> hizmet uç noktalarına sahip bir Application Gateway gelen için özel IP |
| Bir WAF ile uygulamamı koruyun | Application Gateway + ıLB Ao </br> Hizmet uç noktaları ile Application Gateway </br> Erişim kısıtlamalarına sahip Azure ön kapısı |
| Farklı bölgelerde uygulamalarıma trafik yükünü dengelemek | Erişim kısıtlamalarına sahip Azure ön kapısı | 
| Aynı bölgedeki Yük Dengeleme trafiği | Hizmet uç noktaları ile Application Gateway | 

Aşağıdaki giden kullanım örnekleri, uygulamanız için giden erişim ihtiyaçlarını çözümlemek üzere App Service ağ özelliklerini kullanmayı önerir. 

| Giden kullanım örnekleri | Özellik |
|---------------------|-------------------|
| Aynı bölgedeki bir Azure Sanal Ağa gelen kaynaklara erişim | Sanal Ağ Tümleştirmesi </br> ASE |
| Azure sanal içindeki kaynaklara farklı bir bölge Ağa gelen erişme | Ağ Geçidi gerekli VNet tümleştirmesi </br> ASE ve VNet eşlemesi |
| Hizmet uç noktaları ile korunan kaynaklara erişin | Sanal Ağ Tümleştirmesi </br> ASE |
| Azure 'a bağlı olmayan özel bir ağdaki kaynaklara erişme | Karma Bağlantılar |
| ExpressRoute devrelerine göre kaynaklara erişin | VNet tümleştirmesi (şimdilik RFC 1918 adresleriyle kısıtlıdır) </br> ASE | 


### <a name="default-networking-behavior"></a>Varsayılan ağ davranışı

Azure App Service ölçek birimleri her dağıtımda çok sayıda müşteriyi destekler. Ücretsiz ve paylaşılan SKU planları, çok kiracılı çalışanlar üzerinde müşteri iş yüklerini barındırır. Temel ve yukarıdaki planlar, yalnızca bir App Service planına (ASP) ayrılmış müşteri iş yüklerini barındırır. Standart bir App Service planınız varsa, Bu plandaki tüm uygulamalar aynı çalışan üzerinde çalışır. Çalışanı ölçeklendirirseniz, ASP 'deki tüm uygulamalar, ASP 'inizdeki her bir örnek için yeni bir çalışan üzerinde çoğaltılır. Premiumv2 için kullanılan çalışanlar, diğer planlar için kullanılan çalışanlardan farklıdır. Her App Service dağıtımı, bu App Service dağıtımında bulunan uygulamalara yönelik tüm gelen trafik için kullanılan bir IP adresine sahiptir. Ancak, giden çağrıları yapmak için 4 ile 11 arasında adreslerin herhangi bir yerinde kullanılır. Bu adresler, bu App Service dağıtımındaki tüm uygulamalar tarafından paylaşılır. Giden adresler farklı çalışan türlerine göre farklılık açmış. Bu, ücretsiz, paylaşılan, temel, standart ve Premium ASPs tarafından kullanılan adreslerin, Premiumv2 ASPs 'den giden çağrılar için kullanılan adreslerden farklı olduğu anlamına gelir. Uygulamanızın özelliklerine bakarsanız, uygulamanız tarafından kullanılan gelen ve giden adresleri görebilirsiniz. Bir IP ACL 'SI ile bir bağımlılığı kilitlemeniz gerekiyorsa, possibleOutboundAddresses kullanın. 

![Uygulama özellikleri](media/networking-features/app-properties.png)

App Service, hizmeti yönetmek için kullanılan birkaç uç nokta içerir.  Bu adresler ayrı bir belgede yayımlanır ve AppServiceManagement IP hizmeti etiketinde de bulunur. AppServiceManagement etiketi yalnızca bu tür trafiğe izin vermeniz gereken bir App Service Ortamı (Ao) ile kullanılır. App Service gelen adresler AppService IP hizmeti etiketinde izlenir. App Service tarafından kullanılan giden adresleri içeren bir IP hizmeti etiketi yok. 

![Gelen ve giden diyagramı App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Uygulama tarafından atanan adres 

Uygulama tarafından atanan adres özelliği, IP tabanlı SSL 'nin bir listesini temizleyerek uygulamanız ile SSL ayarlanarak erişilir. Bu özellik IP tabanlı SSL çağrıları için kullanılabilir, ancak uygulamanıza yalnızca sahip olduğu bir adres vermek için de kullanılabilir. 

![Uygulama tarafından atanan adres diyagramı](media/networking-features/app-assigned-address.png)

Uygulama tarafından atanan bir adres kullandığınızda, trafiğiniz hala App Service ölçek birimine gelen tüm trafiği işleyen ön uç rollerinden geçer. Ancak uygulamanıza atanan adres yalnızca uygulamanız tarafından kullanılır. Bu özellik için kullanım örnekleri şunlardır:

* Uygulamanız için IP tabanlı SSL gereksinimlerini destekleme
* Uygulamanız için, başka bir şeyle paylaşılmayan özel bir adres ayarlayın

[IP tabanlı SSL 'Yi yapılandırma][appassignedaddress]öğreticisiyle uygulamanızda bir adresin nasıl ayarlanacağını öğrenebilirsiniz. 

### <a name="access-restrictions"></a>Erişim kısıtlamaları 

Erişim kısıtlamaları özelliği, **gelen** istekleri, özgün IP adresine göre filtrelemenize olanak sağlar. Filtreleme eylemi, uygulamalarınızın çalıştığı çalışan rollerden yukarı akış olan ön uç rollerde gerçekleşir. Ön uç rolleri çalışanlardan yukarı akış olduğundan, erişim kısıtlamaları özelliği uygulamalarınız için ağ düzeyinde koruma olarak kabul edilebilir. Özelliği, öncelik sırasına göre değerlendirilen izin verme ve reddetme adres bloklarının bir listesini oluşturmanızı sağlar. Azure ağı 'nda bulunan ağ güvenlik grubu (NSG) özelliğine benzer.  Bu özelliği bir ate veya çok kiracılı hizmette kullanabilirsiniz. ILB Ao ile birlikte kullanıldığında, erişimi özel adres bloklarından kısıtlayabilirsiniz.

![Erişim kısıtlamaları](media/networking-features/access-restrictions.png)

Erişim kısıtlamaları özelliği, uygulamanıza ulaşmak için kullanılabilecek IP adreslerini kısıtlamak istediğiniz senaryolarda yardımcı olur. Bu özellik için kullanım örnekleri arasında:

* Uygulamanıza erişimi iyi tanımlanmış bir adres kümesinden sınırlayın 
* Azure ön kapısı gibi bir yük dengeleme hizmetinden gelen erişimi kısıtlayın. Gelen trafiğinizi Azure ön kapısına kilitlemek isterseniz, 147.243.0.0/16 ' dan trafiğe izin veren kurallar oluşturun ve 2a01:111:2050::/44. 

![Ön kapılı erişim kısıtlamaları](media/networking-features/access-restrictions-afd.png)

Uygulamanıza erişimi yalnızca Azure sanal ağınızdaki (VNet) kaynaklardan ulaşılabilen şekilde kilitlemek istiyorsanız, kaynağınızın VNet 'iniz üzerinde olduğu her şey için statik bir genel adrese ihtiyacınız vardır. Kaynakların ortak bir adresi yoksa, bunun yerine hizmet uç noktaları özelliğini kullanmanız gerekir. [Erişim kısıtlamalarını yapılandırma][iprestrictions]hakkında öğreticiyle bu özelliği etkinleştirmeyi öğrenin.

### <a name="service-endpoints"></a>Hizmet uç noktaları

Hizmet uç noktaları, kaynak adresin seçtiğiniz bir alt ağ kümesinden gelmesi için uygulamanıza **gelen** erişimi kilitlemenize olanak tanır. Bu özellik IP erişim kısıtlamalarıyla birlikte çalışmaktadır. Hizmet uç noktaları, IP erişim kısıtlamalarıyla aynı kullanıcı deneyiminde ayarlanır. Sanal ağlarınızdaki alt ağların yanı sıra ortak adresleri de içeren erişim kuralları için izin verme/reddetme listesi oluşturabilirsiniz. Bu özellik gibi senaryoları destekler:

![hizmet uç noktaları](media/networking-features/service-endpoints.png)

* Uygulamanıza gelen trafiği kilitlemek için uygulamanızla bir Application Gateway ayarlama
* Sanal ağınızdaki kaynaklarla uygulamanıza erişimi kısıtlama. Bu, sanal makineler, ASE veya VNet tümleştirmesi kullanan diğer uygulamalar içerebilir 

![uygulama ağ geçidi ile hizmet uç noktaları](media/networking-features/service-endpoints-appgw.png)

Hizmet [uç noktası erişim kısıtlamalarını yapılandırma][serviceendpoints] konusundaki öğreticide uygulamanızla hizmet uç noktalarını yapılandırma hakkında daha fazla bilgi edinebilirsiniz
 
### <a name="hybrid-connections"></a>Karma Bağlantılar

App Service Karma Bağlantılar, uygulamalarınızın belirtilen TCP uç noktalarına **giden** çağrılar yapmasına olanak sağlar. Uç nokta, bağlantı noktası 443 ' da Azure 'a giden trafiğe izin veren bir sanal ağda veya herhangi bir yerde olabilir. Bu özellik, Windows Server 2012 veya daha yeni bir konakta Karma Bağlantı Yöneticisi (HCM) adlı bir geçiş aracısının yüklenmesini gerektirir. HCM 'nin 443 numaralı bağlantı noktasında Azure Relay erişebiliyor olması gerekir. HCM, portalda App Service Karma Bağlantılar kullanıcı arabiriminden indirilebilir. 

![Karma Bağlantılar ağ akışı](media/networking-features/hybrid-connections.png)

App Service Karma Bağlantılar özelliği Azure Relay Karma Bağlantılar özelliği üzerine kurulmuştur. App Service, yalnızca uygulamanızdan bir TCP ana bilgisayarına ve bağlantı noktasına giden çağrılar yapmayı destekleyen özelliğin özelleşmiş bir biçimini kullanır. Bu konak ve bağlantı noktasının yalnızca HCM 'nin yüklendiği konakta çözümlenmesi gerekir. App Service ' deki uygulama, karma bağlantınızda tanımlanan ana bilgisayar ve bağlantı noktası üzerinde bir DNS araması yaparken, karma bağlantı üzerinden ve Karma Bağlantı Yöneticisi dışarı geçmek için trafik otomatik olarak yönlendirilir. Karma Bağlantılar hakkında daha fazla bilgi edinmek için App Service belgeleri okuyun [karma bağlantılar][hybridconn]

Bu özellik yaygın olarak şu şekilde kullanılır:

* Azure 'a bir VPN veya ExpressRoute ile bağlı olmayan özel ağlardaki kaynaklara erişin
* Destek veritabanlarını da taşımaya gerek kalmadan şirket içi uygulamaların App Service ve kaydırma desteğini destekleme  
* Karma bağlantı başına tek bir konağa ve bağlantı noktasına güvenli bir şekilde erişim sağlama. Birçok ağ özelliği, bir ağa ve yalnızca Karma Bağlantılar ile erişebileceğiniz tek bir ana bilgisayar ve bağlantı noktasına sahip olan erişim için açık erişime sahiptir.
* Diğer giden bağlantı yöntemleri kapsamında bulunmayan kapak senaryoları
* Uygulamaların şirket içi kaynaklardan kolayca yararlanabilen App Service geliştirme yapın 

Özelliği, gelen güvenlik duvarı deliği olmadan şirket içi kaynaklara erişim sağladığından, geliştiriciler ile popüler olur. Diğer giden App Service ağ özellikleri, çok fazla Azure sanal ağ ile ilgilidir. Karma Bağlantılar, VNet üzerinden geçiş yapmak için bir bağımlılığa sahip değildir ve çok çeşitli ağ ihtiyaçları için kullanılabilir. App Service Karma Bağlantılar özelliğinin üzerinde yaptığınız işlemleri ilgilenmediğini veya bilmediğini unutmayın. Diğer bir deyişle, bir ana bilgisayar üzerinde bir veritabanına, Web hizmetine veya rastgele bir TCP yuvasına erişmek için bunu kullanabilirsiniz. Özelliği temel olarak TCP paketlerini tünellerden oluşturur. 

Karma Bağlantılar, geliştirme için popüler olsa da çok sayıda üretim uygulamasında da kullanılır. Bir Web hizmetine veya veritabanına erişmek için idealdir, ancak birçok bağlantı oluşturma ile ilgili durumlar için uygun değildir. 

### <a name="gateway-required-vnet-integration"></a>Ağ Geçidi gerekli VNet tümleştirmesi 

Ağ Geçidi gerekli App Service VNet tümleştirme özelliği, uygulamanızın Azure sanal ağına **giden** istek yapmasına olanak sağlar. Özelliği, uygulamanızın üzerinde çalıştığı konak sanal ağ geçidine, Noktadan siteye VPN ile bağlanarak çalışır. Özelliği yapılandırdığınızda, uygulamanız her örneğe atanan Noktadan siteye adreslerinden birini alır. Bu özellik, herhangi bir bölgedeki klasik veya Kaynak Yöneticisi sanal ağlarda bulunan kaynaklara erişmenizi sağlar. 

![Ağ Geçidi gerekli VNet tümleştirmesi](media/networking-features/gw-vnet-integration.png)

Bu özellik, diğer sanal ağlarda bulunan kaynaklara erişme sorununu çözer ve sanal ağdan diğer sanal ağlara veya hatta şirket içine bağlanmak için de kullanılabilir. ExpressRoute bağlantılı VNET 'ler ile çalışmaz, ancak siteden siteye VPN 'ye bağlı ağlarla çalışır. ASE zaten VNet 'iniz içinde olduğundan, bu özelliği bir App Service Ortamı (ASE) içindeki bir uygulamadan kullanmak genellikle uygun değildir. Bu özelliğin çözdüğü kullanım örnekleri şunlardır:

* Azure sanal ağlarınızdaki özel IP 'lerde kaynaklara erişme 
* Siteden siteye VPN varsa şirket içi kaynaklara erişme 
* Eşlenen VNET 'lerdeki kaynaklara erişme 

Bu özellik etkinleştirildiğinde, uygulamanız hedef VNet 'in yapılandırıldığı DNS sunucusunu kullanır. [App Service VNET tümleştirmesi][vnetintegrationp2s]hakkındaki belgelerde bu özellikten daha fazla bilgi edinebilirsiniz. 

### <a name="vnet-integration"></a>Sanal Ağ Tümleştirmesi

Ağ Geçidi gereken VNet tümleştirme özelliği çok faydalı ancak ExpressRoute genelindeki kaynaklara erişimi hala çözmüyor. ExpressRoute bağlantılarına ulaşmaya gerek duyan en üstte, uygulamaların hizmet uç noktası güvenliği olan hizmetlere çağrı yapabilmeleri için bir gereksinim vardır. Bu ek gereksinimlerinizin her ikisini de çözümlemek için başka bir VNet tümleştirme özelliği eklenmiştir. Yeni VNet tümleştirme özelliği, uygulamanızın arka ucunu aynı bölgedeki bir Kaynak Yöneticisi VNet içindeki bir alt ağa yerleştirmenizi sağlar. Bu özellik, zaten VNet içinde olan bir App Service Ortamı kullanılamıyor. Bu özellik şunları yapmanıza olanak sağlar:

* Aynı bölgedeki Kaynak Yöneticisi sanal ağlarda bulunan kaynaklara erişme
* Hizmet uç noktaları ile güvenliği sağlanmış kaynaklara erişme 
* ExpressRoute veya VPN bağlantıları üzerinden erişilebilen kaynaklara erişme

![Sanal Ağ Tümleştirmesi](media/networking-features/vnet-integration.png)

Bu özellik önizlemededir ve üretim iş yükleri için kullanılmamalıdır. Bu özellik hakkında daha fazla bilgi edinmek için [App Service VNET tümleştirmesinde][vnetintegration]belgeleri okuyun.

## <a name="app-service-environment"></a>App Service Ortamı 

App Service Ortamı (ASE), VNet 'iniz içinde çalışan Azure App Service tek bir kiracı dağıtımsıdır. ATıCı, şöyle bir kullanım durumu sunar:

* VNet 'iniz içindeki kaynaklara erişin
* ExpressRoute genelindeki kaynaklara erişin
* Uygulamalarınızı VNet 'iniz içindeki özel bir adresle kullanıma sunma 
* Hizmet uç noktaları genelinde kaynaklara erişin 

ASE ile, ASE zaten VNet 'iniz içinde olduğundan, VNet tümleştirme veya hizmet uç noktaları gibi özellikleri kullanmanız gerekmez. Hizmet uç noktaları üzerinden SQL veya depolama gibi kaynaklara erişmek istiyorsanız Ao alt ağında hizmet uç noktalarını etkinleştirin. VNet 'teki kaynaklara erişmek istiyorsanız ek yapılandırma gerekmez.  ExpressRoute genelindeki kaynaklara erişmek istiyorsanız, zaten VNet 'te bulunur ve ASE veya içindeki uygulamalar üzerinde herhangi bir şeyi yapılandırmanız gerekmez. 

Bir ıLB Ao 'daki uygulamalar özel bir IP adresi üzerinde sunulebildiğinden, yalnızca İnternet 'e istediğiniz uygulamaları kullanıma sunmak için WAF cihazlarını kolayca ekleyebilirsiniz ve REST güvenliğini koruyun. Çok katmanlı uygulamaların kolayca geliştirilmesi için kendisini katlayın. 

Bir Ao 'dan gelen çok kiracılı hizmetten henüz mümkün olmayan bazı şeyler vardır. Bunlar gibi şeyler şunlardır:

* Uygulamalarınızı özel bir IP adresi üzerinde kullanıma sunma
* Uygulamanızın bir parçası olmayan ağ denetimleriyle tüm giden trafiği güvenli hale getirin 
* Uygulamalarınızı tek bir kiracı hizmetinde barındırın 
* Çok kiracılı hizmette mümkün olandan çok daha fazla örneğe kadar ölçeklendirin 
* Özel CA güvenliği uç noktaları ile uygulamalarınız tarafından kullanılmak üzere özel CA istemci sertifikaları yükleyin 
* Uygulama düzeyinde devre dışı bırakabilme özelliği olmadan sistemde barındırılan tüm uygulamalarda TLS 1,1 ' i zorla 
* Ao 'da herhangi bir müşteriyle paylaşılmayan tüm uygulamalar için ayrılmış bir giden adres sağlayın 

![VNet 'te ASE](media/networking-features/app-service-environment.png)

Ao, yalıtılmış ve adanmış uygulama barındırma konusunda en iyi hikayeyi sağlar ancak bazı yönetim zorlukları ile birlikte gelir. İşlemsel AI 'yi kullanmadan önce göz önünde bulundurmanız gereken bazı noktalar şunlardır:
 
 * ASE, VNet 'iniz içinde çalışır, ancak sanal ağın dışında bağımlılıklara sahip olabilir. Bu bağımlılıklara izin verilmelidir. [App Service ortamı Için ağ ile ilgili dikkat edilmesi gerekenler][networkinfo] hakkında daha fazla bilgi
 * ATıCı, çok kiracılı hizmet gibi hemen ölçeklenmez. Yeniden etkin ölçekleme yerine ölçekleme ihtiyaçlarını önyüklemeniz gerekir. 
 * ATıCı, kendisiyle ilişkili daha yüksek maliyetli bir ön maliyete sahiptir. Ao 'dan en iyi şekilde yararlanmak için, çok sayıda iş yükünü küçük bir çaba için kullanılması yerine bir asa 'ya yerleştirmeyi planlamanız gerekir
 * Bir Ao 'daki uygulamalar, bazı uygulamalara erişimi başka bir şekilde kısıtlayamaz.
 * Ao, bir alt ağ içinde ve tüm ağ kuralları o Ao 'dan gelen ve giden tüm trafiğe uygulanır. Yalnızca bir uygulama için gelen trafik kuralları atamak istiyorsanız, erişim kısıtlamalarını kullanın. 

## <a name="combining-features"></a>Özellikleri birleştirme 

Çok kiracılı hizmet için belirtilen özellikler, daha ayrıntılı kullanım durumlarını çözümlemek için birlikte kullanılabilir. Daha yaygın kullanım örneklerinin ikisi burada açıklanmıştır, ancak bunlar yalnızca örnektir. Çeşitli özelliklerin ne yaptığını anlayarak, neredeyse tüm sistem mimarisi ihtiyaçlarınızı çözebilirsiniz.

### <a name="inject-app-into-a-vnet"></a>Uygulamayı VNet 'e ekleme

Genel bir istek, uygulamanızı VNet 'e yerleştirme hakkında bir uygulamadır. Uygulamanızı VNet 'e koymak, bir uygulamanın gelen ve giden uç noktalarının VNet içinde olduğu anlamına gelir. ATıCı, bu sorunu çözmek için en iyi çözümü sağlar, ancak özellikleri birleştirerek çok kiracılı hizmette nelerin gerekli olduğunu de elde edebilirsiniz. Örneğin, yalnızca intranet uygulamalarını özel gelen ve giden adresleriyle barındırabilmeniz gerekir:

* Özel gelen ve giden adresiyle Application Gateway oluşturma
* Hizmet uç noktaları ile uygulamanıza gelen trafiğin güvenliğini sağlama 
* Uygulamanızın arka ucu VNet 'iniz içinde olduğundan, yeni VNet tümleştirmesini kullanın 

Bu dağıtım stili size internet 'e giden trafik için özel bir adres vermeyebilir veya sizin uygulamanızdaki tüm giden trafiği kilitleme olanağı sağlar.  Bu dağıtım stili size yalnızca bir ASE ile alacağınız kadar çok şey sağlar. 

### <a name="create-multi-tier-applications"></a>Çok katmanlı uygulamalar oluşturma

Çok katmanlı bir uygulama, API arka uç uygulamalarına yalnızca ön uç katmanından erişilebilecekleri bir uygulamadır. Çok katmanlı bir uygulama oluşturmak için şunları yapabilirsiniz:

* Ön uç Web uygulamanızın arka ucunu VNet 'teki bir alt ağla bağlamak için VNet tümleştirmesini kullanın
* Yalnızca ön uç Web uygulamanız tarafından kullanılan alt ağdan gelen trafiği API uygulamanıza güvenli hale getirmek için hizmet uç noktalarını kullanın

![çok katmanlı uygulama](media/networking-features/multi-tier-app.png)

Birden çok ön uç uygulamanız, API uygulamasındaki diğer ön uç uygulamalardan ve hizmet uç noktalarından, alt ağları ile aynı API uygulamasını kullanarak aynı API uygulamasını kullanabilir.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
