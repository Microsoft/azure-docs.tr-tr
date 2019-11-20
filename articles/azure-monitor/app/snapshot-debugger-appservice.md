---
title: Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme | Microsoft Docs
description: Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899885"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service .NET uygulamaları için Snapshot Debugger etkinleştirme

Snapshot Debugger Şu anda Windows hizmet planlarına Azure App Service çalışan ASP.NET ve ASP.NET Core uygulamalar için çalışır.

## <a id="installation"></a>Snapshot Debugger etkinleştir
Bir uygulama için Snapshot Debugger etkinleştirmek üzere aşağıdaki yönergeleri izleyin. Farklı bir Azure hizmeti türü çalıştırıyorsanız, desteklenen diğer platformlarda Snapshot Debugger etkinleştirme talimatları aşağıda verilmiştir:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Hizmetleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Şirket içi sanal veya fiziksel makineler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

.NET Core 'un önizleme sürümünü kullanıyorsanız, uygulama ile [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketini dahil etmek için lütfen daha önce [diğer ortamlar için etkinleştirme Snapshot Debugger](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) yönelik yönergeleri izleyin. ardından aşağıdaki yönergelerin geri kalanını tamamlayın. 

Application Insights Snapshot Debugger, App Services çalışma zamanının bir parçası olarak önceden yüklenir, ancak App Service uygulamanız için anlık görüntüler almak üzere açmanız gerekir. Bir uygulamayı dağıttıktan sonra, kaynak koda Application Insights SDK eklemiş olsanız bile, anlık görüntü hata ayıklayıcıyı etkinleştirmek için aşağıdaki adımları izleyin.

1. Azure portal **App Services** bölmesine gidin.
2. **Ayarlar > Application Insights** bölmesine gidin.

   ![App Services portalında App Insights 'ı etkinleştirme](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Uygulamanızı izlemek üzere yeni bir kaynak oluşturmak veya var olan bir Application Insights kaynağını seçmek için bölmedeki yönergeleri izleyin. Ayrıca Snapshot Debugger için her iki **anahtar de bulunduğundan emin olun.**

   ![App Insights site uzantısı Ekle][Enablement UI]

4. Snapshot Debugger artık bir App Services uygulama ayarı kullanılarak etkinleştirildi.

    ![Snapshot Debugger için uygulama ayarı][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger devre dışı bırak

**Etkinleştirme Snapshot Debugger**ile aynı adımları izleyin, ancak her iki anahtarı da Snapshot Debugger için **kapalı**olarak değiştirin.
Uygulama özel durumlarının tanılamayı kolaylaştırmak için tüm uygulamalarınızda Snapshot Debugger etkinleştirilmiş olması önerilir.

## <a name="next-steps"></a>Sonraki adımlar

- Uygulamanıza bir özel durum tetikleyebilmesi için trafik oluşturun. Ardından, anlık görüntülerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
- Azure portal [anlık görüntüleri](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) görüntüleyin.
- Sorunları giderme Snapshot Debugger konusunda yardım için bkz. [Snapshot Debugger sorun giderme](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

