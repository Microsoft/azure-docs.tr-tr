---
title: App Service Ortamı-Azure ile ağ oluşturma konuları
description: Ao ağ trafiğini ve NSG 'ler ile UDRs 'yi Ao ile ayarlama hakkında bilgiler
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ee7e3cb200a20b52a307dba31682a534e9f7b455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470648"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Ortamı için ağ değerlendirmeleri #

## <a name="overview"></a>Genel Bakış ##

 Azure [App Service ortamı][Intro] , Azure sanal ağınızdaki (VNet) bir alt ağa Azure App Service bir dağıtımdır. App Service ortamı için iki dağıtım türü vardır (Ao):

- **Dış asa**: internet ERIŞIMLI bir IP adresinde asa barındırılan uygulamaları gösterir. Daha fazla bilgi için bkz. [dış Ao oluşturma][MakeExternalASE].
- **ILB ASE**: ASE tarafından barındırılan uygulamaları VNET 'iniz IÇINDEKI bir IP adresi üzerinde gösterir. İç uç nokta, bir iç yük dengeleyiciye (ıLB) ve bu nedenle ıLB Ao 'nun çağrılmasının neden olduğu anlamına gelir. Daha fazla bilgi için bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE].

Tüm ASE, dış ve ıLB 'ler, gelen yönetim trafiği için kullanılan genel bir VIP ve ASE 'den internet 'e çağrılar yaparken kimden adresidir. İnternet 'e gidecek bir ASE 'den yapılan çağrılar, VNet 'i ASE için atanan VIP aracılığıyla bırakır. Bu VIP 'nin genel IP 'si, Ao 'dan internet 'e gidecek tüm çağrılar için kaynak IP 'dir. ASE 'deki uygulamalar, sanal ağınızdaki veya bir VPN genelindeki kaynaklara çağrılar yapıyorsa kaynak IP, ASE 'niz tarafından kullanılan alt ağdaki IP 'lerden biridir. ASE, VNet içinde olduğundan, ek bir yapılandırma gerektirmeden VNet içindeki kaynaklara da erişebilir. VNet şirket içi ağınıza bağlıysa, ASE 'deki uygulamalar da ek yapılandırma olmadan kaynaklara erişebilir.

![Dış Ao][1] 

Bir dış Ao 'niz varsa, genel VIP de Ao uygulamalarınızın şu şekilde çözümli uç noktasıdır:

* HTTP/S 
* FTP/S
* Web dağıtımı
* Uzaktan hata ayıklama

![ıLB ATıCı][2]

ILB ASE varsa, ıLB adresinin adresi HTTP/S, FTP/S, Web dağıtımı ve uzaktan hata ayıklama için uç noktadır.

## <a name="ase-subnet-size"></a>ATıCı alt ağ boyutu ##

Atıcı 'yi barındırmak için kullanılan alt ağın boyutu Ao dağıtıldıktan sonra değiştirilemez.  ATıCı, her bir altyapı rolü için ve her yalıtılmış App Service plan örneği için bir adres kullanır.  Ayrıca, Azure ağ tarafından oluşturulan her alt ağ için beş adres kullanılır.  App Service planı olmayan bir Ao, uygulama oluşturmadan önce 12 adres kullanır.  Bu bir ıLB Ao ise, bu Ao 'da bir uygulama oluşturmadan önce 13 adres kullanacaktır. Ao uygulamanızı ölçeklendirirseniz, altyapı rolleri, 15 ' in her katı ve App Service planı örneklerinizin 20 ' si eklenir.

   > [!NOTE]
   > Alt ağda başka hiçbir şey olamaz, ancak Ao. Gelecekteki büyüme için izin veren bir adres alanı seçtiğinizden emin olun. Bu ayarı daha sonra değiştiremezsiniz. 256 adres içeren `/24` boyutunu öneririz.

Ölçeği artırma veya azaltma yaptığınızda, uygun boyutun yeni rolleri eklenir ve sonra iş yükleriniz geçerli boyuttan hedef boyuta geçirilir. Özgün VM 'Ler yalnızca iş yükleri geçirildikten sonra kaldırılır. 100 ASP örneklerine sahip bir AŞIRINIZ varsa, sanal makine sayısının iki katı olması gereken bir nokta olabilir.  Bu nedenle, gerek duyduğunuz değişiklikleri karşılamak için bir '/24 ' kullanılması önerilir.  

## <a name="ase-dependencies"></a>ATıCı bağımlılıkları ##

### <a name="ase-inbound-dependencies"></a>ATıCı gelen bağımlılıkları ###

ASE 'nin çalışması için, ASE 'nin şu bağlantı noktalarının açık olması gerekir:

| Kullanım | Başlangıç fiyatı | Alıcı |
|-----|------|----|
| Yönetim | Yönetim adreslerini App Service | Ao alt ağı: 454, 455 |
|  ATıCı iç iletişimi | Ao alt ağı: tüm bağlantı noktaları | Ao alt ağı: tüm bağlantı noktaları
|  Azure yük dengeleyiciye izin ver | Azure yük dengeleyici | Ao alt ağı: 16001

Bir bağlantı noktası taraması, 7654 ve 1221 üzerinde açık olarak gösterebilme diğer 2 bağlantı noktası vardır. Bunlar bir IP adresiyle ve başka hiçbir şey ile yanıt vermez. İstenirse engellenebilir. 

Gelen yönetim trafiği, sistem izlemeye ek olarak ATıCı 'in komut ve denetimini sağlar. Bu trafiğin kaynak adresleri [atıcı yönetim adresleri][ASEManagement] belgesinde listelenir. Ağ güvenlik yapılandırmasının, 454 ve 455 bağlantı noktalarında ATıCı yönetim adreslerinden erişime izin verilmesi gerekir. Bu adreslerden erişimi engellerseniz Ao, sağlıksız olur ve askıya alınır. 454 ve 455 bağlantı noktalarında gelen TCP trafiği aynı VIP 'den geri dönmeli veya asimetrik bir yönlendirme sorununa sahip olursunuz. 

ADIR alt ağında, iç bileşen iletişimi için kullanılan birçok bağlantı noktası bulunur ve bunlar değişebilir. Bu, ALSUBNET alt ağındaki tüm bağlantı noktalarının Ao alt ağından erişilebilir olmasını gerektirir. 

Azure Yük Dengeleyici ile Ao alt ağı arasındaki iletişim için, açılması gereken en düşük bağlantı noktaları 454, 455 ve 16001. 16001 bağlantı noktası, yük dengeleyici ve Ao arasında canlı trafiği tutmak için kullanılır. ILB Ao kullanıyorsanız, trafiği yalnızca 454, 455, 16001 bağlantı noktalarıyla kilitleyebilir.  Bir dış Ace kullanıyorsanız, normal uygulama erişimi bağlantı noktalarını hesaba getirmeniz gerekir.  

Kendi kendinize sorun için ihtiyacınız olan diğer bağlantı noktaları uygulama bağlantı noktalardır:

| Kullanım | Bağlantı Noktaları |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio uzaktan hata ayıklama  |  4020, 4022, 4024 |
|  Web Dağıtımı hizmeti | 8172 |

Uygulama bağlantı noktalarını engellerseniz ATıCı 'niz çalışmaya devam edebilir ancak uygulamanız çalışmayabilir.  Uygulama tarafından atanan IP adreslerini bir dış Ao ile kullanıyorsanız, uygulamalarınıza atanan IP 'lerden, Ao Portalı > IP adresleri sayfasında gösterilen bağlantı noktalarında Ao alt ağına giden trafiğe izin vermeniz gerekir.

### <a name="ase-outbound-dependencies"></a>ATıCı giden bağımlılıklar ###

Giden erişim için bir ATıCı birden çok dış sisteme bağlıdır. Bu sistem bağımlılıklarının birçoğu DNS adlarıyla tanımlanır ve sabit bir IP adresleri kümesiyle eşlememelidir. Bu nedenle, Ao alt ağından çok sayıda bağlantı noktasındaki tüm dış IP 'lere giden erişim gerekir. 

ATıCı, aşağıdaki bağlantı noktalarında internet 'e erişilebilen adreslere iletişim kurar:

| Kullanımlar | Bağlantı Noktaları |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, Windows güncelleştirmeleri, Linux bağımlılıkları, Azure hizmetleri | 80/443 |
| Azure SQL | 1433 | 
| İzleme | 12000 |

Giden bağımlılıklar, [giden trafik App Service ortamı kilitlemeyi](./firewall-integration.md)açıklayan belgede listelenir. ASE, bağımlılıklarına erişimi kaybederse çalışmayı durduruyor. Bu yeterince uzun olduğunda Ao, askıya alınır. 

### <a name="customer-dns"></a>Müşteri DNS ###

VNet, müşteri tarafından tanımlanan bir DNS sunucusu ile yapılandırıldıysa, kiracı iş yükleri onu kullanır. ATıCı, yönetim amacıyla Azure DNS kullanır. VNet, müşterinin seçtiği bir DNS sunucusu ile yapılandırıldıysa, DNS sunucusuna ASE 'yi içeren alt ağdan erişilebilir olması gerekir.

Web uygulamanızdan DNS çözümlemesini test etmek için, *nameresolver*konsol komutunu kullanabilirsiniz. Uygulamanız için SCM sitenizdeki hata ayıklama penceresine gidin veya portalda uygulamaya gidin ve konsol ' ı seçin. Kabuk isteminde, arama yapmak istediğiniz DNS adıyla birlikte *nameresolver* komutunu verebilirsiniz. Geri alacağınız sonuç, uygulamanızın aynı aramayı yaparken elde edilmesine benzer. Nslookup kullanırsanız, bunun yerine Azure DNS kullanarak bir arama yapılır.

ASE 'nizin bulunduğu VNet 'in DNS ayarını değiştirirseniz ASE 'nizi yeniden başlatmanız gerekir. ASE 'nizi yeniden başlatmayı önlemek için, ASE 'nizi oluşturmadan önce sanal ağınız için DNS ayarlarınızı yapılandırmanız önemle tavsiye edilir.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portal bağımlılıkları ##

Ao işlevsel bağımlılıklara ek olarak, Portal deneyimiyle ilgili birkaç ek öğe vardır. Azure portal bazı yetenekler, _SCM sitesine_doğrudan erişime bağlıdır. Azure App Service içindeki her uygulama için iki URL bulunur. İlk URL, uygulamanıza erişdir. İkinci URL, _kudu konsolu_olarak da adlandırılan SCM sitesine erişilir. SCM sitesini kullanan özellikler şunlardır:

-   Web işleri
-   İşlevler
-   Günlük akışı
-   Kudu
-   Uzantılar
-   İşlem Gezgini
-   Konsol

ILB ASE kullandığınızda, SCM sitesinin VNet dışından erişilebilir olmaması gerekir. Bazı yetenekler, uygulamanın SCM sitesine erişmesi gerektiğinden uygulama portalından çalışmayacaktır. Portalı kullanmak yerine doğrudan SCM sitesine bağlanabilirsiniz. 

ILB Ao, *contoso.appserviceenvironment.net* etki alanı adıdır ve uygulamanızın adı *TestApp*ise, uygulamaya *TestApp.contoso.appserviceenvironment.net*adresinden ulaşılırsa. *TestApp.scm.contoso.appserviceenvironment.net*adresinde yer alan SCM sitesine ulaşıldı.

## <a name="ase-ip-addresses"></a>ATıCı IP adresleri ##

ATıCı 'nin farkında olması için birkaç IP adresi vardır. Bunlar:

- **Genel gelen IP adresi**: BIR dış Ao 'da uygulama trafiği ve hem dış ate hem de ıLB Ao 'daki yönetim trafiği için kullanılır.
- **Giden genel IP**: sanal ağı bir VPN 'ye YÖNLENDIRMEYEN ASE 'den giden bağlantılar için "Kimden" IP 'si olarak kullanılır.
- **ILB IP adresi**: ıLB IP adresi yalnızca bir ıLB Ao 'da bulunur.
- **Uygulama tarafından atanan IP tabanlı SSL adresleri**: yalnızca BIR dış Ale ve IP tabanlı SSL yapılandırıldığında mümkündür.

Tüm bu IP adresleri ABI kullanıcı arabiriminden Azure portal görünür. ILB Ao 'a sahipseniz ıLB 'nin IP 'si listelenir.

   > [!NOTE]
   > Bu IP adresleri, asa 'niz çalışmaya devam eder ve çalışır durumda kalır.  ATıCı 'niz askıya alınırsa ve geri yüklenirse, Ao 'niz tarafından kullanılan adresler değişecektir. Ace 'nin askıya alınması için normal nedeni, gelen yönetim erişimini engellemeniz veya bir ACE bağımlılığı erişimi engellemeniz olabilir. 

![IP adresleri][3]

### <a name="app-assigned-ip-addresses"></a>Uygulama tarafından atanan IP adresleri ###

Dış bir AO ile, tek tek uygulamalara IP adresleri atayabilirsiniz. Bunu bir ıLB Ao ile yapamazsınız. Uygulamanızı kendi IP adresine sahip olacak şekilde yapılandırma hakkında daha fazla bilgi için, bkz. [Azure App Service BIR SSL bağlamasıyla özel BIR DNS adını güvenli hale getirme](../configure-ssl-bindings.md).

Bir uygulamanın kendi IP tabanlı SSL adresi varsa, ATıCı bu IP adresiyle eşlemek için iki bağlantı noktası ayırır. Bir bağlantı noktası HTTP trafiğine yöneliktir ve diğer bağlantı noktası HTTPS içindir. Bu bağlantı noktaları, IP adresleri bölümündeki Ao Kullanıcı arabiriminde listelenir. Trafik, VIP 'lerden Bu bağlantı noktalarına erişebilmelidir veya uygulamalara erişilemez. Bu gereksinim, ağ güvenlik gruplarını (NSG 'ler) yapılandırırken dikkat etmeniz açısından önemlidir.

## <a name="network-security-groups"></a>Ağ Güvenlik Grupları ##

[Ağ güvenlik grupları][NSGs] , bir sanal ağ içindeki ağ erişimini denetleme olanağı sağlar. Portalı kullandığınızda, her şeyi reddetmek için en düşük öncelikte bir örtük reddetme kuralı vardır. Sizin oluşturduğunuz özellikler, izin verme kurallarıdır.

Ao 'da Ao 'nun kendisini barındırmak için kullanılan VM 'lere erişiminiz yok. Bunlar, Microsoft tarafından yönetilen bir abonelikte yer alırlar. Ao 'daki uygulamalara erişimi kısıtlamak istiyorsanız Ao alt ağında NSG 'leri ayarlayın. Bunu yaparken, AKEN bağımlılıklarıyla dikkatli bir dikkat edin. Herhangi bir bağımlılığı engellerseniz ATıCı çalışmayı durduruyor.

NSG 'ler Azure portal veya PowerShell aracılığıyla yapılandırılabilir. Buradaki bilgiler Azure portal gösterir. Portaldaki NSG 'leri, **ağ**altında en üst düzey kaynak olarak oluşturup yönetirsiniz.

Bir NSG 'de bir Alto işlevi için gerekli girişler trafiğe izin versin:

**Gelen**
* 454.455 bağlantı noktalarında AppServiceManagement IP hizmet etiketinden
* 16001 numaralı bağlantı noktasındaki yük dengeleyiciden
* Ao alt ağından tüm bağlantı noktalarında as alt ağına

**Giden**
* 123 numaralı bağlantı noktasındaki tüm IP 'lere
* 80, 443 bağlantı noktasındaki tüm IP 'lere
* 1433 bağlantı noktalarında AzureSQL IP hizmet etiketine
* 12000 numaralı bağlantı noktasındaki tüm IP 'lere
* Tüm bağlantı noktalarında as alt ağına

DNS 'ye trafik, NSG kurallarından etkilenmediğinden DNS bağlantı noktasının eklenmesi gerekmez. Bu bağlantı noktaları, uygulamalarınızın başarılı bir şekilde kullanılması için gereken bağlantı noktalarını içermez. Normal uygulama erişimi bağlantı noktaları şunlardır:

| Kullanım | Bağlantı Noktaları |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio uzaktan hata ayıklama  |  4020, 4022, 4024 |
|  Web Dağıtımı hizmeti | 8172 |

Gelen ve giden gereksinimler hesaba alındığı zaman, NSG 'ler Bu örnekte gösterilen NSG 'lere benzer olmalıdır. 

![Gelen güvenlik kuralları][4]

Varsayılan bir kural, VNet 'teki IP 'Lerin ASE alt ağıyla iletişim kurmasını sağlar. Diğer bir varsayılan kural, genel VIP olarak da bilinen yük dengeleyicinin Ao ile iletişim kurmasını sağlar. Varsayılan kuralları görmek için **Ekle** simgesinin yanındaki **varsayılan kurallar** ' ı seçin. Varsayılan kuralların önüne başka her şeyi Reddet kuralını yerleştirirseniz, VIP ve Ao arasındaki trafiği engelleyebilirsiniz. VNet 'in içinden gelen trafiği engellemek için, gelen trafiğe izin vermek üzere kendi kuralınızı ekleyin. **\*** bir **kaynak ve bağlantı noktası aralığı olan bir** hedefle birlikte AzureLoadBalancer 'e eşit bir kaynak kullanın. NSG kuralı Ao alt ağına uygulandığından, hedefte özel olması gerekmez.

Uygulamanıza bir IP adresi atadıysanız, bağlantı noktalarının açık kalmasını sağlayın. Bağlantı noktalarını görmek için **App Service Ortamı** > **IP adresleri**' ni seçin.  

Aşağıdaki giden kurallarında gösterilen tüm öğeler, son öğe hariç olmak üzere gereklidir. Bunlar, bu makalenin önceki kısımlarında belirtilen Ao bağımlılıklarına ağ erişimi sağlar. Bunlardan herhangi birini engellerseniz ATıCı çalışmayı durduruyor. Listedeki son öğe, ASE 'nizin sanal ağınızdaki diğer kaynaklarla iletişim kurmasını sağlar.

![Giden güvenlik kuralları][5]

NSG 'larınız tanımlandıktan sonra, bunları ATıCı 'nizin bulunduğu alt ağa atayın. ASE VNet veya alt ağını hatırlamıyorsanız, bunu ASE portalı sayfasından görebilirsiniz. NSG 'yi alt ağa atamak için alt ağ kullanıcı arabirimine gidin ve NSG 'yi seçin.

## <a name="routes"></a>Yollar ##

Zorlamalı tünel, VNet 'iniz içindeki yolları ayarladığınızda giden trafik doğrudan internet 'e, ancak başka bir deyişle, bir ExpressRoute ağ geçidi veya Sanal Gereç gibi bir yere gidemez.  ATıCı 'nizi böyle bir şekilde yapılandırmanız gerekiyorsa, [App Service ortamı zorlamalı tünelle yapılandırma][forcedtunnel]hakkında belgeyi okuyun.  Bu belge, ExpressRoute ve zorlamalı tünelle birlikte çalışmak için kullanılabilen seçenekleri size bildirir.

Portalda bir AO oluşturduğunuzda, Ao ile oluşturulan alt ağda bir rota tabloları kümesi de oluşturur.  Bu yollar yalnızca giden trafiği doğrudan internet 'e göndermek için de kullanılır.  
Aynı rotaları el ile oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalına gidin. **Ağ** > **Rota tabloları**' nı seçin.

2. Sanal ağınız ile aynı bölgede yeni bir rota tablosu oluşturun.

3. Yol tablosu Kullanıcı arabiriminizden, **ekle** > **rotalar** ' ı seçin.

4. **Sonraki atlama türünü** **Internet** olarak ve **Adres ön ekini** **0.0.0.0/0**olarak ayarlayın. **Kaydet**’i seçin.

    Ardından aşağıdakine benzer bir şey görürsünüz:

    ![İşlevsel yollar][6]

5. Yeni yol tablosunu oluşturduktan sonra, ATıCı 'nizi içeren alt ağa gidin. Portaldaki listeden yol tablonuzu seçin. Değişikliği kaydettikten sonra, alt ağlarınız ile belirtilen NSG 'leri ve yolları görmeniz gerekir.

    ![NSG 'ler ve rotalar][7]

## <a name="service-endpoints"></a>Hizmet Uç Noktaları ##

Hizmet Uç Noktaları, çok kiracılı hizmetlere erişimi bir dizi Azure sanal ağı ve alt ağı ile kısıtlamanızı sağlar. [Sanal ağ hizmeti uç noktaları][serviceendpoints] belgelerindeki hizmet uç noktaları hakkında daha fazla bilgi edinebilirsiniz. 

Bir kaynakta Hizmet Uç Noktalarını etkinleştirdiğinizde, diğer tüm yönlendiricilerden daha yüksek öncelikle oluşturulmuş rotalar vardır. Hizmet uç noktalarını herhangi bir Azure hizmetinde kullanırsanız, zorlamalı bir tünel ile, bu hizmetlere giden trafiğe Zorlamalı tünel uygulanmaz. 

Azure SQL örneği içeren bir alt ağda Hizmet Uç Noktaları etkinleştirilirse, o alt ağa veya alt ağdan bağlanan tüm Azure SQL örnekleri için Hizmet Uç Noktaları etkinleştirilmiş olmalıdır. Aynı alt ağdan birden fazla Azure SQL örneğine erişmek istiyorsanız, tek bir Azure SQL örneğinde Hizmet Uç Noktalarını etkinleştirebilir, başka bir örnekte etkinleştiremezsiniz. Hizmet uç noktalarına yönelik olarak Azure SQL gibi başka hiçbir Azure hizmeti söz konusu değildir. Azure Depolama ile Hizmet Uç Noktalarını etkinleştirdiğinizde, alt ağınızdan o kaynağa erişimi kilitlersiniz, ancak Hizmet Uç Noktaları etkinleştirilmiş olmasa da diğer Azure Depolama hesaplarından erişmeye devam edebilirsiniz.  

![Hizmet Uç Noktaları][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
