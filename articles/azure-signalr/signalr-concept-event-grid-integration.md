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
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158200"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR hizmeti olaylarına yeniden davranıma

Azure SignalR hizmeti olayları, uygulamaların modern sunucusuz mimariler kullanılarak bağlanan veya bağlantısı kesilen istemci bağlantılarına tepki vermesini sağlar. Bu, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gereksinimini ortadan kaldırmaz.  Bunun yerine, olaylar [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), hatta kendi özel http dinleyiciniz gibi abonelere [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir ve yalnızca kullandığınız kadar ödersiniz.

Azure SignalR hizmeti olayları, zengin yeniden deneme ilkeleri ve atılacak mektup teslimi aracılığıyla uygulamalarınıza güvenilir teslim hizmetleri sağlayan Event Grid hizmetine güvenilir bir şekilde gönderilir. Daha fazla bilgi edinmek için bkz. [ileti teslimi Event Grid ve yeniden deneyin](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid modeli](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Sunucusuz durum
Azure SignalR hizmeti olayları yalnızca istemci bağlantıları sunucusuz durumundayken etkindir. Genel olarak, bir istemci bir hub sunucusuna yönlendirmezse sunucusuz duruma geçer. Klasik mod yalnızca, istemci bağlantılarının bağlantı aldığı Merkez bir hub sunucusu içermiyorsa çalışır. Ancak, bazı sorunlardan kaçınmak için sunucusuz mod önerilir. Hizmet modu hakkında daha fazla bilgi edinmek için bkz. [hizmet modunu seçme](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Kullanılabilir Azure SignalR hizmeti olayları
Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Azure SignalR hizmeti olay abonelikleri iki tür olayı destekler:  

|Olay adı|Açıklama|
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
> [Azure SignalR Service
> Ile ilgili bir örnek Event Grid tümleştirmesini deneyin](./signalr-howto-event-grid-integration.md) [Event Grid hakkında](../event-grid/overview.md)
