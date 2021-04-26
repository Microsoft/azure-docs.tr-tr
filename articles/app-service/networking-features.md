---
title: Ağ özellikleri
description: Azure App Service ağ özellikleri hakkında bilgi edinin ve güvenlik veya diğer işlevler için hangi özellikleri gerektiğini öğrenin.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/26/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9ba85ecfe2b57ceb1eed5c51929107a95f5a4669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889153"
---
# <a name="app-service-networking-features"></a>App Service ağ özellikleri

Azure App Service uygulamalarını birden çok şekilde dağıtabilirsiniz. Varsayılan olarak, App Service barındırılan uygulamalara doğrudan Internet üzerinden erişilebilir ve yalnızca İnternet 'te barındırılan uç noktalara ulaşabilir. Ancak birçok uygulama için gelen ve giden ağ trafiğini kontrol etmeniz gerekir. App Service, bu ihtiyaçları karşılamanıza yardımcı olmak için çeşitli özellikler vardır. Bu zorluk, belirli bir sorunu çözmek için hangi özelliği kullanacağınızı öğrenmektir. Bu makale, bazı örnek kullanım örneklerine göre hangi özelliğin kullanılacağını belirlemenize yardımcı olur.

Azure App Service için iki ana dağıtım türü vardır: 
- Çok kiracılı genel hizmeti ücretsiz, paylaşılan, temel, standart, Premium, PremiumV2 ve PremiumV3 fiyatlandırma SKU 'larında App Service planları barındırır. 
- Tek kiracılı App Service Ortamı (Ao), doğrudan Azure sanal ağınızda yalıtılmış SKU App Service planları barındırır. 

Kullandığınız özellikler, çok müşterili hizmette veya Ao 'da olmasına bağlı olarak değişir. 

## <a name="multitenant-app-service-networking-features"></a>Çok kiracılı App Service ağ özellikleri 

Azure App Service dağıtılmış bir sistemdir. Gelen HTTP veya HTTPS isteklerini işleyen rollere *ön uçlar* denir. Müşteri iş yükünü barındıran rollere *çalışanlar* adı verilir. Bir App Service dağıtımındaki tüm roller çok kiracılı bir ağda bulunur. Aynı App Service ölçek biriminde birçok farklı müşteri olduğundan, App Service ağını doğrudan ağınıza bağlanamazsınız. 

Ağları bağlamak yerine, uygulama iletişiminin çeşitli yönlerini işlemek için özelliklere ihtiyacınız vardır. *Uygulamanızdaki istekleri işleyen* özellikler, *uygulamanızdan çağrılar yaparken* sorunları çözümlemek için kullanılamaz. Benzer şekilde, uygulamanızdaki çağrılara yönelik sorunları çözebilecek özellikler, uygulamanızdaki sorunları çözümlemek için kullanılamaz.  

| Gelen özellikler | Giden Özellikler |
|---------------------|-------------------|
| Uygulama tarafından atanan adres | Karma Bağlantılar |
| Erişim kısıtlamaları | Ağ Geçidi-gerekli VNet tümleştirmesi |
| Hizmet uç noktaları | Sanal Ağ Tümleştirmesi |
| Özel uç noktalar ||

Not edilen özel durumlar dışında, bu özelliklerin tümünü birlikte kullanabilirsiniz. Sorunlarınızı gidermek için özellikleri karıştırabilirsiniz.

## <a name="use-cases-and-features"></a>Kullanım örnekleri ve Özellikler

Belirli bir kullanım durumu için, sorunu çözmenin birkaç yolu olabilir. En iyi özelliğin seçilmesi, bazen kullanım durumunun ötesinde gelir. Aşağıdaki gelen kullanım örnekleri, uygulamanıza giden trafiği denetleme sorunlarını gidermek için App Service ağ özelliklerini kullanmayı önerir:
 
| Gelen kullanım durumu | Özellik |
|---------------------|-------------------|
| Uygulamanız için IP tabanlı SSL gereksinimlerini destekleme | Uygulama tarafından atanan adres |
| Uygulamanız için paylaşılmayan adanmış gelen adresi destekleme | Uygulama tarafından atanan adres |
| Uygulamanıza erişimi iyi tanımlanmış bir adres kümesinden sınırlayın | Erişim kısıtlamaları |
| Bir sanal ağdaki kaynaklardan uygulamanıza erişimi kısıtlayın | Hizmet uç noktaları </br> ıLB ATıCı </br> Özel uç noktalar |
| Uygulamanızı sanal ağınızda özel bir IP 'de kullanıma sunma | ıLB ATıCı </br> Özel uç noktalar </br> Hizmet uç noktaları olan bir Application Gateway örneğindeki gelen trafik için özel IP |
| Uygulamanızı bir Web uygulaması güvenlik duvarı (WAF) ile koruma | Application Gateway ve ıLB aşırı </br> Özel uç noktalarla Application Gateway </br> Hizmet uç noktalarıyla Application Gateway </br> Erişim kısıtlamalarına sahip Azure ön kapısı |
| Farklı bölgelerde uygulamalarınıza trafiği yük dengeleme | Erişim kısıtlamalarına sahip Azure ön kapısı | 
| Aynı bölgedeki Yük Dengeleme trafiği | [Hizmet uç noktalarıyla Application Gateway][appgwserviceendpoints] | 

Aşağıdaki giden kullanım örnekleri, uygulamanız için giden erişim ihtiyaçlarını çözümlemek üzere App Service ağ özelliklerini kullanmayı önerir:

| Giden kullanım durumu | Özellik |
|---------------------|-------------------|
| Aynı bölgedeki bir Azure sanal ağındaki kaynaklara erişme | Sanal Ağ Tümleştirmesi </br> ASE |
| Farklı bir bölgedeki Azure sanal ağındaki kaynaklara erişme | Ağ Geçidi-gerekli VNet tümleştirmesi </br> ATıCı ve sanal ağ eşlemesi |
| Hizmet uç noktaları ile korunan kaynaklara erişin | Sanal Ağ Tümleştirmesi </br> ASE |
| Azure 'a bağlı olmayan özel bir ağda kaynaklara erişme | Karma Bağlantılar |
| Azure ExpressRoute devrelerine göre kaynaklara erişme | Sanal Ağ Tümleştirmesi </br> ASE | 
| Web uygulamanızdan giden trafiği güvenli hale getirme | VNet tümleştirmesi ve ağ güvenlik grupları </br> ASE | 
| Web uygulamanızdan giden trafiği yönlendirme | VNet tümleştirmesi ve rota tabloları </br> ASE | 


### <a name="default-networking-behavior"></a>Varsayılan ağ davranışı

Azure App Service ölçek birimleri her dağıtımda çok sayıda müşteriyi destekler. Ücretsiz ve paylaşılan SKU planları, çok kiracılı çalışanlar üzerinde müşteri iş yüklerini barındırır. Temel ve daha yüksek planlar, yalnızca bir App Service planına ayrılmış müşteri iş yüklerini barındırır. Standart bir App Service planınız varsa, Bu plandaki tüm uygulamalar aynı çalışan üzerinde çalışır. Çalışanı ölçeklendirirseniz, bu App Service planındaki tüm uygulamalar, App Service planınızdaki her bir örnek için yeni bir çalışan üzerinde çoğaltılır. 

#### <a name="outbound-addresses"></a>Giden adresler

Çalışan VM 'Ler App Service planlarına göre büyük bir bölümde bölünür. Ücretsiz, paylaşılan, temel, standart ve Premium planlar aynı çalışan sanal makine türünü kullanır. PremiumV2 planı başka bir VM türünü kullanır. PremiumV3, henüz başka bir VM türünü kullanır. VM ailesini değiştirdiğinizde, farklı bir giden adres kümesi alırsınız. Standart 'dan PremiumV2 'ye ölçeklendirirseniz, giden adresleriniz değişecektir. PremiumV2 'den PremiumV3 'ye ölçeklendirirseniz, giden adresleriniz değişecektir. Daha eski ölçek birimlerinde, standart olan PremiumV2 ' e ölçeklendirirseniz hem gelen hem de giden adresler değişecektir. 

Giden çağrılar için kullanılan birkaç adres vardır. Uygulamanız tarafından giden çağrıları yapmak için kullanılan giden adresler uygulamanızın özelliklerinde listelenir. Bu adresler, App Service dağıtımında aynı çalışan VM ailesinde çalışan tüm uygulamalar tarafından paylaşılır. Uygulamanızın bir ölçek biriminde kullanabileceği tüm adresleri görmek isterseniz, `possibleOutboundAddresses` Bu özelliği listeleyen adlı bir özellik bulabilirsiniz. 

![Uygulama özelliklerini gösteren ekran görüntüsü.](media/networking-features/app-properties.png)

App Service, hizmeti yönetmek için kullanılan birkaç uç nokta içerir.  Bu adresler ayrı bir belgede yayımlanır ve `AppServiceManagement` IP hizmeti etiketinde de bulunur. `AppServiceManagement`Etiketi yalnızca bu tür trafiğe izin vermeniz gereken App Service ortamlarında kullanılır. App Service gelen adresler `AppService` IP hizmeti etiketinde izlenir. App Service tarafından kullanılan giden adresleri içeren bir IP hizmeti etiketi yoktur. 

![Gelen ve giden trafiği App Service gösteren diyagram.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Uygulama tarafından atanan adres 

Uygulama tarafından atanan adres özelliği, IP tabanlı SSL özelliği üzerinde bir yük atar. Uygulamanızla birlikte SSL ayarlayarak bu uygulamaya erişirsiniz. Bu özelliği, IP tabanlı SSL çağrıları için kullanabilirsiniz. Uygulamanızı yalnızca sahip olduğu bir adres sağlamak için de kullanabilirsiniz. 

![Uygulama tarafından atanan adresi gösteren diyagram.](media/networking-features/app-assigned-address.png)

Uygulama tarafından atanan bir adres kullandığınızda, trafiğiniz hala App Service ölçek birimine gelen tüm trafiği işleyen ön uç rollerinden geçer. Ancak uygulamanıza atanan adres yalnızca uygulamanız tarafından kullanılır. Bu özellik için kullanım örnekleri:

* Uygulamanız için IP tabanlı SSL gereksinimlerini destekler.
* Uygulamanız için paylaşılmayan bir özel adres ayarlayın.

Uygulamanızda bir adres ayarlamayı öğrenmek için [Azure App Service BIR TLS/SSL sertifikası ekleme][appassignedaddress]bölümüne bakın. 

### <a name="access-restrictions"></a>Erişim kısıtlamaları 

Erişim kısıtlamaları *gelen* istekleri filtrelemenize izin verir. Filtreleme eylemi, uygulamalarınızın çalıştığı çalışan rollerden yukarı akış olan ön uç rollerde gerçekleşir. Ön uç rolleri çalışanlardan yukarı akış olduğundan, erişim kısıtlamalarını uygulamalarınız için ağ düzeyinde koruma olarak düşünebilirsiniz. 

Bu özellik, öncelik sırasına göre değerlendirilen izin verme ve reddetme kurallarının bir listesini oluşturmanızı sağlar. Azure ağ iletişimi 'nin ağ güvenlik grubu (NSG) özelliğine benzer. Bu özelliği bir ate veya çok kiracılı hizmette kullanabilirsiniz. Bir ıLB Ao veya özel uç nokta ile kullandığınızda, erişimi özel adres bloklarından kısıtlayabilirsiniz.
> [!NOTE]
> Uygulama başına en fazla 512 erişim kısıtlama kuralı yapılandırılabilir. 

![Erişim kısıtlamalarını gösteren diyagram.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>IP tabanlı erişim kısıtlama kuralları

IP tabanlı erişim kısıtlamaları özelliği, uygulamanıza ulaşmak için kullanılabilecek IP adreslerini kısıtlamak istediğinizde yardımcı olur. Hem IPv4, hem IPv6 desteklenir. Bu özellik için bazı kullanım örnekleri:
* Uygulamanıza erişimi, iyi tanımlanmış bir adresler kümesinden kısıtlayın. 
* Dış Yük Dengeleme hizmetinden veya bilinen çıkış IP adreslerine sahip diğer ağ gereçlerine gelen trafiğe erişimi kısıtlayın. 

Bu özelliği nasıl etkinleştireceğinizi öğrenmek için bkz. [erişim kısıtlamalarını yapılandırma][iprestrictions].

> [!NOTE]
> IP tabanlı erişim kısıtlama kuralları yalnızca uygulamanız bir App Service Ortamı olduğunda sanal ağ adres aralıklarını işler. Uygulamanız çok kiracılı bir hizmettir, trafiği sanal ağınızdaki alt ağları seçmek üzere kısıtlamak için [hizmet uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) kullanmanız gerekir.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Hizmet uç noktalarına dayalı erişim kısıtlama kuralları 

Hizmet uç noktaları, kaynak adresinin seçtiğiniz bir alt ağ kümesinden gelmesi için uygulamanıza *gelen* erişimi kilitlemenize imkan tanır. Bu özellik IP erişim kısıtlamalarıyla birlikte çalışmaktadır. Hizmet uç noktaları uzaktan hata ayıklama ile uyumlu değil. Uygulamanızda uzaktan hata ayıklamayı kullanmak istiyorsanız, istemciniz hizmet uç noktaları etkinleştirilmiş bir alt ağda olamaz. Hizmet uç noktalarını ayarlama işlemi, IP erişim kısıtlamalarını ayarlama sürecine benzerdir. Sanal ağlarınızdaki ortak adresleri ve alt ağları içeren erişim kuralları için bir izin verme/reddetme listesi oluşturabilirsiniz. 

Bu özellik için bazı kullanım örnekleri:

* Uygulamanıza gelen trafiği kilitlemek için uygulamanızla bir uygulama ağ geçidi ayarlayın.
* Sanal ağınızdaki kaynaklarla uygulamanıza erişimi kısıtlayın. Bu kaynaklar VM 'Leri, ASE 'yi veya VNet tümleştirmesi kullanan diğer uygulamaları içerebilir. 

![Application Gateway ile hizmet uç noktalarının kullanımını gösteren diyagram.](media/networking-features/service-endpoints-appgw.png)

Hizmet uç noktalarını uygulamanızla yapılandırma hakkında daha fazla bilgi için bkz. [Azure App Service erişim kısıtlamaları][serviceendpoints].

#### <a name="access-restriction-rules-based-on-service-tags"></a>Hizmet etiketlerine dayalı erişim kısıtlama kuralları

[Azure hizmet etiketleri][servicetags] , Azure HIZMETLERI için IP adreslerinin iyi tanımlanmış kümeleridir. Hizmet etiketleri, çeşitli Azure hizmetlerinde kullanılan IP aralıklarını gruplar ve genellikle belirli bölgelerin kapsamına alınır. Bu, belirli Azure hizmetlerinden *gelen* trafiği filtrelemenizi sağlar. 

Etiketlerin tam listesi ve daha fazla bilgi için yukarıdaki hizmet etiketi bağlantısını ziyaret edin. Bu özelliği nasıl etkinleştireceğinizi öğrenmek için bkz. [erişim kısıtlamalarını yapılandırma][iprestrictions].

#### <a name="http-header-filtering-for-access-restriction-rules"></a>Erişim kısıtlama kuralları için http üst bilgisi filtrelemesi

Her erişim kısıtlama kuralı için, ek http üst bilgisi filtrelemesi ekleyebilirsiniz. Bu, gelen isteği ve belirli http üst bilgi değerlerine göre filtrelemenizi sağlar. Her üstbilginin kural başına en fazla 8 değeri olabilir. Aşağıdaki http üstbilgileri listesi şu anda desteklenmektedir: 
* X-Iletilmiş-Için
* X-Iletilen-konak
* X-Azure-FDıD
* X-FD-Healtharaştırması

Http üst bilgisi filtrelemesinin bazı kullanım örnekleri şunlardır:
* Ana bilgisayar adını ileten proxy sunucularından gelen trafiğe erişimi kısıtla
* Belirli bir Azure ön kapısına erişimi bir hizmet etiketi kuralıyla ve X-Azure-FDıD üst bilgi kısıtlamasıyla kısıtla

### <a name="private-endpoint"></a>Özel Uç Nokta

Özel uç nokta, Web uygulamanıza Azure özel bağlantısı tarafından özel olarak ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan, Web uygulamasını etkin bir şekilde sanal ağınıza getiren özel bir IP adresi kullanır. Bu özellik yalnızca Web uygulamanıza *gelen* akışlara yöneliktir.
Daha fazla bilgi için bkz. [Azure Web uygulaması için özel uç noktaları kullanma][privateendpoints].

Bu özellik için bazı kullanım örnekleri:

* Bir sanal ağdaki kaynaklardan uygulamanıza erişimi kısıtlayın. 
* Uygulamanızı sanal ağınızdaki özel bir IP üzerinde kullanıma sunun. 
* Uygulamanızı bir WAF ile koruyun.

Özel uç noktalar, yapılandırılmış olduğu uygulama olan tek şey, veri taşmasını engeller. 
 
### <a name="hybrid-connections"></a>Karma Bağlantılar

App Service Karma Bağlantılar, uygulamalarınızın belirtilen TCP uç noktalarına *giden* çağrılar yapmasına olanak sağlar. Uç nokta şirket içinde, bir sanal ağda veya 443 numaralı bağlantı noktasında Azure 'a giden trafiğe izin veren herhangi bir yerde olabilir. Özelliğini kullanmak için, Windows Server 2012 veya daha yeni bir konakta Karma Bağlantı Yöneticisi adlı bir geçiş aracısı yüklemeniz gerekir. Karma Bağlantı Yöneticisi 443 numaralı bağlantı noktasında Azure Relay erişebiliyor olması gerekir. Karma Bağlantı Yöneticisi portalda App Service Karma Bağlantılar kullanıcı arabiriminden indirebilirsiniz. 

![Karma Bağlantılar ağ akışını gösteren diyagram.](media/networking-features/hybrid-connections.png)

App Service Karma Bağlantılar Azure Relay Karma Bağlantılar özelliği üzerine kurulmuştur. App Service, yalnızca uygulamanızdan bir TCP ana bilgisayarına ve bağlantı noktasına giden çağrılar yapmayı destekleyen özelliğin özelleşmiş bir biçimini kullanır. Bu konak ve bağlantı noktasının yalnızca Karma Bağlantı Yöneticisi yüklendiği konakta çözümlenmesi gerekir. 

App Service ' deki uygulama, karma bağlantınızda tanımlanan ana bilgisayar ve bağlantı noktası üzerinde bir DNS araması yapar, trafik otomatik olarak karma bağlantı ve Karma Bağlantı Yöneticisi dışına dönerek yeniden yönlendirilir. Daha fazla bilgi için bkz. [App Service karma bağlantılar][hybridconn].

Bu özellik yaygın olarak şu şekilde kullanılır:

* Azure 'a bir VPN veya ExpressRoute ile bağlı olmayan özel ağlardaki kaynaklara erişin.
* Destekleyici veritabanlarını taşımaya gerek olmadan şirket içi uygulamaların App Service geçirilmesini destekler.  
* Karma bağlantı başına tek bir konağa ve bağlantı noktasına geliştirilmiş güvenlik ile erişim sağlama. Çoğu ağ özellikleri bir ağa erişimi açar. Karma Bağlantılar ile yalnızca tek bir konağa ve bağlantı noktasına ulaşabilirsiniz.
* Diğer giden bağlantı yöntemlerinin kapsamadığı kapak senaryoları.
* App Service geliştirmeyi, uygulamaların şirket içi kaynakları kolayca kullanmasına olanak verecek şekilde gerçekleştirin. 

Bu özellik, gelen güvenlik duvarı deliği olmadan şirket içi kaynaklara erişim sağladığından, geliştiriciler sayesinde popüler olur. Diğer giden App Service ağ özellikleri Azure sanal ağı ile ilgilidir. Karma Bağlantılar, bir sanal ağ üzerinden çalışmaya bağlı değildir. Bu, daha çok çeşitli ağ ihtiyaçları için kullanılabilir. 

App Service Karma Bağlantılar üzerinde yaptığınız işlemin farkında olduğunu unutmayın. Bu nedenle, bir ana bilgisayar üzerinde bir veritabanına, Web hizmetine ya da rastgele bir TCP yuvasına erişmek için kullanabilirsiniz. Özelliği temel olarak TCP paketlerini tünellerden oluşturur. 

Geliştirme için popüler Karma Bağlantılar, ancak üretim uygulamalarında da kullanılır. Bir Web hizmetine veya veritabanına erişmek için idealdir, ancak birçok bağlantı oluşturmayı içeren durumlar için uygun değildir. 

### <a name="gateway-required-vnet-integration"></a>Ağ Geçidi-gerekli VNet tümleştirmesi 

Ağ Geçidi-gerekli App Service VNet tümleştirmesi, uygulamanızın Azure sanal ağına *giden* istek yapmasına olanak sağlar. Özelliği, bir noktadan siteye VPN kullanarak uygulamanızın üzerinde çalıştığı ana bilgisayarı sanal ağınızdaki bir sanal ağ geçidine bağlayarak çalışır. Özelliği yapılandırdığınızda, uygulamanız her örneğe atanan Noktadan siteye adreslerinden birini alır. Bu özellik, herhangi bir bölgedeki klasik veya Azure Resource Manager sanal ağlardaki kaynaklara erişmenizi sağlar. 

![Ağ geçidine gereken VNet tümleştirmesini gösteren diyagram.](media/networking-features/gw-vnet-integration.png)

Bu özellik, diğer sanal ağlardaki kaynaklara erişme sorununu çözer. Bu, sanal bir ağ üzerinden diğer sanal ağlara veya şirket içine bağlanmak için de kullanılabilir. ExpressRoute bağlantılı sanal ağlarla çalışmaz, ancak siteden siteye VPN 'ye bağlı ağlarla çalışır. ATıCı zaten sanal ağınızda olduğundan, bu özelliği bir App Service Ortamı (Ao) içindeki bir uygulamadan kullanmak genellikle uygun değildir. Bu özellik için kullanım örnekleri:

* Azure sanal ağlarınızdaki özel IP 'lerde kaynaklara erişin. 
* Siteden siteye VPN varsa, şirket içi kaynaklara erişin. 
* Eşlenen sanal ağlardaki kaynaklara erişin. 

Bu özellik etkinleştirildiğinde, uygulamanız hedef sanal ağın yapılandırıldığı DNS sunucusunu kullanır. Bu özellik hakkında daha fazla bilgi için bkz. [VNET tümleştirmesi App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Sanal Ağ Tümleştirmesi

Ağ Geçidi-gerekli VNet tümleştirmesi kullanışlıdır, ancak ExpressRoute genelindeki kaynaklara erişme sorununu çözmez. ExpressRoute bağlantılarına ulaşmaya gerek duyan en üstünde, uygulamaların hizmet uç noktası ile güvenliği sağlanan hizmetlere çağrı yapabilmeleri için bir gereksinim vardır. Başka bir VNet tümleştirme özelliği bu ihtiyaçları karşılayabilir. 

Yeni VNet tümleştirme özelliği, uygulamanızın arka sonunu, uygulamanızla aynı bölgedeki Kaynak Yöneticisi sanal bir ağda bir alt ağa yerleştirmenizi sağlar. Bu özellik, zaten bir sanal ağda olan bir App Service Ortamı kullanılamıyor. Bu özellik için kullanım örnekleri:

* Aynı bölgedeki Kaynak Yöneticisi sanal ağlarda bulunan kaynaklara erişin.
* Hizmet uç noktaları ile korunan kaynaklara erişin. 
* ExpressRoute veya VPN bağlantıları üzerinden erişilebilen kaynaklara erişin.
* Tüm giden trafiğin güvenliğini sağlamaya yardımcı olma. 
* Tüm giden trafiğe tünel zorla. 

![VNet tümleştirmesini gösteren diyagram.](media/networking-features/vnet-integration.png)

Daha fazla bilgi için bkz. [VNET tümleştirmesi App Service][vnetintegration].

### <a name="app-service-environment"></a>App Service Ortamı 

App Service Ortamı (Ao), sanal ağınızda çalışan Azure App Service tek kiracılı bir dağıtımdır. Bu özellik için bazı durumlar:

* Sanal ağınızdaki kaynaklara erişin.
* ExpressRoute genelindeki kaynaklara erişin.
* Uygulamalarınızı sanal ağınızda özel bir adresle kullanıma sunun. 
* Hizmet uç noktaları genelinde kaynaklara erişin. 

ASE ile, ASE zaten sanal ağınızda olduğundan VNet tümleştirme veya hizmet uç noktaları gibi özellikleri kullanmanız gerekmez. Hizmet uç noktaları üzerinden SQL veya Azure depolama gibi kaynaklara erişmek istiyorsanız Ao alt ağında hizmet uç noktalarını etkinleştirin. Sanal ağdaki kaynaklara erişmek istiyorsanız, herhangi bir ek yapılandırma yapmanız gerekmez. ExpressRoute genelindeki kaynaklara erişmek istiyorsanız, sanal ağda zaten var ve içindeki uygulama veya uygulamalar üzerinde herhangi bir şeyi yapılandırmanız gerekmez. 

Bir ıLB Ao 'daki uygulamalar özel bir IP adresi üzerinde sunulebildiğinden, yalnızca İnternet 'e istediğiniz uygulamaları kullanıma sunmak için WAF cihazlarını kolayca ekleyebilir ve REST güvenliğini korumaya yardımcı olabilirsiniz. Bu özellik, çok katmanlı uygulamaların geliştirilmesine daha kolay hale getirmenize yardımcı olabilir. 

Çok müşterili hizmetten bazı şeyler mümkün değildir ancak Ao 'dan mümkün değildir. İşte bazı örnekler:

* Uygulamalarınızı özel bir IP adresi üzerinde kullanıma sunun.
* Uygulamanızın bir parçası olmayan ağ denetimleriyle tüm giden trafiği güvenli hale getirmeye yardımcı olun.
* Uygulamalarınızı tek kiracılı bir hizmette barındırın. 
* Çok kiracılı hizmette mümkün olandan çok daha fazla örneğe kadar ölçeklendirin. 
* Özel CA ile güvenli uç noktalarla uygulamalarınız tarafından kullanılmak üzere özel CA istemci sertifikaları yükleyin.
* Uygulama düzeyinde devre dışı bırakabilme özelliği olmadan sistemde barındırılan tüm uygulamalarda TLS 1,1 ' i zorlayın. 
* Ao 'daki müşterilerle paylaşılmayan tüm uygulamalar için ayrılmış bir giden adres sağlayın. 

![Bir sanal ağda Ao 'yu gösteren diyagram.](media/networking-features/app-service-environment.png)

Ao, yalıtılmış ve adanmış uygulama barındırma konusunda en iyi hikayeyi sağlar, ancak bazı yönetim zorlukları içerir. İşlemsel AI kullanmadan önce göz önünde bulundurmanız gereken bazı noktalar:
 
 * Bir Ao, sanal ağınızın içinde çalışır, ancak sanal ağın dışında bağımlılıklara sahip olur. Bu bağımlılıklara izin verilmelidir. Daha fazla bilgi için bkz. [App Service ortamı Için ağ değerlendirmeleri][networkinfo].
 * ASA, çok kiracılı hizmet gibi hemen ölçeklenmez. Yeniden etkin ölçekleme yerine ölçekleme ihtiyaçlarını önyüklemeniz gerekir. 
 * ATıCı, daha yüksek bir ön maliyete sahiptir. Ao 'dan en iyi şekilde yararlanmak için, çok sayıda iş yükünü küçük çalışmalar için kullanmak yerine tek bir asa 'ya yerleştirmeniz gerekir.
 * Bir Ao 'daki uygulamalar, daha fazla değil, Ao 'daki bazı uygulamalara erişimi seçmeli olarak kısıtlayamaz.
 * Ao bir alt ağda bulunur ve tüm ağ kuralları bu Ao 'dan gelen ve giden tüm trafiğe uygulanır. Yalnızca bir uygulama için gelen trafik kuralları atamak istiyorsanız, erişim kısıtlamalarını kullanın. 

## <a name="combining-features"></a>Özellikleri birleştirme 

Çok kiracılı hizmet için belirtilen özellikler, daha ayrıntılı kullanım durumlarını çözümlemek için birlikte kullanılabilir. Daha yaygın kullanım örneklerinin ikisi burada açıklanmıştır, ancak bunlar yalnızca örnektir. Çeşitli özelliklerin ne yaptığını anlayarak, neredeyse tüm sistem mimarisi ihtiyaçlarınızı karşılayabilirsiniz.

### <a name="place-an-app-into-a-virtual-network"></a>Bir uygulamayı sanal ağa yerleştirme

Bir uygulamayı bir sanal ağa nasıl koyabileceğiniz merak ediyor olabilirsiniz. Uygulamanızı bir sanal ağa yerleştirirseniz, uygulamanın gelen ve giden uç noktaları sanal ağ içindedir. ATıCı, bu sorunu çözmenin en iyi yoludur. Ancak özellikleri birleştirerek çoklu kiracı hizmetinde gereksinimlerinizin çoğunu karşılayabilirsiniz. Örneğin, yalnızca Intranet uygulamalarını özel gelen ve giden adresleriyle barındırabilirsiniz:

* Özel gelen ve giden adreslere sahip bir uygulama ağ geçidi oluşturma.
* Hizmet uç noktaları ile uygulamanıza gelen trafiğin güvenliğini sağlama. 
* Yeni VNet tümleştirme özelliği kullanılarak uygulamanızın arka ucu sanal ağınızda olur. 

Bu dağıtım stili size internet 'e giden trafik için özel bir adres vermeyecek veya tüm giden trafiği uygulamanızdan kilitleyebilme olanağı vermez. Size yalnızca bir ASE ile alacağınız kadar çok şey verecektir. 

### <a name="create-multitier-applications"></a>Çok katmanlı uygulamalar oluşturma

Çok katmanlı bir uygulama, API arka ucu uygulamalarına yalnızca ön uç katmanından erişilebilen bir uygulamadır. Çok katmanlı bir uygulama oluşturmanın iki yolu vardır. Her ikisi de, ön uç Web uygulamanızı sanal bir ağdaki bir alt ağa bağlamak için VNet tümleştirmesini kullanarak başlayın. Bunu yaptığınızda Web uygulamanızın sanal ağınıza çağrı yapmasını olanaklı hale getirir. Ön uç uygulamanız sanal ağa bağlandıktan sonra, API uygulamanıza erişimi nasıl kilitleyecağınıza karar vermeniz gerekir. Seçenekleriniz şunlardır:

* Hem ön uç hem de API uygulamasını aynı ıLB Ao 'da barındırın ve uygulama ağ geçidi kullanarak ön uç uygulamayı internet 'e sunun.
* Ön ucu çok kiracılı hizmette ve arka uçta bir ıLB Ao 'da barındırın.
* Hem ön ucu hem de API uygulamasını çoklu kiracı hizmetinde barındırın.

Çok katmanlı bir uygulama için hem ön uç hem de API uygulamasını barındırıyorsanız şunları yapabilirsiniz:

- Sanal ağınızdaki özel uç noktaları kullanarak API uygulamanızı kullanıma sunma:

  ![İki katmanlı bir uygulamadaki özel uç noktaların kullanımını gösteren diyagram.](media/networking-features/multi-tier-app-private-endpoint.png)

- API uygulamanıza gelen trafiğin yalnızca ön uç Web uygulamanız tarafından kullanılan alt ağdan geldiğinden emin olmak için hizmet uç noktalarını kullanın:

  ![Bir uygulamanın güvenliğini sağlamaya yardımcı olmak için hizmet uç noktalarının kullanımını gösteren diyagram.](media/networking-features/multi-tier-app.png)

Hangi yöntemi kullanacağınıza karar vermenize yardımcı olacak bazı konular aşağıda verilmiştir:

* Hizmet uç noktalarını kullandığınızda, tümleştirme alt ağına yalnızca API uygulamanız için trafiğin güvenliğini sağlayabilirsiniz. Bu, API uygulamasının güvenliğini sağlamaya yardımcı olur, ancak yine de ön uç uygulamanızdan App Service 'taki diğer uygulamalara veri taşlarından yararlanmaya devam edebilirsiniz.
* Özel uç noktalar kullandığınızda, oynatma sırasında iki alt ağınız vardır ve bu da karmaşıklık ekler. Ayrıca, Özel uç nokta üst düzey bir kaynaktır ve yönetim yükü ekler. Özel uç noktaları kullanmanın avantajı, veri kaybı olasılığını ortadan kaldırmaya yarar. 

Her iki yöntem de birden çok ön uç ile çalışır. Küçük ölçekte, ön uç tümleştirme alt ağında API uygulaması için hizmet uç noktalarını etkinleştirdiğiniz için hizmet uç noktalarının kullanımı daha kolaydır. Daha fazla ön uç uygulaması eklerken, tümleştirme alt ağına sahip hizmet uç noktalarını içerecek şekilde her API uygulamasını ayarlamanız gerekir. Özel uç noktalar kullandığınızda, daha karmaşık olabilir, ancak özel bir uç nokta ayarladıktan sonra API uygulamalarınızda herhangi bir şeyi değiştirmeniz gerekmez. 

### <a name="line-of-business-applications"></a>İş kolu uygulamaları

İş kolu (LOB) uygulamaları, normalde Internet 'ten erişim için açık olmayan iç uygulamalardır. Bu uygulamalar, erişimin tamamen denetlenebileceği kurumsal ağların içinden çağrılır. ILB asa kullanıyorsanız iş kolu uygulamalarınızı barındırmak kolaydır. Çoklu kiracı hizmetini kullanıyorsanız, Özel uç noktaları kullanabilir veya bir uygulama ağ geçidiyle birleştirilmiş hizmet uç noktalarını kullanabilirsiniz. Özel uç noktaları kullanmak yerine, hizmet uç noktaları ile uygulama ağ geçidi kullanmanın iki nedeni vardır:
* LOB uygulamalarınızda WAF koruması gerekir.
* LOB uygulamalarınızın birden çok örneğine yük dengelemek istiyorsunuz.

Bu ihtiyaçlardan hiçbiri uygulanmazsa, Özel uç noktaları kullanarak daha iyi bir işlem yapabilirsiniz. App Service özel uç noktalar sayesinde, uygulamalarınızı sanal ağınızdaki özel adresler üzerinde kullanıma sunabilirsiniz. Sanal ağınıza yerleştirdiğiniz özel uç noktaya ExpressRoute ve VPN bağlantıları arasında erişilebilir. 

Özel uç noktaları yapılandırmak, uygulamalarınızı özel bir adreste kullanıma sunar, ancak DNS 'yi Şirket içinden bu adrese ulaşacak şekilde yapılandırmanız gerekir. Bu yapılandırmanın çalışmasını sağlamak için, Özel uç noktalarınızı içeren Azure DNS özel bölgesini şirket içi DNS sunucularınıza iletmeniz gerekir. Azure DNS özel bölgeler bölge iletmeyi desteklemez, ancak bu amaçla bir DNS sunucusu kullanarak bölge iletmeyi destekleyebilirsiniz. [DNS ileticisi](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) şablonu, Azure DNS özel diliminizi ŞIRKET içi DNS sunucularınıza iletmeyi kolaylaştırır.

## <a name="app-service-ports"></a>App Service bağlantı noktaları

App Service taradıysanız, gelen bağlantılar için sunulan birkaç bağlantı noktası bulacaksınız. Çok kiracılı hizmette Bu bağlantı noktalarına erişimi engellemek veya denetlemek için bir yol yoktur. Aşağıda gösterilen bağlantı noktalarının listesi verilmiştir:

| Kullanın | Bağlantı noktası veya bağlantı noktaları |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Yönetim | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio uzaktan hata ayıklama  |  4020, 4022, 4024 |
|  Web Dağıtımı hizmeti | 8172 |
|  Altyapı kullanımı | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md