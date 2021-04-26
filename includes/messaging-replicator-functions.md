---
title: include dosyası
description: include dosyası
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901259"
---
## <a name="what-is-a-replication-task"></a>Çoğaltma görevi nedir?

Çoğaltma görevi bir kaynaktan olayları alır ve bunları bir hedefe iletir.
Çoğu çoğaltma görevi olayları değiştirmeden iletir ve kaynak ve hedef protokoller farklıysa meta veri yapıları arasında en çok eşleme gerçekleştirir. 

Çoğaltma görevleri genellikle durumsuz, yani bir görevin sıralı veya paralel yürütmeleri arasında durum veya diğer yan etkileri paylaşmazlar. Bu, her ikisi de bir akışın mevcut durumunun üstüne uygulanabilecek toplu işlem ve zincirleme için de geçerlidir. 

Bu, çoğaltma görevlerinin genellikle durum bilgisi olan toplama görevlerinden farklı olmasını sağlar ve [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md)gibi analiz çerçeveleri ve Hizmetleri etki alanıdır.

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Işlevleri 'nde çoğaltma uygulamaları ve görevler

Azure Işlevleri 'nde, bir çoğaltma görevi, yapılandırılmış bir kaynaktan bir veya daha fazla giriş iletisini alan bir [tetikleyici](../articles/azure-functions/functions-triggers-bindings.md) kullanılarak uygulanır ve kaynaktan yapılandırılan bir hedefe kopyalanmış iletileri ileten bir [Çıkış bağlamadır](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) . 

| Tetikleyici  | Çıktı |
|----------|--------|
| [Azure Event Hubs tetikleyicisi](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure Olay Hub 'ları çıkış bağlama](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus tetikleyicisi](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Çıkış bağlamayı Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT Hub tetikleyicisi](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT Hub çıkış bağlama](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure Event Grid tetikleyicisi](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Çıkış bağlamayı Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure Kuyruk Depolama tetikleyicisi](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure kuyruk depolama çıkışı bağlama](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka tetikleyicisi](https://github.com/azure/azure-functions-kafka-extension) | [Çıkış bağlamayı Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Oybbitmq tetikleyicisi](https://github.com/azure/azure-functions-rabbitmq-extension) | [Kbbitmq çıkış bağlama](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure Notification Hubs çıkış bağlama](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Azure SignalR hizmeti çıkış bağlama](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid çıkış bağlaması](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

Çoğaltma görevleri, diğer Azure Işlevleri uygulamasıyla aynı dağıtım yöntemleriyle çoğaltma uygulamasına olarak dağıtılır. Aynı uygulama için birden çok görev yapılandırabilirsiniz. 

Azure Işlevleri Premium ile birden çok çoğaltma uygulaması, App Service plan olarak adlandırılan aynı temel kaynak havuzunu paylaşabilir. Diğer bir deyişle, .NET içinde yazılmış çoğaltma görevlerini Java 'da, örneğin, Java 'da yazılmış çoğaltma görevleriyle kolayca bulabilirsiniz. Bu, yalnızca Java için kullanılabilir olan ve belirli bir tümleştirme yolu için en iyi seçenek olan Apache Camel gibi belirli kitaplıkların avantajlarından yararlanmak istediğinizde, diğer yineleme görevleri için genellikle farklı bir dil ve çalışma zamanı tercih edebilse bile, bu önemlidir. 

Her seferinde, tek tek olaylar veya iletiler sunan Tetikleyiciler üzerinde toplu işlem odaklı Tetikleyicileri tercih etmelisiniz ve Azure Işlevinin [parametre bağlama ifadelerine](../articles/azure-functions/functions-bindings-expressions-patterns.md)güventense, her zaman tam olay veya ileti yapısını edinmeniz gerekir.

İşlevin adı, bağlanmakta olduğunuz kaynak ve hedefin çiftini yansıtmalıdır ve bu ada sahip uygulama yapılandırma dosyalarındaki bağlantı dizelerine veya diğer yapılandırma öğelerine başvuru öneki uygulamanız gerekir. 

### <a name="data-and-metadata-mapping"></a>Veri ve meta veri eşleme

Giriş tetikleyicisi ve çıkış bağlamasının bir çiftiyle karar verdikten sonra, tetikleyicinizin türü ve çıktı aynı değilse farklı olay veya ileti türleri arasında bazı eşleme yapmanız gerekir.

Event Hubs ile Service Bus arasında ileti oluşturan basit çoğaltma görevleri için kendi kodunuzu yazmanız gerekmez, ancak çoğaltma örnekleri ile [sağlanan bir yardımcı program kitaplığını](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) de kullanabilirsiniz.

### <a name="retry-policy"></a>Yeniden Deneme ilkesi

Bir çoğaltma işlevinin her iki tarafında da kullanılabilirlik olayı sırasında veri kaybını önlemek için yeniden deneme ilkesini sağlam olacak şekilde yapılandırmanız gerekir. Yeniden deneme ilkesini yapılandırmak için [yeniden denemeler hakkında Azure işlevleri belgelerine](../articles/azure-functions/functions-bindings-error-pages.md) bakın. 

[Örnek depodaki](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) örnek projeler için seçilen ilke ayarları, yeniden deneme aralıklarıyla 5 saniyeden 15 dakikaya kadar olan ve veri kaybını önlemek için sonsuz yeniden denemeler içeren bir üstel geri alma stratejisi yapılandırır. 

Service Bus için, tetikleyicilerin etkileşimini ve sıra için tanımlanan maksimum teslim sayısını anlamak için ["tetikleyici esnekliği üzerinde yeniden deneme desteği kullanma"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) bölümünü gözden geçirin.

### <a name="setting-up-a-replication-application-host"></a>Çoğaltma uygulama konağını ayarlama

Bir çoğaltma uygulaması, bir veya daha fazla çoğaltma görevi için bir yürütme konağından biridir. 

Bu, bir Azure Işlevleri Premium planında tüketim planında veya (önerilen) çalışacak şekilde yapılandırılmış bir Azure Işlevleri uygulamasıdır. Tüm çoğaltma uygulamalarının, [sistem veya Kullanıcı tarafından atanan yönetilen kimlik](../articles/app-service/overview-managed-identity.md)altında çalışması gerekir. 

Bağlı Azure Resource Manager (ARM) şablonları, ile bir çoğaltma uygulaması oluşturur ve yapılandırır:

* çoğaltma ilerlemesini ve günlükleri izlemek için bir Azure depolama hesabı,
* sistem tarafından atanan yönetilen kimlik ve 
* İzleme için Azure Izleme ve Application Insights tümleştirme.

Bir Azure sanal ağına (VNet) bağlanan Event Hubs erişmesi gereken çoğaltma uygulamaları, Azure Işlevleri Premium planını kullanmalıdır ve ayrıca kullanılabilir seçeneklerden biri olan aynı VNet 'e iliştirilecek şekilde yapılandırılmalıdır.


|       | Dağıtma | Görselleştirme  
|-------|------------------|--------------|---------------|
| **Azure Işlevleri tüketim planı** | [![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Görselleştirme](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Işlevleri Premium planı** |[![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Görselleştirme](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **VNet ile Azure Işlevleri Premium planı** | [![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Görselleştirme](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Örnekler

[Örnek deposu](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) , olayları Event Hubs ve/veya Service Bus varlıkları arasında kopyalamak için birkaç çoğaltma görevi örneği içerir.

Event Hubs arasında olay kopyalama için, Olay Hub 'ı çıkış bağlaması ile bir olay hub 'ı tetikleyicisi kullanın:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Service Bus varlıkları arasında ileti kopyalamak için, Service Bus tetikleyiciyi ve çıkış bağlamasını kullanın:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Yardımcı yöntemler Event Hubs ve Service Bus arasında çoğaltmayı kolay hale getirebilirsiniz:

| Kaynak      | Hedef      | Girdi Noktası 
|-------------|-------------|------------------------------------------------------------------------
| Olay Hub'ı   | Olay Hub'ı   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Olay Hub'ı   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Olay Hub'ı   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>İzleme

Çoğaltma uygulamanızı nasıl izleyebileceğinizi öğrenmek için lütfen Azure Işlevleri belgelerinin [izleme bölümüne](../articles/azure-functions/configure-monitoring.md) bakın.

Çoğaltma görevlerinin izlenmesi için özellikle faydalı bir görsel araç, yakalanan izleme bilgileri tarafından otomatik olarak oluşturulan ve çoğaltma görevi kaynağı ve hedef aktarımlarının güvenilirliğini ve performansını keşfetmeye izin veren Application Insights [uygulama haritadır](../articles/azure-monitor/app/app-map.md).

Anında tanılama öngörüleri için, günlük ayrıntılarının düşük gecikme süreli görselleştirmesini sağlayan [canlı ölçümler](../articles/azure-monitor/app/live-stream.md) Portal aracıyla çalışabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Işlevleri dağıtımları](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Işlevleri tanılaması](../articles/azure-functions/functions-diagnostics.md)
* [Azure Işlevleri ağ seçenekleri](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)