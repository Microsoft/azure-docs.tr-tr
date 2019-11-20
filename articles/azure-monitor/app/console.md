---
title: Konsol uygulamaları için Azure Application Insights | Microsoft Docs
description: Kullanılabilirlik, performans ve kullanım için Web uygulamalarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.openlocfilehash: 1cafa78fb4fba28fbd0691e256efe482fc9664ef
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678197"
---
# <a name="application-insights-for-net-console-applications"></a>.NET konsol uygulamaları için Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) , Web uygulamanızı kullanılabilirlik, performans ve kullanım açısından izlemenize olanak sağlar.

[Microsoft Azure](https://azure.com)bir aboneliğiniz olması gerekir. Windows, Xbox Live veya diğer Microsoft bulut hizmetleri için sahip olabileceğiniz bir Microsoft hesabı oturum açın. Takımınız Azure 'a yönelik bir kurumsal aboneliğe sahip olabilir: sahibinden Microsoft hesabı kullanarak sizi eklemesini isteyin.

> [!NOTE]
> Herhangi bir konsol uygulaması için Application Insights etkinleştirmek üzere kullanılabilecek [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) adlı yeni bir beta Application Insights SDK 'sı vardır. Bu paketin ve ilgili yönergelerin [burada](../../azure-monitor/app/worker-service.md)kullanılması önerilir. Bu paket [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)' i hedefler ve bu nedenle .net Core 2,0 veya üzeri sürümlerde ve .NET Framework 4.7.2 veya üzeri sürümlerde kullanılabilir.
Bu yeni paketin kararlı bir sürümü kullanıma sunulduktan sonra bu belge kullanımdan kalkacaktır.

## <a name="getting-started"></a>Başlangıç

* [Azure portalında](https://portal.azure.com) [bir Application Insights kaynağı oluşturun](../../azure-monitor/app/create-new-resource.md). Uygulama türü için **genel**' i seçin.
* İzleme Anahtarının bir kopyasını oluşturun. Oluşturduğunuz yeni kaynağın **temel** bileşenler açılan penceresinde anahtarı bulun. 
* En son [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) paketini yükler.
* Herhangi bir Telemetriyi izlemeden önce kodunuzda izleme anahtarını ayarlayın (veya APPINSIGHTS_INSTRUMENTATIONKEY ortam değişkeni ayarlayın). Bundan sonra Telemetriyi el ile izleyebilmeniz ve Azure portal üzerinde görebilmelisiniz

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) paketinin en son sürümünü yükler-http, SQL veya diğer dış bağımlılık çağrılarını otomatik olarak izler.

Koddan Application Insights başlatabilir veya `ApplicationInsights.config` dosyası kullanarak yapılandırabilirsiniz. Başlatmanın olabildiğince erken göründüğünden emin olun. 

> [!NOTE]
> **ApplicationInsights. config** dosyasına başvuran yönergeler yalnızca .NET Framework hedefleyen ve .NET Core uygulamaları için geçerli olmayan uygulamalar için geçerlidir.

### <a name="using-config-file"></a>Yapılandırma dosyası kullanma
Application Insights SDK varsayılan olarak, `TelemetryConfiguration` oluşturulduğunda çalışma dizininde `ApplicationInsights.config` dosyası arar

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Yapılandırma dosyasının yolunu da belirtebilirsiniz.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Daha fazla bilgi için bkz. [yapılandırma dosyası başvurusu](configuration-with-applicationinsights-config.md).

[Microsoft. ApplicationInsights. WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) paketinin en son sürümünü yükleyerek yapılandırma dosyasına tam bir örnek alabilirsiniz. Aşağıda, kod örneğine denk gelen bağımlılık koleksiyonu için **En düşük** yapılandırma bulunur.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Koddan telemetri toplamayı yapılandırma
> [!NOTE]
> Yapılandırma dosyası okuma, .NET Core 'da desteklenmez. [ASP.NET Core için APPLICATION INSIGHTS SDK](../../azure-monitor/app/asp-net-core.md) kullanmayı düşünebilirsiniz

* Uygulama başlatma sırasında `DependencyTrackingTelemetryModule` örneği oluşturma ve yapılandırma-tek olmalı ve uygulama ömrü için korunması gerekir.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Ortak telemetri başlatıcıları ekleme

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Yapılandırmayı düz `TelemetryConfiguration()` Oluşturucusu ile oluşturduysanız, ayrıca bağıntı desteğini etkinleştirmeniz gerekir. Dosyadan yapılandırmayı okuduğunuzda `TelemetryConfiguration.CreateDefault()` veya `TelemetryConfiguration.Active` ' yi kullandıysanız **Bu gerekli değildir** .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* [Burada](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/) açıklandığı gibi performans sayacı toplayıcı modülünü de yüklemek ve başlatmak isteyebilirsiniz


#### <a name="full-example"></a>Tam örnek

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Sonraki adımlar
* REST, SQL veya diğer dış kaynakların sizi yavaşlattığını görmek için [bağımlılıkları izleyin](../../azure-monitor/app/asp-net-dependencies.md) .
* Uygulamanızın performansını ve kullanımını daha ayrıntılı bir görünüm için kendi olaylarınızı ve ölçümlerini göndermek üzere [API 'Yi kullanın](../../azure-monitor/app/api-custom-events-metrics.md) .
