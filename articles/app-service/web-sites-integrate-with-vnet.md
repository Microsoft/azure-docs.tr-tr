---
title: Uygulamayı Azure sanal ağı ile tümleştirme-Azure App Service
description: Azure App Service bir uygulamanın yeni veya mevcut bir Azure sanal ağına nasıl bağlanacağını gösterir
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a6d0cba41e694e154da32a878cb4c076aae13e65
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034716"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uygulamanızı bir Azure sanal ağıyla tümleştirin
Bu belgede Azure App Service sanal ağ tümleştirme özelliği ve [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)uygulamalarla nasıl ayarlanacağı açıklanmaktadır. [Azure sanal ağları][VNETOverview] (VNet), internet 'ten yönlendirilebilir olmayan bir ağa Azure kaynaklarınızın çoğunu yerleştirme izni verir.  

Azure App Service iki çeşitliliğe sahiptir. 

1. Yalıtılmış haricinde fiyatlandırma planlarının tam aralığını destekleyen çok kiracılı sistemler
2. Sanal ağınıza dağıtan ve yalıtılmış fiyatlandırma planı uygulamalarını destekleyen App Service Ortamı (ASE)

Bu belge, çok kiracılı App Service kullanılmak üzere olmak üzere iki VNet tümleştirme özelliği aracılığıyla gider. Uygulamanız [App Service ortamı][ASEintro]yer alıyorsa, zaten VNET 'te bulunur ve aynı VNET 'teki kaynaklara ulaşmak Için VNET tümleştirme özelliğinin kullanılmasını gerektirmez. Tüm App Service ağ özellikleriyle ilgili ayrıntılar için, [App Service ağ özelliklerini](networking-features.md) okuyun

VNet tümleştirme özelliğine iki biçim vardır

1. Bir sürüm aynı bölgedeki VNET 'ler ile tümleştirmeyi mümkün. Bu özelliğin bu biçimi, aynı bölgedeki bir VNet 'te bir alt ağ gerektirir. Bu özellik hala önizlemededir ancak aşağıda belirtilen bazı uyarılarla Windows uygulama üretim iş yükleri için desteklenir.
2. Diğer sürüm, diğer bölgelerde veya klasik VNET 'lerde VNET 'ler ile tümleştirmeyi mümkün bir şekilde sunar. Özelliğin bu sürümü, sanal ağ geçidinin VNet 'iniz üzerinde dağıtılmasını gerektirir. Bu, Noktadan siteye VPN tabanlı özelliktir ve yalnızca Windows uygulamaları ile desteklenir.

Bir uygulama tek seferde VNet tümleştirme özelliğinin yalnızca bir biçimini kullanabilir. Bu soruyu daha sonra kullanmanız gereken özelliktir. Birçok şey için kullanabilirsiniz. Farklılık göstericiler şunlardır:

| Sorun  | Çözüm | 
|----------|----------|
| Aynı bölgedeki bir RFC 1918 adresine (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ulaşmak ister | bölgesel VNet tümleştirmesi |
| Klasik VNet veya başka bir bölgedeki VNet 'teki kaynaklara ulaşmak ister | Ağ Geçidi gerekli VNet tümleştirmesi |
| ExpressRoute üzerinde RFC 1918 uç noktalarına ulaşmak ister | bölgesel VNet tümleştirmesi |
| Hizmet uç noktaları genelinde kaynaklara ulaşmak ister misiniz? | bölgesel VNet tümleştirmesi |

Her iki özellik de ExpressRoute üzerinde RFC olmayan 1918 adreslerine ulaşmanıza imkan tanır. Bunu yapmak için, şimdilik bir AO kullanmanız gerekir.

Bölgesel VNet tümleştirmesinin kullanılması, VNet 'iniz şirket içi sunucuya bağlanamaz veya hizmet uç noktalarını yapılandırmaz. Bu, ayrı ağ yapılandırması. Bölgesel VNet tümleştirmesi, uygulamanızın bu bağlantı türleri arasında çağrı yapmasına olanak sağlar.

VNet tümleştirmesi, kullanılan sürümden bağımsız olarak, Web uygulamanıza sanal ağınızdaki kaynaklara erişmenizi sağlar, ancak sanal ağdan Web uygulamanıza gelen özel erişim izni vermez. Özel site erişimi, uygulamanızı yalnızca bir Azure sanal ağı içindeki gibi özel bir ağdan erişilebilir hale getirme anlamına gelir. VNet tümleştirmesi yalnızca uygulamanızdan VNet 'e giden çağrılar yapmak içindir. 

VNet tümleştirme özelliği:

* Standart, Premium veya PremiumV2 fiyatlandırma planı gerektirir 
* TCP ve UDP 'yi destekler
* App Service uygulamalarla ve Işlev uygulamalarıyla çalışır

VNet tümleştirmesinin şunları dahil desteklemediği bazı şeyler vardır:

* sürücü bağlama
* AD tümleştirmesi 
* NetBios

## <a name="regional-vnet-integration"></a>Bölgesel VNet tümleştirmesi 

> [!NOTE]
> Eşleme, Linux tabanlı App Service için henüz kullanılabilir değil.
>

VNet tümleştirmesi, uygulamanızla aynı bölgedeki sanal ağlar ile kullanıldığında, en az 32 adresi olan bir temsilci alt ağın kullanılmasını gerektirir. Alt ağ başka bir şey için kullanılamaz. Uygulamanızdan gerçekleştirilen giden çağrılar, temsilcili alt ağdaki adreslerden yapılır. VNet tümleştirmesinin bu sürümünü kullandığınızda, sanal ağınızdaki adreslerden çağrılar yapılır. Sanal ağınızdaki adreslerin kullanılması, uygulamanızın şunları yapmasını sağlar:

* Hizmet uç noktası güvenliği sağlanmış hizmetlerine çağrılar yapın
* ExpressRoute bağlantıları arasında kaynaklara erişme
* Bağlı olduğunuz VNet 'teki kaynaklara erişin
* ExpressRoute bağlantıları dahil olmak üzere eşlenen bağlantılar arasındaki kaynaklara erişin

Bu özellik önizleme aşamasındadır ancak Windows uygulama üretim iş yükleri için aşağıdaki sınırlamalara sahip olarak desteklenir:

* Yalnızca RFC 1918 aralığındaki adreslere ulaşabilirsiniz. Bunlar 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 adres bloklarından adreslerdir.
* Genel eşleme bağlantıları genelindeki kaynaklara ulaşılamıyor
* Uygulamanızdan sanal ağınıza gelen trafik üzerinde yollar ayarlayamazsınız
* Özelliği yalnızca PremiumV2 App Service planlarını destekleyen daha yeni App Service ölçek birimlerinden kullanılabilir.
* Tümleştirme alt ağı yalnızca bir App Service planı tarafından kullanılabilir
* Özellik, App Service Ortamı yalıtılmış plan uygulamaları tarafından kullanılamaz
* Özellik, Kaynak Yöneticisi VNet 'iniz için 32 adresi veya daha büyük bir/27 olan kullanılmayan bir alt ağ gerektiriyor
* Uygulama ve VNet aynı bölgede olmalıdır
* Tümleşik bir uygulamayla VNet 'i silemezsiniz. Önce tümleştirmeyi kaldırmanız gerekir 
* App Service planı başına yalnızca bir bölgesel VNet tümleştirmesi olabilir. Aynı App Service planındaki birden çok uygulama aynı VNet 'i kullanabilir. 
* Bölgesel VNet tümleştirmesi kullanan bir uygulama varken, bir uygulamanın veya App Service planının aboneliğini değiştiremezsiniz

Her bir App Service plan örneği için bir adres kullanılır. Uygulamanızı 5 örneğe ölçeklendirdiyseniz 5 adres kullanılır. Alt ağ boyutu atamadan sonra değiştirilemediğinden, uygulamanızın ulaşabileceği ölçeğe uyum sağlayacak kadar büyük bir alt ağ kullanmanız gerekir. 64 adresi olan bir/26 önerilen boyutdir. App Service planının boyutunu değiştirmediyseniz, 32 adres içeren a/27, Premium App Service plan 20 örneklerine sahip olur. Bir App Service planını yukarı veya aşağı ölçeklendirirseniz, kısa bir süre için birçok adrese iki kez ihtiyacınız vardır. 

Daha önce başka bir App Service planında bulunan uygulamalar tarafından zaten bağlı olan bir VNet 'e erişmek için başka bir App Service planındaki uygulamalarınızın, önceden var olan VNet tümleştirmesi tarafından kullanılandan farklı bir alt ağ seçmeniz gerekir.  

Özelliği, Linux için de önizleme aşamasındadır. VNet tümleştirme özelliğini aynı bölgedeki bir Kaynak Yöneticisi VNet ile kullanmak için:

1. Portalda ağ kullanıcı arabirimine gidin. Uygulamanız yeni özelliği kullanabiliyor ise VNet (Önizleme) ekleme seçeneği görüntülenir.  

   ![VNet tümleştirmesini seçin][6]

1. **VNET Ekle (Önizleme)** öğesini seçin.  

1. Tümleştirmek istediğiniz Kaynak Yöneticisi VNet ' i seçin ve ardından yeni bir alt ağ oluşturun veya önceden varolan boş bir alt ağ seçin. Tümleştirmenin tamamlanmasını bir dakikadan kısa sürer. Tümleştirme sırasında uygulamanız yeniden başlatılır.  Tümleştirme tamamlandığında, ile tümleştirildiği VNet 'teki ayrıntıları ve en üstte, özelliğin önizlemede olduğunu belirten bir başlık görürsünüz.

   ![VNet ve alt ağ seçin][7]

Uygulamanız VNet 'iniz ile tümleştirildiğinde, sanal ağınızın yapılandırıldığı aynı DNS sunucusunu kullanır. 

Bölgesel VNet tümleştirmesi, tümleştirme alt ağınızın Microsoft. Web 'e devredilmesini gerektirir.  VNet tümleştirme Kullanıcı arabirimi, alt ağı Microsoft. Web 'e otomatik olarak devredebilir. Hesabınız bunu ayarlamak için yeterli ağ izinlerine sahip değilse, alt ağı temsilci olarak atamak için tümleştirme alt ağlarınızın özniteliklerini ayarlayabilen bir kişiye ihtiyacınız olacaktır. Tümleştirme alt ağını el ile atamak için Azure sanal ağ alt ağı Kullanıcı arabirimine gidin ve Microsoft. Web için temsilci belirleyin.

Uygulamanızın VNet bağlantısını kesmek için **bağlantıyı kes**' i seçin. Bu işlem Web uygulamanızı yeniden başlatacak. 


#### <a name="web-app-for-containers"></a>Kapsayıcılar için Web Apps

Yerleşik görüntülerle Linux üzerinde App Service kullanıyorsanız, bölgesel VNet tümleştirme özelliği ek değişiklik yapılmadan işe yarar. Kapsayıcılar için Web App kullanıyorsanız, VNet tümleştirmesini kullanabilmeniz için Docker görüntünüzü değiştirmeniz gerekir. Docker yansımanıza, sabit kodlanmış bir bağlantı noktası numarası kullanmak yerine ana Web sunucusunun dinleme bağlantı noktası olarak bağlantı noktası ortam değişkenini kullanın. Bağlantı noktası ortam değişkeni, kapsayıcı başlangıç saatinde App Service platformu tarafından otomatik olarak ayarlanır. SSH kullanıyorsanız, SSH Daemon 'ın bölgesel VNet tümleştirmesi kullanılırken SSH_PORT ortam değişkeni tarafından belirtilen bağlantı noktası numarasını dinlemek üzere yapılandırılması gerekir.

### <a name="service-endpoints"></a>Hizmet Uç Noktaları

Yeni VNet tümleştirme özelliği, hizmet uç noktalarını kullanmanıza olanak sağlar.  Hizmet uç noktalarını uygulamanızla birlikte kullanmak için, seçili bir sanal ağa bağlanmak üzere yeni VNet tümleştirmesini kullanın ve ardından tümleştirme için kullandığınız alt ağdaki hizmet uç noktalarını yapılandırın. 


### <a name="how-vnet-integration-works"></a>VNet tümleştirmesinin çalışması

App Service uygulamalar çalışan rollerinde barındırılır. Temel ve daha yüksek fiyatlandırma planları, aynı çalışanlar üzerinde çalışan başka müşteri iş yükleri bulunmayan ayrılmış barındırma planlardır. VNet tümleştirmesi, yetkilendirilmiş alt ağdaki adreslerle sanal arabirimler bağlayarak işe yarar. Kimden adresi sanal ağınız içinde olduğundan, VNET 'iniz içindeki bir VM 'de olduğu gibi, sanal ağınız üzerinde veya içinde birçok şeyin erişimi vardır. Ağ uygulamasının VNet 'iniz üzerinde bir VM çalıştırmasının dışında ve bu özellik kullanılırken bazı ağ özellikleri henüz kullanılamaz.

![Sanal Ağ Tümleştirmesi](media/web-sites-integrate-with-vnet/vnet-integration.png)

VNet tümleştirmesi etkinleştirildiğinde, uygulamanız yine de normal olarak aynı kanallar üzerinden İnternet 'e giden çağrılar yapmaya devam edecektir. Uygulama özellikleri portalında listelenen giden adresler hala uygulamanız tarafından kullanılan adreslerdir. Uygulamanız için değişiklikler, hizmet uç noktası güvenliği sağlanmış hizmetler veya RFC 1918 adresleri için de sanal ağınıza gider. 

Özelliği, çalışan başına yalnızca bir sanal arabirimi destekler.  Çalışan başına bir sanal arabirim, App Service plan başına bir bölgesel VNet tümleştirmesi anlamına gelir. Aynı App Service planındaki tüm uygulamalar aynı VNet tümleştirmesini kullanabilir, ancak ek bir sanal ağa bağlanmak için bir uygulamaya ihtiyacınız varsa, başka bir App Service planı oluşturmanız gerekir. Kullanılan sanal arabirim, müşterilerin doğrudan erişimine sahip olduğu bir kaynak değildir.

Bu teknolojinin nasıl çalıştığı doğası nedeniyle, VNet tümleştirmeyle kullanılan trafik ağ Izleyicisi veya NSG akış günlüklerinde gösterilmez.  

## <a name="gateway-required-vnet-integration"></a>Ağ Geçidi gerekli VNet tümleştirmesi 

Ağ Geçidi gereken VNet tümleştirme özelliği:

* Herhangi bir bölgedeki sanal ağlara bağlanmak için kullanılabilir Kaynak Yöneticisi veya klasik VNET 'ler
* Bir uygulamanın tek seferde yalnızca 1 VNet 'e bağlanmasını sağlar
* App Service planında tümleştirilebilen beş adede kadar VNET sağlar 
* Bir App Service planı tarafından kullanılabilecek toplam sayıyı etkilemeden bir App Service planında birden çok uygulama tarafından kullanılmasını sağlar.  Aynı App Service planında aynı VNet 'i kullanan 6 uygulamanız varsa, bu, kullanılmakta olan 1 VNet olarak sayılır. 
* Noktadan siteye VPN ile yapılandırılmış bir sanal ağ geçidi gerektirir
* Ağ geçidinde SLA nedeniyle% 99,9 SLA 'sını destekler

Bu özellik şunları desteklemez:
* Linux uygulamalarıyla kullanma
* ExpressRoute üzerinden kaynaklara erişme 
* Hizmet Uç Noktaları üzerinden kaynaklara erişme 

### <a name="getting-started"></a>Başlangıç

Web uygulamanızı bir sanal ağa bağlamadan önce göz önünde bulundurmanız gereken bazı noktalar şunlardır:

* Hedef sanal ağın, uygulamaya bağlanmadan önce rota tabanlı bir ağ geçidiyle etkin noktadan siteye VPN 'i olması gerekir. 
* VNet, App Service planınız (ASP) ile aynı abonelikte olmalıdır.
* Bir sanal ağ ile tümleştirilen uygulamalar bu sanal ağ için belirtilen DNS 'yi kullanır.

### <a name="set-up-a-gateway-in-your-vnet"></a>Sanal ağınız için bir ağ geçidi ayarlama ###

Noktadan siteye adresleriyle yapılandırılmış bir ağ geçidiniz zaten varsa, uygulamanızla VNet tümleştirmesini yapılandırmaya atlayabilirsiniz.  
Bir ağ geçidi oluşturmak için:

1. Sanal ağınız için [bir ağ geçidi alt ağı oluşturun][creategatewaysubnet] .  

1. [VPN ağ geçidini oluşturun][creategateway]. Rota tabanlı VPN türü seçin.

1. [Noktayı site adresleri olarak ayarlayın][setp2saddresses]. Ağ Geçidi temel SKU 'da değilse, ıKEV2 'nin Noktadan siteye yapılandırmasında devre dışı bırakılması ve SSTP 'nin seçili olması gerekir. Adres alanı, RFC 1918 adres blokları, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 biçiminde olmalıdır

Yalnızca App Service VNet tümleştirmesiyle kullanmak üzere ağ geçidini oluşturuyorsanız, bir sertifikayı karşıya yüklemeniz gerekmez. Ağ geçidinin oluşturulması 30 dakika sürebilir. Ağ Geçidi sağlanıncaya kadar uygulamanızı VNet ile tümleştirimeyeceksiniz. 

### <a name="configure-vnet-integration-with-your-app"></a>Uygulama ile VNet tümleştirmesini yapılandırma 

Uygulamanızda VNet tümleştirmesini etkinleştirmek için: 

1. Azure portal uygulamanıza gidin ve uygulama ayarları ' nı açın ve ağ > VNet tümleştirmesi ' ni seçin. ASP 'niz standart bir SKU 'da olmalıdır veya VNet tümleştirme özelliğinin kullanılması daha iyidir. 
 ![VNet tümleştirmesi Kullanıcı arabirimi @ no__t-1

1. **VNET Ekle**' yi seçin. 
 ![Vnet tümleştirmesi ekleme @ no__t-1

1. VNet ' i seçin. 
  @no__t-VNet 'iniz @ no__t-1 ' i seçin
  
Uygulamanız, bu son adımdan sonra yeniden başlatılacak.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Ağ Geçidi gerekli VNet tümleştirme özelliği nasıl çalışacaktır?

Ağ Geçidi gerekli VNet tümleştirme özelliği, Noktadan siteye VPN teknolojisinin üzerine kurulmuştur. Noktadan siteye teknolojisi, ağ erişimini yalnızca uygulamayı barındıran sanal makineye kısıtlar. Uygulamalar, Karma Bağlantılar veya VNet tümleştirmesi aracılığıyla yalnızca İnternet 'e trafik gönderecek şekilde kısıtlanır. 

![VNet tümleştirmesinin çalışması][3]

## <a name="managing-vnet-integration"></a>VNet tümleştirmesini yönetme
Bir sanal ağa bağlanma ve bağlantıyı kesme özelliği uygulama düzeyindedir. Birden çok uygulama genelinde VNet tümleştirmesini etkileyebilecek işlemler App Service plan düzeyidir. App > Ağ > VNet tümleştirme Portalı ' ndan VNet 'iniz hakkında ayrıntılı bilgi edinebilirsiniz. Bu App Service planındaki uygulamaların belirli bir tümleştirmeyi kullandığını da içeren ASP > ağı > VNet tümleştirme portalındaki ASP düzeyinde benzer bilgileri görebilirsiniz.

 ![VNet ayrıntıları][4]

VNet tümleştirme Kullanıcı arabiriminde size sunulan bilgiler, uygulama ve ASP portalları arasında aynıdır.

* VNet adı-sanal ağ kullanıcı arabirimine bağlantılar
* Konum-sanal ağınızın konumunu yansıtır. Başka bir konumdaki VNet ile tümleştirme, uygulamanız için gecikme sorunlarına neden olabilir. 
* Sertifika durumu-sertifikalarınızın App Service planınız ile VNet 'iniz arasında eşitlenmiş olup olmadığını gösterir.
* Ağ Geçidi durumu-ağ geçidi gerekli VNet tümleştirmesini kullanmanız gerekir, ağ geçidi durumunu görebilirsiniz.
* VNet adres alanı-VNet 'iniz için IP adresi alanını gösterir. 
* Noktadan siteye adres alanı-VNet 'iniz için Site IP adresi alanının noktasını gösterir. Ağ Geçidi gerekli özelliğini kullanırken sanal ağınıza çağrı yaparken, adresiniz bu adreslerden biri olacaktır. 
* Siteden siteye adres alanı-VNet 'nizi şirket içi kaynaklarınıza veya diğer VNet 'e bağlamak için siteden siteye VPN 'Ler kullanabilirsiniz. Bu VPN bağlantısıyla tanımlanan IP aralıkları burada gösterilmiştir.
* DNS sunucuları-sanal ağınız ile yapılandırılmış DNS sunucularını gösterir.
* VNet 'e yönlendirilen IP adresleri-VNet 'iniz için trafiği yönlendirmek üzere kullanılan adres bloklarını gösterir 

VNet tümleştirmenize ilişkin uygulama görünümünde gerçekleştirebileceğiniz tek işlem, uygulamanızın bağlı olduğu VNet 'ten bağlantısını kesmesidir. Uygulamanızın VNet bağlantısını kesmek için **bağlantıyı kes**' i seçin. VNet bağlantısı kesildiğinde uygulamanız yeniden başlatılacak. Bağlantı kesilmesi VNet 'i değiştirmez. Alt ağ veya ağ geçidi kaldırılmaz. Daha sonra sanal ortamınızı silmek istiyorsanız, öncelikle uygulamanızın VNet bağlantısını kesmeniz ve ağ geçitleri gibi kaynakları silmeniz gerekir. 

ASP VNet tümleştirmesi Kullanıcı arabirimine ulaşmak için ASP Kullanıcı arabirimini açın ve **ağ**' ı seçin.  VNet tümleştirmesi altında, **yapılandırmak için buraya tıklayın ' ı** seçerek ağ özelliği durum Kullanıcı arabirimini açın.

![ASP VNet tümleştirme bilgileri][5]

ASP VNet tümleştirmesi Kullanıcı arabirimi, ASP 'inizdeki uygulamalar tarafından kullanılan tüm sanal ağları gösterir. Her VNet hakkındaki ayrıntıları görmek için ilgilendiğiniz VNet 'e tıklayın. Burada gerçekleştirebileceğiniz iki eylem vardır.

* **Ağ eşitleme**. Ağ eşitleme işlemi yalnızca ağ geçidine bağımlı VNet tümleştirme özelliği için geçerlidir. Bir eşitleme ağı işleminin gerçekleştirilmesi, sertifikalarınızın ve ağ bilgilerinizin eşitlenmiş olmasını sağlar. VNet 'nizin DNS 'sini ekler veya değiştirirseniz, bir **eşitleme ağı** işlemi gerçekleştirmeniz gerekir. Bu işlem, bu VNet 'i kullanan tüm uygulamaları yeniden başlatacak.
* **Rota Ekle** Yolların eklenmesi, giden trafiği sanal ağınıza yönlendirir.

**Yönlendirme** VNet 'iniz üzerinde tanımlanan yollar uygulamanızdan sanal ağınıza giden trafiği yönlendirmek için kullanılır. VNet 'e ek giden trafik göndermeniz gerekiyorsa, bu adres bloklarını buraya ekleyebilirsiniz. Bu özellik yalnızca ağ geçidi gerekli VNet tümleştirmesi ile kullanılabilir.

**Sertifikalar** Ağ Geçidi gereken VNet tümleştirmesi etkinleştirildiğinde, bağlantının güvenliğini sağlamak için gerekli bir sertifika alışverişi vardır. Sertifikaların yanı sıra, DNS yapılandırması, rotalar ve ağı tanımlayan diğer benzer şeylerdir.
Sertifikalar veya ağ bilgileri değiştirilirse "ağı Eşitle" seçeneğine tıklamanız gerekir. "Ağ Eşitle" seçeneğine tıkladığınızda, uygulamanız ve VNet 'iniz arasındaki bağlantıda kısa bir kesinti oluşmasına neden olursunuz. Uygulamanız yeniden başlatılana karşın bağlantı kaybı sitenizin düzgün şekilde çalışmamasına neden olabilir. 

## <a name="accessing-on-premises-resources"></a>Şirket içi kaynaklara erişme
Uygulamalar, siteden siteye bağlantıları olan VNET 'ler ile tümleştirerek şirket içi kaynaklara erişebilir. Ağ Geçidi gerekli VNet tümleştirmesini kullanıyorsanız, şirket içi VPN ağ geçidi yollarınızı Noktadan siteye adres bloklarında güncelleştirmeniz gerekir. Siteden siteye VPN ilk kez ayarlandığında, yapılandırmak için kullanılan betikler yolları doğru şekilde ayarlayabilmelidir. Siteden siteye VPN 'nizi oluşturduktan sonra Noktadan siteye adresleri eklerseniz, rotaları el ile güncelleştirmeniz gerekir. Bu şekilde, ağ geçidi başına farklılık gösteren ve burada açıklanmayan Ayrıntılar açıklanmaktadır. Siteden siteye VPN bağlantısıyla BGP ile yapılandırılmış olamaz.

Bölgesel VNet tümleştirme özelliğinin sanal ağınıza ve şirket içi ağa ulaşması için ek bir yapılandırma gerekmez. Sanal ortamınızı ExpressRoute veya siteden siteye VPN kullanarak şirket içi olarak bağlamanız yeterlidir. 

> [!NOTE]
> Ağ Geçidi gerekli VNet tümleştirme özelliği, bir uygulamayı ExpressRoute ağ geçidi olan bir VNet ile tümleştirmez. ExpressRoute ağ geçidi, birlikte [bulunma modunda][VPNERCoex] yapılandırılmış olsa bile VNET tümleştirmesi çalışmaz. Kaynaklara bir ExpressRoute bağlantısı aracılığıyla erişmeniz gerekiyorsa, bölgesel VNet tümleştirme özelliğini veya sanal ağınız üzerinde çalışan bir [App Service ortamı][ASE]kullanabilirsiniz. 
> 
> 

## <a name="peering"></a>Eşleme
Bölgesel VNet tümleştirmesiyle eşleme kullanıyorsanız, ek yapılandırma yapmanız gerekmez. 

Ağ Geçidi gereken VNet tümleştirmesini eşleme ile kullanıyorsanız, birkaç ek öğe yapılandırmanız gerekir. Eşleme 'yi uygulamanızla çalışacak şekilde yapılandırmak için:

1. Uygulamanızın bağlandığı VNet 'e bir eşleme bağlantısı ekleyin. Eşleme bağlantısı eklenirken **sanal ağ erişimine Izin ver** ' i etkinleştirin ve **iletilen trafiğe** izin ver ' i işaretleyin ve **ağ geçidi aktarımına izin verin**.
1. Bağlı olduğunuz VNet 'e eşlenmekte olan VNet üzerinde bir eşleme bağlantısı ekleyin. Hedef VNet 'e eşleme bağlantısı eklenirken **sanal ağ erişimine Izin ver** ' i etkinleştirin ve **iletilen trafiğe** izin ver ' i işaretleyin ve **uzak ağ geçitlerine izin**verin.
1. Portalda App Service plan > Ağ > VNet tümleştirme Kullanıcı arabirimine gidin.  Uygulamanızın bağlandığı sanal ağı seçin. Yönlendirme bölümünde, uygulamanızın bağlı olduğu VNet ile ilişkilendirilen VNet 'in adres aralığını ekleyin.  


## <a name="pricing-details"></a>Fiyatlandırma ayrıntıları
Bölgesel VNet tümleştirme özelliğinin, ASP fiyatlandırma katmanı ücretleri dışında kullanılmak üzere ek ücret yoktur.

Ağ Geçidi gerekli VNet tümleştirme özelliğinin kullanımına yönelik üç ilgili ücret vardır:

* ASP fiyatlandırma katmanı ücretleri-uygulamalarınızın standart, Premium veya PremiumV2 App Service bir planda olması gerekir. Bu maliyetler hakkında daha fazla ayrıntı için şu adımları görebilirsiniz: [App Service fiyatlandırması][ASPricing]. 
* Veri aktarımı maliyetleri-VNet aynı veri merkezinde olsa bile veri çıkışı için ücret uygulanır. Bu ücretler [veri aktarımı fiyatlandırma ayrıntılarında][DataPricing]açıklanmaktadır. 
* VPN Gateway maliyetler-Noktadan siteye VPN için gereken VNet ağ geçidinin maliyeti vardır. Ayrıntılar [VPN Gateway fiyatlandırma][VNETPricing] sayfasıdır.


## <a name="troubleshooting"></a>Sorun giderme
Özelliği kolayca ayarlanabilir, bu da deneyiminizin ücretsiz olacağı anlamına gelmez. İstenen uç noktanıza erişmede sorun yaşamanız gerekir, bu, uygulama konsolundan bağlantıyı test etmek için kullanabileceğiniz bazı yardımcı programlar vardır. Kullanabileceğiniz iki konsol vardır. Birisi kudu konsoludur ve diğeri Azure portal konsoludur. Uygulamanızın kudu konsoluna ulaşmak için Araçlar-> kudu ' ye gidin. Ayrıca, Kudo konsoluna [SiteName]. scm. azurewebsites. net adresinden ulaşabilirsiniz. Web sitesi yüklendiğinde, hata ayıklama konsolu sekmesine gidin. Daha sonra Azure portal barındırılan konsola ulaşmak için Araçlar-> konsoluna gidin. 

#### <a name="tools"></a>Araçlar
Araçlar **ping**, **nslookup** ve **tracert** , güvenlik kısıtlamaları nedeniyle konsolda çalışmaz. Void 'yi dolduracak şekilde iki ayrı araç eklenmiştir. DNS işlevselliğini test etmek için, nameresolver. exe adlı bir araç ekledik. Söz dizimi aşağıdaki gibidir:

    nameresolver.exe hostname [optional: DNS Server]

Uygulamanızın bağımlı olduğu ana bilgisayar adlarını denetlemek için **nameresolver** kullanabilirsiniz. Bu şekilde, DNS 'niz ile yanlış yapılandırılmış herhangi bir şey varsa veya belki DNS sunucunuza erişiminiz yoksa test edebilirsiniz. Uygulamanızın konsolda kullanacağı DNS sunucusunu WEBSITE_DNS_SERVER ve WEBSITE_DNS_ALT_SERVER ortam değişkenlerine bakarak görebilirsiniz.

Sonraki araç, bir konak ve bağlantı noktası birleşimine TCP bağlantısı için test etmenizi sağlar. Bu araca, **tcma** adı verilir ve sözdizimi şöyledir:

    tcpping.exe hostname [optional: port]

**Tcter** yardımcı programı, belirli bir konağa ve bağlantı noktasına ulaşabilseniz size bildirir. Yalnızca şu durumlarda başarı gösterebilir: konak ve bağlantı noktası birleşimine dinleme yapan bir uygulama varsa ve uygulamanızdan belirtilen konağa ve bağlantı noktasına ağ erişimi varsa.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet barındırılan kaynaklara erişim hatası ayıklama
Uygulamanızın belirli bir konağa ve bağlantı noktasına ulaşmasını engelleyebilen birçok şey vardır. Çoğu zaman üç işlemlerden biridir:

* **Bir güvenlik duvarı bu şekilde yapılır.** Bu şekilde bir güvenlik duvarınız varsa, TCP zaman aşımına uğrayacaktır. Bu durumda TCP zaman aşımı 21 saniyedir. Bağlantıyı sınamak için **tcma** aracını kullanın. TCP zaman aşımları, güvenlik duvarlarının ötesinde çok sayıda şey olabilir ancak buradan başlayabilir. 
* **DNS erişilebilir değil.** DNS sunucusu başına DNS zaman aşımı üç saniyedir. İki DNS sunucunuz varsa, zaman aşımı 6 saniyedir. DNS çalışıp çalışmadığını görmek için nameresolver kullanın. Sanal ağınızın yapılandırıldığı DNS 'i kullanmayan için nslookup 'ı kullanamıyoruz. Erişilemezse, DNS erişimi engelleyen bir güvenlik duvarına veya NSG 'ye sahip olabilirsiniz veya bu durumda olabilir.

Bu öğeler sorunlarınızı yanıtlamazsanız, şunun gibi şeyler için göz atın: 

**bölgesel VNet tümleştirmesi**
* hedef bir RFC 1918 adresi mi?
* Tümleştirme alt ağınızdan çıkış engelliyor bir NSG var
* ExpressRoute veya VPN üzerinden gitecekseniz, şirket içi ağ geçidiniz trafiği Azure 'a yeniden yönlendirmek üzere yapılandırılmış mı? VNet 'iniz içindeki uç noktalara ulaşabiliyor ancak şirket içinde değil, bu denetlenecek.

**Ağ Geçidi gerekli VNet tümleştirmesi**
* , RFC 1918 aralıklarında (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255) Noktadan siteye adres aralığıdır mi?
* Ağ Geçidi portalda mi çalışıyor? Ağ geçidinizin kapalıysa, daha sonra geri getirin.
* Sertifikalar eşitlenmiş olarak gösteriliyor mu veya ağ yapılandırmasının değiştiğini kuşkulu mı?  Sertifikalarınız eşitlenmemiş veya VNet yapılandırmanızda ASPs 'niz ile eşitlenmemiş bir değişiklik yapıldığından şüphelenirseniz, "ağa eşitleme" düğmesine basın.
* ExpressRoute veya VPN üzerinden gitecekseniz, şirket içi ağ geçidiniz trafiği Azure 'a yeniden yönlendirmek üzere yapılandırılmış mı? VNet 'iniz içindeki uç noktalara ulaşabiliyor ancak şirket içinde değil, bu denetlenecek.

Belirli bir konağa erişimi engelleyip engellemediğini göremediği için, ağ sorunlarını ayıklama işlemi bir zorluk mıdır: bağlantı noktası birleşimi. Bazı nedenler şunlardır:

* ana bilgisayarınızda, Noktadan siteye IP aralığına olan uygulama bağlantı noktasına erişimi engellediği bir güvenlik duvarınız var. Çakışan alt ağlar genellikle genel erişim gerektirir.
* hedef ana bilgisayarınız çalışmıyor
* uygulamanız çalışmıyor
* yanlış IP veya ana bilgisayar adı vardı
* Uygulamanız, beklediğinizden farklı bir bağlantı noktasını dinliyor. Uç nokta konağında "netstat-Aon" öğesini kullanarak, işlem KIMLIĞINIZI dinleme bağlantı noktasıyla eşleştirebilirsiniz. 
* ağ güvenlik gruplarınız, Noktadan siteye IP aralığına, uygulama ana bilgisayarınıza ve bağlantı noktasına erişimi engelleyecek şekilde yapılandırılır.

Uygulamanızın gerçekten kullanacağı adresi bilmiyorsanız unutmayın. Tümleştirme alt ağında veya Noktadan siteye adres aralığında herhangi bir adres olabilir, bu nedenle tüm adres aralığından erişime izin vermeniz gerekir. 

Ek hata ayıklama adımları şunlardır:

* VNet 'iniz içindeki bir VM 'ye bağlanın ve kaynak konağınız üzerinde bağlantı noktasını buradan ulaşmaya çalışın. TCP erişimini test etmek için PowerShell komut **testi-NetConnection**komutunu kullanın. Söz dizimi aşağıdaki gibidir:

      test-netconnection hostname [optional: -Port]

* bir VM üzerinde bir uygulama açın ve **tcup** kullanarak uygulamadan konsoldan bu konağa ve bağlantı noktasına erişimi test edin

#### <a name="on-premises-resources"></a>Şirket içi kaynaklar ####

Uygulamanız Şirket içindeki bir kaynağa ulaşamadıysanız, sanal ağınızdan kaynağa ulaşabilseniz kontrol edin. TCP erişimini denetlemek için **Test-NetConnection** PowerShell komutunu kullanın. VM 'niz şirket içi kaynağına ulaşamadıysa, VPN veya ExpressRoute bağlantınız düzgün şekilde yapılandırılmamış olabilir.

VNet barındırılan VM 'niz şirket içi sisteminize ulaşılırsa ancak uygulamanız bu nedenle, nedeni aşağıdakilerden biri olabilir:

* rotalarınız, alt ağlarınız ile yapılandırılmaz veya şirket içi ağ geçidinizdeki site adres aralıklarını işaret etmez
* ağ güvenlik gruplarınız, Noktadan siteye IP aralığınız için erişimi engelliyor
* Şirket içi güvenlik duvarları, Noktadan siteye IP aralığından trafiği engelliyor
* bölgesel VNet tümleştirme özelliğini kullanarak, RFC 1918 olmayan bir adrese ulaşmaya çalışıyorsunuz


## <a name="powershell-automation"></a>PowerShell Otomasyonu

PowerShell kullanarak App Service bir Azure sanal ağı ile tümleştirebilirsiniz. Bir çalıştırma için, bkz. bir [uygulamayı Azure sanal ağına bağlama Azure App Service](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
