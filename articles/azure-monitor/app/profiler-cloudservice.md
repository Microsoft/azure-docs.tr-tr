---
title: Application Insights ile canlı Azure Cloud Services profili | Microsoft Docs
description: Azure Cloud Services için Application Insights Profiler etkinleştirin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 682711d7681e3646ae14686b01542bc5d7432179
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820503"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights ile canlı Azure Cloud Services profili

Ayrıca, bu hizmetlerde Application Insights Profiler dağıtabilirsiniz:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric uygulamaları](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Sanal Makineler](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler, Azure Tanılama uzantısıyla yüklenir. Profil oluşturucuyu yüklemek ve Application Insights kaynağına profil göndermek için Azure Tanılama yapılandırmanız yeterlidir.

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services için profil oluşturucuyu etkinleştir
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) veya daha yeni bir sürümünü kullandığınızdan emin olun. İşletim sistemi ailesi 4 kullanıyorsanız, bir [Başlangıç göreviyle](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet).NET Framework 4.6.1 veya daha yeni bir sürümü yüklemeniz gerekir. İşletim sistemi ailesi 5, varsayılan olarak .NET Framework uyumlu bir sürümünü içerir. 

1. [Application Insights SDK 'Yı Azure Cloud Services 'a](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)ekleyin.

    **Cloud Services için WAD içinde yer alan Profiler 'daki hata düzeltildi.** Cloud Services için en son WAD (1.12.2.0) sürümü, App Insights SDK 'sının tüm son sürümleriyle birlikte çalışmaktadır. Bulut hizmeti ana bilgisayarları WAD 'yi otomatik olarak yükseltir, ancak bu işlem anında değil. Bir yükseltmeyi zorlamak için hizmetinizi yeniden dağıtabilirsiniz veya düğümü yeniden başlatabilirsiniz.

1. Application Insights ile istekleri izle:

    * ASP.NET Web rolleri için Application Insights istekleri otomatik olarak izleyebilir.

    * Çalışan rolleri için [istekleri izlemek için kod ekleyin](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Profil oluşturucuyu etkinleştirmek için Azure Tanılama uzantısını yapılandırın:

    a. Aşağıda gösterildiği gibi, uygulama rolünüzün [Azure tanılama](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics. wadcfgx* dosyasını bulun:  

      ![Tanılama yapılandırma dosyasının konumu](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Dosyayı bulamıyorsanız bkz. [Azure Cloud Services için tanılamayı ayarlama ve sanal makineler](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Aşağıdaki `SinksConfig` bölümünü `WadCfg`alt öğesi olarak ekleyin:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > *Diagnostics. wadcfgx* dosyası aynı zamanda ApplicationInsights türünde başka bir havuz içeriyorsa, aşağıdaki izleme anahtarlarından üçü de aynı olmalıdır:  
    > * Uygulamanız tarafından kullanılan anahtar. 
    > * ApplicationInsights havuzu tarafından kullanılan anahtar. 
    > * Applicationınsightsprofiler havuzu tarafından kullanılan anahtar. 
    >
    > *ServiceConfiguration.\*. cscfg* dosyalarında `ApplicationInsights` havuzu tarafından kullanılan gerçek izleme anahtarı değerini bulabilirsiniz. 
    > Visual Studio 15,5 Azure SDK sürümünden sonra, yalnızca uygulama ve Applicationınsightsprofiler havuzunun kullandığı izleme anahtarlarının birbirleriyle eşleşmesi gerekir.

1. Hizmetinizi yeni tanılama yapılandırmasıyla dağıtın ve Application Insights Profiler hizmetinize çalışacak şekilde yapılandırılır.
 
## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanıza trafik oluşturun (örneğin, bir [Kullanılabilirlik testi](monitor-web-app-availability.md)başlatın). Ardından, izlemelerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
* Azure portal [Profil Oluşturucu izlemeleri](profiler-overview.md?toc=/azure/azure-monitor/toc.json) bölümüne bakın.
* Profil Oluşturucu sorunlarını gidermek için bkz. [Profil Oluşturucu sorun giderme](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
