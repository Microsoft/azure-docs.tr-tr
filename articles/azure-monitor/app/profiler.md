---
title: Application Insights ile canlı Azure App Service uygulamalar profili Microsoft Docs
description: Azure App Service Application Insights Profiler ile canlı uygulamalar profilini yapın.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a53db9deb07863010c792943c71eb0af5d845af8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026514"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights ile canlı Azure App Service uygulamalar profili

ASP.NET üzerinde profil oluşturucuyu ve temel hizmet katmanını veya üstünü kullanarak Azure App Service çalıştıran uygulamaları ASP.NET Core çalıştırabilirsiniz. Linux 'ta profil oluşturucuyu etkinleştirmek Şu anda yalnızca [Bu yöntem](profiler-aspnetcore-linux.md)aracılığıyla mümkündür.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Uygulamanız için profil oluşturucuyu etkinleştirin
Bir uygulama için profil oluşturucuyu etkinleştirmek üzere aşağıdaki yönergeleri izleyin. Farklı bir Azure hizmeti türü çalıştırıyorsanız, desteklenen diğer platformlarda profil oluşturucuyu etkinleştirme talimatları aşağıda verilmiştir:
* [Bulut Hizmetleri](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric uygulamalar](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Sanal Makineler](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler, App Services çalışma zamanının bir parçası olarak önceden yüklenir. Aşağıdaki adımlarda, App Service için nasıl etkinleştirileceği gösterilmektedir. Application Insights SDK 'sını uygulamanıza derleme zamanında dahil etseniz bile bu adımları izleyin.

> [!NOTE]
> Codeless Application Insights Profiler yüklemesi .NET Core destek ilkesini izler.
> Desteklenen çalışma zamanları hakkında daha fazla bilgi için bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. App Service için Azure Denetim Masası ' na gidin.
1. App Service için "Always On" ayarını etkinleştirin. Bu ayarı **Ayarlar**, **yapılandırma** sayfası (bir sonraki adımda ekran görüntüsüne bakın) altında bulabilir ve **Genel ayarlar** sekmesini seçebilirsiniz.
1. **Ayarlar > Application Insights** sayfasına gidin.

   ![App Services portalında App Insights 'ı etkinleştirme](./media/profiler/AppInsights-AppServices.png)

1. Uygulamanızı izlemek üzere yeni bir kaynak oluşturmak veya var olan bir Application Insights kaynağını seçmek için bölmedeki yönergeleri izleyin. Ayrıca, profil oluşturucunun **Açık** olduğundan da emin olun. Application Insights kaynağınız App Service farklı bir abonelikte yer alıyorsa Application Insights yapılandırmak için bu sayfayı kullanamazsınız. Gerekli uygulama ayarlarını el ile oluşturarak yine de bunu el ile yapabilirsiniz. [Sonraki bölümde, profil oluşturucuyu el ile etkinleştirmeye yönelik yönergeler yer alır.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights site uzantısı Ekle][Enablement UI]

1. Profil Oluşturucu artık bir App Services uygulama ayarı kullanılarak etkinleştirildi.

    ![Profil Oluşturucu için uygulama ayarı][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Profil oluşturucuyu el ile veya Azure Resource Manager etkinleştirme
Application Insights Profiler, Azure App Service için uygulama ayarları oluşturularak etkinleştirilebilir. Yukarıda gösterilen seçeneklere sahip sayfa sizin için bu uygulama ayarlarını oluşturur. Ancak bu ayarların oluşturulmasını, şablon veya başka yollarla otomatik hale getirebilirsiniz. Application Insights kaynağınız Azure App Service farklı bir abonelikte ise bu ayarlar da çalışacaktır.
Profil oluşturucuyu etkinleştirmek için gereken ayarlar şunlardır:

|Uygulama Ayarı    | Değer    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights kaynağınız için Ikey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Bu değerleri [Azure Resource Manager şablonları](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure POWERSHELL](/powershell/module/az.websites/set-azwebapp),  [Azure CLI](/cli/azure/webapp/config/appsettings)kullanarak ayarlayabilirsiniz.

## <a name="enable-profiler-for-other-clouds"></a>Diğer bulutlar için profil oluşturucuyu etkinleştir

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide)' dir.

|Uygulama Ayarı    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Applicationınsightsprofilerendpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|Applicationınsi, Sendpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profil oluşturucuyu devre dışı bırak

Tek bir uygulamanın örneği için profil oluşturucuyu durdurmak veya yeniden başlatmak için, sol kenar çubuğundan **Web işleri** ' ni seçin ve adlı WebJob 'ı durdurun `ApplicationInsightsProfiler3` .

  ![Web işi için profil oluşturucuyu devre dışı bırakma][disable-profiler-webjob]

Herhangi bir performans sorununu mümkün olduğunca erken bulacak şekilde sağlamak için tüm uygulamalarınızda profil oluşturucunun etkin olmasını öneririz.

Web uygulamanıza değişiklikler dağıtmak için WebDeploy kullanılırken profil oluşturucunun dosyaları silinebilir. App_Data klasörünün dağıtım sırasında silinmesini dışlayarak silme işlemini engelleyebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio’da Application Insights ile çalışma](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png