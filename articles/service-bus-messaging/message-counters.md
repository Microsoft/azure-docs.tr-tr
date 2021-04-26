---
title: Azure Service Bus-ileti sayısı
description: Azure Resource Manager ve Azure Service Bus NamespaceManager API 'Leri kullanarak kuyruklarda ve aboneliklerde tutulan ileti sayısını alın.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814622"
---
# <a name="get-message-counters"></a>İleti sayaçlarını al
Bu makalede, bir kuyruk veya abonelik için aşağıdaki ileti sayılarını almanın farklı yolları gösterilmektedir. Etkin ileti sayısını bilmek, bir kuyruğun daha önce dağıtılmış olandan daha fazla kaynağın işlemesini gerektiren bir biriktirme listesi oluşturup oluşturmadığını belirlemek için faydalıdır. 

| Sayaç | Description |
| ----- | ---------- | 
| ActiveMessageCount | Kuyruktaki veya abonelikteki, etkin durumdaki ve teslim için hazırlanma iletilerinin sayısı. |
| ScheduledMessageCount | Zamanlanan durumdaki ileti sayısı. |
| DeadLetterMessageCount | Atılacak ileti sırasındaki ileti sayısı. |
| TransferMessageCount | Başka bir kuyruğa veya konuya Aktarılmayı bekleyen ileti sayısı. |
| TransferDeadLetterMessageCount | Başka bir kuyruğa veya konuya aktarılamayan ve aktarım atılacak mektup kuyruğuna taşınan ileti sayısı. |

Bir uygulama, sıranın uzunluğuna bağlı olarak kaynakları ölçeklendirmek isterse, bunu ölçülen bir hızda yapmanız gerekir. İleti sayaçlarının alımı, ileti aracısının içindeki pahalı bir işlemdir ve genellikle doğrudan yürütülerek varlık performansını olumsuz yönde etkiler.

> [!NOTE]
> Bir Service Bus konusuna gönderilen iletiler, bu konunun aboneliklerine iletilir. Bu nedenle, söz konusu iletiler aboneliğe başarıyla iletileceği için konunun etkin ileti sayısı 0 ' dır. Abonelikte ileti sayısını alın ve 0 ' dan büyük olduğunu doğrulayın. Abonelikte iletileri görseniz bile, bunlar aslında konunun sahip olduğu bir depolama alanında depolanır. Aboneliklerle karşılaşırsanız, sıfır olmayan ileti sayımına sahip olur (Bu, tüm varlık için en fazla 323 MB alan ekler).


## <a name="using-azure-portal"></a>Azure portalını kullanma
Ad alanına gidip kuyruğu seçin. Kuyruğun **genel bakış** sayfasında ileti sayaçlarını görürsünüz.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Kuyruğa Genel Bakış sayfasında ileti sayaçları":::

Ad alanınız ' ne gidin, konuyu seçin ve ardından konunun aboneliğini seçin. Kuyruğun **genel bakış** sayfasında ileti sayaçlarını görürsünüz.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Abonelik Genel Bakış sayfasında ileti sayaçları":::

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
[`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show)Aşağıdaki örnekte gösterildiği gibi bir kuyruğun ileti sayısı ayrıntılarını almak için komutunu kullanın. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Örnek bir çıktı aşağıda verilmiştir:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

[`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show)Aşağıdaki örnekte gösterildiği gibi, bir aboneliğin ileti sayısı ayrıntılarını almak için komutunu kullanın. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
PowerShell ile bir sıranın ileti sayısı ayrıntılarını aşağıdaki gibi edinebilirsiniz:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Örnek çıktı aşağıda verilmiştir:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

bir aboneliğin ileti sayısı ayrıntılarını aşağıdaki gibi edinebilirsiniz:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Döndürülen `MessageCountDetails` nesne şu özelliklere sahiptir: `ActiveMessageCount` ,,, `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` , `TransferMessageCount` . 

## <a name="next-steps"></a>Sonraki adımlar

Azure Service Bus özellikleri araştırmak için istediğiniz dildeki örnekleri deneyin. 

- [Java için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [.NET için Azure. Messaging. ServiceBus örnekleri](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Aşağıdaki eski .NET ve Java istemci kitaplıkları için örnekler bulun:
- [.NET için Microsoft. Azure. ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java için Azure-ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
