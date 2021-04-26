---
title: EventFlow ile Azure Service Fabric olay toplama
description: Azure Service Fabric kümelerini izleme ve Tanılama için EventFlow kullanarak olayları toplama ve toplama hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 2/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a502fe05a862893d2c4879a6976d8d8d1044cbf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627770"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>EventFlow kullanarak olay toplama ve toplama

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) , olayları bir düğümden bir veya daha fazla izleme hedefine yönlendirebilir. Hizmet projenizde bir NuGet paketi olarak eklendiğinden, EventFlow kodu ve yapılandırması hizmetle birlikte gezinir ve Azure Tanılama hakkında daha önce bahsedilen düğüm başına yapılandırma sorununu ortadan kaldırır. EventFlow hizmeti işleminiz dahilinde çalışır ve doğrudan yapılandırılan çıkışlara bağlanır. Doğrudan bağlantı nedeniyle Azure, Container ve şirket içi hizmet dağıtımları için EventFlow geçerlidir. Her EventFlow işlem hattı bir dış bağlantı yaptığından, EventFlow ' ı bir kapsayıcıda olduğu gibi yüksek yoğunluklu senaryolarda çalıştırırsanız dikkatli olun. Bu nedenle, birkaç işlem barındırdıysanız, birkaç giden bağlantı alırsınız! Aynı işlemde bir çalıştırmanın tüm çoğaltmaları olduğundan `ServiceType` ve bu, giden bağlantı sayısını sınırladığından, bu, Service Fabric uygulamalarda çok önemli değildir. EventFlow, yalnızca belirtilen filtreyle eşleşen olayların gönderilmesi için olay filtreleme olanağı da sunar.

## <a name="set-up-eventflow"></a>EventFlow ayarlama

EventFlow ikilileri, bir NuGet paketleri kümesi olarak kullanılabilir. Service Fabric bir hizmet projesine EventFlow eklemek için, Çözüm Gezgini projeye sağ tıklayın ve "NuGet Paketlerini Yönet" i seçin. "Araştır" sekmesine geçin ve "" araması yapın `Diagnostics.EventFlow` :

![Visual Studio NuGet Paket Yöneticisi Kullanıcı arabirimindeki EventFlow NuGet paketleri](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

"Girişler" ve "çıktılar" ile etiketlenmiş çeşitli paketlerin bir listesini görürsünüz. EventFlow, çeşitli farklı günlük sağlayıcılarını ve Çözümleyicileri destekler. EventFlow barındırma hizmeti, uygulama günlüklerinin kaynağına ve hedefine bağlı olarak uygun paketleri içermelidir. Çekirdek ServiceFabric paketine ek olarak, en az bir giriş ve çıkış yapılandırılmış olmalıdır. Örneğin, Application Insights 'e EventSource olayları göndermek için aşağıdaki paketleri ekleyebilirsiniz:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` hizmetin EventSource sınıfından ve *Microsoft-servicefabric-Services* ve *Microsoft-Servicefabric-aktör* gibi standart eventkaynaklardaki verileri yakalamak için
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (günlükleri bir Azure Application Insights kaynağına göndereceğiz)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(Service Fabric hizmet yapılandırmasından EventFlow işlem hattının başlatılmasını ve Service Fabric sistem durumu raporları olarak tanılama verileri gönderme sorunlarını rapor etmesini

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` paket, hizmet projesinin .NET Framework 4,6 veya daha yeni bir sürümü hedeflemesini gerektiriyor. Bu paketi yüklemeden önce proje özelliklerinde uygun hedef Framework 'ü ayarladığınızdan emin olun.

Tüm paketler yüklendikten sonra, bir sonraki adım hizmette EventFlow öğesini yapılandırmak ve etkinleştirmek olur.

## <a name="configure-and-enable-log-collection"></a>Günlük toplamayı yapılandırma ve etkinleştirme
Günlükleri gönderdikten sorumlu EventFlow işlem hattı, bir yapılandırma dosyasında depolanan bir belirtiden oluşturulur. `Microsoft.Diagnostics.EventFlow.ServiceFabric`Paket `PackageRoot\Config` , adlı çözüm klasörü altına bir başlangıç EventFlow yapılandırma dosyası yüklüyor `eventFlowConfig.json` . Bu yapılandırma dosyasının, varsayılan hizmet `EventSource` sınıfından verileri, yapılandırmak istediğiniz diğer girişleri ve verileri uygun yere göndermesini sağlamak için değiştirilmesi gerekir.

>[!NOTE]
>Proje dosyanızda VisualStudio 2017 biçimi varsa `eventFlowConfig.json` dosya otomatik olarak eklenmez. Bu işlemi onarmak için, klasörde dosyayı oluşturun `Config` ve derleme eylemini olarak ayarlayın `Copy if newer` . 

Aşağıda belirtilen NuGet paketlerini temel alan örnek bir *eventFlowConfig.js* aşağıda verilmiştir:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Hizmetin ServiceEventSource öğesinin adı, `EventSourceAttribute` serviceeventsource sınıfına uygulanan öğesinin Name özelliğinin değeridir. Bu, `ServiceEventSource.cs` hizmet kodunun bir parçası olan dosyasında belirtilmiştir. Örneğin, aşağıdaki kod parçacığında ServiceEventSource adı *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Bu `eventFlowConfig.json` dosyanın hizmet yapılandırma paketinin bir parçası olduğunu unutmayın. Bu dosyada yapılan değişiklikler, hizmetin tamamen veya yalnızca yapılandırma yükseltmeleri kapsamında, yükseltme hatası varsa yükseltme sistem durumu denetimlerine ve otomatik geri almaya Service Fabric tabidir. Daha fazla bilgi için bkz. [uygulama yükseltme Service Fabric](service-fabric-application-upgrade.md).

Config 'in *Filters* bölümü, çıkışlara EventFlow işlem hattı üzerinden gideceğim bilgileri daha fazla özelleştirmenizi sağlar ve belirli bilgileri bırakıp dahil etmenize ya da olay verilerinin yapısını değiştirmenize izin verir. Filtreleme hakkında daha fazla bilgi için bkz. [EventFlow Filters](https://github.com/Azure/diagnostics-eventflow#filters).

Son adım, hizmetin başlangıç kodunda, dosyasında bulunan EventFlow işlem hattının örneklendirilecek `Program.cs` :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Yönteminin parametresi olarak geçirilen ad, `CreatePipeline` `ServiceFabricDiagnosticsPipelineFactory` EventFlow günlük koleksiyonu ardışık düzenini temsil eden *sistem durumu varlığının* adıdır. Bu ad, EventFlow ile karşılaştığında ve Service Fabric sistem durumu alt sistemi üzerinden raporladığında kullanılır.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>EventFlowConfig içindeki Service Fabric ayarlarını ve uygulama parametrelerini kullanma

EventFlow, EventFlow ayarlarını yapılandırmak için Service Fabric ayarlarını ve uygulama parametrelerini kullanmayı destekler. Değerler için bu özel sözdizimini kullanarak Service Fabric ayarları parametrelerine bakabilirsiniz:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` , Service Fabric yapılandırma bölümünün adıdır ve `<setting-name>` bir EventFlow ayarı yapılandırmak için kullanılacak değeri sağlayan yapılandırma ayarıdır. Bunun nasıl yapılacağı hakkında daha fazla bilgi edinmek için [Service Fabric ayarları ve uygulama parametreleri Için destek](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)bölümüne gidin.

## <a name="verification"></a>Doğrulama

Hizmetinizi başlatın ve Visual Studio 'daki hata ayıklama çıkışı penceresini inceleyin. Hizmet başladıktan sonra, hizmetinizin yapılandırdığınız çıktıya kayıt gönderdiğini kanıtı görmeye başlamanız gerekir. Olay analizinizi ve görselleştirme platformunuza gidin ve günlüklerin gösterilmeye başlatıldığını onaylayın (birkaç dakika sürebilir).

## <a name="next-steps"></a>Sonraki adımlar

* [Application Insights ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Izleyici günlükleri ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow belgeleri](https://github.com/Azure/diagnostics-eventflow)
