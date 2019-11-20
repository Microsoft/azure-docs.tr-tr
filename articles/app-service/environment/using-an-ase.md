---
title: App Service ortamı kullanma-Azure
description: Azure App Service ortamında uygulama oluşturma, yayımlama ve ölçeklendirme
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 841271c474ba8e24bc352bcae1fa037cf382a8ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470589"
---
# <a name="use-an-app-service-environment"></a>App Service ortamı kullanma #

Azure App Service Ortamı, bir müşterinin Azure sanal ağındaki bir alt ağa Azure App Service bir dağıtımdır. Aşağıdakilerden oluşur:

- **Ön uçlar**: ön uçlar, http/https 'nin bir App Service ortamında (ASE) sonlandırmakta olduğu yerdir.
- **Çalışanlar**: uygulamalarınızı barındıran kaynaklar.
- **Veritabanı**: veritabanı ortamı tanımlayan bilgileri barındırır.
- **Depolama**: depolama alanı, müşteri tarafından yayımlanan uygulamaları barındırmak için kullanılır.

> [!NOTE]
> App Service Ortamı iki sürümü vardır: ASEv1 ve ASEv2. ASEv1 ' de, kaynakları kullanabilmeniz için önce kaynakları yönetmeniz gerekir. ASEv1 'ı yapılandırma ve yönetme hakkında bilgi edinmek için bkz. [App Service ortam v1 yapılandırma][ConfigureASEv1]. Bu makalenin geri kalanı ASEv2 ' ye odaklanmaktadır.
>
>

Uygulama erişimi için bir AO (ASEv1 ve ASEv2), dış veya iç VIP ile dağıtabilirsiniz. Dış VIP ile dağıtım genellikle dış Ao olarak adlandırılır. İç sürüm, iç yük dengeleyici (ıLB) kullandığından ıLB aşırı olarak adlandırılır. ILB Ao hakkında daha fazla bilgi edinmek için bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>AS 'de uygulama oluşturma ##

Ao 'da bir uygulama oluşturmak için, normal olarak oluşturduğunuz ve birkaç küçük farklılık ile aynı süreci kullanırsınız. Yeni bir App Service planı oluşturduğunuzda:

- Uygulamanızın dağıtılacağı coğrafi bir konum seçmek yerine konumunuz olarak bir AO seçin.
- Bir as içinde oluşturulan tüm App Service planları yalıtılmış bir fiyatlandırma katmanında olmalıdır.

Ao 'a sahip değilseniz, [App Service ortamı oluşturma][MakeExternalASE]bölümündeki yönergeleri izleyerek bir tane oluşturabilirsiniz.

AS 'de bir uygulama oluşturmak için:

1. **Web uygulaması** > **Web ve mobil** > **kaynak oluştur** ' u seçin.

2. Uygulama için bir ad girin. Ao 'da zaten bir App Service planı seçtiyseniz, uygulamanın etki alanı adı Ao 'nun etki alanı adını yansıtır.

    ![Uygulama adı seçimi][1]

1. Bir abonelik seçin.

1. Yeni bir kaynak grubu için bir ad girin veya **var olanı kullan** ' ı seçin ve açılan listeden birini seçin.

1. İşletim sisteminizi seçin. 

1. AŞIRDE mevcut bir App Service planı seçin veya aşağıdaki adımları izleyerek yeni bir tane oluşturun:

    a. **Yeni oluştur**' u seçin.

    b. App Service planınız için bir ad girin.

    c. **Konum** açılır listesinden atıcı ' i seçin. 
    
    d. **Yalıtılmış** bir fiyatlandırma katmanı seçin. **Seç**' i seçin.

    e. **Tamam**’ı seçin.
    
    ![Yalıtılmış fiyatlandırma katmanları][2]

    > [!NOTE]
    > Linux uygulamaları ve Windows uygulamaları aynı App Service planında olamaz, ancak aynı App Service Ortamı olabilir. 
    >

2. **Oluştur**'u seçin.

## <a name="how-scale-works"></a>Ölçeklendirmenin çalışması ##

Her App Service uygulaması bir App Service planında çalışır. Kapsayıcı modeli, ortamlar App Service planları ve App Service planları uygulama olarak tutar. Bir uygulamayı ölçeklendirirseniz, App Service planı ölçeklendirebilirsiniz ve böylece aynı plandaki tüm uygulamaları ölçeklendirebilirsiniz.

ASEv2 ' de, bir App Service planını ölçeklendirirseniz, gerekli altyapı otomatik olarak eklenir. Altyapı eklenirken, işlemleri ölçeklendirmek için zaman gecikmesi vardır. ASEv1 ' de, App Service planınızı oluşturabilmeniz veya ölçeklendirmeniz için gereken altyapının eklenmesi gerekir. 

Çok kiracılı App Service, bir kaynak havuzu bunu destekleyecek şekilde kullanıma hazır olduğundan ölçekleme genellikle anında gerçekleşir. Bir Ao 'da bu tür bir arabellek yoktur ve kaynaklar ihtiyaç duymak üzere ayrılır.

Ao 'da, 100 örneğe kadar ölçeklendirebilirsiniz. Bu 100 örnekleri tek bir App Service planında olabilir veya birden çok App Service planı arasında dağıtılabilir.

## <a name="ip-addresses"></a>IP adresleri ##

App Service bir uygulamaya adanmış bir IP adresi ayırabilme olanağı vardır. Bu özellik, [var olan bir özel SSL sertifikasını Azure App Service bağlama][ConfigureSSL]bölümünde açıklandığı gıbı, IP tabanlı bir SSL yapılandırıldıktan sonra kullanılabilir. Ancak, bir AO 'da bir önemli özel durumu vardır. ILB Ao 'da IP tabanlı SSL için kullanılacak ek IP adresleri ekleyemezsiniz.

ASEv1 ' de, bunları kullanabilmeniz için IP adreslerini kaynak olarak ayırmanız gerekir. ASEv2 ' de, bunları uygulamanızdaki çok kiracılı App Service yaptığınız gibi kullanırsınız. ASEv2 ' de en fazla 30 IP adresine sahip her zaman bir yedek adres vardır. Her birini kullandığınızda bir adres, her zaman kullanıma hazır olacak şekilde eklenir. Başka bir IP adresi ayırmak için zaman gecikmesi gerekir, bu da IP adreslerini hızlı bir şekilde art arda eklemeyi önler.

## <a name="front-end-scaling"></a>Ön uç ölçeklendirme ##

ASEv2 ' de, App Service planlarınızı ölçeklendirirseniz, çalışanlar onları destekleyecek şekilde otomatik olarak eklenir. Her Ao, iki ön uç ile oluşturulur. Ayrıca, ön uçlar App Service planlarınızda her 15 örnek için bir ön uç hızında otomatik olarak ölçeklendirilir. Örneğin, 15 örnek varsa, üç ön Uçmış olursunuz. 30 örneğe ölçeklendirirseniz, dört ön Uçmış olursunuz ve bu şekilde devam edersiniz.

Bu ön uçların çoğu senaryo için yeterince büyük olmalıdır. Ancak, daha hızlı bir şekilde ölçeklendirebilirsiniz. Oranı her beş örnek için bir ön uç olarak düşük olarak değiştirebilirsiniz. Oranı değiştirme ücreti vardır. Daha fazla bilgi için bkz. [Azure App Service fiyatlandırması][Pricing].

Ön uç kaynakları ASE için HTTP/HTTPS uç noktasıdır. Varsayılan ön uç yapılandırmasıyla, ön uç başına bellek kullanımı yaklaşık olarak yüzde 60 ' dir. Müşteri iş yükleri ön uç üzerinde çalışmaz. Bir ön uç için ölçeğe göre anahtar faktörü, öncelikli olarak HTTPS trafiği tarafından yönetilen CPU olur.

## <a name="app-access"></a>Uygulama erişimi ##

Bir dış ate, uygulama oluştururken kullanılan etki alanı çok kiracılı App Service farklıdır. ATıCı 'in adını içerir. Dış Ao oluşturma hakkında daha fazla bilgi için bkz. [App Service ortam oluşturma][MakeExternalASE]. Dış bir AO 'daki etki alanı adı, şöyle görünür. *&lt;asename&gt;. p.azurewebsites.net*. Örneğin, ATıCı 'niz _External-Ao_ olarak adlandırılmışsa ve _contoso_ ADLı bir uygulamayı bu Ao 'da barındırdıysanız, bu URL 'ye aşağıdaki URL 'lerde ulaşabilirsiniz:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Contoso.scm.external-ase.p.azurewebsites.net URL 'SI, kudu konsoluna erişmek veya uygulamanızı Web dağıtımı kullanarak yayımlamak için kullanılır. Kudu Konsolu hakkında daha fazla bilgi için bkz. [Azure App Service kudu konsolu][Kudu]. Kudu konsolu, hata ayıklama, dosyaları karşıya yükleme, dosyaları düzenlemeyle ve çok daha fazlası için bir Web Kullanıcı arabirimi sağlar.

Bir ıLB Ao 'da, etki alanını dağıtım zamanında belirlersiniz. ILB ATıCı oluşturma hakkında daha fazla bilgi için, bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE]. _İlb-ASE.info_etki alanı adını belirtirseniz, bu etki alanındaki uygulamalar uygulama oluşturma sırasında bu etki alanını kullanır. _Contoso_adlı uygulama Için URL 'ler şunlardır:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Yayımlama ##

Çok kiracılı App Service gibi, bir AO 'da ile yayımlayabilirsiniz:

- Web dağıtımı.
- FTP.
- Sürekli tümleştirme.
- Kudu konsoluna sürükleyip bırakın.
- Visual Studio, tutulma veya IntelliJ fıkrı gibi bir IDE.

Bir dış Ao ile, bu yayımlama seçeneklerinin hepsi aynı şekilde davranır. Daha fazla bilgi için bkz. [Azure App Service dağıtım][AppDeploy]. 

Yayımlamayla ilgili önemli fark, bir ıLB Ao 'ya göre belirlenir. ILB Ao ile, yayımlama uç noktaları yalnızca ıLB ile kullanılabilir. ILB, sanal ağdaki Ao alt ağında özel bir IP üzerinde yer alır. ILB 'ye ağ erişiminiz yoksa, o Ao 'da herhangi bir uygulama yayımlayamazsınız. [BIR ıLB Ai oluşturma ve kullanma][MakeILBASE]bölümünde belirtildiği gibi, sistemdeki uygulamalar için DNS 'yi yapılandırmanız gerekir. Bu, SCM uç noktasını içerir. Bunlar düzgün tanımlanmamışsa yayımlanamıyor. Ayrıca, doğrudan kendisine yayımlamak için Ides 'in ıLB 'ye ağ erişimi olması gerekir.

Kullanıma hazır olmayan, GitHub ve Azure DevOps gibi Internet tabanlı CI sistemleri, yayımlama uç noktası Internet 'e erişilemediğinden bir ıLB Ao ile çalışmaz. Azure DevOps için, iç ağınıza şirket içinde barındırılan bir yayın Aracısı yükleyerek bu soruna geçici bir çözüm ekleyebilirsiniz. Alternatif olarak, Dropbox gibi bir çekme modeli kullanan bir CI sistemini de kullanabilirsiniz.

Bir ILB ASE’deki uygulamalar için yayımlama uç noktaları, ILB ASE oluşturulurken kullanılan etki alanını kullanır. Uygulamanın yayımlama profilinde ve uygulamanın Portal dikey penceresinde ( **genel bakış** > **Essentials** ve ayrıca **Özellikler**bölümünde) görebilirsiniz. 

## <a name="pricing"></a>Fiyatlandırma ##

**Yalıtılmış** olarak adlandırılan fiyatlandırma SKU 'Su yalnızca ASEv2 ile kullanım için oluşturulmuştur. ASEv2 içinde barındırılan tüm App Service planları yalıtılmış fiyatlandırma SKU 'sunda. Yalıtılmış App Service plan ücretleri bölge başına farklılık gösterebilir. 

App Service planlarınızın fiyatına ek olarak, ASE 'nin kendisi için düz bir ücret vardır. Sabit Fiyat, asa 'nın boyutuyla değişmez ve Ao altyapısı için, her 15 App Service plan örneği için varsayılan ölçeklendirme hızında 1 ek ön uç için ödeme yapar.  

Her 15 App Service plan örneği için varsayılan ölçek oranı 1 ön ucu yeterince hızlı değilse, ön uçların eklenme oranını veya ön uçların boyutunu ayarlayabilirsiniz.  Oran veya boyut ayarlarını ayarlarken, varsayılan olarak eklenmemelidir ön uç çekirdekleri için ödeme yaparsınız.  

Örneğin, ölçek oranını 10 olarak ayarlarsanız App Service planlarınızda her 10 örnek için bir ön uç eklenir. Düz ücret, her 15 örnek için bir ön ucun ölçek oranını içerir. 10 ' un ölçek oranıyla 10 App Service plan örnekleri için eklenen üçüncü ön uç için ücret ödersiniz. Otomatik olarak eklendiğinden, 15 örneğe ulaştığınızda bu ücret için ödeme yapmanız gerekmez.

Ön uçların boyutunu 2 çekirdeğe ayarlarsanız ancak oranı ayarlamadıysanız ek çekirdekler için ödeme yaparsınız.  2 ön uçlarla bir ASE oluşturulur. bu nedenle, otomatik ölçeklendirme eşiğinin altına bile, boyutu 2 çekirdekli ön uçları artırdıysanız 2 ek çekirdek için ödeme yaparsınız.

Daha fazla bilgi için bkz. [Azure App Service fiyatlandırması][Pricing].

## <a name="delete-an-ase"></a>AS 'yi silme ##

ATıCı 'yi silmek için: 

1. **App Service ortamı** dikey penceresinin üstündeki **Sil** ' i kullanın. 

1. Silmek istediğinizi onaylamak için ATıCı 'nizin adını girin. Bir AI 'yi sildiğinizde, içindeki içeriğin tümünü de silersiniz. 

    ![ATıCı silme][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
