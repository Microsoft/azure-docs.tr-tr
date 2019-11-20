---
title: Application Insights ile canlı Azure Service Fabric uygulamaları profilini yayınlama | Microsoft Docs
description: Service Fabric bir uygulama için profil oluşturucuyu etkinleştirme
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a7a64be3c73ea82c6bf3d905772f3278f9bda5df
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818469"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights ile canlı Azure Service Fabric uygulamaları profilini yayınlama

Ayrıca, bu hizmetlerde Application Insights Profiler dağıtabilirsiniz:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Sanal Makineler](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Ortam dağıtım tanımını ayarlama

Application Insights Profiler Azure Tanılama dahildir. Azure Tanılama uzantısını Service Fabric kümeniz için bir Azure Resource Manager şablonu kullanarak yükleyebilirsiniz. [Bir Service Fabric kümesine Azure tanılama yükleyen bir şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)alın.

Ortamınızı ayarlamak için aşağıdaki işlemleri gerçekleştirin:

1. Profiler .NET Framework ve .Net Core destekler. .NET Framework kullanıyorsanız, [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) veya üstünü kullandığınızdan emin olun. Dağıtılan işletim sisteminin `Windows Server 2012 R2` veya üzeri olduğunu doğrulamak yeterlidir. Profiler, .NET Core 2,1 ve daha yeni uygulamaları destekler.

1. Dağıtım şablonu dosyasında [Azure tanılama](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) uzantısını arayın.

1. Aşağıdaki `SinksConfig` bölümünü `WadCfg`alt öğesi olarak ekleyin. `ApplicationInsightsProfiler` özelliği değerini kendi Application Insights izleme anahtarınızla değiştirin:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Tanılama uzantısını dağıtım şablonunuza ekleme hakkında daha fazla bilgi için bkz. [WINDOWS VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanma](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Service Fabric kümenizi Azure Resource Manager şablonunuzu kullanarak dağıtın.  
  Ayarlarınız doğruysa, Azure Tanılama uzantısı yüklendiğinde Application Insights Profiler yüklenir ve etkinleştirilir. 

1. Service Fabric uygulamanıza Application Insights ekleyin.  
  İstekleriniz için profillerin toplanacağı profil oluşturucunun, uygulamanız Application Insights izleme işlemleri olmalıdır. Durum bilgisi olmayan API 'Ler için, [profil oluşturma isteklerini izleme](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)yönergelerine bakabilirsiniz. Diğer uygulama türlerindeki özel işlemleri izleme hakkında daha fazla bilgi için bkz. [.NET SDK ile özel işlemleri izleme Application Insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Uygulamanızı yeniden dağıtın.


## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanıza trafik oluşturun (örneğin, bir [Kullanılabilirlik testi](monitor-web-app-availability.md)başlatın). Ardından, izlemelerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
* Azure portal [Profil Oluşturucu izlemeleri](profiler-overview.md?toc=/azure/azure-monitor/toc.json) bölümüne bakın.
* Profil Oluşturucu sorunlarını gidermeye yönelik yardım için bkz. [Profil Oluşturucu sorun giderme](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
