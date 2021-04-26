---
title: Service Bus kuyrukları ve konuları Azure Event Grid olaylar için olay işleyicileri olarak
description: Azure Event Grid olaylar için Service Bus kuyrukları ve konuları olay işleyicileri olarak nasıl kullanabileceğinizi açıklar.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 12b72420e3475b46a4cd61ce5032b478af740dde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97399868"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus kuyrukları ve konuları Azure Event Grid olaylar için olay işleyicileri olarak
Olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için başka bir eylem gerçekleştirir. Çeşitli Azure Hizmetleri, olayları işleyecek şekilde otomatik olarak yapılandırılır ve bunlardan biridir **Azure Service Bus** . 

Bir hizmet kuyruğunu veya konuyu, Event Grid olaylar için işleyici olarak kullanabilirsiniz. 

## <a name="service-bus-queues"></a>Service Bus kuyrukları
Event Grid olayları, ara belleğe almak veya komut & denetim senaryolarında kurumsal uygulamalarda kullanmak üzere Service Bus sıralara doğrudan yönlendirebilirsiniz.

Azure portal, bir olay aboneliği oluştururken, uç nokta türü olarak **Service Bus kuyruğu** ' nu seçin ve ardından bir **uç nokta seç** ' e tıklayarak bir Service Bus kuyruğu seçin.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Service Bus kuyruğu işleyicisi eklemek için CLı kullanma

Azure CLı için aşağıdaki örnek abone olur ve bir olay Kılavuzu konusunu Service Bus kuyruğuna bağlar:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus konuları

Olayları, Service Bus konular ile Azure sistem olaylarını işlemek veya komut & denetim mesajlaşma senaryoları için Event Grid doğrudan Service Bus konularına yönlendirebilirsiniz.

Azure portal, bir olay aboneliği oluştururken, uç nokta türü olarak **Service Bus konu** ' ı seçin ve ardından bir Service Bus konu başlığı seçmek için **Seç ve uç nokta** ' a tıklayın.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Service Bus konu işleyicisi eklemek için CLı kullanma

Azure CLı için aşağıdaki örnek, Service Bus bir konuya abone olur ve bir Event Grid konusuna bağlanır:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Bir Service Bus kuyruğuna veya konusuna aracılı bir ileti olarak bir olay gönderirken `messageid` aracılı iletinin bir iç SISTEM kimliği olması gerekir.

İleti için iç sistem KIMLIĞI, olay yeniden teslim sırasında korunur, böylece, Service Bus varlığında **yinelenen saptamayı** etkinleştirerek yinelenen teslimleri önleyebilirsiniz. Service Bus varlığındaki yinelenen algılamanın süresinin, etkinliğin yaşam süresi (TTL) veya en fazla yeniden deneme süresi (hangisi daha uzun) olması için etkinleştirmenizi öneririz.

## <a name="rest-examples-for-put"></a>REST örnekleri (PUT için)

### <a name="service-bus-queue"></a>Service Bus kuyruğu

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus kuyruğu-yönetilen kimlikle teslim

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus konusu

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus konu-yönetilen kimlikle teslim

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Desteklenen olay işleyicilerinin bir listesi için bkz. [olay işleyicileri](event-handlers.md) makalesi. 
