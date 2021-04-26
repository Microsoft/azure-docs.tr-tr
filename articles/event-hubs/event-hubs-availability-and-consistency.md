---
title: Kullanılabilirlik ve tutarlılık-Azure Event Hubs | Microsoft Docs
description: Bölümler kullanılarak Azure Event Hubs maksimum kullanılabilirlik ve tutarlılık sağlama.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952873"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs’da kullanılabilirlik ve tutarlılık
Bu makalede, Azure Event Hubs tarafından desteklenen kullanılabilirlik ve tutarlılık hakkında bilgi sağlanır. 

## <a name="availability"></a>Kullanılabilirlik
Azure Event Hubs, bir veri merkezinde birden çok hata etki alanına yayılan kümeler genelinde tek tek makinelerin veya hatta tamamen oluşan tüm hatalardan oluşan ciddi arızaların riskini yayar. Hizmetin, garanti edilen hizmet düzeylerinde çalışmaya devam edebilmesi ve genellikle bu tür bir hata oluştuğunda dikkat çekici kesintiler olmadan, saydam hata algılama ve yük devretme mekanizmaları uygular. 

[Kullanılabilirlik alanları](../availability-zones/az-overview.md) etkin olan bir Event Hubs ad alanı oluşturulduysa, kesinti riski üç fiziksel olarak ayrılmış tesislere göre daha fazla yayılır ve hizmette, tüm tesis için eksiksiz ve çok zararlı bir kayıpla hemen başa çıkabilecek kapasite ayırmalar vardır. Daha fazla bilgi için bkz. [Azure Event Hubs-coğrafi olağanüstü durum kurtarma](event-hubs-geo-dr.md).

Bir istemci uygulaması, bir bölümü belirtmeden olayları bir olay hub 'ına gönderdiğinde, olaylar Olay Hub 'ınızdaki bölümler arasında otomatik olarak dağıtılır. Bir bölüm bir nedenle kullanılamıyorsa, olaylar kalan bölümler arasında dağıtılır. Bu davranış, en fazla çalışma süresi sağlar. En uzun süre gerektiren kullanım örnekleri için, bu model belirli bir bölüme olay göndermek yerine tercih edilir. 

## <a name="consistency"></a>Tutarlılık
Bazı senaryolarda, olayların sıralaması önemli olabilir. Örneğin, arka uç sisteminizin silme komutundan önce bir update komutunu işlemesini isteyebilirsiniz. Bu senaryoda, bir istemci uygulaması sıralama korunabilmesi için olayları belirli bir bölüme gönderir. Bir tüketici uygulaması bölümden bu olayları tükettiği zaman sırayla okunabilir. 

Bu yapılandırmayla, gönderdiğiniz belirli bir bölüm kullanılamaz durumdaysa, bir hata yanıtı alacağını aklınızda bulundurun. Karşılaştırma noktası olarak, tek bir bölüme yönelik benzeşim yoksa Event Hubs hizmeti, olaylarınızı bir sonraki kullanılabilir bölüme gönderir.

Bu nedenle, yüksek kullanılabilirlik en önemse, belirli bir bölümü (bölüm KIMLIĞI/anahtar kullanarak) hedeflememeniz gerekmez. Bölüm KIMLIĞI/anahtar kullanımı, bir olay hub 'ının bölüm düzeyinde kullanılabilirliğini daha eski bir şekilde açar. Bu senaryoda, kullanılabilirlik (bölüm KIMLIĞI/anahtar yok) ve tutarlılık (olayları belirli bir bölüme sabitleme) arasında açık bir seçim yapabilirsiniz. Event Hubs bölümler hakkında ayrıntılı bilgi için bkz. [bölümler](event-hubs-features.md#partitions).

## <a name="appendix"></a>Ek

### <a name="send-events-without-specifying-a-partition"></a>Bir bölüm belirtmeden olayları gönder
Event Hubs hizmetinin bölümler arasında yük dengelenmesi için bölüm bilgilerini ayarlamadan olayları bir olay hub 'ına göndermeyi öneririz. Farklı programlama dillerinde bunu nasıl yapacağınızı öğrenmek için aşağıdaki hızlı çalışmaya bakın. 

- [.NET kullanarak olay gönderme](event-hubs-dotnet-standard-getstarted-send.md)
- [Java kullanarak olay gönderme](event-hubs-java-get-started-send.md)
- [JavaScript kullanarak olay gönderme](event-hubs-python-get-started-send.md)
- [Python kullanarak olayları gönderme](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Olayları belirli bir bölüme gönder
Bu bölümde, farklı programlama dilleri kullanarak belirli bir bölüme olay gönderme hakkında bilgi edineceksiniz. 

### <a name="net"></a>[.NET](#tab/dotnet)
Olayları belirli bir bölüme göndermek için, [](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) `PartitionId` `PartitionKey` [createbatchoptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions)içinde veya ' i belirterek EventHubProducerClient. createbatchasync metodunu kullanarak Batch oluşturun. Aşağıdaki kod, bir bölüm anahtarı belirterek belirli bir bölüme olay toplu işi gönderir. Event Hubs, bölüm anahtarı değerini paylaşan tüm olayların birlikte depolanmasını ve varış sırasına göre teslim edilmesini sağlar.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Ayrıca, [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions)içinde **PartitionID** veya **Partitionkey** belirterek [EventHubProducerClient. sendadsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) yöntemini de kullanabilirsiniz.

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Olayları belirli bir bölüme göndermek için [Createbatchoptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions)IÇINDE **bölüm kimliğini** veya **bölüm anahtarını** belirterek [CreateBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) yöntemini kullanarak Batch oluşturun. Aşağıdaki kod, bir bölüm anahtarı belirterek belirli bir bölüme olay toplu işi gönderir. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Ayrıca, [Sendoleyde](/java/api/com.azure.messaging.eventhubs.models.sendoptions) **bölüm kimliği** veya **bölüm anahtarı** belirterek [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) yöntemini kullanabilirsiniz.

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Olayları belirli bir bölüme göndermek için yöntemini kullanarak bir Batch oluştururken, [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) veya öğesini belirtin `partition_id` `partition_key` . Ardından, [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) toplu işi Olay Hub 'ının bölümüne göndermek için yöntemini kullanın. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Veya parametreleri için değerler belirterek [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) yöntemini de kullanabilirsiniz `partition_id` `partition_key` .

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Olayları belirli bir bölüme göndermek için, veya belirterek [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) nesnesini kullanarak [bir Batch oluşturun](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) `partitionId` `partitionKey` . Ardından, [EventHubProducerClient. sendbatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) yöntemini kullanarak toplu işi Olay Hub 'ına gönderin. 

Aşağıdaki örneğe bakın.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Ayrıca, [Sendbatchoptions](/javascript/api/@azure/event-hubs/sendbatchoptions)IÇINDE **bölüm kimliğini** veya **bölüm anahtarını** belirterek [EventHubProducerClient. sendbatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) yöntemini de kullanabilirsiniz.

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

- [Event Hubs hizmete genel bakış](./event-hubs-about.md)
- [Event Hubs terminolojisi](event-hubs-features.md)
