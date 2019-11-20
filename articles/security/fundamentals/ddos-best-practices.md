---
title: Azure DDoS koruması-dayanıklı çözümler tasarlama | Microsoft Docs
description: Uygulamanız hakkında derin Öngörüler elde etmek için günlük verilerini nasıl kullanabileceğinizi öğrenin.
services: security
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: ac36a4c59dbec8bf27850de1565e86b78643148a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595412"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS koruması-dayanıklı çözümler tasarlama

Bu makale BT karar mekanizmaları ve güvenlik personeli içindir. Azure, ağ ve güvenlik hakkında bilgi sahibi olduğunuzu bekliyor.
DDoS, uygulama kaynaklarını tüketmeye çalışan bir saldırı türüdür. Amaç, uygulamanın kullanılabilirliğini ve meşru istekleri işleme yeteneğini etkiler. Saldırılar, boyut ve etki açısından daha karmaşık ve daha büyük hale geliyor. DDoS saldırıları internet üzerinden genel olarak erişilebilen herhangi bir uç noktasını hedefleyebilir. Dağıtılmış hizmet reddi (DDoS) dayanıklılığı için tasarlamak, çeşitli hata modlarında planlama ve tasarlama gerektirir. Azure, DDoS saldırılarına karşı sürekli koruma sağlar. Bu koruma, varsayılan olarak ve ek maliyet olmadan Azure platformunda tümleşiktir.

[Azure DDoS koruma standardı](https://azure.microsoft.com/services/ddos-protection/) , platformda Core DDoS korumasına ek olarak ağ saldırılarına karşı gelişmiş DDoS azaltma özellikleri sağlar. Belirli Azure kaynaklarınızı korumak için otomatik olarak ayarlanır. Yeni sanal ağların oluşturulması sırasında korumanın etkinleştirilmesi basittir. Ayrıca, oluşturulduktan sonra yapılabilir ve uygulama ya da kaynak değişikliği gerektirmez.

![Azure DDoS koruması 'nın, müşterileri ve bir saldırgandan bir sanal ağı koruma rolü](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Temel en iyi uygulamalar

Aşağıdaki bölümler Azure 'da DDoS-dayanıklı hizmetler oluşturmaya yönelik öngörülü rehberlik sağlar.

### <a name="design-for-security"></a>Güvenlik için tasarlama

Tasarımın, bir uygulamanın tüm yaşam döngüsünün tamamında, tasarım ve uygulama ile dağıtım ve işlemlere kadar öncelikli olduğundan emin olun. Uygulamalar görece düşük bir istek hacmine izin veren hatalara sahip olabilir ve bu da hizmet kesintisi oluşmasına neden olur. 

Microsoft Azure üzerinde çalışan bir hizmetin korunmasına yardımcı olmak için, uygulama mimarinizi iyi bir şekilde kavramanız ve [yazılım kalitesinin beş](/azure/architecture/guide/pillars)ile ilgili olarak odaklanmanız gerekir.
Tipik trafik birimlerini, uygulama ve diğer uygulamalar arasındaki bağlantı modelini ve genel İnternet 'e açık olan hizmet uç noktalarını bilmeniz gerekir.

Bir uygulamanın, uygulamanın kendisi için hedeflenen bir hizmet reddine yetecek kadar dayanıklı olmasını sağlamak, en önemli öneme sahiptir. Güvenlik ve gizlilik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl/default.aspx)Ile başlayan Azure platformunda yerleşik olarak bulunur. SDL her geliştirme aşamasında güvenliği adresleyen ve Azure 'un sürekli olarak daha güvenli hale getirmek için güncelleştirilmesini sağlar.

### <a name="design-for-scalability"></a>Ölçeklenebilirlik için tasarım

Ölçeklenebilirlik, sistemin artan yükü işleyebilme konusunda ne kadar iyi bir sistem olabilir. Uygulamalarınızı, özellikle de DDoS saldırısı durumunda, yükseltilmiş bir yükün talebini karşılayacak şekilde [ölçeklendirmek](/azure/architecture/guide/design-principles/scale-out) üzere tasarlayın. Uygulamanız bir hizmetin tek bir örneğine bağımlıysa, tek bir hata noktası oluşturur. Birden çok örneği sağlamak, sisteminizi daha dayanıklı ve daha ölçeklenebilir hale getirir.

[Azure App Service](/azure/app-service/app-service-value-prop-what-is)için, birden çok örnek sunan bir [App Service planı](/azure/app-service/overview-hosting-plans) seçin. Azure Cloud Services için, rollerinizin her birini [birden çok örnek](/azure/cloud-services/cloud-services-choose-me)kullanacak şekilde yapılandırın. [Azure sanal makineler](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)için, sanal makıne (VM) mimarinizin bırden fazla VM içerdiğinden ve her VM 'nin bir [kullanılabilirlik kümesine](/azure/virtual-machines/virtual-machines-windows-manage-availability)eklendiğinden emin olun. Otomatik ölçeklendirme özellikleri için [Sanal Makine Ölçek Kümeleri](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) kullanmanızı öneririz.

### <a name="defense-in-depth"></a>Derinlemesine savunma

Derinlemesine savunma 'nın arkasındaki fikir, farklı savunma stratejileri kullanarak riskleri yönetmenizde yarar vardır. Bir uygulamadaki güvenlik savunmaları katmanlama, başarılı bir saldırı olasılığını azaltır. Azure platformunun yerleşik yeteneklerini kullanarak uygulamalarınız için güvenli tasarımlar uygulamanızı öneririz.

Örneğin, saldırı riski uygulamanın boyutuyla (*yüzey alanı*) artar. Açık IP adresi alanını ve yük dengeleyiciler üzerinde gerekli olmayan dinleme bağlantı noktalarını ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) ve [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)) kapatmak için, Beyaz listeyi kullanarak yüzey alanını azaltabilirsiniz. [Ağ güvenlik grupları (NSG 'ler)](/azure/virtual-network/security-overview) , saldırı yüzeyini azaltmak için başka bir yoldur.
Uygulama yapısının doğal bir uzantısı olarak güvenlik kuralları oluşturma ve ağ güvenliğini yapılandırma karmaşıklığını en aza indirmek için [hizmet etiketlerini](/azure/virtual-network/security-overview#service-tags) ve [uygulama güvenlik gruplarını](/azure/virtual-network/security-overview#application-security-groups) kullanabilirsiniz.

Mümkün olduğunda Azure hizmetlerini bir [Sanal ağda](/azure/virtual-network/virtual-networks-overview) dağıtmanız gerekir. Bu uygulama, hizmet kaynaklarının özel IP adresleri üzerinden iletişim kurmasına izin verir. Bir sanal ağdan gelen Azure hizmet trafiği, varsayılan olarak kaynak IP adresleri olarak genel IP adreslerini kullanır. [Hizmet uç noktalarının](/azure/virtual-network/virtual-network-service-endpoints-overview) kullanılması, hizmet trafiğini bir sanal ağdan Azure hizmetine ERIŞIRKEN kaynak IP adresleri olarak sanal ağ özel adreslerini kullanacak şekilde geçer.

Müşterilerin Şirket içi kaynaklarını Azure 'daki kaynaklarıyla birlikte saldırıya uğradığını görtik. Şirket içi bir ortamı Azure 'a bağlıyorsanız, şirket içi kaynakların açık internet 'te etkilenme olasılığını en aza indirmenizi öneririz. Azure 'un ölçek ve gelişmiş DDoS koruma özelliklerini kullanarak Azure 'da iyi bilinen genel varlıkları dağıtabilirsiniz. Bu genel olarak erişilebilen varlıklar, DDoS saldırıları için genellikle bir hedef olduğundan, bunları Azure 'a koymak, şirket içi kaynaklarınızın etkisini azaltır.

## <a name="azure-offerings-for-ddos-protection"></a>DDoS koruması için Azure teklifleri

Azure 'da ağ saldırılarına karşı koruma sağlayan iki DDoS hizmeti teklifi vardır (katman 3 ve 4): DDoS koruması temel ve DDoS koruma standardı. 

### <a name="ddos-protection-basic"></a>DDoS koruması temel

Temel koruma, hiçbir ek ücret ödemeden Azure ile varsayılan olarak tümleşiktir. Küresel olarak dağıtılan Azure ağının ölçeği ve kapasitesi, her zaman açık trafik izleme ve gerçek zamanlı risk azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı savunma sağlar. DDoS koruması temel, Kullanıcı Yapılandırması veya uygulama değişikliği gerektirmez. DDoS koruması temel, Azure DNS gibi PaaS hizmetleri de dahil olmak üzere tüm Azure hizmetlerini korumanıza yardımcı olur.

!["Küresel DDoS risk azaltma varlığı" ve "önde gelen DDoS azaltma kapasitesi" metniyle birlikte Azure ağının, harita gösterimi](./media/ddos-best-practices/image3.png)

Azure 'da temel DDoS koruması hem yazılım hem de donanım bileşenlerinden oluşur. Yazılım denetim düzlemi, saldırı trafiğini çözümleyen ve kaldıracak donanım gereçlerinde ne zaman, nerede ve ne tür trafiğe göz atacağını belirler. Denetim düzlemi, altyapı genelinde bir DDoS koruma *ilkesini*temel alarak bu kararı sağlar. Bu ilke, tüm Azure müşterilerine statik olarak ayarlanır ve evrensel olarak uygulanır.

Örneğin, DDoS koruma ilkesi, korumanın hangi trafik biriminde *tetikleneceğini belirtir.* (Yani, kiracının trafiği, temizleme gereçlerine göre yönlendirilmelidir.) İlke daha sonra, temizleme gereçlerinin saldırıyı nasıl *azaltmalıdır* .

Azure DDoS koruması temel hizmeti, Azure platformunun altyapısının ve korumasının korunmasını hedeflemektedir. Çok kiracılı bir ortamda birden çok müşteriyi etkileyebilecek önemli bir oranı aştığında trafiği azaltır. Uyarı veya müşteri başına özelleştirilmiş ilke sağlamaz.

### <a name="ddos-protection-standard"></a>DDoS koruma standardı

Standart koruma, gelişmiş DDoS azaltma özellikleri sağlar. Bir sanal ağdaki belirli Azure kaynaklarınızı korumaya yardımcı olmak üzere otomatik olarak ayarlanır. Korumanın, yeni veya mevcut bir sanal ağda etkinleştirilmesi basittir ve uygulama veya kaynak değişikliği gerektirmez. Günlük, uyarı ve telemetri dahil olmak üzere temel hizmet üzerinde çeşitli avantajları vardır. Aşağıdaki bölümlerde, Azure DDoS koruması standart hizmetinin temel özellikleri ana hatlarıyla verilmiştir.

#### <a name="adaptive-real-time-tuning"></a>Uyarlamalı gerçek zamanlı ayarlama

Azure DDoS koruması temel hizmeti, müşterileri korumanıza ve diğer müşterilerin etkilerini önlemeye yardımcı olur. Örneğin, altyapı genelindeki DDoS koruma ilkesinin *tetikleme hızından* daha küçük olan bir hizmetin tipik bir birimi için sağlanması halinde, müşterinin kaynaklarına yönelik bir DDoS saldırısı fark etmeyebilir. Daha genel olarak, son saldırıların karmaşıklığı (örneğin, çok vektör DDoS) ve kiracıların uygulamaya özgü davranışları, müşteri başına, özelleştirilmiş koruma ilkeleri için çağrı. Hizmet, bu özelleştirmeyi iki öngörü kullanarak gerçekleştirir:

- Katman 3 ve 4 için müşteri başına (IP başına) trafik desenlerini otomatik öğrenme.

- Azure 'un ölçeğinin önemli miktarda trafiğe artışlarını devralarak izin verdiğinden emin olmak için hatalı pozitif sonuçları en aza indirir.

![DDoS koruma standardı 'nın "Ilke oluşturma" daire içinde nasıl çalıştığı diyagramı](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS koruması telemetrisi, izleme ve uyarı

DDoS koruma standardı, DDoS saldırısı süresince [Azure izleyici](/azure/azure-monitor/overview) aracılığıyla zengin telemetri sunar. DDoS korumasının kullandığı Azure Izleyici ölçümlerinden herhangi biri için uyarıları yapılandırabilirsiniz. Azure Izleyici tanılama arabirimi aracılığıyla, gelişmiş analiz için Azure depolama ile splunk (Azure Event Hubs), Azure Izleyici günlükleri ve Azure Storage ile günlüğe kaydetmeyi tümleştirebilir.

##### <a name="ddos-mitigation-policies"></a>DDoS risk azaltma ilkeleri

Azure portal, **izleme**  > **ölçümleri**' ni seçin. **Ölçümler** bölmesinde, kaynak grubunu seçin, **ortak IP adresi**kaynak türünü SEÇIN ve Azure genel IP adresinizi seçin. DDoS ölçümleri **kullanılabilir ölçümler** bölmesinde görünür.

DDoS koruması standardı, korumalı kaynağın her genel IP 'si için, DDoS özellikli olan sanal ağdaki üç etkin Azaltma ilkesi (TCP SYN, TCP ve UDP) uygular. **DDoS risk azaltma tetiklenecek ölçüm gelen paketleri**seçerek ilke eşiklerini görüntüleyebilirsiniz.

![Kullanılabilir ölçümler ve ölçümler grafiği](./media/ddos-best-practices/image7.png)

İlke eşikleri, makine öğrenimi tabanlı ağ trafiği profili oluşturma yoluyla otomatik olarak yapılandırılır. Yalnızca ilke eşiği aşıldığında saldırı altında bir IP adresi için DDoS azaltma durumu oluşur.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS saldırısı kapsamındaki bir IP adresi ölçümü

Genel IP adresi saldırı altındaysa, DDoS koruması için **DDoS saldırısının altındaki** ölçüm değeri, saldırı trafiği üzerinde hafifletme uygular.

!["DDoS saldırısı altında" ölçüm ve grafik](./media/ddos-best-practices/image8.png)

Bu ölçüm üzerinde bir uyarı yapılandırmanızı öneririz. Daha sonra, genel IP adresiniz üzerinde gerçekleştirilen etkin bir DDoS risk azaltma olduğunda size bildirim verilecektir.

Daha fazla bilgi için, [Azure Portal kullanarak Azure DDoS koruması standardını yönetme](/azure/virtual-network/ddos-protection-manage-portal)makalesine bakın.

#### <a name="web-application-firewall-for-resource-attacks"></a>Kaynak saldırıları için Web uygulaması güvenlik duvarı

Uygulama katmanında kaynak saldırılarına özgü, Web uygulamalarının güvenliğini sağlamaya yardımcı olmak için bir Web uygulaması güvenlik duvarı (WAF) yapılandırmanız gerekir. Bir WAF, SQL ınjec, siteler arası komut dosyası, DDoS ve diğer katman 7 saldırılarını engellemek için gelen Web trafiğini inceler. Azure, yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi olarak korunması için [Application Gateway bir özelliği olarak WAF](/azure/application-gateway/application-gateway-web-application-firewall-overview) sağlar. Azure iş ortaklarından [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)aracılığıyla gereksinimlerinize uygun olabilecek başka WAF teklifleri de mevcuttur.

Web uygulaması güvenlik duvarları, Volumetric ve durum tükenmesi saldırılarına açıktır. Volumetric ve protokol saldırılarına karşı korumaya yardımcı olmak için WAF sanal ağı üzerinde DDoS koruma standardının etkinleştirilmesini önemle öneririz. Daha fazla bilgi için [DDoS koruması başvuru mimarileri](#ddos-protection-reference-architectures) bölümüne bakın.

### <a name="protection-planning"></a>Koruma planlaması

Planlama ve hazırlık, bir sistemin DDoS saldırısı sırasında nasıl gerçekleştirileceğini anlamak için önemlidir. Bir olay yönetimi yanıt planı tasarlamak, bu çabanın bir parçasıdır.

DDoS koruma standardı varsa, internet 'e yönelik uç noktaların sanal ağı üzerinde etkinleştirildiğinden emin olun. DDoS uyarılarını yapılandırmak, altyapınızdaki olası saldırıları sürekli olarak izlemenize yardımcı olur. 

Uygulamalarınızı bağımsız olarak izleyin. Bir uygulamanın normal davranışını anlayın. DDoS saldırısı sırasında uygulama beklendiği gibi davranmıyorsa, uygulamaya hazırlanın.

#### <a name="testing-through-simulations"></a>Benzetimler üzerinden test etme

Düzenli benzetimler sunarak hizmetlerinizin bir saldırıya nasıl yanıt vereceğini gösteren varsayımları test etmek iyi bir uygulamadır. Sınama sırasında, hizmetlerinizin veya uygulamalarınızın beklenen şekilde çalışmaya devam etmesini ve kullanıcı deneyiminin kesintiye uğramaması gerektiğini doğrulayın. Hem teknoloji hem de süreç açısından boşlukları belirleyip DDoS yanıt stratejisinden dahil edin. Üretim ortamına etkisini en aza indirmek için, hazırlama ortamlarında veya yoğun olmayan saatlerde bu tür testleri gerçekleştirmenizi öneririz.

Azure müşterilerinin benzetimler için DDoS koruması etkinleştirilmiş ortak uç noktalara karşı trafik oluşturabileceği bir arabirim oluşturmak için [BreakingPoint bulutu](https://www.ixiacom.com/products/breakingpoint-cloud) ile ortaklıyoruz. [BreakingPoint bulut](https://www.ixiacom.com/products/breakingpoint-cloud) benzetimini şu şekilde kullanabilirsiniz:

- Azure DDoS korumasının Azure kaynaklarınızı DDoS saldırılarına karşı korumanıza nasıl yardımcı olduğunu doğrulayın.

- DDoS saldırısı altındayken olay yanıt sürecinizi iyileştirin.

- DDoS uyumluluğunu belgeleyin.

- Ağ Güvenlik ekiplerinizi eğitme.

Cybersecurity, savunma için sabit yenilik gerektirir. Azure DDoS standart koruması, giderek artan karmaşık DDoS saldırılarını hafifletmek için etkili bir çözüm sunan bir son teknoloji sunumudur.

## <a name="components-of-a-ddos-response-strategy"></a>DDoS yanıt stratejisinin bileşenleri

Azure kaynaklarını hedefleyen bir DDoS saldırısı genellikle Kullanıcı açısından çok az müdahale gerektirir. Yine de, bir olay yanıt stratejisinin parçası olarak DDoS azaltma 'yı eklemek, iş sürekliliği etkisini en aza indirmenize yardımcı olur.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft 'un kapsamlı bir tehdit zekası ağı vardır. Bu ağ, Internet güvenlik topluluğundaki Microsoft çevrimiçi hizmetler, Microsoft iş ortakları ve ilişkileri destekleyen bir genişletilmiş güvenlik topluluğunun toplu bilgisini kullanır. 

Kritik bir altyapı sağlayıcısı olarak, Microsoft tehditler hakkında erken uyarılar alır. Microsoft, çevrimiçi hizmetler tehdit zekasını ve küresel müşteri temelini toplar. Microsoft bu tehdit zekasını Azure DDoS koruma ürünlerine geri ekler.

Ayrıca, Microsoft Digital Crimes birimi (DCU) botları için rahatsız edici stratejiler uygular. Botlar, DDoS saldırıları için ortak bir komut ve denetim kaynağıdır.

### <a name="risk-evaluation-of-your-azure-resources"></a>Azure kaynaklarınızın risk değerlendirmesi

DDoS saldırılarına karşı risk kapsamını sürekli olarak anlamanız zorunludur. Düzenli olarak kendinize sorun:

- Hangi yeni genel kullanıma açık Azure kaynakları koruması gerekir?

- Hizmette tek hata noktası var mı? 

- Hizmetler geçerli müşteriler için kullanılabilir olmaya devam ederken, bir saldırının etkilerini sınırlamak için hizmetler nasıl yalıtılarak?

- DDoS koruma standardının etkinleştirilmesi gereken ancak olmaması gereken sanal ağlar var mı? 

- Hizmetlerim, birden çok bölgede yük devretmeyle etkin/etkin mi?

### <a name="customer-ddos-response-team"></a>Müşteri DDoS yanıt ekibi

DDoS yanıt ekibi oluşturmak, bir saldırının hızla ve etkili bir şekilde yanıt vermemesine neden olan önemli bir adımdır. Kuruluşunuzda hem planlama hem de yürütmeyi alacak kişileri belirler. Bu DDoS yanıt ekibi, Azure DDoS koruması standart hizmetini iyice anlamalıdır. Ekibin, Microsoft destek ekibi de dahil olmak üzere dahili ve harici müşterilerle koordine ederek bir saldırıyı belirleyebilmesi ve hafifletmek için emin olun.

DDoS yanıt ekibiniz için, hizmet kullanılabilirliği ve süreklilik planlamanızın normal bir parçası olarak simülasyon alıştırmaları kullanmanızı öneririz. Bu alýþtýrmalar, ölçek testi içermelidir.

### <a name="alerts-during-an-attack"></a>Saldırı sırasında uyarılar

Azure DDoS koruması standardı, herhangi bir kullanıcı müdahalesi olmadan DDoS saldırılarını tanımlar ve azaltır. Korunan genel IP için etkin bir risk azaltma olduğunda bildirim almak için **DDoS saldırısının altındaki**ölçüm üzerinde [bir uyarı yapılandırabilirsiniz](/azure/virtual-network/ddos-protection-manage-portal) . Saldırının ölçeğini, bırakılmakta olan trafiği ve diğer ayrıntıları anlamak için diğer DDoS ölçümleri için uyarı oluşturmayı tercih edebilirsiniz.

#### <a name="when-to-contact-microsoft-support"></a>Microsoft desteği 'ne ne zaman iletişim kura

- DDoS saldırısı sırasında, korunan kaynağın performansının önemli ölçüde düştüğü olduğunu veya kaynağın kullanılabilir olduğunu fark edersiniz.

- DDoS koruma hizmeti 'nin beklenen şekilde davranmadığından düşünün. 

  DDoS Koruması hizmeti yalnızca **DDoS azaltma (TCP/TCP SYN/UDP) tetiklemesi için** ölçüm değeri ilkesi, korunan genel IP kaynağında alınan trafikten düşükse hafifletme işlemi başlatır.

- Ağ trafiğinizi önemli ölçüde arttırabileceğiniz bir viral olayı planlıyorsunuz.

- Bir aktör, kaynaklarınız için DDoS saldırısı başlatmayı tehdit etti.

- Azure DDoS koruma standardı 'ndan bir IP veya IP aralığı listesine izin vermeniz gerekiyorsa. Trafik bir dış buluttan Azure 'a yönlendirilmemişse, yaygın bir senaryo liste IP 'si için izin vermesidir. 

Kritik bir iş etkisi olan saldırılar için bir [destek bileti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)oluşturun.

### <a name="post-attack-steps"></a>Saldırı sonrası adımlar

Bir saldırıya uğradıktan sonra her zaman iyi bir stratejidir ve DDoS yanıt stratejisini gereken şekilde ayarlayın. Göz önüne almanız gerekenler:

- Ölçeklenebilir mimarinin olmaması nedeniyle hizmet veya Kullanıcı deneyimine bir kesinti vardı mi?

- En çok hangi uygulama veya hizmet var?

- DDoS yanıt stratejisi ne kadar etkili oldu ve nasıl geliştirilebilir?

DDoS saldırısının altında olduğunuzdan şüpheleniyorsanız, normal Azure destek kanallarınız aracılığıyla ilerletin.

## <a name="ddos-protection-reference-architectures"></a>DDoS koruması başvuru mimarileri

DDoS koruma standardı, [sanal bir ağa dağıtılan hizmetler için](/azure/virtual-network/virtual-network-for-azure-services)tasarlanmıştır. Diğer hizmetler için, varsayılan DDoS koruması temel hizmeti geçerlidir. Aşağıdaki başvuru mimarileri, mimari desenleriyle birlikte gruplanmış senaryolar tarafından düzenlenir.

### <a name="virtual-machine-windowslinux-workloads"></a>Sanal makine (Windows/Linux) iş yükleri

#### <a name="application-running-on-load-balanced-vms"></a>Yük dengeli VM 'lerde çalışan uygulama

Bu başvuru mimarisi, kullanılabilirliği ve ölçeklenebilirliği artırmak için bir yük dengeleyicinin arkasındaki ölçek kümesinde birden çok Windows sanal makinesi çalıştırmaya yönelik kanıtlanmış bir yöntemler kümesi gösterir. Bu mimari, bir Web sunucusu gibi durum bilgisi olmayan herhangi bir iş yükü için kullanılabilir.

![Yük dengeli VM 'lerde çalışan bir uygulama için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image9.png)

Bu mimaride, bir iş yükü birden çok VM örneğine dağıtılır. Tek bir genel IP adresi vardır ve internet trafiği VM 'lere yük dengeleyici aracılığıyla dağıtılır. DDoS koruma standardı, ortak IP ile ilişkili olan Azure (internet) yük dengeleyicinin sanal ağı üzerinde etkinleştirilmiştir.

Yük dengeleyici, gelen internet isteklerini VM örneklerine dağıtır. Sanal Makine Ölçek Kümeleri, sanal makinelerin sayısının el ile veya dışarı veya ön tanımlı kurallara göre otomatik olarak ölçeklendirilmesine olanak tanır. Kaynak DDoS saldırısının altındaysa bu önemlidir. Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)bakın.

#### <a name="application-running-on-windows-n-tier"></a>Windows N katmanında çalışan uygulama

N katmanlı mimari uygulamanın birçok yolu vardır. Aşağıdaki diyagramda tipik bir üç katmanlı Web uygulaması gösterilmektedir. Bu mimari, [ölçeklenebilirlik ve kullanılabilirlik için yük dengeli VM 'Ler çalıştırma](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)makalesinde oluşturulur. Web ve iş katmanları yük dengeli VM’leri kullanır.

![Windows N katmanında çalışan bir uygulama için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image10.png)

Bu mimaride, DDoS koruma standardı sanal ağ üzerinde etkinleştirilmiştir. Sanal ağdaki tüm genel IP 'Ler 3 ve 4. katman için DDoS koruması 'nı alır. Katman 7 koruması için WAF SKU 'sunda Application Gateway dağıtın. Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)bakın.

#### <a name="paas-web-application"></a>PaaS web uygulaması

Bu başvuru mimarisi, tek bir bölgede Azure App Service uygulamasının çalıştığını gösterir. Bu mimari, [Azure SQL veritabanı](https://azure.microsoft.com/documentation/services/sql-database/) and [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) kullanan bir Web uygulaması için kanıtlanmış bir yöntemler kümesi gösterir.
Yük devretme senaryoları için bir bekleme bölgesi ayarlanır.

![PaaS Web uygulaması için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image11.png)

Azure Traffic Manager, gelen istekleri bölgelerden birinde Application Gateway yönlendirir. Normal işlemler sırasında, istekleri etkin bölgede Application Gateway yönlendirir. Bu bölge kullanılamaz duruma gelirse, Traffic Manager bekleme bölgesinde Application Gateway devreder.

İnternet 'ten Web uygulamasına giden tüm trafik, Traffic Manager üzerinden [Application Gateway genel IP adresine](/azure/application-gateway/application-gateway-web-app-overview) yönlendirilir. Bu senaryoda, App Service (Web App) doğrudan dışarıdan verilmez ve Application Gateway tarafından korunur. 

Katman 7 (HTTP/HTTPS/WebSocket) saldırılarına karşı korumaya yardımcı olmak için Application Gateway WAF SKU 'SU (mod engelleme) yapılandırmanızı öneririz. Ayrıca, Web Apps [yalnızca Application Gateway IP adresinden gelen trafiği kabul](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) edecek şekilde yapılandırılmıştır.

Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/app-service-web-app/multi-region)bakın.

### <a name="mitigation-for-non-web-paas-services"></a>Web dışı PaaS hizmetleri için risk azaltma

#### <a name="hdinsight-on-azure"></a>Azure 'da HDInsight

Bu başvuru mimarisinde, [Azure HDInsight kümesi](/azure/hdinsight/)Için DDoS koruma standardı yapılandırma gösterilmektedir. HDInsight kümesinin bir sanal ağa bağlı olduğundan ve sanal ağ üzerinde DDoS korumasının etkinleştirildiğinden emin olun.

![Sanal ağ ayarlarına sahip "HDInsight" ve "Gelişmiş ayarlar" bölmeleri](./media/ddos-best-practices/image12.png)

![DDoS korumasını etkinleştirme seçimi](./media/ddos-best-practices/image13.png)

Bu mimaride, Internet 'ten HDInsight kümesine giden trafik, HDInsight ağ geçidi yük dengeleyicisiyle ilişkili genel IP 'ye yönlendirilir. Ağ Geçidi yük dengeleyici daha sonra trafiği baş düğümlere veya çalışan düğümlerine doğrudan gönderir. HDInsight sanal ağında DDoS koruması standardı etkinleştirildiğinden, sanal ağdaki tüm genel IP 'Ler 3. ve 4. katman için DDoS koruması 'nı alır. Bu başvuru mimarisi, N katmanlı ve çok bölgeli başvuru mimarileri ile birleştirilebilir.

Bu başvuru mimarisi hakkında daha fazla bilgi için Azure [sanal ağ kullanarak Azure HDInsight 'ı genişletme](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) bölümüne bakın.


> [!NOTE]
> PowerApps veya API Management 'ın ortak IP içeren bir sanal ağda Azure App Service Ortamı, yerel olarak desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

* [Bulutta paylaşılan sorumluluk](shared-responsibility.md)

* [Azure DDoS koruması ürün sayfası](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS koruması belgeleri](/azure/virtual-network/ddos-protection-overview)
