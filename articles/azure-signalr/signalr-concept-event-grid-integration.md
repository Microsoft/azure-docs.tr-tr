---
title: Azure SignalR hizmeti olaylarına tepki verme
description: Azure SignalR hizmeti olaylarına abone olmak için Azure Event Grid kullanın. Diğer aşağı akış Hizmetleri bu olaylar tarafından tetiklenebilir.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151112"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Hizmeti olaylarına yanıt verme

Azure SignalR hizmeti olayları, uygulamaların modern sunucusuz mimariler kullanılarak bağlanan veya bağlantısı kesilen istemci bağlantılarına tepki vermesini sağlar. Bu, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gereksinimini ortadan kaldırmaz.  Bunun yerine, olaylar [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)gibi abonelere veya kendi özel http dinleyicinize de [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir. Azure SignalR ile yalnızca kullandığınız kadar ödersiniz.

Azure SignalR hizmeti olayları, zengin yeniden deneme ilkeleri ve atılacak mektup teslimi aracılığıyla uygulamalarınıza güvenilir teslim hizmetleri sağlayan Event Grid hizmetine güvenilir bir şekilde gönderilir. Daha fazla bilgi edinmek için bkz. [ileti teslimi Event Grid ve yeniden deneyin](../event-grid/delivery-and-retry.md).

![Event Grid modeli](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Sunucusuz durum
Azure SignalR hizmeti olayları yalnızca istemci bağlantıları sunucusuz bir durumda olduğunda etkindir. Bir istemci bir hub sunucusuna yönlendirmezse sunucusuz duruma geçer. Klasik mod yalnızca, istemci bağlantılarının bağlanacağı Hub bir hub sunucusu içermiyorsa geçerlidir. En iyi uygulama olarak sunucusuz mod önerilir. Hizmet modu hakkında daha fazla bilgi edinmek için bkz. [hizmet modunu seçme](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Kullanılabilir Azure SignalR hizmeti olayları
Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Azure SignalR hizmeti olay abonelikleri iki tür olayı destekler:  

|Olay Adı|Description|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|İstemci bağlantısı bağlıyken tetiklenir.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Bir istemci bağlantısı kesildiğinde tetiklenir.|

## <a name="event-schema"></a>Olay şeması
Azure SignalR hizmeti olayları, verilerdeki değişikliklere yanıt vermek için ihtiyacınız olan tüm bilgileri içerir. EventType özelliği "Microsoft. SignalRService" ile başlıyorsa, bir Azure SignalR hizmeti olayını tanımlayabilirsiniz. Event Grid olay özelliklerinin kullanımı hakkında ek bilgiler [Event Grid olay şemasında](../event-grid/event-schema.md)belgelenmiştir.  

İstemci bağlantısı bağlı olayına bir örnek aşağıda verilmiştir:
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

Daha fazla bilgi için bkz. [SignalR hizmeti olayları şeması](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve Azure SignalR hizmeti olaylarına bir deneme verin:

> [!div class="nextstepaction"]
> [Azure SignalR hizmeti](./signalr-howto-event-grid-integration.md) 
>  ile örnek Event Grid tümleştirmeyi deneyin [Event Grid hakkında](../event-grid/overview.md)