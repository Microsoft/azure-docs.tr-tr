---
title: Event Grid kaynağı olarak Azure SignalR
description: Azure Event Grid ile Azure SignalR olayları için sunulan özellikleri açıklar
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 321dc4d21485af23a9cca0d42d74da0a3e121b7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363704"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR hizmeti için Azure Event Grid olay şeması

Bu makalede, SignalR hizmeti olaylarına yönelik özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md). Ayrıca, Azure SignalR 'yi bir olay kaynağı olarak kullanmak için hızlı başlayan ve öğreticilerin bir listesini sağlar.


## <a name="available-event-types"></a>Kullanılabilir olay türleri

SignalR hizmeti aşağıdaki olay türlerini yayar:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | İstemci bağlantısı bağlandığında tetiklenir. |
| Microsoft. SignalRService. Clientconnectionconnected | İstemci bağlantısı kesildiğinde tetiklenir. |

## <a name="example-event"></a>Örnek olay

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)
Aşağıdaki örnek, istemci bağlantısı bağlantılı olayının şemasını gösterir: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

İstemci bağlantısı kesik bağlantısı olayının şeması benzerdir: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnek, istemci bağlantısı bağlantılı olayının şemasını gösterir: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionConnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "specversion": "1.0"
}]
```

İstemci bağlantısı kesik bağlantısı olayının şeması benzerdir: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Olay özellikleri


# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)
Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | SignalR hizmeti olay verileri. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | SignalR hizmeti olay verileri. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `timestamp` | dize | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `hubName` | string | İstemci bağlantısının ait olduğu merkez. |
| `connectionId` | string | İstemci bağlantısı için benzersiz tanımlayıcı. |
| `userId` | string | Talep içinde tanımlanan kullanıcı tanımlayıcısı. |
| `errorMessage` | string | Bağlantının bağlantısının kesilmesine neden olan hata. |

## <a name="tutorials-and-how-tos"></a>Öğreticiler ve nasıl yapılır kılavuzları
|Başlık | Açıklama |
|---------|---------|
| [Event Grid kullanarak Azure SignalR hizmeti olaylarına tepki verme](../azure-signalr/signalr-concept-event-grid-integration.md) | Event Grid ile Azure SignalR hizmeti tümleştirilmesine genel bakış. |
| [Event Grid için Azure SignalR hizmeti olaylarını gönderme](../azure-signalr/signalr-howto-event-grid-integration.md) | Event Grid aracılığıyla Azure SignalR hizmeti olaylarının bir uygulamaya nasıl gönderileceğini gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).
