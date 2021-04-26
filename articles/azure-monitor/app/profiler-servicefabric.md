---
title: Application Insights ile canlı Azure Service Fabric uygulamaları profilini yayınlama
description: Service Fabric bir uygulama için profil oluşturucuyu etkinleştirme
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589564"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights ile canlı Azure Service Fabric uygulamaları profilini yayınlama

Ayrıca, bu hizmetlerde Application Insights Profiler dağıtabilirsiniz:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Sanal Makineler](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Ortam dağıtım tanımını ayarlama

Application Insights Profiler Azure Tanılama dahildir. Azure Tanılama uzantısını Service Fabric kümeniz için bir Azure Resource Manager şablonu kullanarak yükleyebilirsiniz. [Bir Service Fabric kümesine Azure tanılama yükleyen bir şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)alın.

Ortamınızı ayarlamak için aşağıdaki işlemleri gerçekleştirin:

1. Profiler .NET Framework ve .Net Core destekler. .NET Framework kullanıyorsanız, [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) veya üstünü kullandığınızdan emin olun. Dağıtılan işletim sisteminin `Windows Server 2012 R2` veya daha yeni bir sürümü olduğunu doğrulamak yeterlidir. Profiler, .NET Core 2,1 ve daha yeni uygulamaları destekler.

1. Dağıtım şablonu dosyasında [Azure tanılama](../agents/diagnostics-extension-overview.md) uzantısını arayın.

1. Aşağıdaki `SinksConfig` bölümü öğesinin bir alt öğesi olarak ekleyin `WadCfg` . `ApplicationInsightsProfiler`Özellik değerini kendi Application Insights izleme anahtarınızla değiştirin:  

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

      Tanılama uzantısını dağıtım şablonunuza ekleme hakkında daha fazla bilgi için bkz. [WINDOWS VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanma](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Service Fabric kümenizi Azure Resource Manager şablonunuzu kullanarak dağıtın.  
  Ayarlarınız doğruysa, Azure Tanılama uzantısı yüklendiğinde Application Insights Profiler yüklenir ve etkinleştirilir. 

1. Service Fabric uygulamanıza Application Insights ekleyin.  
  İstekleriniz için profillerin toplanacağı profil oluşturucunun, uygulamanız Application Insights izleme işlemleri olmalıdır. Durum bilgisi olmayan API 'Ler için, [profil oluşturma isteklerini izleme](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)yönergelerine bakabilirsiniz. Diğer uygulama türlerindeki özel işlemleri izleme hakkında daha fazla bilgi için bkz. [.NET SDK ile özel işlemleri izleme Application Insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Uygulamanızı yeniden dağıtın.


## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanıza trafik oluşturun (örneğin, bir [Kullanılabilirlik testi](monitor-web-app-availability.md)başlatın). Ardından, izlemelerin Application Insights örneğine gönderilmesi için 10 ila 15 dakika bekleyin.
* Azure portal [Profil Oluşturucu izlemeleri](profiler-overview.md?toc=/azure/azure-monitor/toc.json) bölümüne bakın.
* Profil Oluşturucu sorunlarını gidermeye yönelik yardım için bkz. [Profil Oluşturucu sorun giderme](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
