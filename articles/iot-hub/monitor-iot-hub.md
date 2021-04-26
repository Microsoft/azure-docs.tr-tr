---
title: Azure IoT Hub izleme
description: Azure IoT Hub izlemeyi öğrenmek için Buradan başlayın
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: c9e6107e981d2bae96c5d9f368c8122d3ce245f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570947"
---
# <a name="monitoring-azure-iot-hub"></a>Azure IoT Hub izleme

Azure kaynaklarına bağlı kritik Uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Azure IoT Hub tarafından oluşturulan izleme verileri ve bu verileri çözümlemek ve uyarmak için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanır.

## <a name="monitor-overview"></a>İzlemeye genel bakış

Her bir IoT Hub 'ı için Azure portal **genel bakış** sayfasında, kullanılan ileti sayısı ve IoT Hub 'ına bağlı cihazların sayısı gibi bazı kullanım ölçümleri sağlayan grafikler bulunur.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT Hub 'a Genel Bakış sayfasında varsayılan ölçüm grafikleri.":::

İleti sayısı değerinin 1 dakika geciktirilebildiği ve bu nedenle IoT Hub hizmet altyapısında olması gereken nedenlerden dolayı değer bazen yenileme sırasında daha yüksek ve daha düşük değerler arasında sıçramaya neden olabilir. Bu sayaç yalnızca geçen dakika boyunca tahakkuk edilen değerler için yanlış olmalıdır.

Genel Bakış bölmesinde sunulan bilgiler kullanışlıdır, ancak bir IoT Hub 'ı için kullanılabilen izleme verilerinin yalnızca küçük bir miktarını temsil eder. Bazı izleme verileri otomatik olarak toplanır ve IoT Hub 'ınızı oluşturandan hemen sonra analiz için kullanılabilir. Bazı yapılandırma ile ek veri koleksiyonu türlerini etkinleştirebilirsiniz.

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?

Azure IoT Hub, Azure 'da, diğer bulutlardaki ve Şirket içindeki kaynaklara ek olarak Azure kaynaklarınızı izlemeye yönelik eksiksiz bir özellik kümesi sunan [Azure izleyici](../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur.

Aşağıdaki kavramları açıklayan Azure [izleyici Ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md)makalesini başlatın:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

Aşağıdaki bölümler, Azure IoT Hub için toplanan belirli verileri açıklayarak ve veri toplamayı yapılandırmaya ve bu verileri Azure araçlarıyla analiz etmeye yönelik örnekler sunarak bu makaleye yöneliktir.

## <a name="monitoring-data"></a>Verileri izleme

Azure IoT Hub, [Azure kaynaklarından gelen verileri izleme](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar.

Azure IoT Hub tarafından oluşturulan ölçümler ve Günlükler hakkında ayrıntılı bilgi için bkz. [azure IoT Hub veri başvurusunu izleme](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> Azure Izleyici kaynak günlüklerini kullanarak IoT Hub hizmeti tarafından yayılan olayların güvenilir veya sıralı olması garanti edilmez. Bazı olaylar kaybolmuş veya sıra dışında teslim edilebilir. Kaynak günlükleri de gerçek zamanlı olarak değildir ve olayların seçtiğiniz hedefe eklenmesi birkaç dakika sürebilir.

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

Platform ölçümleri ve etkinlik günlüğü otomatik olarak toplanır ve depolanır, ancak bir tanılama ayarı kullanılarak başka konumlara yönlendirilebilir.

Kaynak günlükleri, bir tanılama ayarı oluşturup bunları bir veya daha fazla konuma yönlendirene kadar toplanmaz ve depolanmaz.

Ölçümler ve Günlükler aşağıdakiler dahil olmak üzere çeşitli konumlara yönlendirilebilir:
- Azure Izleyici, ilişkili bir Log Analytics çalışma alanı aracılığıyla depolar. Log Analytics kullanılarak çözümlenebilirler.
- Arşivleme ve çevrimdışı analiz için Azure depolama 
- Dış uygulamalar tarafından okunabilecek bir Event Hubs uç noktası (örneğin, üçüncü taraf SıEM araçları).

Azure portal, IoT Hub 'ınızın sol bölmesindeki **izleme** altında **Tanılama ayarları** ' nı ve ardından IoT Hub 'ınız tarafından yayınlanan Günlükler ve platform ölçümleri kapsamındaki tanılama ayarlarını oluşturmak için **Tanılama ayarı Ekle** ' yi seçebilirsiniz.

Aşağıdaki ekran görüntüsünde, kaynak günlüğü türü *bağlantı işlemlerini* ve tüm platform ölçümlerini bir Log Analytics çalışma alanına yönlendirmeye yönelik bir tanılama ayarı gösterilmektedir.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT Hub 'ı için Tanılama ayarları bölmesi.":::

Azure portal, CLı veya PowerShell kullanarak bir tanılama ayarı oluşturmaya yönelik ayrıntılı süreç için [Azure 'da platform günlüklerini ve ölçümlerini toplamak üzere tanılama ayarı oluşturma](../azure-monitor/essentials/diagnostic-settings.md) konusuna bakın. Bir tanılama ayarı oluşturduğunuzda hangi günlük kategorilerinin toplanacağını belirlersiniz. Azure IoT Hub kategorileri [Izleme azure IoT Hub veri başvurusunda kaynak günlükleri](monitor-iot-hub-reference.md#resource-logs)altında listelenmiştir.

IoT Hub platform ölçümlerini diğer konumlara yönlendirçalışırken şunu göz önünde bulundurun:

- Aşağıdaki platform ölçümleri, Tanılama ayarları aracılığıyla dışarı aktarılabilir değildir: *bağlı cihazlar (Önizleme)* ve *Toplam cihaz (Önizleme)*.

- Çok boyutlu ölçümler, örneğin bazı [yönlendirme ölçümleri](monitor-iot-hub-reference.md#routing-metrics)Şu anda boyut değerleri arasında toplanan düzleştirilmiş tek boyutlu ölçümler olarak içeri aktarılmaktadır. Daha fazla ayrıntı için bkz. [Platform ölçümlerini diğer konumlara dışarı aktarma](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

Azure **izleyici** menüsünden **ölçümler** ' i açarak ölçüm gezgini 'ni kullanarak Azure IoT Hub ölçümlerini, diğer Azure hizmetlerinden alınan ölçümlerle çözümleyebilirsiniz. Bu aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](../azure-monitor/essentials/metrics-getting-started.md) kullanmaya başlama.

Azure portal, IoT Hub 'ınızın sol bölmesindeki **izleme** altında bulunan **ölçümler** ' i seçerek, varsayılan olarak, IoT Hub 'ınız tarafından yayılan platform ölçümlerine Ölçüm Gezgini kapsamı ' nı açabilirsiniz:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT Hub 'ı için ölçüm Gezgini sayfası.":::

Azure IoT Hub için toplanan platform ölçümlerinin bir listesi için bkz. [azure IoT Hub veri başvurusu 'Ndaki ölçümler](monitor-iot-hub-reference.md#metrics). Tüm Azure hizmetleri için toplanan platform ölçümlerinin bir listesi için bkz. [Azure izleyici Ile desteklenen ölçümler](../azure-monitor/essentials/metrics-supported.md).

Sayı birimlerinde toplanan IoT Hub platform ölçümleri için bazı toplamalar kullanılamayabilir veya kullanılamıyor olabilir. Daha fazla bilgi edinmek için bkz. [Azure IoT Hub veri başvurusunda desteklenen toplamalar](monitor-iot-hub-reference.md#supported-aggregations).

[Yönlendirme ölçümleri](monitor-iot-hub-reference.md#routing-metrics)gibi bazı IoT Hub ölçümleri çok boyutlu. Bu ölçümler için, [filtre](../azure-monitor/essentials/metrics-charts.md#filters) uygulayabilir ve bir boyuta göre grafiklerinize [bölme](../azure-monitor/essentials/metrics-charts.md#apply-splitting) uygulayabilirsiniz.

## <a name="analyzing-logs"></a>Günlükler çözümleniyor

Azure Izleyici günlüklerindeki veriler, her tablonun kendine ait benzersiz özellikler kümesine sahip olduğu tablolarda depolanır. Bu tablolardaki veriler bir Log Analytics çalışma alanıyla ilişkilendirilir ve Log Analytics sorgulanabilir. Azure Izleyici günlükleri hakkında daha fazla bilgi edinmek için Azure izleyici belgelerinde [Azure Izleyici günlüklerine genel bakış](../azure-monitor/logs/data-platform-logs.md) konusuna bakın. 

Verileri Azure Izleyici günlüklerine yönlendirmek için bir Log Analytics çalışma alanına kaynak günlükleri veya platform ölçümleri göndermek üzere bir tanılama ayarı oluşturmanız gerekir. Daha fazla bilgi için bkz. [koleksiyon ve yönlendirme](#collection-and-routing).

Azure portal, IoT Hub 'ınız için Azure Izleyici günlüklerinde toplanan günlüklere ve ölçümlere varsayılan olarak, kapsamdaki Log Analytics sorguları gerçekleştirmek üzere IoT Hub 'ınızın sol bölmesindeki **izleme** altında bulunan **Günlükler** ' i seçebilirsiniz.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT Hub 'ı için Günlükler sayfası.":::

Azure Izleyici günlükleri tarafından kullanılan tablolar ve Log Analytics tarafından sorgulanabilir bir liste için, [Izleme azure IoT Hub veri başvurusunda Azure Izleyici günlükleri tabloları](monitor-iot-hub-reference.md#azure-monitor-logs-tables)bölümüne bakın.

Azure Izleyici 'deki tüm kaynak günlüklerine aynı alanlar ve hizmete özgü alanlar gelir. Ortak şema, [Azure izleyici kaynak günlüğü şemasında](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)özetlenmiştir. Azure IoT Hub için toplanan kaynak günlüklerinin şemasını ve kategorilerini, [Izleme azure IoT Hub veri başvurusu 'Nda kaynak günlüklerinde](monitor-iot-hub-reference.md#resource-logs)bulabilirsiniz.

Etkinlik günlüğü, Azure 'da abonelik düzeyindeki olaylara ilişkin Öngörüler sağlayan bir platform [Günlüğliğidir](../azure-monitor/essentials/activity-log.md) . Bunu bağımsız olarak görüntüleyebilir veya Azure Izleyici günlüklerine yönlendirebilirsiniz, burada Log Analytics kullanarak çok daha karmaşık sorgular yapabilirsiniz.  

IoT Hub platform ölçümlerini Azure Izleyici günlüklerine yönlendirçalışırken şunu göz önünde bulundurun:

- Aşağıdaki platform ölçümleri, Tanılama ayarları aracılığıyla dışarı aktarılabilir değildir: *bağlı cihazlar (Önizleme)* ve *Toplam cihaz (Önizleme)*.

- Çok boyutlu ölçümler, örneğin bazı [yönlendirme ölçümleri](monitor-iot-hub-reference.md#routing-metrics)Şu anda boyut değerleri arasında toplanan düzleştirilmiş tek boyutlu ölçümler olarak içeri aktarılmaktadır. Daha fazla ayrıntı için bkz. [Platform ölçümlerini diğer konumlara dışarı aktarma](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

IoT Hub olan bazı ortak sorgular için bkz. [Sample kusto Queries](#sample-kusto-queries). Log Analytics sorguları kullanma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>IoT Hub günlüklerinde SDK sürümü

IoT Hub kaynak günlüklerindeki bazı işlemler `sdkVersion` , nesneleri için bir özellik döndürür `properties` . Bu işlemler için, bir cihaz veya arka uç uygulaması Azure IoT SDK 'Lardan birini kullanırken, bu özellik kullanılan SDK, SDK sürümü ve SDK 'nın çalıştığı platform hakkındaki bilgileri içerir. Aşağıdaki örnek, `sdkVersion` [`deviceConnect`](monitor-iot-hub-reference.md#connections) Node.js cihaz SDK 'sı kullanılırken bir işlem için yayılan özelliği gösterir: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . .NET (C#) SDK 'Sı için yayılan değere bir örnek aşağıda verilmiştir: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

Aşağıdaki tabloda farklı Azure IoT SDK 'Ları için kullanılan SDK adı gösterilmektedir:

| SdkVersion özelliğinde SDK adı | Dil |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | .NET (C#) hizmeti SDK 'Sı |
| Microsoft. Azure. Devices. Client | .NET (C#) cihaz SDK 'Sı |
| ıothubclient | C veya Python v1 (kullanım dışı) cihaz SDK 'Sı |
| iothubserviceclient | C veya Python v1 (kullanım dışı) hizmet SDK 'Sı |
| Azure-IoT-Device-ıothub-Kopyala | Python cihaz SDK 'Sı |
| azure-iot-device | Node.js cihaz SDK 'Sı |
| azure-iothub | Node.js hizmeti SDK 'Sı |
| com. Microsoft. Azure. ıothub-Java-Client | Java cihaz SDK 'Sı |
| com. Microsoft. Azure. ıothub. Service. SDK | Java hizmeti SDK 'Sı |
| com. Microsoft. Azure. SDK. IoT. IoT-Device-Client | Java cihaz SDK 'Sı |
| com. Microsoft. Azure. SDK. IoT. IoT-Service-Client | Java hizmeti SDK 'Sı |
| C | Gömülü C |
| C + (Osbasitleştirilmiş = Azure RTOS) | Azure RTOS |

IoT Hub kaynak günlüklerinde sorgu gerçekleştirdiğinizde SDK sürüm özelliğini ayıklayabilirsiniz. Örneğin, aşağıdaki sorgu, bağlantı işlemleri tarafından döndürülen özelliklerden SDK sürüm özelliğini (ve cihaz KIMLIĞI) ayıklar. Bu iki özellik, işlemin süresi ve cihazın bağlandığı IoT Hub 'ının kaynak KIMLIĞI ile birlikte sonuçlara yazılır.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

> [!IMPORTANT]
> IoT Hub menüsünden **Günlükler** ' i seçtiğinizde Log Analytics, sorgu kapsamı geçerli IoT Hub 'ına ayarlanmış olarak açılır. Bu, günlük sorgularının yalnızca bu kaynaktaki verileri dahil olacağı anlamına gelir. Diğer IoT Hub 'larından veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](../azure-monitor/logs/scope.md) .

IoT Hub 'ınızı izlemenize yardımcı olması için kullanabileceğiniz sorgular aşağıda verilmiştir.

- Bağlantı hataları: cihaz bağlantı hatalarını tanımla.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Azaltma hataları: en çok istek yapan cihazları, azaltma hatalarına neden olacak şekilde belirler.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Atılacak uç noktalar: ölü ve sağlıksız uç noktaları, sorunun bildirildiği sayı ve neden nedenini belirler.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Hata Özeti: türe göre tüm işlemler arasındaki hataların sayısı.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Son bağlı cihazlar: IoT Hub gördük cihazların listesi, belirtilen dönemde bağlanır.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Cihazların SDK sürümü: Cihaz bağlantıları ya da cihaz için SDK sürümleri ve bulut ikizi işlemlerine cihazların listesi.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs günlüklerini okuyun

Tanılama ayarları aracılığıyla olay günlüğü ayarladıktan sonra, içerdikleri bilgilere göre işlem yapabilmeniz için günlükleri okuyan uygulamalar oluşturabilirsiniz. Bu örnek kod, bir olay hub 'ından günlükleri alır:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Uyarılar

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, müşterilerinizin sorunları fark etmeden önce sisteminizdeki sorunları tanımlamanızı ve ele belirlemenizi sağlar. [Ölçümler](../azure-monitor/alerts/alerts-metric-overview.md), [Günlükler](../azure-monitor/alerts/alerts-unified-log.md)ve [etkinlik günlüğü](../azure-monitor/alerts/activity-log-alerts.md)hakkında uyarı ayarlayabilirsiniz. Farklı uyarı türlerinin avantajları ve dezavantajları vardır.

Platform ölçümlerini temel alan bir uyarı kuralı oluştururken, sayı birimlerinde toplanan IoT Hub platform ölçümleri için bazı toplamaların kullanılamayabilir veya kullanılamıyor olabileceğini unutmayın. Daha fazla bilgi edinmek için bkz. [Azure IoT Hub veri başvurusunda desteklenen toplamalar](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Cihaz başına bağlantı kesildiğinde izleme Event Grid

Azure Izleyici, IoT Hub bağlı cihazların sayısını izlemek için kullanabileceğiniz ve bağlı cihazların sayısı bir eşik değerin altına düştüğünde bir uyarı tetikleyebileceğiniz bir ölçüm, *bağlı cihazlar* sağlar. Bu işlem bazı senaryolar için yeterli olabileceğinden, [Azure Event Grid](../event-grid/index.yml) kritik cihazlar ve altyapıya yönelik cihaz bağlantılarını izlemek için kullanabileceğiniz düşük gecikmeli, cihaz başına izleme çözümü sağlar.

Event Grid ile, uyarıları tetiklemek ve cihaz bağlantı durumunu izlemek için IoT Hub [ **DeviceConnected** ve **DeviceConnected** olayına](iot-hub-event-grid.md#event-types) abone olabilirsiniz. Event Grid, Azure Izleyici 'den çok daha düşük olay gecikmesi sağlar ve toplam bağlı cihaz sayısı yerine cihaz başına temelinde izleyebilirsiniz. Bu faktörler, kritik cihazlar ve altyapılara yönelik bağlantıları izlemek için tercih edilen yöntemi Event Grid yapar. Üretim ortamlarında cihaz bağlantılarını izlemek için Event Grid kullanmanızı kesinlikle öneririz.

Event Grid ve Azure Izleyici ile cihaz bağlantılarını izleme hakkında daha ayrıntılı bilgi için bkz. [azure IoT Hub ile kesilen izleyici, tanılama ve sorun giderme](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Sonraki adımlar

- Ölçümler, Günlükler ve [hizmet adı] tarafından oluşturulan diğer önemli değerlerin bir başvurusu için bkz. [Azure IoT Hub veri başvurusunu izleme](monitor-iot-hub-reference.md) .

- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md) .
