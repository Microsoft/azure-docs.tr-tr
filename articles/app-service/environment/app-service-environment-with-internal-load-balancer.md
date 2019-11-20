---
title: App Service ortamı ile iç yük dengeleyici oluşturma ve kullanma-Azure | Microsoft Docs
description: ILB ile ATıCı oluşturma ve kullanma
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 65d62df954dbbfbdd221adb33eccd82f73588fae
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069889"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Ortamı ile Iç Load Balancer kullanma

> [!NOTE] 
> Bu makale App Service Ortamı v1 ile ilgilidir. Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
>

App Service Ortamı (Ao) özelliği, çok kiracılı damgalar içinde kullanılamayan gelişmiş bir yapılandırma özelliği sunan Azure App Service bir Premium hizmet seçeneğidir. ASE özelliği temel olarak Azure sanal ağınızdaki (VNet) Azure App Service dağıtır. App Service ortamları tarafından sunulan yetenekler hakkında daha fazla bilgi edinmek için [App Service ortamı][WhatisASE] belgelerini okuyun. VNet 'te çalışan avantajlarından haberdar değilseniz, [Azure sanal ağ hakkında SSS][virtualnetwork]makalesini okuyun. 

## <a name="overview"></a>Genel Bakış
Bir ASE, internet 'e erişilebilen bir uç nokta veya VNet 'iniz için bir IP adresi ile dağıtılabilir. IP adresini bir sanal ağ adresine ayarlamak için bir Iç Load Balancer (ıLB) ile ASE 'nizi dağıtmanız gerekir. Ao 'niz bir ıLB ile yapılandırıldığında şunları sağlarsınız:

* kendi etki alanınız veya alt etki alanı. Bu belgede, daha kolay hale getirmek için alt etki alanı varsayılır ancak her iki şekilde de yapılandırabilirsiniz. 
* HTTPS için kullanılan sertifika
* Alt etki alanınızın DNS yönetimi. 

Buna karşılık, şunun gibi şeyler yapabilirsiniz:

* bir siteden siteye veya ExpressRoute VPN aracılığıyla erişebileceğiniz bulutta güvenli bir şekilde, iş kolu uygulamaları gibi intranet uygulamalarını barındırın.
* bulutta, genel DNS sunucularında listelenmeyen uygulamaları barındırın
* ön uç uygulamalarınızın güvenli bir şekilde tümleştirebileceği Internet yalıtılmış arka uç uygulamaları oluşturun

#### <a name="disabled-functionality"></a>Devre dışı bırakılan işlevler
ILB Ao kullanırken yapaistemediğiniz bazı şeyler vardır. Bu şeyler şunları içerir:

* IPSSL kullanma
* belirli uygulamalara IP adresleri atama
* Portal üzerinden bir uygulamayla bir sertifika satın alma ve kullanma. Kuşkusuz, bir sertifika yetkilisi ile doğrudan sertifikalar elde edebilir ve bunu uygulamalarınızla kullanabilirsiniz, ancak Azure portal.

## <a name="creating-an-ilb-ase"></a>ILB ATıCı oluşturma
ILB ATıCı oluşturmak, normal olarak bir AO oluşturmaktan daha farklı değildir. Ao oluşturma hakkında daha ayrıntılı bir tartışma için bkz. [nasıl App Service ortamı oluşturma][HowtoCreateASE]. ILB ASE oluşturma işlemi, ASE oluşturma sırasında VNet oluşturma veya önceden var olan VNet seçme arasında aynıdır. ILB ASE oluşturmak için: 

1. Azure portal, **kaynak oluştur-> Web ve mobil-> App Service ortamı**' ı seçin.
2. Aboneliğinizi seçin.
3. Kaynak grubunu seçin veya oluşturun.
4. Bir VNet seçin veya oluşturun.
5. VNet seçilirse bir alt ağ oluşturun.
6. **Sanal ağ/konum-> VNET yapılandırması** ' nı SEÇIN ve VIP türünü iç olarak ayarlayın.
7. Bir alt etki alanı adı belirtin (Bu ad, bu Ao 'da oluşturulan uygulamalar için kullanılan alt etki alanıdır).
8. **Tamam** ' ı ve ardından **Oluştur**' u seçin.

![][1]

Sanal ağ bölmesinde, bir dış VIP veya Iç VIP arasında seçim yapmanızı sağlayan bir VNet yapılandırma seçeneği vardır. Varsayılan değer External ' dır. Dış olarak ayarlandıysa Ao 'niz internet 'e erişilebilir bir VIP kullanır. Dahili ' ı seçerseniz ASE 'niz VNet 'iniz içindeki bir IP adresi üzerinde ıLB ile yapılandırılır. 

Dahili ' ı seçtikten sonra Ao verilerinize daha fazla IP adresi ekleyebilme kaldırılır ve bunun yerine Ao 'nun alt etki alanını sağlamanız gerekir. Bir dış VIP ile Ao 'da, Ao 'nun adı bu Ao 'da oluşturulan uygulamalar için alt etki alanında kullanılır. Ao 'niz ***contosotest*** olarak adlandırılmışsa ve uygulamanızın adı ***MyTest***ise, alt etki alanı ***contosotest.p.azurewebsites.net*** biçimindedir ve bu uygulamanın URL 'si ***MyTest.contosotest.p.azurewebsites.net***olur. VIP türünü Iç olarak ayarlarsanız Ao adınız Ao 'nun alt etki alanında kullanılmaz. Alt etki alanını açık olarak belirtirsiniz. Alt etki alanı ***contoso.Corp.net*** ise ve bu, ***timeretaşıma***adlı bir uygulama yaptıysanız, bu uygulamanın URL 'si ***timereporting.contoso.Corp.net***olur.

## <a name="apps-in-an-ilb-ase"></a>ILB Ao 'da uygulamalar
ILB Ao 'da bir uygulama oluşturmak, genellikle bir uygulama oluşturmak için de aynıdır. 

1. Azure portal, **kaynak oluştur-> Web ve mobil-> Web** veya **Mobil** ya da **API uygulaması**seçin.
2. Uygulamanın adını girin.
3. Aboneliğinizi seçin.
4. Kaynak grubunu seçin veya oluşturun.
5. App Service planı (ASP) seçin veya oluşturun. Yeni bir ASP oluşturuyorsanız, konum olarak ATıCı ' ı seçin ve ASP 'nizin oluşturulmasını istediğiniz çalışan havuzunu seçin. ASP 'yi oluşturduğunuzda, konum ve çalışan havuzu olarak ATıCı 'nizi seçersiniz. Uygulamanın adını belirttiğinizde, uygulama adınızın altındaki alt etki alanının, Ao 'nizin alt etki alanı ile değiştirildiğini görürsünüz. 
6. **Oluştur**’u seçin. Uygulamanın panonuzda gösterilmesini istiyorsanız **panoya sabitle** onay kutusunu seçtiğinizden emin olun. 

![][2]

Uygulama adı altında, alt etki alanı adı Ao 'nizin alt etki alanını yansıtacak şekilde güncelleştirilir. 

## <a name="post-ilb-ase-creation-validation"></a>ILB as oluşturma doğrulama doğrulaması
Bir ILB ASE, ILB olmayan ASE’den biraz farklıdır. Zaten belirtildiği gibi, kendi DNS 'nizi yönetmeniz ve HTTPS bağlantıları için kendi sertifikanızı sağlamanız gerekir. 

Ao 'nizi oluşturduktan sonra, alt etki alanının belirttiğiniz alt etki alanını gösterdiğini ve **ayar** menüsünde **ILB sertifikası**adlı yeni bir öğe olduğunu fark edeceksiniz. ASE, HTTPS sınamasını kolaylaştıran kendinden imzalı bir sertifika ile oluşturulur. Portal, HTTPS için kendi sertifikanızı sağlamanız gerektiğini söyler, ancak bunun yerine alt etki alanınızın bulunduğu bir sertifikaya sahip olmanız önerilir. 

![][3]

Yalnızca bir sertifika oluşturmaya çalışıyorsanız ve sertifika oluşturmayı bilmiyorsanız, otomatik olarak imzalanan bir sertifika oluşturmak için IIS MMC konsol uygulamasını kullanabilirsiniz. Oluşturulduktan sonra, bir. pfx dosyası olarak dışarı aktarabilir ve ardından ıLB sertifikası Kullanıcı arabiriminde karşıya yükleyebilirsiniz. Otomatik olarak imzalanan bir sertifikayla güvenliği sağlanmış bir siteye eriştiğinizde, tarayıcınız, sertifikayı doğrulayamadığı için eriştiğiniz sitenin güvenli olduğunu belirten bir uyarı verir. Bu uyarıyı önlemek istiyorsanız, alt etki alanıyla eşleşen ve tarayıcınız tarafından tanınan bir güven zincirine sahip olan, doğru şekilde imzalanmış bir sertifikaya sahip olmanız gerekir.

![][6]

Akışı kendi sertifikalarınız ile denemek ve ASE 'nize hem HTTP hem de HTTPS erişimini test etmek istiyorsanız:

1. **Ao-> Settings-> ıLB sertifikaları**oluşturulduktan sonra Ao Kullanıcı arabirimine gidin.
2. Sertifika pfx dosyası seçerek ıLB sertifikasını ayarlayın ve parola sağlayın. Bu adımın işlenmesi biraz zaman alır ve bir ölçeklendirme işleminin sürmekte olduğu ileti görünür.
3. AIN (**Ao-> özellikleri-> sanal IP adresi**) IÇIN ILB adresini alın.
4. Oluşturulduktan sonra Ao 'da bir Web uygulaması oluşturun. 
5. O VNET 'te (ASE veya nesnelerin sonuyla aynı alt ağda değil) bir tane yoksa bir VM oluşturun.
6. Alt etki alanınızın DNS 'i ayarlayın. DNS 'inizdeki alt etki alanı ile bir joker karakter kullanabilirsiniz veya bazı basit sınamalar yapmak istiyorsanız, Web uygulaması adını VIP IP adresine ayarlamak için VM 'nizin ana bilgisayar dosyasını düzenleyin. Ao, ilbase.com alt etki alanı adını içeriyorsa ve mytestapp.ilbase.com adresinde giderilebilmesi için Web App mytestapp ' ı yaptıysanız, ana bilgisayar dosyanızda bu ayarı belirleyin. (Windows 'da, Hosts dosyası C:\Windows\System32\drivers\etc\)
7. Bu VM 'de bir tarayıcı kullanın ve adresine gidin https://mytestapp.ilbase.com (veya Web uygulamanızın adı alt etki alanınızın bulunduğu her ne olursa olsun).
8. Veya bu sanal makinedeki bir tarayıcıyı kullanıp https://mytestapp.ilbase.com sayfasına gidin. Otomatik olarak imzalanan sertifika kullanıyorsanız güvenlik olmaması gerekir. 

ILB 'nizin IP adresi, sanal IP adresi olarak özelliklerinde listelenir.

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE kullanır
#### <a name="network-security-groups"></a>Ağ Güvenlik Grupları
ILB Ao, uygulamalarınız için ağ yalıtımına izin vermez. Uygulamalar erişilebilir değildir ve internet tarafından da bilinir. Bu yaklaşım, iş kolu uygulamaları gibi intranet sitelerini barındırmak için idealdir. Erişimi daha da kısıtlamanız gerektiğinde ağ düzeyinde erişimi denetlemek için ağ güvenlik gruplarını (NSG 'ler) kullanmaya devam edebilirsiniz. 

Erişimi daha fazla kısıtlamak için NSG 'leri kullanmak istiyorsanız, ASE 'nin çalışması için gereken iletişimi kesmediğinizden emin olmanız gerekir. HTTP/HTTPS erişimi yalnızca ASE tarafından kullanılan ıLB aracılığıyla olsa da, ASE, sanal ağın dışındaki kaynaklara bağlı olmaya devam eder. Ağ erişiminin hala gerekli olduğunu görmek için bkz. [ExpressRoute ile App Service ortamları için][ExpressRoute] [bir App Service ortamı gelen trafiği denetleme][ControlInbound] ve ağ yapılandırması ayrıntıları. 

NSG 'lerinizi yapılandırmak için, Azure tarafından ATıCı 'nizi yönetmek üzere kullanılan IP adresini bilmeniz gerekir. Bu IP adresi Ayrıca, internet istekleri yapıyorsa Ao 'ınızdan giden IP adresidir. ASE 'nizin giden IP adresi, ASE 'nizin ömrü boyunca statik kalır. ATıCı 'nizi silip yeniden oluşturursanız, yeni bir IP adresi alırsınız. IP adresini bulmak için **Ayarlar-> Özellikler** ' e gidin ve **giden IP adresini**bulun. 

![][5]

#### <a name="general-ilb-ase-management"></a>Genel ıLB ATıCı yönetimi
ILB Ao 'nun yönetilmesi büyük ölçüde genellikle bir AO 'nın yönetilmesine benzer. Daha fazla ASP örneği barındırmak için çalışan havuzlarınızı ölçeklendirmeli ve artan sayıda HTTP/HTTPS trafiğini işleyecek ön uç sunucularınızı ölçeklendirebilirsiniz. Ao 'un yapılandırmasını yönetme hakkında genel bilgi için, bkz. [App Service ortamı yapılandırma][ASEConfig]. 

Ek yönetim öğeleri sertifika yönetimi ve DNS yönetimi. ILB ASE oluşturulduktan sonra HTTPS için kullanılan sertifikayı edinip karşıya yüklemeniz ve süresi dolmadan önce değiştirmeniz gerekir. Azure temel etki alanına sahip olduğundan, bir dış VIP ile ASE için sertifikalar sağlayabilir. ILB ASE tarafından kullanılan alt etki alanı herhangi bir şey olduğundan, HTTPS için kendi sertifikanızı sağlamanız gerekir. 

#### <a name="dns-configuration"></a>DNS yapılandırması
Dış VIP kullanılırken DNS, Azure tarafından yönetilir. ASE’nizde oluşturulan herhangi bir uygulama, genel bir DNS olan Azure DNS'e otomatik olarak eklenir. ILB ASE'de kendi DNS’inizi yönetmeniz gerekir. Contoso.corp.net gibi belirli bir alt etki alanı için, için ıLB adresinizi işaret eden DNS A kayıtları oluşturmanız gerekir:

    * 
    *. SCM FTP Yayımlama 


## <a name="getting-started"></a>Başlarken
App Service ortamları ile çalışmaya başlamak için bkz. [App Service ortamlarına giriş][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
