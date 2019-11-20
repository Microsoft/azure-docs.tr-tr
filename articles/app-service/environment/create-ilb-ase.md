---
title: App Service Ortamı-Azure ile iç yük dengeleyici oluşturma
description: İnternet’ten yalıtılmış bir Azure App Service Ortamı oluşturma ve kullanma ayrıntıları
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 07b47374484cf954b1fc4279c93dddcc6cec7e61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470559"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Iç Load Balancer App Service Ortamı oluşturma ve kullanma 

Azure App Service Ortamı, bir Azure sanal ağı 'ndaki (VNet) bir alt ağa Azure App Service dağıtımdır. Bir App Service Ortamı (ASE) iki şekilde dağıtılabilir: 

- Genellikle Dış ASE olarak adlandırılan durumda, bir dış IP adresi üzerindeki VIP ile.
- Genellikle iç uç nokta bir iç yük dengeleyici (ILB) olduğu için ILB ASE olarak adlandırılan durumda, bir iç IP adresi üzerindeki VIP ile. 

Bu makale bir ILB ASE oluşturma işlemini gösterir. Ao 'ya genel bakış için bkz. [App Service ortamlara giriş][Intro]. Dış Ao oluşturma hakkında bilgi edinmek için bkz. [dış Ao oluşturma][MakeExternalASE].

## <a name="overview"></a>Genel Bakış 

Bir ASE’yi İnternet’ten erişilebilen bir uç nokta ile ya da sanal ağınızdaki bir IP adresi ile dağıtabilirsiniz. IP adresini bir sanal ağ adresine ayarlamak için, ASE’nin ILB ile dağıtılması gerekir. ATıCı 'nizi bir ıLB ile dağıtırken Ao 'nizin adını sağlamalısınız. Ao 'nizin adı, Ao 'daki uygulamalar için etki alanı son ekinde kullanılır.  ILB ATıCı için etki alanı soneki &lt;Ao adı&gt;. appserviceenvironment.net. ILB Ao 'da yapılan uygulamalar genel DNS 'ye yerleştirmez. 

ILB ASE 'nin önceki sürümleri, bir etki alanı soneki ve HTTPS bağlantıları için varsayılan bir sertifika sağlamanız gerekir. Etki alanı soneki artık ıLB AI oluşturma sırasında toplanmaz ve varsayılan sertifika da artık toplanmaz. Artık bir ıLB AI oluşturduğunuzda, varsayılan sertifika Microsoft tarafından sağlanır ve tarayıcı tarafından güvenilirdir. Ao 'da uygulamalar üzerinde özel etki alanı adları ayarlayabiliyor ve bu özel etki alanı adlarında sertifikalar ayarlamış olabilirsiniz. 

ILB Ao ile şunları yapabilirsiniz:

-   Siteden siteye veya ExpressRoute aracılığıyla erişebileceğiniz intranet uygulamalarını bulutta güvenli bir şekilde barındırın.
-   Bir WAF cihazındaki uygulamaları koruma
-   Genel DNS sunucularında listelenmeyen uygulamaları bulutta barındırma.
-   Ön uç uygulamalarınızın güvenli bir şekilde tümleştirilebileceği, İnternet’ten yalıtılmış arka uç uygulamaları oluşturma.

### <a name="disabled-functionality"></a>Devre dışı bırakılan işlevler ###

ILB ASE’yi kullanırken bazı işlemleri yapamazsınız:

-   IP tabanlı SSL kullanma.
-   Belirli uygulamalara IP adresleri atama.
-   Azure portalı üzerinden bir uygulama ile sertifika satın alma ve kullanma. Sertifikaları doğrudan bir sertifika yetkilisinden alabilir ve uygulamalarınızla kullanabilirsiniz. Sertifikaları Azure portalı üzerinden alamazsınız.

## <a name="create-an-ilb-ase"></a>ILB ASE oluşturma ##

ILB ASE oluşturmak için:

1. Azure portalda **Kaynak oluştur** > **Web** > **App Service Ortamı**’nı seçin.

2. Aboneliğinizi seçin.

3. Kaynak grubunu seçin veya oluşturun.

4. App Service Ortamı adını girin.

5. Iç sanal IP türünü seçin.

    ![ASE oluşturma](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Ağ seçin

7. Bir sanal ağ seçin veya oluşturun. Burada yeni bir sanal ağ oluşturursanız, bu adres aralığı 192.168.250.0/23 olacak şekilde tanımlanır. Farklı bir adres aralığına veya ASE 'den farklı bir kaynak grubuna sahip bir VNet oluşturmak için Azure sanal ağ oluşturma portalını kullanın. 

8. Boş bir alt ağ seçin veya oluşturun. Bir alt ağ seçmek istiyorsanız, boş ve Temsilcili olmalıdır. ATıCı oluşturulduktan sonra alt ağ boyutu değiştirilemez. 256 adres içeren ve en büyük boyutlu ASE’yi işleyebilen ve ölçeklendirme ihtiyaçlarını karşılayabilen `/24` dosya boyutu önerilir. 

    ![ATıCı ağı][1]

7. **Gözden geçir '** i seçin ve **Oluştur**' u seçin.

## <a name="create-an-app-in-an-ilb-ase"></a>ILB ASE'de uygulama oluşturma ##

ILB ASE'de uygulama oluşturma işlemi, normalde bir ASE’de uygulama oluşturma işlemiyle aynıdır.

1. Azure portal **bir kaynak oluştur** > **Web** > **Web uygulaması**' nı seçin.

1. Uygulamanın adını girin.

1. Aboneliği seçin.

1. Kaynak grubunu seçin veya oluşturun.

1. Yayımla, çalışma zamanı yığını ve Işletim sisteminizi seçin.

1. Konumun mevcut bir ıLB Ao olduğu bir konum seçin.  Ayrıca, yalıtılmış bir App Service planı seçerek uygulama oluşturma sırasında yeni bir AO da oluşturabilirsiniz. Yeni bir AO oluşturmak istiyorsanız, ATıCı 'in içinde oluşturulmasını istediğiniz bölgeyi seçin.

1. Bir App Service planı seçin ya da oluşturun. 

1. **Gözden geçir ' i ve Oluştur '** u seçin ve ardından **Oluştur** ' u seçin.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Web işleri, İşlevler ve ILB ASE 

Hem İşlevler hem de web işleri ILB ASE’de desteklenir, ancak portalın bunlarla çalışabilmesi için SCM sitesine ağ erişiminiz olmalıdır.  Başka bir deyişle, tarayıcınız sanal ağ içinde veya sanal ağa bağlı bir konakta olmalıdır. ILB ASE 'niz *appserviceenvironment.net*'de bitmeyecek bir etki alanı adına sahipse,, SCM siteniz tarafından kullanılan HTTPS sertifikasına güvenmesi için tarayıcınızı almanız gerekir.

## <a name="dns-configuration"></a>DNS yapılandırması 

Dış VIP kullandığınızda DNS, Azure tarafından yönetilir. ASE’nizde oluşturulan herhangi bir uygulama, genel bir DNS olan Azure DNS'e otomatik olarak eklenir. ILB ASE'de kendi DNS’inizi yönetmeniz gerekir. ILB as ile kullanılan etki alanı soneki Ao 'nun adına bağlıdır. Etki alanı son eki *&lt;Ao adı&gt;. appserviceenvironment.net*. ILB 'nizin IP adresi, portalda **IP adresleri**altında bulunur. 

DNS 'nizi yapılandırmak için:

- *&lt;atıcı adı* için bir bölge oluşturun&gt;. appserviceenvironment.net
- Bu bölgede * ıLB IP adresine işaret eden bir kayıt oluşturun
- Bu bölgede @ adresli ıLB IP adresine işaret eden bir kayıt oluşturma
- *&lt;Ao adı&gt;. appserviceenvironment.net* adlı SCM adlı bir bölge oluşturun
- SCM bölgesinde * ıLB IP adresine işaret eden bir kayıt oluşturun

## <a name="publish-with-an-ilb-ase"></a>ILB ASE ile yayımlama

Oluşturulan her uygulama için iki uç nokta vardır. ILB Ao 'da, *&lt;app name&gt;.&lt;ILB Ao etki alanı&gt;* ve *&lt;uygulama adı&gt;. scm.&lt;ILB ao etki alanı&gt;* . 

SCM site adı sizi Azure portalı içinde **Gelişmiş portal** olarak adlandırılan Kudu konsoluna götürür. Kudu konsolunu kullanarak ortam değişkenlerini görüntüleyebilir, diski keşfedebilir, bir konsolu kullanabilir ve daha fazlasını yapabilirsiniz. Daha fazla bilgi için bkz. [Azure App Service Için kudu konsolu][Kudu]. 

GitHub ve Azure DevOps gibi İnternet tabanlı CI sistemleri, derleme aracısına İnternet’ten erişilebiliyorsa ve aracı ILB ASE ile aynı ağdaysa ILB ASE ile çalışmaya devam eder. Bu nedenle, Azure DevOps örneğinde derleme aracısı ILB ASE ile aynı sanal ağda (alt ağın farklı olması sorun yaratmaz) oluşturulursa Azure DevOps git’ten kod çekip ILB ASE’ye dağıtabilir. Kendi derleme aracınızı oluşturmak istemiyorsanız çekme modeli kullanan bir CI sistemi (Dropbox gibi) kullanmanız gerekir.

Bir ILB ASE’deki uygulamalar için yayımlama uç noktaları, ILB ASE oluşturulurken kullanılan etki alanını kullanır. Bu etki alanı uygulamanın yayımlama profilinde ve uygulamanın portal dikey penceresinde görünür (**Genel Bakış** > **Temel Bilgiler** ve ayrıca **Özellikler**). Etki alanı sonekine sahip bir ıLB Ao *adı&gt;. appserviceenvironment.net&lt;* ve *MyTest*adlı bir uygulama varsa, *MyTest.&lt;ao adı&gt;. appserviceenvironment.net* for FTP veWeb dağıtımı için MyTest.scm.contoso.net.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Bir ıDB ATıCı 'yi WAF cihazından yapılandırma ##

Web uygulaması güvenlik duvarı (WAF) cihazını yalnızca İnternet 'e istediğiniz uygulamaları göstermek ve geri kalanını yalnızca VNet 'ten erişilebilir durumda tutmak için ıLB ASE 'niz ile birleştirebilirsiniz. Bu, diğer şeyler arasında güvenli çok katmanlı uygulamalar oluşturmanıza olanak sağlar.

ILB ATıCı 'nizi bir WAF cihazından yapılandırma hakkında daha fazla bilgi edinmek için bkz. [App Service ortamınızla Web uygulaması güvenlik duvarını yapılandırma][ASEWAF]. Bu makalede, bir Barracuda sanal gerecinin ASE’nizle nasıl kullanılacağı gösterilir. Bir diğer seçenek ise Azure Application Gateway’in kullanılmasıdır. Application Gateway, arkasına yerleştirilmiş uygulamaların güvenliğini sağlamak için OWASP temel kurallarını kullanır. Application Gateway hakkında daha fazla bilgi için bkz. [Azure Web uygulaması güvenlik duvarı 'Na giriş][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs, 2019 Mayıs 'tan önce yapıldı

2019 tarihinden önce yapılan ıLB 'ler, ASE oluşturma sırasında etki alanı sonekini ayarlamanıza gerek yoktur. Ayrıca, bu etki alanı sonekine dayalı bir varsayılan sertifikayı karşıya yüklemeniz gerekir. Ayrıca, daha eski bir ıLB ASE ile kudu konsolunda o ıLB ASE 'deki uygulamalarla çoklu oturum açma işlemleri gerçekleştiremezsiniz. Daha eski bir ıLB ASE için DNS yapılandırırken, etki alanı sonekinden eşleşen bir bölgede bir kayıt joker karakterini ayarlamanız gerekir. 

## <a name="get-started"></a>Kullanmaya başlayın ##

* ASE 'yi kullanmaya başlamak için bkz. [App Service ortamlara giriş][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
