---
title: Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme | Microsoft Docs
description: Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025688"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme

Snapshot Debugger Şu anda Windows hizmet planları üzerinde Azure App Service çalışan ASP.NET ve ASP.NET Core uygulamalarını desteklemektedir.

Anlık görüntü hata ayıklayıcısı kullanırken uygulamanızı temel hizmet katmanında veya daha yüksek bir sürüme çalıştırmanızı öneririz.

Çoğu uygulama için, ücretsiz ve paylaşılan hizmet katmanlarında anlık görüntüleri kaydetmek için yeterli bellek veya disk alanı yoktur.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Snapshot Debugger etkinleştir
Bir uygulama için Snapshot Debugger etkinleştirmek üzere aşağıdaki yönergeleri izleyin.

Farklı bir Azure hizmeti türü çalıştırıyorsanız, desteklenen diğer platformlarda Snapshot Debugger etkinleştirme talimatları aşağıda verilmiştir:
* [Azure İşlevi](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Hizmetleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Şirket içi sanal veya fiziksel makineler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> .NET Core 'un önizleme sürümünü kullanıyorsanız veya uygulamanız Application Insights SDK 'ya bir bağımlı derleme aracılığıyla doğrudan veya dolaylı olarak başvuruyorsa, diğer ortamların [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketini uygulamayla birlikte içermesini sağlamak Için [Snapshot Debugger etkinleştirme](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) yönergelerini izleyin ve ardından aşağıdaki yönergeleri tamamlayın. 
>
> Application Insights Snapshot Debugger birlikte yükleme, .NET Core destek ilkesini izler.
> Desteklenen çalışma zamanları hakkında daha fazla bilgi için bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Snapshot Debugger, App Services çalışma zamanının bir parçası olarak önceden yüklenir, ancak App Service uygulamanız için anlık görüntüler almak üzere açmanız gerekir.

Bir uygulamayı dağıttıktan sonra, anlık görüntü hata ayıklayıcısını etkinleştirmek için aşağıdaki adımları izleyin:

1. App Service için Azure Denetim Masası ' na gidin.
2. **Ayarlar > Application Insights** sayfasına gidin.

   ![App Services portalında App Insights 'ı etkinleştirme](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Uygulamanızı izlemek üzere yeni bir kaynak oluşturmak veya var olan bir Application Insights kaynağını seçmek için sayfadaki yönergeleri izleyin. Ayrıca Snapshot Debugger için her iki **anahtar de bulunduğundan emin olun.**

   ![App Insights site uzantısı Ekle][Enablement UI]

4. Snapshot Debugger artık bir App Services uygulama ayarı kullanılarak etkinleştirildi.

    ![Snapshot Debugger için uygulama ayarı][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Diğer bulutlar için Snapshot Debugger etkinleştir

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide) Application Insights bağlantı dizesi aracılığıyla yapılır.

|Bağlantı dizesi özelliği    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Anlık görüntü Tendpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Diğer bağlantı geçersiz kılmaları hakkında daha fazla bilgi için bkz. [Application Insights belgeleri](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger devre dışı bırak

**Etkinleştirme Snapshot Debugger** ile aynı adımları izleyin, ancak her iki anahtarı da Snapshot Debugger için **kapalı** olarak değiştirin.

Uygulama özel durumlarının tanılamayı kolaylaştırmak için tüm uygulamalarınızda Snapshot Debugger etkinleştirilmiş olması önerilir.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Azure App Service için, Azure Resource Manager şablonu içinde uygulama ayarlarını Snapshot Debugger ve profil oluşturucuyu etkinleştirmek üzere ayarlayabilirsiniz ve aşağıdaki şablon kod parçacığına bakın:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Sonraki adımlar

- Uygulamanıza bir özel durum tetikleyebilmesi için trafik oluşturun. Ardından, anlık görüntülerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
- Azure portal [anlık görüntüleri](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) görüntüleyin.
- Sorunları giderme Snapshot Debugger konusunda yardım için bkz. [Snapshot Debugger sorun giderme](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png