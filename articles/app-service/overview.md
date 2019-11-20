---
title: App Service genel bakış-Azure | Microsoft Docs
description: Azure App Service’in web uygulamaları geliştirmenize ve barındırmanıza nasıl yardımcı olabileceğini öğrenin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ee80667789e661927f611257b438eb2eecbd0c83
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074101"
---
# <a name="app-service-overview"></a>App Service’a genel bakış

*Azure App Service* Web UYGULAMALARıNı, REST API 'leri ve mobil arka uçları BARıNDıRMAK için HTTP tabanlı bir hizmettir. .NET, .NET Core, Java, Ruby, Node.js, PHP veya Python dahil en sevdiğiniz dilde geliştirebilirsiniz. Uygulamalar hem Windows hem de Linux tabanlı ortamlarda kolayca çalışır ve ölçeklenebilir. Linux tabanlı ortamlar için bkz. [Linux’ta App Service](containers/app-service-linux-intro.md). 

App Service, uygulamanıza güvenlik, Yük Dengeleme, otomatik ölçeklendirme ve otomatik yönetim gibi Microsoft Azure gücünü eklemektedir. Aynı zamanda Azure DevOps, GitHub, Docker Hub ve diğer kaynaklardan sürekli dağıtım, paket yönetimi, hazırlık ortamları, özel etki alanı ve SSL sertifikaları gibi DevOps özelliklerinden de yararlanabilirsiniz. 

App Service ile kullandığınız Azure işlem kaynakları için ödeme yaparsınız. Kullandığınız işlem kaynakları, uygulamalarınızı çalıştırdığınız _App Service planına_ göre belirlenir. Daha fazla bilgi için bkz. [Azure App Service planlarına genel bakış](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>App Service nedir?

App Service temel özelliklerinden bazıları aşağıda verilmiştir:

* **Birden çok dil ve çerçeve** -App Service ASP.NET, ASP.NET Core, Java, Ruby, Node. js, php veya Python için birinci sınıf desteğe sahiptir. Ayrıca, [PowerShell’i ve diğer betikleri ya da yürütülebilir hizmetleri](webjobs-create.md) arka plan hizmetleri olarak çalıştırabilirsiniz.
* **DevOps iyileştirmesi** - Azure DevOps, GitHub, BitBucket, Docker Hub veya Azure Container Registry ile [sürekli tümleştirme ve dağıtım](deploy-continuous-deployment.md) ayarlayın. [Test ve hazırlık ortamları](deploy-staging-slots.md) aracılığıyla güncelleştirmeleri yükseltin. [Azure PowerShell](/powershell/azureps-cmdlets-docs) veya [platformlar arası komut satırı arabirimi (CLI)](/cli/azure/install-azure-cli) kullanarak uygulamalarınızı App Service’de yönetin.
* **Yüksek kullanılabilirlik ile küresel ölçeklendirme** - El ile veya otomatik olarak ölçek [artırabilir](manage-scale-up.md) veya [genişletebilirsiniz](../monitoring-and-diagnostics/insights-how-to-scale.md). Uygulamalarınızı Microsoft'un küresel veri merkezi altyapılarının herhangi bir yerinde barındırın. App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)’sı yüksek kullanılabilirlik taahhüt eder.
* **SaaS platformları ve şirket için veri bağlantıları** - Kurumsal sistemler (SAP gibi), SaaS hizmetleri (Salesforce gibi) ve İnternet hizmetleri (Facebook gibi) için 50’den fazla [bağlayıcı](../connectors/apis-list.md) arasından seçim yapın. [Karma Bağlantılar](app-service-hybrid-connections.md)’ı ve [Azure Sanal Ağlar](web-sites-integrate-with-vnet.md)’ı kullanarak şirket içi verilere erişin.
* **Güvenlik ve uyumluluk** - App Service [ISO, SOC ve PCI uyumludur](https://www.microsoft.com/en-us/trustcenter). [Azure Active Directory](configure-authentication-provider-aad.md) veya sosyal oturum açma ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) ve [Microsoft](configure-authentication-provider-microsoft.md)) aracılığıyla kullanıcılarınızın kimliğini doğrulayın. [IP adresi kısıtlamaları](app-service-ip-restrictions.md) oluşturun ve [hizmet kimliklerini yönetin](overview-managed-identity.md).
* **Uygulama şablonları** - [Azure Market](https://azure.microsoft.com/marketplace/)’teki WordPress, Joomla ve Drupal’i de içeren kapsamlı uygulama şablonu listesinden seçiminizi yapın.
* **Visual Studio tümleştirmesi** -Visual Studio’daki ayrılmış araçlar oluşturma, dağıtma ve hata ayıklama işlemlerini kolaylaştırır.
* **API ve mobil Özellikler** -App Service, daha fazla API senaryosu Için, YENIDEN anahtar cors desteği sağlar ve kimlik doğrulama, çevrimdışı veri eşitleme, anında iletme bildirimleri ve daha fazlasını etkinleştirerek mobil uygulama senaryolarını basitleştirir.
* **Sunucusuz kod** - Açıkça altyapı sağlamanıza veya yönetmenize gerek kalmadan isteğe bağlı olarak bir kod parçacığı veya betik çalıştırın ve yalnızca kodunuzun gerçekte kullandığı işlem süresi (bkz. [Azure İşlevleri](/azure/azure-functions/)) için ücret ödeyin.

App Service yanı sıra Azure, Web sitelerini ve Web uygulamalarını barındırmak için kullanılabilecek başka hizmetler de sunar. Çoğu senaryo için en iyi seçenektir App Service.  Mikro hizmet mimarisi için [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)’i göz önünde bulundurun. Kodlarınızın çalıştığı sanal makineler üzerinde daha fazla denetime sahip olmanız gerekiyorsa [Azure Sanal Makineler](https://azure.microsoft.com/documentation/services/virtual-machines/)’i düşünün. Bu Azure hizmetleri arasında seçim yapma hakkında daha fazla bilgi için bkz. [Azure App Service, Virtual Machines, Service Fabric ve Cloud Services karşılaştırması](overview-compare.md).

## <a name="next-steps"></a>Sonraki adımlar

İlk web uygulamanızı oluşturun.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linux üzerinde)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (Linux üzerinde)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
