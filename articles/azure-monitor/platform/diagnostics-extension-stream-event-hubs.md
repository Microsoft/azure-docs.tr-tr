---
title: Event Hubs veri akışı Azure Tanılama
description: Yaygın senaryolar için rehberlik dahil Event Hubs uçtan uca Azure Tanılama yapılandırma.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/13/2017
ms.openlocfilehash: 2b24618e4d7c12366db5e72226c6f94924d4d3a5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555538"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Azure Tanılama Event Hubs kullanarak etkin yoldaki verileri akışa alma
Azure Tanılama, bulut Hizmetleri sanal makinelerinden (VM 'Ler) ölçümleri ve günlükleri toplamanın yanı sıra sonuçları Azure Storage 'a aktarmaya yönelik esnek yollar sunar. Mart 2016 (SDK 2,9) zaman çerçevesinde, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)kullanarak özel veri kaynaklarına tanılama gönderebilir ve dinamik yol verilerini Saniyeler içinde aktarabilirsiniz.

Desteklenen veri türleri şunlardır:

* Windows için Olay İzleme (ETW) olayları
* Performans sayaçları
* Windows olay günlükleri
* Uygulama günlükleri
* Azure Tanılama altyapısı günlükleri

Bu makalede, Event Hubs uçtan uca Azure Tanılama nasıl yapılandırılacağı gösterilir. Aşağıdaki yaygın senaryolar için de rehberlik sunulmaktadır:

* Event Hubs gönderilen günlükleri ve ölçümleri özelleştirme
* Her ortamda konfigürasyonları değiştirme
* Event Hubs Stream verilerini görüntüleme
* Bağlantı sorunlarını giderme  

## <a name="prerequisites"></a>Önkoşullar
Azure Tanılama Event Hubs veri alma Cloud Services, VM 'Ler, sanal makine ölçek kümeleri ve Azure SDK 2,9 ve Visual Studio için karşılık gelen Azure Araçları 'nda başlayan Service Fabric desteklenir.

* Azure Tanılama uzantısı 1,6 ([.net 2,9 Için Azure SDK veya sonraki sürümler](https://azure.microsoft.com/downloads/) bu varsayılan olarak bunu hedefliyor)
* [Visual Studio 2013 veya üzeri](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Bir *. wadcfgx* dosyası ve aşağıdaki yöntemlerden birini kullanarak bir uygulamada Azure tanılama var olan konfigürasyonlar:
  * Visual Studio: [Azure Cloud Services ve sanal makineler Için tanılamayı yapılandırma](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [PowerShell kullanarak Azure Cloud Services tanılamayı etkinleştirme](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Makale başına sağlanan Event Hubs ad alanı [Event Hubs kullanmaya başlayın](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Azure Tanılama Event Hubs havuzuna bağlama
Azure Tanılama, varsayılan olarak her zaman günlükleri ve ölçümleri bir Azure depolama hesabına gönderir. Bir uygulama, *. wadcfgx* dosyasının **Publicconfig**  / **wadcfg** öğesi altına yeni bir **Havuz** bölümü ekleyerek Event Hubs verileri de gönderebilir. Visual Studio 'da *. wadcfgx* dosyası şu yolda saklanır: **bulut hizmeti projesi**  > **Roller**  >  **(roleName)**  > **Diagnostics. wadcfgx** dosyası.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

Bu örnekte, Olay Hub 'ı URL 'SI Olay Hub 'ının tam ad alanı olarak ayarlanır: Event Hubs ad alanı + "/" + Olay Hub 'ı adı.  

Olay Hub 'ı URL 'SI, Event Hubs panosundaki [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) görüntülenir.  

Aynı değer yapılandırma dosyası boyunca tutarlı olarak kullanıldığı sürece **Havuz** adı herhangi bir geçerli dizeye ayarlanabilir.

> [!NOTE]
> Bu bölümde, *ApplicationInsights* gibi ek havuzlar olabilir. Azure Tanılama, her havuz aynı zamanda **Privateconfig** bölümünde bildirilirse bir veya daha fazla havuzun tanımlanmasını sağlar.  
>
>

Event Hubs havuz Ayrıca *. wadcfgx* yapılandırma dosyasının **privateconfig** bölümünde bildirilmelidir ve tanımlanmalıdır.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

@No__t_0 değeri, **Event Hubs** ad alanında tanımlanan bir paylaşılan erişim IMZASı (SAS) anahtarı ve ilkesiyle eşleşmelidir. [Azure portal](https://portal.azure.com)Event Hubs panosuna gidin, **Yapılandır** sekmesine tıklayın ve *gönderme* izinleri olan adlandırılmış bir Ilke (örneğin, "sendrule") ayarlayın. **Storageaccount** , **privateconfig**içinde de bildirilmiştir. Bu değerler çalışıyorsa burada değişiklik yapmanız gerekmez. Bu örnekte, bir aşağı akış varlığının değerlerini ayarlayacağı bir işaret olan bir işaret olan değerler boş bırakıyoruz. Örneğin, *ServiceConfiguration. Cloud. cscfg* ortam yapılandırma dosyası, ortama uygun adları ve anahtarları ayarlar.  

> [!WARNING]
> Event Hubs SAS anahtarı, *. wadcfgx* dosyasında düz metin olarak depolanır. Bu anahtar genellikle kaynak kodu denetimine iade edilir veya yapı sunucunuzdaki bir varlık olarak kullanılabilir, bu nedenle uygun şekilde korumanız gerekir. Kötü amaçlı bir kullanıcının Olay Hub 'ına yazabilmesi, ancak dinleyememesi veya yönetebilmesi için *yalnızca Send* izinleri Ile bir SAS anahtarı kullanmanızı öneririz.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Günlükleri ve ölçümleri Event Hubs göndermek için Azure Tanılama yapılandırın
Daha önce anlatıldığı gibi, tüm varsayılan ve özel tanılama verileri, diğer bir deyişle, ölçümler ve Günlükler otomatik olarak yapılandırılmış aralıklarda Azure depolama 'ya gönderilir. Event Hubs ve ek havuzlarla, hiyerarşide Olay Hub 'ına gönderilmek üzere herhangi bir kök veya yaprak düğüm belirtebilirsiniz. Bu, ETW olayları, performans sayaçları, Windows olay günlükleri ve uygulama günlükleri içerir.   

Kaç veri noktasının gerçekten Event Hubs aktarılmasının gerektiği göz önünde bulundurmanız önemlidir. Genellikle, geliştiriciler hızlı bir şekilde tüketilmesi ve yorumlanması gereken düşük gecikmeli etkin yol verilerini aktarır. Uyarıları veya otomatik ölçeklendirme kurallarını izleyen sistemler örnektir. Geliştirici aynı zamanda alternatif bir analiz deposu veya arama deposu da yapılandırabilir; Örneğin, Azure Stream Analytics, Elana Search, özel bir izleme sistemi veya başkalarından sık kullanılan bir izleme sistemi.

Aşağıda bazı örnek yapılandırmalardan bazıları verilmiştir.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

Yukarıdaki örnekte havuz, hiyerarşide üst **PerformanceCounters** düğümüne uygulanır, yani tüm alt **PerformanceCounters** Event Hubs gönderilir.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

Önceki örnekte, havuz yalnızca üç sayaca uygulanır: **sıraya alınan istekler**, **Reddedilen Istekler**ve **% işlemci zamanı**.  

Aşağıdaki örnek, bir geliştiricinin gönderilen veri miktarını bu hizmetin sistem durumu için kullanılan kritik ölçümler olacak şekilde nasıl sınırlayamayacağını gösterir.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

Bu örnekte, havuz günlüklere uygulanır ve yalnızca hata düzeyi izleme ile filtrelenmiştir.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Cloud Services uygulama ve tanılama yapılandırmasını dağıtma ve güncelleştirme
Visual Studio, uygulama ve Event Hubs havuz yapılandırmasını dağıtmak için en kolay yolu sağlar. Dosyayı görüntülemek ve düzenlemek için, Visual Studio 'da *. wadcfgx* dosyasını açın, düzenleyin ve kaydedin. Yol, **bulut hizmeti projesi** ** >   > ** **(roleName)**  > **Diagnostic. wadcfgx**' dir.  

Bu noktada, Visual Studio, Visual Studio Team System ve MSBuild 'i temel alan tüm dağıtım ve dağıtım güncelleştirme eylemleri ve **/t: Publish** hedefini kullanan tüm komutlar veya betikler Paketleme sürecinde *. wadcfgx* içerir. Ayrıca, dağıtımlar ve güncelleştirmeler, sanal makinelerinizdeki uygun Azure Tanılama Aracısı uzantısını kullanarak dosyayı Azure 'a dağıtır.

Uygulamayı ve Azure Tanılama yapılandırmayı dağıttıktan sonra etkinlik, Olay Hub 'ının panosunda hemen görüntülenir. Bu, seçtiğiniz dinleyici istemcisi veya çözümleme aracında etkin yol verilerini görüntülemeye devam etmek için hazırsınız demektir.  

Aşağıdaki şekilde Event Hubs panosu, 11 PM sonrasında bir süre sonra tanılama verilerinin Olay Hub 'ına sağlıklı bir şekilde gönderilmesini gösterir. Bu, uygulama güncelleştirilmiş bir *. wadcfgx* dosyasıyla dağıtıldığında ve havuz düzgün şekilde yapılandırıldığında.

![][0]  

> [!NOTE]
> Azure Tanılama yapılandırma dosyasında (. wadcfgx) güncelleştirmeler yaptığınızda, güncelleştirmeleri Visual Studio yayımlaması veya bir Windows PowerShell komut dosyası kullanarak tüm uygulamaya ve yapılandırmaya de göndermeniz önerilir.  
>
>

## <a name="view-hot-path-data"></a>Etkin yol verilerini görüntüleme
Daha önce anlatıldığı gibi Event Hubs verileri dinlemek ve işlemek için birçok kullanım durumu vardır.

Basit bir yaklaşım, Olay Hub 'ını dinlemek ve çıkış akışını yazdırmak için küçük bir test konsolu uygulaması oluşturmaktır. Konsol uygulamasındaki [Event Hubs kullanmaya başlama](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)bölümünde daha ayrıntılı olarak açıklanan aşağıdaki kodu yerleştirebilirsiniz.  

Konsol uygulamasının [olay Işlemcisi Konağı NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)içermesi gerektiğini unutmayın.  

**Main** işlevindeki açılı parantez içindeki değerleri kaynaklarınızın değerleriyle değiştirmeyi unutmayın.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs havuzları sorunlarını giderme
* Olay Hub 'ı, gelen veya giden olay etkinliğini beklenen şekilde göstermiyor.

    Olay Hub 'ınızın başarıyla sağlandığını denetleyin. *. Wadcfgx* öğesinin **privateconfig** bölümündeki tüm bağlantı bilgileri, portalda görüldüğü gibi kaynağınızın değerleriyle eşleşmelidir. Portalda bir SAS ilkesinin (örnekte "SendRule") tanımlanmış olduğundan ve *gönderme* izninin verildiğinden emin olun.  
* Bir güncelleştirmeden sonra, Olay Hub 'ı artık gelen veya giden olay etkinliğini göstermez.

    İlk olarak, Olay Hub 'ı ve yapılandırma bilgilerinin daha önce anlatıldığı gibi doğru olduğundan emin olun. Bazen bir dağıtım güncelleştirmesinde **Privateconfig** sıfırlanır. Önerilen çözüm, projenin *. wadcfgx* dosyasında tüm değişiklikleri yapmak ve sonra da bir uygulama güncelleştirmesini göndermedir. Bu mümkün değilse, tanılama güncelleştirmesinin SAS anahtarını içeren bir bütün **Privateconfig** 'e iter olduğundan emin olun.  
* Önerilere çalıştım ve Olay Hub 'ı hala çalışmıyor.

    Azure Tanılama kendisi için Günlükler ve hatalar içeren Azure Storage tablosuna bakmayı deneyin: **WADDiagnosticInfrastructureLogsTable**. Tek bir seçenek, bu depolama hesabına bağlanmak, bu tabloyu görüntülemek ve son 24 saat içinde zaman damgasına bir sorgu eklemek için [Azure Depolama Gezgini](https://www.storageexplorer.com) gibi bir araç kullanmaktır. Aracı, bir. csv dosyasını dışarı aktarmak ve Microsoft Excel gibi bir uygulamada açmak için kullanabilirsiniz. Excel, hangi hatanın raporlandığını görmek için **Eventhubs**gibi arama kartı dizelerini aramanızı kolaylaştırır.  

## <a name="next-steps"></a>Sonraki adımlar
• [Event Hubs hakkında daha fazla bilgi edinin](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Ek: Azure Tanılama yapılandırma dosyası (. wadcfgx) örneği
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Bu örnek için tamamlayıcı *ServiceConfiguration. Cloud. cscfg* aşağıdaki gibi görünür.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Sanal makineler için eşdeğer JSON ayarları aşağıdaki gibidir:

Genel ayarlar:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Korumalı ayarlar:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış](../../event-hubs/event-hubs-about.md)
* [Olay Hub’ı oluşturma](../../event-hubs/event-hubs-create.md)
* [Event Hubs ile ilgili SSS](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

