---
title: App Service Ortamı-Azure için bir Web uygulaması güvenlik duvarı (WAF) yapılandırma
description: App Service Ortamınızın önünde bir web uygulaması güvenlik duvarını yapılandırma hakkında bilgi edinin.
services: app-service\web
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: 01224e4270ba8a7c7df4a311823dd6156038438a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070055"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>App Service Ortamı için Web Uygulaması Güvenlik Duvarı (WAF) Yapılandırma
## <a name="overview"></a>Genel Bakış

Web uygulaması güvenlik duvarları (WAF), SQL eklemelerini, Siteler Arası Betik Kullanmayı, kötü amaçlı yazılım yüklemelerini ve uygulama DDoS ve diğer saldırıları engellemek üzere gelen web trafiğini denetleyerek web uygulamalarınızın güvenliğini sağlamaya yardımcı olur. Ayrıca, Veri Kaybı Önleme (DLP) için arka uç web sunucularından gelen yanıtları denetler. App Service Ortamlarının sağladığı yalıtım ve ek ölçeklendirme ile bir araya geldiğinde bu özellik, kötü amaçlı isteklere ve yüksek hacimli trafiğe dayanıklı olması gereken iş açısından kritik web uygulamalarını barındırmak için ideal bir ortam sağlar. Azure, [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) ile bir WAF özelliği sağlar.  App Service Ortamınızı bir Application Gateway ile tümleştirme hakkında bilgi almak için [ILB ASE’nizi bir Application Gateway ile tümleştirme](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway) belgesine bakın.

Azure Application Gateway’e ek olarak, [Azure Market](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/)’te [Azure için Barracuda WAF](https://www.barracuda.com/programs/azure) gibi birden çok market seçeneği mevcuttur. Bu belgenin geri kalanında App Service Ortamınızı bir Barracuda WAF cihazı ile tümleştirme işlemine odaklanılmaktadır.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Kurulum
Bu belgede, yalnızca WAF’den gelen trafiğin App Service Ortamına ulaşabilmesi ve DMZ’den erişilememesi için App Service Ortamını Barracuda WAF’nin yük dengelemesi yapılmış birden fazla örneğinin arkasında yapılandırıyoruz. Ayrıca, Azure veri merkezleri ve bölgeleri arasında yük dengelemek için Barracuda WAF örneklerinin önünde Azure Traffic Manager kullanıyoruz. Kurulumun yüksek düzey bir diyagramı aşağıdaki görüntü gibi görünür:

![Mimari][Architecture] 

> [!NOTE]
> [App Service Ortamı için ILB desteğinin](app-service-environment-with-internal-load-balancer.md) kullanıma sunulmasıyla birlikte ASE’yi DMZ’den erişilemeyecek ve yalnızca özel ağda kullanılabilecek şekilde yapılandırabilirsiniz. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>App Service Ortamınızı yapılandırma
Bir App Service Ortamını yapılandırmak için konu ile ilgili [belgelerimize](app-service-web-how-to-create-an-app-service-environment.md) bakın. Bir App Service Ortamı oluşturduktan sonra bu ortamda, hepsi sonraki bölümde yapılandıracağımız WAF’nin ardında korunacak Web Uygulamaları, API Uygulamaları ve [Mobil Uygulamalar](../../app-service-mobile/app-service-mobile-value-prop.md) oluşturabilirsiniz.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Barracuda WAF Bulut Hizmetinizi yapılandırma
Barracuda, WAF’yi Azure’daki bir sanal makineye dağıtma hakkında [ayrıntılı bir makaleye](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sahiptir. Ancak yedeklilik istediğimiz ve tek bir hata noktası tanımlamak istemediğimiz için, bu yönergeleri takip ederken aynı Bulut Hizmetine en az iki WAF örneği sanal makinesi dağıtmanız gerekir.

### <a name="adding-endpoints-to-cloud-service"></a>Bulut Hizmetine Uç Nokta Ekleme
Bulut Hizmetinizde 2 veya daha fazla WAF VM örneği olduğunda, aşağıdaki görüntüde gösterildiği gibi [Azure portalını](https://portal.azure.com/) kullanarak uygulamanız tarafından kullanılan HTTP ve HTTPS uç noktaları ekleyebilirsiniz:

![Uç Nokta Yapılandırma][ConfigureEndpoint]

Uygulamalarınız başka uç noktalar kullanıyorsa onları da bu listeye eklediğinizden emin olun. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Barracuda WAF’yi Yönetim Portalından yapılandırma
Barracuda WAF, yönetimi portalı üzerinden yapılandırma için TCP Bağlantı Noktası 8000’i kullanır. Birden fazla WAF VM örneğiniz varsa, buradaki adımları her VM örneği için yinelemeniz gerekir. 

> [!NOTE]
> WAF yapılandırmasını tamamladıktan sonra WAF’nin güvenliğini korumak için TCP/8000 uç noktasını tüm WAF VM’lerinizden kaldırın.
> 
> 

Barracuda WAF’nizi yapılandırmak için aşağıdaki görüntüde gösterildiği gibi yönetim uç noktasını ekleyin.

![Yönetim Uç Noktası Ekleme][AddManagementEndpoint]

Bulut Hizmetinizdeki yönetim uç noktasına göz atmak için bir tarayıcı kullanın. Bulut Hizmetinizin adı test.cloudapp.net ise, `http://test.cloudapp.net:8000` adresine göz atarak bu uç noktaya erişebilirsiniz. Aşağıdaki görüntüde gösterildiği gibi, WAF VM kurulum aşamasında belirttiğiniz kimlik bilgilerini kullanarak oturum açabileceğiniz bir oturum açma sayfası görmeniz gerekir.

![Yönetim Oturum Açma Sayfası][ManagementLoginPage]

Oturum açtıktan sonra, aşağıdaki görüntüde gösterildiği gibi WAF korumasına ilişkin temel istatistikler sunan bir pano görmeniz gerekir.

![Yönetim Panosu][ManagementDashboard]

**Hizmetler** sekmesine tıklayarak WAF’yi koruduğu hizmetler için yapılandırabilirsiniz. Barracuda WAF’nizi yapılandırma hakkında daha ayrıntılı bilgi için [ilgili belgelere](https://techlib.barracuda.com/waf/getstarted1) bakın. Aşağıdaki örnekte, HTTP ve HTTPS üzerinde trafiğe hizmet veren bir App Service uygulaması yapılandırılmıştır.

![Yönetim Hizmet Ekleme][ManagementAddServices]

> [!NOTE]
> Uygulamalarınızın nasıl yapılandırıldığına ve App Service Ortamı hangi özelliklerin kullanıldığına bağlı olarak, 80 ve 443 dışındaki TCP bağlantı noktaları için trafiği iletmeniz gerekir, örneğin, bir App Service uygulaması için IP SSL kurulum seçeneğiniz vardır. App Service Ortamlarında kullanılan ağ bağlantı noktalarının listesi için [Gelen Trafiği Denetleme belgelerinde](app-service-app-service-environment-control-inbound-traffic.md) Ağ Bağlantı Noktaları bölümüne bakın.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Manager’ı Yapılandırma (İSTEĞE BAĞLI)
Uygulamanız birden fazla bölgede kullanılabiliyorsa, [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)’ın arkasında bu bölgelere yük dengelemesi uygulamak isteyebilirsiniz. Bunu yapmak için, aşağıdaki görüntüde gösterildiği gibi Traffic Manager profilindeki WAF’nizin Bulut Hizmeti adını kullanarak [Azure portalına](https://portal.azure.com) bir uç nokta ekleyebilirsiniz. 

![Traffic Manager Uç Noktası][TrafficManagerEndpoint]

Uygulamanız kimlik doğrulaması gerektiriyorsa, uygulamanızın kullanılabilirliği için ping işlemi yapmak üzere Traffic Manager için herhangi bir kimlik doğrulaması gerektirmeyen bazı kaynaklarınızın olduğundan emin olun. [Azure portalının](https://portal.azure.com) **Yapılandırma** sayfasındaki URL’yi aşağıdaki görüntüde gösterildiği gibi yapılandırabilirsiniz:

![Traffic Manager'ı Yapılandırma][ConfigureTrafficManager]

WAF’nizden gelen Traffic Manager pinglerini uygulamanıza iletmek için, aşağıdaki görüntüde gösterildiği gibi trafiği uygulamanıza iletmek üzere Barracuda WAF’niz üzerinde Web Sitesi Çevirilerini ayarlamanız gerekir:

![Web Sitesi Çevirileri][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Ağ Güvenlik Gruplarını (NSG) Kullanarak App Service Ortamına Giden Trafiğin Güvenliğini Sağlama
Bulut Hizmetinizin VIP adresini kullanarak App Service Ortamınıza gelen trafiği yalnızca WAF’den gelecek şekilde kısıtlamaya ilişkin ayrıntılar için [Gelen Trafiği Denetleme belgelerini](app-service-app-service-environment-control-inbound-traffic.md) takip edin. TCP bağlantı noktası 80 için bu görevi gerçekleştirmeye yönelik örnek bir Powershell komutu aşağıda verilmiştir.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

SourceAddressPrefix değerini WAF Bulut Hizmetinin Sanal IP Adresi (VIP) ile değiştirin.

> [!NOTE]
> Bulut Hizmetini silip yeniden oluşturduğunuzda Bulut Hizmetinizin VIP’si değişir. Bunu yaptıktan sonra Ağ Kaynağı grubunda IP adresini güncelleştirmeyi unutmayın. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
