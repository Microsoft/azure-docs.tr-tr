---
title: Ağları Azure İzleyici'ye güvenle bağlamak için Azure Özel Bağlantı'yı kullanma
description: Ağları Azure İzleyici'ye güvenle bağlamak için Azure Özel Bağlantı'yı kullanma
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b3bbd8510c47831c9568bb10c62e2a5ca751902a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863038"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Ağları Azure İzleyici'ye güvenle bağlamak için Azure Özel Bağlantı'yı kullanma

[Azure özel bağlantı](../../private-link/private-link-overview.md) , Özel uç noktaları kullanarak Azure PaaS hizmetlerini sanal ağınıza güvenli bir şekilde bağlayabilmeniz için izin verir. Birçok hizmet için, yalnızca kaynak başına bir uç nokta ayarlarsınız. Bununla birlikte, Azure Izleyici, iş yüklerinizi izlemek için birlikte çalışan farklı bağlantılı hizmetlerden oluşan bir tutarlılık sağlar. Sonuç olarak, Azure Izleyici özel bağlantı kapsamı (AMPLS) adlı bir kaynak oluşturduk. AMPLS, izleme ağınızın sınırlarını tanımlamanızı ve sanal ağınıza bağlanmanızı sağlar. Bu makalede, ne zaman kullanılacağı ve Azure Izleyici özel bağlantı kapsamının nasıl ayarlanacağı ele alınmaktadır.

## <a name="advantages"></a>Avantajlar

Özel bağlantıyla birlikte şunları yapabilirsiniz:

- Herhangi bir genel ağ erişimi açmadan Azure Izleyici 'ye özel olarak bağlanma
- İzleme verilerinize yalnızca yetkili özel ağlar aracılığıyla erişildiğinden emin olun
- Özel uç noktanızla bağlanan belirli Azure Izleyici kaynaklarını tanımlayarak özel ağlarınızdan veri alımını önleyin
- ExpressRoute ve özel bağlantı kullanarak özel şirket içi ağınızı Azure Izleyici 'ye güvenle bağlayın
- Tüm trafiği Microsoft Azure omurga ağı içinde tut

Daha fazla bilgi için bkz.  [özel bağlantının önemli avantajları](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Nasıl çalışır?

Azure Izleyici özel bağlantı kapsamı (AMPLS), Özel uç noktaları (ve içerdikleri sanal ağları) bir veya daha fazla Azure Izleyici kaynağına (Log Analytics çalışma alanı ve Application Insights bileşenleri) bağlar.

![Temel kaynak topolojisi diyagramı](./media/private-link-security/private-link-basic-topology.png)

* Sanal ağınızdaki özel uç nokta, bu uç noktaların genel IP 'lerini kullanmak yerine ağınızın havuzundan özel IP 'Ler aracılığıyla Azure Izleyici uç noktalarına ulaşmasını sağlar. Bu, Azure Izleyici kaynaklarınızı VNet 'i açmadan, gerekli olmayan giden trafiğe karşı kapatmanıza olanak sağlar. 
* Özel uç noktadan Azure Izleyici kaynaklarınıza giden trafik, Microsoft Azure omurgasına giderek ortak ağlara yönlendirilmeyecektir. 
* Çalışma alanlarınızın veya bileşenlerinizin her birini, Ortak ağlardan alma veya reddetme için izin vermek veya reddetmek üzere yapılandırabilirsiniz. Bu, belirli kaynaklara yönelik trafiği denetleyebilmeniz için kaynak düzeyinde bir koruma sağlar.

> [!NOTE]
> Tek bir Azure Izleyici kaynağı birden fazla AMPLSs 'ye ait olabilir, ancak tek bir sanal ağı birden fazla AMPLS 'e bağlanamazsınız. 

## <a name="planning-your-private-link-setup"></a>Özel bağlantı kurulumunuzu planlama

Azure Izleyici özel bağlantı kurulumunuzu ayarlamadan önce, ağ topolojinizi ve özellikle DNS yönlendirme topolojinizi değerlendirin. 

### <a name="the-issue-of-dns-overrides"></a>DNS geçersiz kılmalarının sorunu
Bazı Azure Izleyici Hizmetleri Genel uç noktaları kullanır, bu da herhangi bir çalışma alanını/bileşeni hedefleyen isteklere hizmet verir. Birkaç örnek Application Insights alma uç noktasıdır ve hem Application Insights hem de Log Analytics sorgu uç noktasıdır.

Bir özel bağlantı bağlantısı ayarladığınızda DNS 'niz Azure Izleyici uç noktalarını sanal Ağınızın IP aralığından özel IP adresleriyle eşlenecek şekilde güncelleştirilir. Bu değişiklik, bu uç noktaların önceki eşlemesini geçersiz kılar ve bu, aşağıda gözden geçirilmiş anlamlı etkileri olabilir. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Izleyici özel bağlantısı tüm Azure Izleyici kaynakları için geçerlidir; hepsi veya hiçbir şey yoktur
Bazı Azure Izleyici uç noktaları genel olduğundan, belirli bir bileşen veya çalışma alanı için özel bağlantı bağlantısı oluşturmak olanaksızdır. Bunun yerine, tek bir Application Insights bileşenine veya Log Analytics çalışma alanına özel bir bağlantı ayarladığınızda, DNS kayıtlarınız **tüm** Application Insights bileşenleri için güncelleştirilir. Bir bileşeni alma veya sorgulama girişimi, özel bağlantı üzerinden geçer ve muhtemelen başarısız olur. Log Analytics, alma ve yapılandırma uç noktaları, çalışma alanına özeldir. Bu, özel bağlantı kurulumunun yalnızca belirtilen çalışma alanları için uygulanacağı anlamına gelir. Diğer çalışma alanlarının alımı ve yapılandırılması, varsayılan genel Log Analytics uç noktalarına yönlendirilir.

![Tek bir sanal ağda DNS geçersiz kılmaların diyagramı](./media/private-link-security/dns-overrides-single-vnet.png)

Bu doğru yalnızca belirli bir sanal ağ için değil, ancak aynı DNS sunucusunu paylaşan tüm sanal ağlar için ( [DNS geçersiz kılmaların sorununa](#the-issue-of-dns-overrides)bakın). Bu nedenle, örneğin, her bir Application Insights bileşeni için günlük alma isteği, her zaman özel bağlantı rotası aracılığıyla gönderilir. AMPLS 'e bağlı olmayan bileşenler özel bağlantı doğrulama işlemi başarısız olur ve devam etmez.

> [!NOTE]
> Bu ayarı yapmak için: tek bir kaynağa özel bağlantı kurulduktan sonra, bu, ağınızdaki Azure Izleyici kaynakları için geçerlidir. Application Insights kaynaklar için, bu ' tümü veya Nothing '. Bu etkili bir şekilde, ağınızdaki tüm Application Insights kaynaklarını AMPLS 'e eklemeniz veya hiçbirini hiç eklememelisiniz.
> 
> Veri kaybı risklerini işlemek için, tüm Application Insights ve Log Analytics kaynaklarını AMPLS 'e eklemek ve ağlarınızın trafiği mümkün olduğunca çok engellemek için önerimiz.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Izleyici özel bağlantısı, tüm ağınız için geçerlidir
Bazı ağlar birden çok VNET 'ten oluşur. Sanal ağlar aynı DNS sunucusunu kullanıyorsa, bunların her bir DNS eşlemelerini geçersiz kılar ve Azure Izleyici ile birbirini büyük olasılıkla keser ( [DNS geçersiz kılmalarının sorununa](#the-issue-of-dns-overrides)bakın). Son olarak, DNS Azure Izleyici uç noktalarını bu sanal ağlar aralığından özel IP 'Ler (diğer sanal ağlara ulaşılamayabilir) ile eşleyebildiğinden, Azure izleyici ile yalnızca son VNet iletişim kurabilir.

![Birden çok VNET 'teki DNS geçersiz kılmaların diyagramı](./media/private-link-security/dns-overrides-multiple-vnets.png)

Yukarıdaki diyagramda, VNet 10.0.1. x, önce AMPLS1 'e bağlanır ve Azure Izleyici küresel uç noktalarını kendi aralığından IP 'lere eşler. Daha sonra, VNet 10.0.2. x AMPLS2 'e bağlanır ve *aynı küresel uç NOKTALARıN* DNS eşlemesini kendi aralığından IP 'ler ile geçersiz kılar. Bu sanal ağlar eşlenmediği için, ilk VNet artık bu uç noktalara ulaşamamıştır.

> [!NOTE]
> : AMPLS kurulumu, aynı DNS bölgelerini paylaşan tüm ağları etkiler. Her birinin DNS uç nokta eşlemelerini geçersiz kılmayı önlemek için, eşlenmiş bir ağda (hub VNet gibi) tek bir özel uç nokta kurmak veya ağı DNS düzeyinde (DNS ileticileri veya DNS sunucuları tamamen ayrı ayrı ayırmak için) ayırmak en iyisidir.

### <a name="hub-spoke-networks"></a>Hub-ışınsal-uç ağları
Hub-ışınsal-posta topolojileri, her VNet için ayrı bir özel bağlantı kurmak yerine Hub (ana) VNet üzerinde özel bir bağlantı ayarlayarak DNS geçersiz kılma sorununa engel olabilir. Bu kurulum özellikle, bağlı olan VNET 'ler tarafından kullanılan Azure Izleyici kaynakları paylaşıldığında anlamlı hale gelir. 

![Hub-ve-bağlı-tek-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Örneğin, her VNet 'in sınırlı bir izleme kaynakları kümesine erişmesine izin vermek gibi, bağlı sanal ağlar için ayrı özel bağlantılar oluşturmayı tercih edebilirsiniz. Böyle durumlarda, her VNet için adanmış bir özel uç nokta ve AMPLS oluşturabilirsiniz, ancak DNS geçersiz kılmalarını önlemek için aynı DNS bölgelerini paylaşmamaları da gerekir.


### <a name="consider-limits"></a>Limitleri değerlendirin

[Kısıtlamalar ve sınırlamalar](#restrictions-and-limitations)bölümünde listelendiği gıbı, ampls nesnesi aşağıdaki topolojide gösterildiği gibi bir dizi sınıra sahiptir:
* Her VNet yalnızca **1** ampls nesnesine bağlanır.
* AMPLS B, 10 olası özel uç nokta bağlantılarından 2 ' yi kullanarak iki sanal ağ (VNet2 ve VNet3) özel uç noktalarına bağlanır.
* AMPLS A, 50 olası Azure Izleyici kaynakları bağlantılarının 3 ' ü kullanarak iki çalışma alanına ve bir Application Insight bileşenine bağlanır.
* Workspace2, 5 olası AMPLS bağlantı 2 ' yi kullanarak AMPLS A ve AMPLS B 'ye bağlanır.

![AMPLS limitlerinin diyagramı](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Örnek bağlantı

Azure Izleyici özel bağlantı kapsamı kaynağı oluşturarak başlayın.

1. Azure portal **bir kaynak oluştur ' a** gidin ve **Azure Izleyici özel bağlantı kapsamını** arayın.

   ![Azure Izleyici özel bağlantı kapsamını bulun](./media/private-link-security/ampls-find-1c.png)

2. **Oluştur**’u seçin.
3. Bir abonelik ve kaynak grubu seçin.
4. AMPLS 'e bir ad verin. "AppServerProdTelem" gibi anlamlı ve açık bir ad kullanmak en iyisidir.
5. **Gözden geçir + Oluştur**’u seçin. 

   ![Azure Izleyici özel bağlantı kapsamı oluştur](./media/private-link-security/ampls-create-1d.png)

6. Doğrulama geçişine izin verin ve ardından **Oluştur**' u seçin.

### <a name="connect-azure-monitor-resources"></a>Azure Izleyici kaynaklarını bağlama

Azure Izleyici kaynaklarını (Log Analytics çalışma alanları ve Application Insights bileşenleri) AMPLS 'nize bağlayın.

1. Azure Izleyici özel bağlantı kapsamınızda, sol taraftaki menüden **Azure Izleyici kaynakları** ' nı seçin. **Ekle** düğmesini seçin.
2. Çalışma alanını veya bileşeni ekleyin. **Ekle** düğmesinin seçilmesi, Azure izleyici kaynaklarını seçebileceğiniz bir iletişim kutusu getirir. Aboneliklerinize ve kaynak gruplarınıza göz atabilir veya bunları filtrelemek için ad yazabilirsiniz. Çalışma alanını veya bileşeni seçin ve bunları kapsamınızda eklemek için **Uygula** ' yı seçin.

    ![Kapsam seçin UX ekran görüntüsü](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Izleyici kaynaklarını silmek için önce bağlandıkları tüm AMPLS nesneleriyle bağlantılarını kesmeniz gerekir. Bir AMPLS 'e bağlı kaynakları silmek mümkün değildir.

### <a name="connect-to-a-private-endpoint"></a>Özel bir uç noktaya Bağlan

Şimdi, AMPLS 'larınızla bağlantılı kaynaklarınız olduğuna göre, ağımızı bağlamak için özel bir uç nokta oluşturun. Bu görevi, bu örnekte olduğu gibi [Azure Portal özel bağlantı merkezinde](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)veya Azure Izleyici özel bağlantı kapsamınızda yapabilirsiniz.

1. Kapsam kaynağında, sol taraftaki kaynak menüsünde **Özel uç nokta bağlantıları** ' nı seçin. Uç nokta oluşturma işlemini başlatmak için **Özel uç nokta** ' ı seçin. Ayrıca, özel bağlantı merkezinde başlatılan bağlantıları onları seçerek ve **Onayla**' yı seçerek onaylayabilirsiniz.

    ![Özel uç nokta bağlantıları UX görüntüsü](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Abonelik, kaynak grubu ve uç noktanın adını ve üzerinde etkin olması gereken bölgeyi seçin. Bölgenin, bağlandığınız VNet ile aynı bölgede olması gerekir.

3. **Sonraki: kaynak**' ı seçin. 

4. Kaynak ekranında,

   a. Azure Izleyici özel kapsam kaynağınızı içeren **aboneliği** seçin. 

   b. **Kaynak türü** için **Microsoft. Insights/privatelinkscopes**' ı seçin. 

   c. **Kaynak** açılan listesinden daha önce oluşturduğunuz özel bağlantı kapsamınızı seçin. 

   d. **İleri ' yi seçin: yapılandırma >**.
      ![Özel uç nokta oluştur Seç ekran görüntüsü](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Yapılandırma bölmesinde,

   a.    Azure Izleyici kaynaklarınıza bağlamak istediğiniz **sanal ağı** ve **alt ağı** seçin. 
 
   b.    **Özel DNS bölgesi Ile tümleştirme** için **Evet** ' i seçin ve otomatik olarak yeni bir özel DNS bölgesi oluşturmasını sağlayın. Gerçek DNS bölgeleri aşağıdaki ekran görüntüsünde gösterilenden farklı olabilir. 
   > [!NOTE]
   > **Hayır** ' ı SEÇIN ve DNS kayıtlarını el ile yönetmeyi tercih ederseniz, bu özel uç nokta ve ampls yapılandırması dahil olmak üzere, Ilk olarak özel bağlantınız kurulumunu tamamlayabilirsiniz. Ardından DNS'yi [Azure Özel Uç Nokta DNS yapılandırması](../../private-link/private-endpoint-dns.md) makalesinde yer alan yönergelere göre yapılandırın. Özel Bağlantı kurulumunuza hazırlık yaparken boş kayıtlar oluşturmadığınızdan emin olun. Oluşturduğunuz DNS kayıtları mevcut ayarları geçersiz kılabilir ve Azure İzleyici ile bağlantınızı etkileyebilir.
 
   c.    **Gözden geçir ve oluştur**’u seçin.
 
   d.    Doğrulama geçişine izin verin. 
 
   e.    **Oluştur**’u seçin. 

    ![Özel uç nokta Ayrıntıları Seç ekran görüntüsü.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Şimdi bu AMPLS 'e bağlı yeni bir özel uç nokta oluşturdunuz.

## <a name="review-and-validate-your-private-link-setup"></a>Özel bağlantı kurulumunuzu gözden geçirin ve doğrulayın

### <a name="reviewing-your-endpoints-dns-settings"></a>Uç noktanızın DNS ayarlarını gözden geçirme
Oluşturduğunuz özel uç noktasının artık dört DNS bölgesi yapılandırılmış olmalıdır:

[![Özel uç nokta DNS bölgelerinin ekran görüntüsü.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* Privatelink-izleme-Azure-com
* Privatelink-OMS-OpInsights-Azure-com
* Privatelink-ODS-OpInsights-Azure-com
* Privatelink-Agentsvc-Azure-Automation-net

> [!NOTE]
> Bu bölgelerin her biri belirli Azure Izleyici uç noktalarını VNet 'in IP havuzundan özel IP 'lere eşler. Aşağıdaki görüntülerde showns IP adresleri yalnızca örnektir. Bunun yerine kendi ağınızdan özel IP 'Ler gösterilmesi gerekir.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-izleme-Azure-com
Bu bölge, Azure Izleyici tarafından kullanılan küresel uç noktaları kapsadığından, bu uç noktaların belirli bir kaynağı değil tüm kaynakları dikkate alır. Bu bölgede, için eşlenmiş uç noktalar olmalıdır:
* `in.ai` -Application Insights alma uç noktası (hem genel hem de bölgesel giriş)
* `api` -Application Insights ve Log Analytics API uç noktası
* `live` -Application Insights canlı ölçüm uç noktası
* `profiler` -Application Insights Profiler uç noktası
* `snapshot`-Application Insights Snapshot Endpoint [ ![ ekran görüntüsü özel DNS bölge izleyicisinin-Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>Privatelink-OMS-OpInsights-Azure-com
Bu bölge, OMS uç noktalarına çalışma alanına özgü eşlemeyi içerir. Bu özel uç noktayla bağlantılı olan AMPLS 'e bağlı her çalışma alanı için bir giriş görmeniz gerekir.
[![Özel DNS Zone OMS-OpInsights-Azure-com ekran görüntüsü.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>Privatelink-ODS-OpInsights-Azure-com
Bu bölge, ODS uç noktalarına çalışma alanına özgü eşlemeyi içerir. Log Analytics alma uç noktasıdır. Bu özel uç noktayla bağlantılı olan AMPLS 'e bağlı her çalışma alanı için bir giriş görmeniz gerekir.
[![Özel DNS Zone ODS-OpInsights-Azure-com ekran görüntüsü.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>Privatelink-Agentsvc-Azure-Automation-net
Bu bölge, aracı hizmeti Otomasyon uç noktalarına çalışma alanına özgü eşlemeyi içerir. Bu özel uç noktayla bağlantılı olan AMPLS 'e bağlı her çalışma alanı için bir giriş görmeniz gerekir.
[![Özel DNS bölge Aracısı svc-Azure-Automation-net ekran görüntüsü.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>Privatelink-blob-çekirdek-Windows-NET
Bu bölge, genel aracıların çözüm paketleri depolama hesabıyla bağlantıyı yapılandırır. Aracıda, aracılar yeni veya güncelleştirilmiş çözüm paketlerini (yönetim paketleri olarak da bilinir) indirebilir. Kaç çalışma alanı kullanıldığına bakılmaksızın Log Analytics aracılarını işlemek için yalnızca bir giriş gereklidir.
[![Özel DNS Zone blob 'un ekran görüntüsü-çekirdek-Windows-net.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Bu giriş yalnızca 19 Nisan 2021 ' de veya sonrasında oluşturulan özel bağlantı kurulumlarına eklenir.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Özel bir bağlantı üzerinden iletişim kurduğunuz doğrulanıyor
* İsteklerinizin özel uç nokta ve özel IP eşlenmiş uç noktalar aracılığıyla gönderilmesini sağlamak için, bunları bir ağ izleme aracı veya hatta tarayıcınız ile inceleyebilirsiniz. Örneğin, çalışma alanınızı veya uygulamanızı sorgulamaya çalışırken, isteğin API uç noktasına eşlenmiş özel IP 'ye gönderildiğinden emin olun, bu örnekte *172.17.0.9*.

    Note: bazı tarayıcılarda diğer DNS ayarları (bkz. [tarayıcı DNS ayarları](#browser-dns-settings)) kullanılabilir. DNS ayarlarınızın uygulanmasını sağlayın.

* Çalışma alanınızın veya bileşeninizin ortak ağlardan (AMPLS ile bağlantılı olmayan) istek aldığından emin olmak için kaynağın ortak alma ve sorgu bayraklarını [özel bağlantı kapsamları dışında erişimi yönetme](#manage-access-from-outside-of-private-links-scopes)bölümünde açıklanan şekilde *Hayır* olarak ayarlayın.

* Korumalı ağınızdaki bir istemciden, `nslookup` DNS bölgelerinde listelenen uç noktalara kadar kullanın. DNS sunucunuz, varsayılan olarak kullanılan genel IP 'Ler yerine, eşlenmiş özel IP 'Ler ile çözümlenmelidir.


## <a name="configure-log-analytics"></a>Log Analytics’i Yapılandırma

Azure portala gidin. Log Analytics çalışma alanı kaynak menüsünde, sol taraftaki **ağ yalıtımı** adlı bir öğe vardır. Bu menüden iki farklı durumu kontrol edebilirsiniz.

![LA ağ yalıtımı](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Bağlı Azure Izleyici özel bağlantı kapsamları
Çalışma alanına bağlı tüm kapsamlar Bu ekranda görünür. Kapsamlara bağlanma (AMPLSs), her bir AMPLS 'e bağlanan sanal ağdan gelen ağ trafiğinin bu çalışma alanına ulaşmasını sağlar. Buradan bir bağlantı oluşturmak, [Azure izleyici kaynaklarını bağlarken](#connect-azure-monitor-resources)olduğu gibi kapsamda ayarlama ile aynı etkiye sahiptir. Yeni bir bağlantı eklemek için **Ekle** ' yi seçin ve Azure Izleyici özel bağlantı kapsamını seçin. Bu uygulamayı bağlamak için **Uygula** ' yı seçin. Çalışma alanının, [sınırlamalar ve sınırlamalar](#restrictions-and-limitations)bölümünde belirtildiği gıbı 5 ampls nesnesine bağlanabildiğini unutmayın. 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Özel bağlantı kapsamları dışından erişimi yönetme
Bu sayfanın alt kısmındaki ayarlar ortak ağlardan erişimi denetler, yani listelenen kapsamlar (AMPLSs) ile bağlı değildir. Giriş **için genel ağ erişimine Izin ver** ayarı, bağlı kapsamlar dışındaki makinelerden gelen günlüklerin biriktirme listesini **içermez** . **Sorgular için ortak ağ erişimine Izin ver** ayarı, kapsamların dışındaki makinelerden gelen **hiçbir** blok sorgusu vermez. Bu, çalışma kitapları, panolar, API tabanlı istemci deneyimleri, Azure portal içgörüler ve daha fazlası aracılığıyla çalıştırılan sorgular içerir. Azure portal dışında çalışan deneyimler ve sorgu Log Analytics verileri özel bağlantılı VNET içinde de çalışıyor olması gerekir.

### <a name="exceptions"></a>Özel durumlar
Yukarıda açıklandığı gibi erişimin sınırlandırılması Azure Resource Manager uygulanmaz ve bu nedenle aşağıdaki sınırlamalara sahiptir:
* Verilere erişim-ortak ağlardan gelen sorguların çoğu Log Analytics deneyim için geçerli olduğundan, bazı deneyimler verileri Azure Resource Manager aracılığıyla sorgular ve bu nedenle özel bağlantı ayarları Kaynak Yöneticisi de (yakında kullanıma sunulacak özellik) uygulanmamışsa verileri sorgulayamaz. Azure Izleyici çözümleri, çalışma kitapları ve Öngörüler ve LogicApp Connector örnekleri verilmiştir.
* Çalışma alanı yönetimi-çalışma alanı ayarı ve yapılandırma değişiklikleri (Bu erişim ayarlarını açma veya kapatma dahil) Azure Resource Manager tarafından yönetilir. Uygun rolleri, izinleri, ağ denetimlerini ve denetimi kullanarak çalışma alanı yönetimine erişimi kısıtlayın. Daha fazla bilgi için bkz. [Azure Izleyici rolleri, izinleri ve güvenliği](../roles-permissions-security.md).

> [!NOTE]
> [Tanılama ayarları](../essentials/diagnostic-settings.md) aracılığıyla bir çalışma alanına yüklenen Günlükler ve ölçümler güvenli bir özel Microsoft kanalının üzerine gider ve bu ayarlar tarafından denetlenmez.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics çözüm paketlerini indirme
Log Analytics aracıların çözüm paketlerini indirmek için küresel bir depolama hesabına erişmesi gerekir. 19 Nisan 2021 ' de veya sonrasında oluşturulan özel bağlantı kurulumları, aracıların çözüm paketleri depolamasına özel bağlantı üzerinden erişebilir. Bu, [BLOB.Core.Windows.net](#privatelink-blob-core-windows-net)için oluşturulan yeni DNS bölgesi aracılığıyla yapılabilir.

Özel bağlantı kurulumunuzu 19 Nisan 2021 ' den önce oluşturulduysa, özel bir bağlantı üzerinden çözüm paketleri depolamasına erişmez. Bunu işlemek için aşağıdakilerden birini yapabilirsiniz:
* AMPLS 'lerinizi ve ona bağlı özel uç noktayı yeniden oluşturun
* Güvenlik duvarınızın allowlist 'nize aşağıdaki kuralları ekleyerek, aracılarınızın genel uç noktası aracılığıyla depolama hesabına ulaşmasını sağlar:

    | Bulut ortamı | Aracı Kaynağı | Bağlantı noktaları | Yön |
    |:--|:--|:--|:--|
    |Azure Genel     | scadvisorcontent.blob.core.windows.net         | 443 | Giden
    |Azure Kamu | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Giden
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Giden


## <a name="configure-application-insights"></a>Application Insights'ı Yapılandırma

Azure portala gidin. Azure Izleyici Application Insights bileşen kaynağında, sol taraftaki bir menü öğesi **ağ yalıtımı** olur. Bu menüden iki farklı durumu kontrol edebilirsiniz.

![AI ağ yalıtımı](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

İlk olarak, bu Application Insights kaynağını, erişiminiz olan Azure Izleyici özel bağlantı kapsamlarına bağlayabilirsiniz. **Ekle** ' yi seçin ve **Azure Izleyici özel bağlantı kapsamını** seçin. Bu uygulamayı bağlamak için Uygula ' yı seçin. Tüm bağlı kapsamlar Bu ekranda görünür. Bu bağlantının yapılması, bağlı sanal ağlardaki ağ trafiğinin bu bileşene ulaşmasını sağlar ve [Azure izleyici kaynaklarını bağladığımızda](#connect-azure-monitor-resources)kapsama bağlama ile aynı etkiye sahiptir. 

Daha sonra, bu kaynağa daha önce listelenen özel bağlantı kapsamları (AMPLS) dışından nasıl ulaşılgidebileceğini kontrol edebilirsiniz. Alma **için genel ağ erişimine Izin ver** ' i **Hayır** olarak ayarlarsanız, bağlı kapsamların dışındaki makineler veya SDK 'lar bu bileşene veri yükleyebilir. **Sorgular için ortak ağ erişimine Izin ver** ' i **Hayır** olarak ayarlarsanız, kapsamların dışındaki makineler bu Application Insights kaynaktaki verilere erişemez. Bu veriler APM günlüklerine, ölçümlere ve canlı ölçüm akışına erişimi, ayrıca çalışma kitapları, panolar, sorgu API tabanlı istemci deneyimleri, Azure portal içgörüler ve daha fazlası gibi en üstte oluşturulan deneyimlerden de oluşur. 

> [!NOTE]
> Portal dışı tüketim deneyimleri, izlenen iş yüklerini içeren özel bağlantılı VNET üzerinde de çalışmalıdır.

İzlenen iş yüklerini barındıran kaynakları özel bağlantıya eklemeniz gerekir. Örneğin, bkz. [Azure Web uygulaması Için özel uç noktaları kullanma](../../app-service/networking/private-endpoint.md).

Erişimin bu şekilde kısıtlanması yalnızca Application Insights kaynaktaki veriler için geçerlidir. Ancak, bu erişim ayarlarını açma veya kapatma dahil olmak üzere yapılandırma değişiklikleri Azure Resource Manager tarafından yönetilir. Bu nedenle, uygun rolleri, izinleri, ağ denetimlerini ve denetimi kullanarak Kaynak Yöneticisi erişimini kısıtlamalısınız. Daha fazla bilgi için bkz. [Azure Izleyici rolleri, izinleri ve güvenliği](../roles-permissions-security.md).

> [!NOTE]
> Çalışma alanı tabanlı Application Insights tamamen güvenli hale getirmek için, Application Insights kaynağa erişimin yanı sıra temel Log Analytics çalışma alanını da kapatmanız gerekir.
>
> Kod düzeyinde Tanılamalar (Profiler/Debugger), özel bağlantıyı desteklemek için [kendi depolama hesabınızı sağlamanızı](../app/profiler-bring-your-own-storage.md) gerektirir.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Özel bağlantıların tümünü veya herhangi bir şeyi işleme
[Özel bağlantı kurulumunuzu planlama](#planning-your-private-link-setup)bölümünde açıklandığı gibi, tek bir kaynak Için bile özel bir bağlantı kurmak, bu ağlardaki tüm Azure izleyici kaynaklarını ve aynı DNS 'yi paylaşan diğer ağları etkiler. Bu davranış, ekleme sürecinizi zorlu hale getirir. Aşağıdaki seçenekleri göz önünde bulundurun:

* Tüm en basit ve en güvenli yaklaşım, tüm Application Insights bileşenlerinizi AMPLS 'a eklemektir. Diğer ağlardan hala erişmeye devam etmek istediğiniz bileşenler için "alma/sorgu için genel internet erişimine Izin ver" bayrakları Evet (varsayılan) olarak ayarlayın.
* Ağları ayır-bağlı olan VNET 'leri kullanarak (veya ile hizaladıysanız), [Azure 'Da Merkez-uç ağ topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)içindeki yönergeleri izleyin. Ardından, ilgili bağlı olan sanal ağlarda ayrı özel bağlantı ayarları kurun. DNS bölgelerini diğer bağlı ağlar ile paylaşmak [DNS geçersiz kılmalara](#the-issue-of-dns-overrides)neden OLACAĞı için DNS bölgelerini de ayırdığınızdan emin olun.
* Belirli uygulamalar için özel DNS bölgelerini kullanın-Bu çözüm, özel bir bağlantı üzerinden seçme Application Insights bileşenlerine erişmenize izin verir ve diğer tüm trafiği ortak yollarla tutarken bu şekilde çalışır.
    - [Özel bir özel DNS bölgesi](../../private-link/private-endpoint-dns.md)ayarlayın ve internal.Monitor.Azure.com gibi benzersiz bir ad verin.
    - Bir AMPLS ve özel uç nokta oluşturun ve özel DNS ile otomatik tümleştirme **seçeneğini belirleyin**
    - Özel uç nokta-> DNS yapılandırması ' na gidin ve FQDN 'lerin önerilen eşlemesini gözden geçirin.
    - Yapılandırma eklemeyi seçin ve yeni oluşturduğunuz internal.monitor.azure.com bölgesini seçin
    - ![YAPıLANDıRıLMıŞ DNS bölgesinin yukarıdaki ekran görüntüsüne ait kayıtları ekleyin](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Application Insights bileşenine gidin ve [bağlantı dizesini](../app/sdk-connection-string.md)kopyalayın.
    - Özel bir bağlantı üzerinden bu bileşeni çağırmak isteyen uygulamalar veya betikler, bağlantı dizesini EndpointSuffix = Internal. Monitor. Azure. com ile kullanmalıdır
* Uç noktaları DNS yerine Hosts dosyaları aracılığıyla eşle-yalnızca ağınızdaki belirli bir makineden/VM 'den özel bir bağlantı erişimine sahip olmak için:
    - Bir AMPLS ve özel uç nokta ayarlayın ve özel DNS ile otomatik tümleştirme **seçeneğini belirleyin** 
    - Ana bilgisayar dosyasındaki uygulamayı çalıştıran bir makinedeki yukarıdaki kayıtları yapılandırma


## <a name="use-apis-and-command-line"></a>API 'Leri ve komut satırını kullanma

Azure Resource Manager şablonları, REST ve komut satırı arabirimlerini kullanarak daha önce açıklanan işlemi otomatikleştirebilirsiniz.

Özel bağlantı kapsamları oluşturup yönetmek için [REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) veya [Azure CLI (az Monitor Private-link-scope)](/cli/azure/monitor/private-link-scope)kullanın.

Ağ erişimini yönetmek için, bayraklarını `[--ingestion-access {Disabled, Enabled}]` ve `[--query-access {Disabled, Enabled}]` [Log Analytics çalışma alanlarını](/cli/azure/monitor/log-analytics/workspace) veya [Application Insights bileşenlerini](/cli/azure/monitor/app-insights/component)kullanın.

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Özel Günlükler ve IIS oturumunu özel bağlantı üzerinden topla

Depolama hesapları özel günlüklerin alma işleminde kullanılır. Varsayılan olarak, hizmet tarafından yönetilen depolama hesapları kullanılır. Ancak özel bağlantılar üzerinde özel Günlükler almak için kendi depolama hesaplarınızı kullanmanız ve bunları Log Analytics çalışma alanı (ler) ile ilişkilendirmeniz gerekir. Bu hesapları [komut satırını](/cli/azure/monitor/log-analytics/workspace/linked-storage)kullanarak ayarlama hakkında daha fazla ayrıntı için bkz..

Kendi depolama hesabınızı getirme hakkında daha fazla bilgi için bkz. [günlük alma Için müşterinin sahip olduğu depolama hesapları](private-storage.md)

## <a name="restrictions-and-limitations"></a>Kısıtlamalar ve sınırlamalar

### <a name="ampls"></a>AMPLS
AMPLS nesnesinin özel bağlantı kurulumunuzu planlarken göz önünde bulundurmanız gereken birkaç sınırı vardır:

* VNet yalnızca 1 AMPLS nesnesine bağlanabilir. Bu, AMPLS nesnesinin, VNet 'in erişimi olması gereken tüm Azure Izleyici kaynaklarına erişim sağlaması gerektiği anlamına gelir.
* Bir Azure Izleyici kaynağı (çalışma alanı veya Application Insights bileşeni), en çok 5 AMPLSs 'ye bağlanabilir.
* Bir AMPLS nesnesi, en çok 50 Azure Izleyici kaynağına bağlanabilir.
* AMPLS nesnesi, en çok 10 özel uç noktaya bağlanabilir.

Bu limitleri daha ayrıntılı bir şekilde gözden geçirmek için bkz. [limitleri değerlendirin](#consider-limits) .

### <a name="agents"></a>Aracılar

Windows ve Linux aracılarının en son sürümleri, Log Analytics çalışma alanlarına güvenli alımı desteklemek için kullanılmalıdır. Eski sürümler, izleme verilerini özel bir ağ üzerinden karşıya yükleyemiyor.

**Log Analytics Windows aracısı**

Log Analytics Agent sürümü 10.20.18038.0 veya üstünü kullanın.

**Log Analytics Linux aracısı**

Aracı sürümü 1.12.25 veya üstünü kullanın. Bunu yapmak istemiyorsanız, VM 'niz üzerinde aşağıdaki komutları çalıştırın.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure portalı

Application Insights ve Log Analytics gibi Azure Izleyici portalı deneyimlerini kullanmak için, Azure portal ve Azure Izleyici uzantılarına özel ağlarda erişilebilir durumda izin vermeniz gerekir. Ağ güvenlik grubunuza **AzureActiveDirectory**, **AzureResourceManager**, **Azurefrontkapısı. Firstpartisi** ve **Azurefrontkapısı. ön uç** [hizmeti etiketleri](../../firewall/service-tags.md) ekleyin.

### <a name="querying-data"></a>Veri sorgulama
[ `externaldata` İşleci](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) , depolama hesaplarından veri okuduğu ancak depolamaya özel olarak erişildiğine dair özel bir bağlantı üzerinden desteklenmez.

### <a name="programmatic-access"></a>Programlı erişim

REST API, [CLI](/cli/azure/monitor) veya PowerShell 'i özel ağlarda Azure izleyici ile kullanmak için,**AzureActiveDirectory** ve AzureResourceManager [hizmet etiketlerini](../../virtual-network/service-tags-overview.md)güvenlik duvarınızın  içine ekleyin.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Bir içerik teslim ağından SDK indirmelerini Application Insights

Tarayıcı bir CDN 'den kod indirmeye çalışmaması için betikinizdeki JavaScript kodunu paketleyin. [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) 'da bir örnek verilmiştir

### <a name="browser-dns-settings"></a>Tarayıcı DNS ayarları

Azure Izleyici kaynaklarınıza özel bir bağlantı üzerinden bağlanıyorsanız, bu kaynaklara giden trafik ağınızda yapılandırılan özel uç nokta üzerinden gitmelidir. Özel uç noktayı etkinleştirmek için DNS ayarlarınızı [özel bir uç noktaya bağlanma](#connect-to-a-private-endpoint)bölümünde açıklandığı gibi güncelleştirin. Bazı tarayıcılar, sizin ayarladığınız kullanıcılar yerine kendi DNS ayarlarını kullanır. Tarayıcı, Azure Izleyici genel uç noktalarına bağlanmayı ve özel bağlantıyı tamamen atlamayı deneyebilir. Tarayıcıların ayarlarının eski DNS ayarlarını geçersiz kılmayacağını veya önbelleğe aldığını doğrulayın. 

## <a name="next-steps"></a>Sonraki adımlar

- [Özel depolama](private-storage.md) hakkında bilgi edinin
