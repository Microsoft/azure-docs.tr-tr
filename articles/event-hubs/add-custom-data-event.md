---
title: Azure Event Hubs özel verileri olaylara ekleme
description: Bu makalede, Azure Event Hubs 'daki olaylara nasıl özel veri ekleyeceğiniz gösterilmektedir.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893483"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Azure Event Hubs özel verileri olaylara ekleme
Bir olay çoğunlukla donuk bir bayt kümesini içerdiğinden, bu olayların müşterileri tarafından nasıl işleyebileceği hakkında bilinçli kararlar almak zor olabilir. Olay yayımcılarının tüketicilere daha iyi bağlam sunmasına izin vermek için olaylar, anahtar-değer çiftleri kümesi biçiminde özel meta veriler de içerebilir. Meta verilerin dahil edilmesi için bir genel senaryo, bir olayın içerdiği verilerin türü hakkında bir ipucu sağlamaktır ve bu sayede, tüketicilerin kendi biçimini anlaması ve uygun şekilde seri durumdan çıkarabilmesi.

> [!NOTE]
> Bu meta veriler, Event Hubs hizmeti ile herhangi bir şekilde kullanılamaz; yalnızca olay yayımcıları ve tüketiciler arasındaki koordinasyon vardır. 

Aşağıdaki bölümlerde, farklı programlama dillerinde olaylara özel verileri nasıl ekleyeceğiniz gösterilmektedir. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Tam kod örneği için bkz. [özel meta verilerle olayları yayımlama](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Tam kod örneği için bkz. [özel meta verilerle olayları yayımlama](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Tam kod örneği için bkz. [özelliklerle olay verileri toplu Işi gönderme](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki hızlı başlangıçlara ve örneklere bakın. 

- Hızlı başlangıç: [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- GitHub 'daki örnekler: [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
