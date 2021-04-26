---
title: Konular veya etki alanları için Azure Event Grid tanılama günlükleri
description: Bu makalede bir Azure olay Kılavuzu konusu veya etki alanı için tanılama günlükleri hakkında kavramsal bilgiler sağlanmaktadır.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574998"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Azure Event Grid konuları/etki alanları için tanılama günlükleri
Tanılama ayarları Event Grid kullanıcıların bir depolama hesabında, Olay Hub 'ında veya bir Log Analytics çalışma alanında **Yayımlama ve teslim hatası** günlüklerini yakalayıp görüntülemesine olanak tanır. Bu makalede Günlükler ve örnek günlük girdisi için şema sağlanmaktadır.


## <a name="schema-for-publishdelivery-failure-logs"></a>Yayımlama/teslim hatası günlükleri için şema

| Özellik adı | Veri türü | Açıklama |
| ------------- | --------- | ----------- | 
| Saat | DateTime | Günlük girişinin oluşturulduğu saat <p>**Örnek değer:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | Dize | Olay aboneliğinin adı <p>**Örnek değer:** "EVENTSUB1"</p> <p>Bu özellik yalnızca teslim hatası günlükleri için geçerlidir.</p>  |
| Kategori | Dize | Günlük kategorisi adı. <p>**Örnek değerler:** "Deliveryarızaları" veya "Publishhatalarıyla" | 
| OperationName | Dize | İşlemin adı hataya neden oldu.<p>**Örnek değerler:** Teslim hatalarının "teslim edilmesi". |
| İleti | Dize | Kullanıcının hatanın nedenini ve diğer ek ayrıntıları açıklayan günlük iletisi. |
| ResourceId | Dize | Konu/etki alanı kaynağı için kaynak KIMLIĞI<p>**Örnek değerler:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Örnek

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Sonraki adımlar
Konular veya etki alanları için tanılama günlüklerini etkinleştirme hakkında bilgi edinmek için bkz. [tanılama günlüklerini etkinleştirme](enable-diagnostic-logs-topic.md).
