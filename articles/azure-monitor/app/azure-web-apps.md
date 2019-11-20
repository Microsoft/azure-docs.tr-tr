---
title: Azure App Services performansını izleme | Microsoft Docs
description: Azure Uygulama Hizmetleri için uygulama performansı izleme. Grafik yükleme ve yanıt süresi, bağımlılık bilgileri ve performans üzerinde Uyarılar ayarlama.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/04/2019
ms.openlocfilehash: 1937cce03412db55dafc2025c6a59b037deee3d1
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677648"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service performansını izleme

[Azure Uygulama hizmetlerinde](https://docs.microsoft.com/azure/app-service/) çalışan ASP.NET ve ASP.NET Core tabanlı Web uygulamalarında izlemenin etkinleştirilmesi artık hiç olmadığı kadar kolay. Daha önce bir site uzantısını el ile yüklemek için, en son uzantı/aracı artık varsayılan olarak App Service görüntüsüne yerleşik olarak bulunur. Bu makale, Application Insights izlemenin nasıl etkinleştirilebileceğine ve büyük ölçekli dağıtımlar için işlemi otomatikleştirmek üzere ön kılavuz sağlamanıza yol gösterecektir.

> [!NOTE]
> **Geliştirme araçları**  > **uzantıları** aracılığıyla Application Insights bir site uzantısının el ile eklenmesi kullanım dışıdır. Bu uzantı yükleme yöntemi, her yeni sürüm için el ile güncelleştirmelere bağımlıdır. Uzantının en son kararlı sürümü artık App Service görüntüsünün bir parçası olarak [önceden yüklenmiştir](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) . Dosyalar `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` ' da bulunur ve her kararlı sürümle otomatik olarak güncelleştirilir. Aşağıda izlemeyi etkinleştirmek için aracı tabanlı yönergeleri izlerseniz, devre dışı bırakılmış uzantıyı sizin için otomatik olarak kaldırır.

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Azure App Services 'da barındırılan uygulamalar için uygulama izlemeyi etkinleştirmenin iki yolu vardır:

* **Aracı tabanlı uygulama izleme** (applicationınsiizsagent).  
    * Bu yöntem etkinleştirilmesi en kolayıdır ve gelişmiş yapılandırma gerekmez. Genellikle "çalışma zamanı" izleme olarak adlandırılır. Azure Uygulama Hizmetleri için, bu izleme düzeyini en az etkinleştirme ve ardından belirli senaryonuza bağlı olarak, el ile izleme yoluyla daha gelişmiş izleme gerekip gerekmediğini değerlendirebilirsiniz.

* Application Insights SDK 'Yı yükleyerek **uygulamayı kodla el ile işaretleme** .

    * Bu yaklaşım çok daha özelleştirilebilir, ancak [APPLICATION INSIGHTS SDK NuGet paketlerine bağımlılık eklemeyi](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)gerektirir. Bu yöntem, ayrıca paketlerin en son sürümüne yönelik güncelleştirmeleri yönetmeniz anlamına gelir.

    * Aracı tabanlı izleme ile varsayılan olarak yakalanmayan olayları/bağımlılıkları izlemek için özel API çağrıları yapmanız gerekiyorsa, bu yöntemi kullanmanız gerekir. Daha fazla bilgi edinmek için [özel olaylar ve ölçümler makalesine yönelik API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 'ye göz atın.

> [!NOTE]
> Hem aracı tabanlı izleme hem de el ile SDK tabanlı izleme algılanırsa, yalnızca el ile izleme ayarları kabul edilir. Bu, yinelenen verilerin gönderilmesini önlemektir. Bu konuda daha fazla bilgi edinmek için aşağıdaki [sorun giderme bölümüne](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) bakın.

## <a name="enable-agent-based-monitoring-for-net-applications"></a>.NET uygulamaları için aracı tabanlı izlemeyi etkinleştir

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED ve urlCompression birleşimi desteklenmez. Daha fazla bilgi için bkz. [sorun giderme bölümündeki](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)açıklama.


1. App Service için Azure Denetim Masası 'nda Application Insights ' yi **seçin** .

    ![Ayarlar altında Application Insights ' ı seçin.](./media/azure-web-apps/settings-app-insights-01.png)

   * Bu uygulama için zaten bir Application Insights kaynağı ayarlamadığınız takdirde yeni bir kaynak oluşturmayı seçin. 

     > [!NOTE]
     > Yeni kaynağı oluşturmak için **Tamam** ' a tıkladığınızda, **izleme ayarlarını uygulamanız**istenir. **Devam** ' ı seçerseniz, yeni Application Insights kaynağınızı App Service 'e bağlayacaksınız, bunun yapılması da **App Service 'in yeniden başlatılmasını tetikler**. 

     ![Web uygulamanızı izleme](./media/azure-web-apps/create-resource-01.png)

2. Hangi kaynağı kullanacağınızı belirttikten sonra, Application Insights 'ın uygulamanız için her platform için veri toplamasını nasıl istediğinizi seçebilirsiniz. ASP.NET uygulama izleme iki farklı koleksiyon düzeyiyle varsayılan olarak yapılır.

    ![Platform başına seçenekleri belirleyin](./media/azure-web-apps/choose-options-new.png)

   * .NET **temel koleksiyon** düzeyi, temel tek örnekli APM özellikleri sunar.

   * .NET tarafından **Önerilen koleksiyon** düzeyi:
       * CPU, bellek ve g/ç kullanım eğilimlerini ekler.
       * Mikro hizmetleri istek/bağımlılık sınırları arasında ilişkilendirir.
       * Kullanım eğilimlerini toplar ve kullanılabilirlik sonuçlarından işlemlere bağıntı sağlar.
       * Ana bilgisayar işlemi tarafından işlenmeyen özel durumları toplar.
       * Örnekleme kullanıldığında yük altında APM ölçüm doğruluğunu geliştirir.

3. Daha önce ApplicationInsights. config dosyası aracılığıyla denetleyebileceği örnekleme gibi ayarları yapılandırmak için artık ilgili bir ön ek ile uygulama ayarları aracılığıyla aynı ayarlarla etkileşime geçebilirsiniz. 

    * Örneğin, ilk örnekleme yüzdesini değiştirmek için, bir uygulama ayarı oluşturabilirsiniz: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` ve `100` değeri.

    * Desteklenen Uyarlamalı örnekleme telemetri işlemcisi ayarlarının listesi için [koda](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) ve [ilişkili belgelere](https://docs.microsoft.com/azure/azure-monitor/app/sampling)başvurabilirsiniz.

## <a name="enable-agent-based-monitoring-for-net-core-applications"></a>.NET Core uygulamaları için aracı tabanlı izlemeyi etkinleştir

Aşağıdaki .NET Core sürümleri desteklenir: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2

.NET Core, kendinden bağımsız dağıtım ve ASP.NET Core 3,0 ' den tam Framework 'ü hedeflemek aracı/uzantısı tabanlı izleme ile Şu anda **desteklenmemektedir** . (Kod aracılığıyla[el ile izleme](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) , önceki senaryolardan tümünde çalışır.)

1. App Service için Azure Denetim Masası 'nda Application Insights ' yi **seçin** .

    ![Ayarlar altında Application Insights ' ı seçin.](./media/azure-web-apps/settings-app-insights-01.png)

   * Bu uygulama için zaten bir Application Insights kaynağı ayarlamadığınız takdirde yeni bir kaynak oluşturmayı seçin. 

     > [!NOTE]
     > Yeni kaynağı oluşturmak için **Tamam** ' a tıkladığınızda, **izleme ayarlarını uygulamanız**istenir. **Devam** ' ı seçerseniz, yeni Application Insights kaynağınızı App Service 'e bağlayacaksınız, bunun yapılması da **App Service 'in yeniden başlatılmasını tetikler**. 

     ![Web uygulamanızı izleme](./media/azure-web-apps/create-resource-01.png)

2. Hangi kaynağı kullanacağınızı belirttikten sonra, uygulamanız için her platform için verileri nasıl toplayacağınızı Application Insights istediğinizi seçebilirsiniz. .NET Core, .NET Core 2,0, 2,1 ve 2,2 için **Önerilen koleksiyon** veya **devre dışı** olanakları sunmaktadır.

    ![Platform başına seçenekleri belirleyin](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-for-net-applications"></a>.NET uygulamaları için istemci tarafı izlemeyi etkinleştir

İstemci tarafı izleme, ASP.NET için kabul edilir. İstemci tarafı izlemeyi etkinleştirmek için:

* **Ayarları** seçin > * * * * uygulama ayarları * * * *
   * Uygulama ayarları ' nın altında yeni bir **uygulama ayarı adı** ve **değeri**ekleyin:

     Ad: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Değer:`true`

   * Ayarları **Kaydedin** ve uygulamanızı **Yeniden başlatın**.

![Uygulama ayarları Kullanıcı arabiriminin ekran görüntüsü](./media/azure-web-apps/appinsights-javascript-enabled.png)

İstemci tarafı izlemeyi devre dışı bırakmak için, uygulama ayarlarından ilişkili anahtar değer çiftini kaldırın ya da değeri false olarak ayarlayın.

## <a name="enable-client-side-monitoring-for-net-core-applications"></a>.NET Core uygulamaları için istemci tarafı izlemeyi etkinleştir

İstemci tarafı izleme, ' APPINSIGHTS_JAVASCRIPT_ENABLED ' uygulama ayarının mevcut olup olmamasına bakılmaksızın **Önerilen koleksiyonla**birlikte .NET Core uygulamaları için **Varsayılan olarak etkindir** .

Bazı nedenlerle istemci tarafı izlemeyi devre dışı bırakmak istiyorsanız:

* **Uygulama ayarları**  >  **ayarları** seçin
   * Uygulama ayarları ' nın altında yeni bir **uygulama ayarı adı** ve **değeri**ekleyin:

     ad: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Değer:`false`

   * Ayarları **Kaydedin** ve uygulamanızı **Yeniden başlatın**.

![Uygulama ayarları Kullanıcı arabiriminin ekran görüntüsü](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>İzlemeyi otomatikleştirin

Application Insights ile telemetri toplamayı etkinleştirmek için, yalnızca uygulama ayarlarının ayarlanması gerekir:

   ![Kullanılabilir Application Insights ayarları ile uygulama ayarlarını App Service](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Uygulama ayarları tanımları

|Uygulama ayarı adı |  Tanım | Değer |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Çalışma zamanı izlemeyi denetleyen ana uzantı. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Yalnızca varsayılan modda, en iyi performansı sağlamak için temel özellikler etkindir. | `default` veya `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | İkili yeniden yazma altyapısının `InstrumentationEngine` açılıp açılmeyeceğini denetler. Bu ayarın performans etkileri vardır ve soğuk başlatma/başlatma süresini etkiler. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL & Azure Tablo metninin bağımlılık çağrılarında birlikte yakalanıp yakalanmayacağını denetler. Performans uyarısı: Bu ayar `InstrumentationEngine` gerektirir. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager ile uygulama ayarlarını App Service

Uygulama Hizmetleri için uygulama ayarları, [Azure Resource Manager şablonlarıyla](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)yönetilebilir ve yapılandırılabilir. Bu yöntem, Azure Resource Manager otomasyonu ile yeni App Service kaynakları dağıtıldığında veya mevcut kaynakların ayarlarını değiştirirken kullanılabilir.

App Service için uygulama ayarları JSON ' un temel yapısı aşağıda verilmiştir:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Application Insights için yapılandırılmış uygulama ayarlarına sahip Azure Resource Manager bir şablon örneği için, bu [şablon](https://github.com/Andrew-MSFT/BasicImageGallery) faydalı olabilir, özellikle de [238. satırdan](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)başlayan bir bölümdür.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Bir Application Insights kaynağı oluşturmayı otomatikleştirin ve yeni oluşturduğunuz App Service bağlayın.

Yapılandırılmış tüm varsayılan Application Insights ayarlarla Azure Resource Manager şablonu oluşturmak için, Application Insights etkin yeni bir Web uygulaması oluşturacağım gibi işlemi başlatın.

**Otomasyon seçeneklerini** belirleyin

   ![Web uygulaması oluşturma menüsünü App Service](./media/azure-web-apps/create-web-app.png)

Bu seçenek, tüm gerekli ayarlarla yapılandırılmış en son Azure Resource Manager şablonunu oluşturur.

  ![App Service Web uygulaması şablonu](./media/azure-web-apps/arm-template.png)

Aşağıda, `AppMonitoredSite` ' ın tüm örneklerinin site adınızla yerine geçecek bir örnek verilmiştir:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Şablon, uygulama ayarlarını "varsayılan" modunda oluşturacaktır. Bu mod performansı en iyi duruma getirilmiştir, ancak istediğiniz özellikleri etkinleştirmek için şablonu değiştirebilirsiniz.

### <a name="enabling-through-powershell"></a>PowerShell aracılığıyla etkinleştirme

Uygulama izlemeyi PowerShell aracılığıyla etkinleştirmek için, yalnızca temeldeki uygulama ayarlarının değiştirilmesi gerekir. Aşağıda, "Appmonitortoredrg" kaynak grubunda "Appmonitortoredsite" adlı bir Web sitesi için uygulama izlemeye olanak tanıyan bir örnek verilmiştir ve "012345678-abcd-EF01-2345-6789abcd" izleme anahtarına gönderilecek verileri yapılandırır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Yükseltme izleme uzantısı/Aracısı

### <a name="upgrading-from-versions-289-and-up"></a>2\.8.9 ve up sürümlerinden yükseltme

2\.8.9 sürümünden yükseltme, ek eylemler olmadan otomatik olarak gerçekleşir. Yeni izleme bitleri, hedef App Service 'e arka planda teslim edilir ve uygulama yeniden başlatıldığında alınır.

Çalıştırdığınız uzantının hangi sürümünün olduğunu denetlemek için `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![URL yolu http://yoursitename.scm.azurewebsites.net/ApplicationInsights ekran görüntüsü](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>1\.0.0-2.6.5 sürümlerinden yükseltme

Version 2.8.9 sürümünden itibaren önceden yüklenmiş site uzantısı kullanılır. Önceki bir sürümdeyse, iki şekilde güncelleştirebilirsiniz:

* [Portal üzerinden etkinleştirerek yükseltme](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)yapın. (Azure App Service Application Insights uzantısı yüklü olsa da, UI yalnızca **Etkinleştir** düğmesini gösterir. Arka planda, eski özel site uzantısı kaldırılır.)

* [PowerShell aracılığıyla Yükselt](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Uygulama ayarlarını, önceden yüklenmiş site uzantısını Applicationınsimalsagent olacak şekilde ayarlayın. Bkz. [PowerShell aracılığıyla etkinleştirme](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Azure App Service için Application Insights uzantısı adlı özel site uzantısını el ile kaldırın.

Yükseltme, 2.5.1 ' den önceki bir sürümden yapılamıyorsa, uygulama bin klasöründen Applicationınsigyoze dll 'lerinin kaldırılıp kaldırılmadığını denetleyin. [sorun giderme adımları bölümüne bakın](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Sorun giderme

Azure Uygulama Hizmetleri 'nde çalışan .NET ve .NET Core tabanlı uygulamalar için uzantı/aracı tabanlı izlemeye yönelik adım adım sorun giderme kılavuzumuz aşağıda verilmiştir.

> [!NOTE]
> Java ve Node. js uygulamaları yalnızca el ile SDK tabanlı araçlar aracılığıyla Azure Uygulama hizmetlerinde desteklenir ve bu nedenle aşağıdaki adımlar bu senaryolara uygulanmaz.

> [!NOTE]
> ASP.NET Core 3,0 uygulamaları desteklenmez. Lütfen ASP.NET Core 3,0 uygulamaları için kod aracılığıyla [el ile](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) izleme uygulayın.

1. Uygulamanın `ApplicationInsightsAgent` ile izlenip izlenmediğini denetleyin.
    * @No__t_0 uygulama ayarının "~ 2" değerine ayarlandığını denetleyin.
2. Uygulamanın izlenecek gereksinimleri karşıladığından emin olun.
    * @No__t_0 gidin

    ![@No__t_0 sonuçları sayfasının ekran görüntüsü](./media/azure-web-apps/app-insights-sdk-status.png)

    * @No__t_0 `Pre-Installed Site Extension, version 2.8.12.1527, is running.` olduğunu onaylayın
        * Çalışmıyorsa, [etkinleştirme Application Insights izleme yönergelerini](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) izleyin

    * Durum kaynağının var olduğunu ve şu şekilde göründüğünü onaylayın: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Benzer bir değer yoksa, uygulamanın Şu anda çalışmadığı veya desteklenmediği anlamına gelir. Uygulamanın çalıştığından emin olmak için, uygulama URL 'si/uygulama uç noktalarını el ile ziyaret etmeyi deneyin, bu, çalışma zamanı bilgilerinin kullanılabilir hale gelmesini sağlar.

    * @No__t_0 `true` olduğunu onaylayın
        * Yanlış ise, uygulama ayarlarınıza ' APPINSIGHTS_INSTRUMENTATIONKEY ' ekleyin.

    * @No__t_0, `AppContainsDiagnosticSourceAssembly` ve `AppContainsAspNetTelemetryCorrelationAssembly` için hiçbir giriş olmadığını doğrulayın.
        * Bu girdilerden herhangi biri mevcutsa, şu paketleri uygulamanızdan kaldırın: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` ve `Microsoft.AspNet.TelemetryCorrelation`.

Aşağıdaki tabloda, bu değerlerin ne anlama geldiğini, temeldeki nedenleri ve önerilen düzeltmeleri verilmiştir:

|Sorun değeri|Açıklama|Onar
|---- |----|---|
| `AppAlreadyInstrumented:true` | Bu değer, uzantının bir SDK 'nın bazı yönlerinin uygulamada zaten bulunduğunu algıladı ve geri dönecek. Bunun nedeni, `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` veya `Microsoft.ApplicationInsights` ' e bir başvuru olabilir  | Başvuruları kaldırın. Bu başvurulardan bazıları varsayılan olarak belirli Visual Studio şablonlarından eklenmiştir ve Visual Studio 'nun eski sürümleri `Microsoft.ApplicationInsights` ' a başvuruları ekleyebilir.
|`AppAlreadyInstrumented:true` | Uygulama .NET Core 2,1 veya 2,2 ' i hedefliyorsanız ve [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta Package öğesine başvuruyorsa, Application Insights ve uzantı geri açılır. | .NET Core 2.1, 2.2 ' deki müşterilerin yerine Microsoft. AspNetCore. app meta paketini kullanması [önerilir](https://github.com/aspnet/Announcements/issues/287) .|
|`AppAlreadyInstrumented:true` | Bu değere, önceki bir dağıtımdan uygulama klasöründeki yukarıdaki dll 'lerin varlığı da neden olabilir. | Bu dll 'lerin kaldırıldığından emin olmak için uygulama klasörünü temizleyin. Hem yerel uygulamanızın bin dizinini hem de App Service Wwwroot dizinini denetleyin. (App Service Web uygulamanızın Wwwroot dizinini denetlemek için: Gelişmiş araçlar (kudu) > hata ayıklama Konsolu > CMD > home\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Bu değer, uzantının uygulamada `Microsoft.AspNet.TelemetryCorrelation` ' a başvurduğu ve geri dönecek olduğunu gösterir. | Başvuruyu kaldırın.
|`AppContainsDiagnosticSourceAssembly**:true`|Bu değer, uzantının uygulamada `System.Diagnostics.DiagnosticSource` ' a başvurduğu ve geri dönecek olduğunu gösterir.| Başvuruyu kaldırın.
|`IKeyExists:false`|Bu değer, `APPINSIGHTS_INSTRUMENTATIONKEY` AppSetting 'de izleme anahtarının bulunmadığını gösterir. Olası nedenler: değerler yanlışlıkla kaldırılmış olabilir, Otomasyon betikindeki değerleri ayarlamayı unuttu, vb. | Ayarın App Service uygulama ayarlarında bulunduğundan emin olun.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED ve urlCompression desteklenmez

İçeriğin kodlandığı durumlarda APPINSIGHTS_JAVASCRIPT_ENABLED = true kullanırsanız, şöyle bir hata alabilirsiniz: 

- 500 URL yeniden yazma hatası
- 500,53 URL yeniden yazma hatası ileti giden yeniden yazma kuralları HTTP yanıtının içeriği kodlanmışsa (' gzip ') uygulanamaz. 

Bunun nedeni, APPINSIGHTS_JAVASCRIPT_ENABLED uygulama ayarından aynı anda doğru ve içerik kodlamasının mevcut olmasını sağlar. Bu senaryo henüz desteklenmiyor. Geçici çözüm, APPINSIGHTS_JAVASCRIPT_ENABLED uygulamasını uygulama ayarlarından kaldırdır. Ne yazık ki, istemci/tarayıcı tarafı JavaScript izleme hâlâ gerekliyse, Web sayfalarınız için el ile SDK başvuruları gerekir. Lütfen JavaScript SDK 'Sı ile el ile izleme [yönergelerini](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) izleyin.

Application Insights Aracısı/uzantısıyla ilgili en son bilgiler için, [sürüm notlarına](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)göz atın.

## <a name="next-steps"></a>Sonraki adımlar
* [Canlı uygulamanızda profil oluşturucuyu çalıştırın](../app/profiler.md).
* [Azure İşlevleri](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights ile Azure İşlevlerini izleyin
* Application Insights’a gönderilmek üzere [Azure tanılamayı etkinleştirin](../platform/diagnostics-extension-to-application-insights.md).
* Hizmetinizin kullanılabilir ve yanıt verir durumda oluğundan emin olmak için [hizmet durumu ölçümlerini izleyin](../platform/data-platform.md).
* İşletimsel olaylar gerçekleştiğinde ya da ölçümler bir eşiği aştığında [uyarı bildirimleri alın](../platform/alerts-overview.md).
* Bir web sayfasını ziyaret eden tarayıcılardan istemci telemetrisi toplamak istiyorsanız [JavaScript uygulamaları ve web sayfaları için Application Insights](javascript.md)’ı kullanın.
* Sitenizin kapalı olması durumunda uyarı almak istiyorsanız [Kullanılabilirlik web testleri](monitor-web-app-availability.md) ayarlayın.
