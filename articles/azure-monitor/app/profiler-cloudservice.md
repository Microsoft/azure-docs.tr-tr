---
title: Application Insights ile canlı Azure Cloud Services profili | Microsoft Docs
description: Azure Cloud Services için Application Insights Profiler etkinleştirin.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 5eb6680bba44872821a2f0d094d31d0ad994f8a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589588"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights ile canlı Azure Cloud Services profili

Ayrıca, bu hizmetlerde Application Insights Profiler dağıtabilirsiniz:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric uygulamaları](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Sanal Makineler](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler, Azure Tanılama uzantısıyla yüklenir. Profil oluşturucuyu yüklemek ve Application Insights kaynağına profil göndermek için Azure Tanılama yapılandırmanız yeterlidir.

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services için profil oluşturucuyu etkinleştir
1. [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) veya daha yeni bir sürümünü kullandığınızdan emin olun. İşletim sistemi ailesi 4 kullanıyorsanız, bir [Başlangıç göreviyle](../../cloud-services/cloud-services-dotnet-install-dotnet.md).NET Framework 4.6.1 veya daha yeni bir sürümü yüklemeniz gerekir. İşletim sistemi ailesi 5, varsayılan olarak .NET Framework uyumlu bir sürümünü içerir. 

1. [Application Insights SDK 'Yı Azure Cloud Services 'a](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json)ekleyin.

    **Cloud Services için WAD içinde yer alan Profiler 'daki hata düzeltildi.** Cloud Services için en son WAD (1.12.2.0) sürümü, App Insights SDK 'sının tüm son sürümleriyle birlikte çalışmaktadır. Bulut hizmeti ana bilgisayarları WAD 'yi otomatik olarak yükseltir, ancak bu işlem anında değil. Bir yükseltmeyi zorlamak için hizmetinizi yeniden dağıtabilirsiniz veya düğümü yeniden başlatabilirsiniz.

1. Application Insights ile istekleri izle:

    * ASP.NET Web rolleri için Application Insights istekleri otomatik olarak izleyebilir.

    * Çalışan rolleri için [istekleri izlemek için kod ekleyin](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Profil oluşturucuyu etkinleştirmek için Azure Tanılama uzantısını yapılandırın:

    a. Aşağıda gösterildiği gibi, uygulama rolünüzün [Azure tanılama](../agents/diagnostics-extension-overview.md) *Diagnostics. wadcfgx* dosyasını bulun:  

      ![Tanılama yapılandırma dosyasının konumu](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Dosyayı bulamıyorsanız bkz. [Azure Cloud Services için tanılamayı ayarlama ve sanal makineler](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Aşağıdaki `SinksConfig` bölümü öğesinin bir alt öğesi olarak ekleyin `WadCfg` :  

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
    > `ApplicationInsights`ServiceConfiguration içinde havuz tarafından kullanılan gerçek izleme anahtarı değerini bulabilirsiniz *. \* cscfg* dosyaları. 
    > Visual Studio 15,5 Azure SDK sürümünden sonra, yalnızca uygulama ve Applicationınsightsprofiler havuzunun kullandığı izleme anahtarlarının birbirleriyle eşleşmesi gerekir.

1. Hizmetinizi yeni tanılama yapılandırmasıyla dağıtın ve Application Insights Profiler hizmetinize çalışacak şekilde yapılandırılır.
 
## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanıza trafik oluşturun (örneğin, bir [Kullanılabilirlik testi](monitor-web-app-availability.md)başlatın). Ardından, izlemelerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
* Azure portal [Profil Oluşturucu izlemeleri](profiler-overview.md?toc=/azure/azure-monitor/toc.json) bölümüne bakın.
* Profil Oluşturucu sorunlarını gidermek için bkz. [Profil Oluşturucu sorun giderme](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

