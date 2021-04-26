---
title: Azure Service Bus kuyrukları ve abonelikleri için Otomatik iletmeyi etkinleştirme
description: Bu makalede Azure portal, PowerShell, CLı ve programlama dillerini (C#, Java, Python ve JavaScript) kullanarak kuyruklar ve abonelikler için otomatik yönlendirmeyi etkinleştirme açıklanmaktadır
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: e5f69a82aac96deaf96f0ae6aaa1a26d0ee3aaf3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814694"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus kuyrukları ve abonelikleri için Otomatik iletmeyi etkinleştirme
Service Bus otomatik iletme özelliği, bir kuyruğu veya aboneliği aynı ad alanının parçası olan başka bir kuyruğa veya konuya zincirlemenize olanak sağlar. Otomatik iletme etkinleştirildiğinde Service Bus, ilk sıraya veya aboneliğe (kaynak) yerleştirilmiş iletileri otomatik olarak kaldırır ve bunları ikinci kuyruğa veya konuya (hedef) koyar. Hedef varlığa doğrudan bir ileti göndermek yine de mümkündür. Daha fazla bilgi için bkz. [otomatik iletme ile Service Bus varlıkları zincirleme](service-bus-auto-forwarding.md). Bu makalede, Service Bus kuyrukları ve abonelikleri için Otomatik iletmeyi etkinleştirmenin farklı yolları gösterilmektedir. 

> [!IMPORTANT]
> Service Bus temel katmanı otomatik iletme özelliğini desteklemez. Standart ve Premium katmanlar özelliği destekler. Bu katmanlar arasındaki farklar için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal konu başlığı için bir **kuyruk** veya **abonelik** oluştururken, aşağıdaki örneklerde gösterildiği gibi **iletileri kuyruğa/konuya ilet** ' i seçin. Ardından, iletilerin bir kuyruğa veya konuya iletilmesini isteyip istemediğinizi belirtin. Bu örnekte, **kuyruk** seçeneği seçilidir ve aynı ad alanından bir sıra seçilir.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Otomatik iletme etkin olan bir kuyruk oluşturma
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Sıra oluşturma sırasında otomatik iletmeyi etkinleştir":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Otomatik iletme özelliği etkin olan bir konu için abonelik oluşturma
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Abonelik oluşturma sırasında otomatik iletmeyi etkinleştirin":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>Mevcut bir sıra için otomatik iletme ayarını Güncelleştir
Service Bus kuyruğunuzun **genel bakış** sayfasında, **görüntülenecek iletme iletileri** için geçerli değeri seçin. Aşağıdaki örnekte, geçerli değer **devre dışıdır**. **İletileri kuyruğa/konuya ilet** penceresinde, iletilerin iletilmesini istediğiniz kuyruğu veya konuyu seçebilirsiniz. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Mevcut bir sıra için Otomatik iletmeyi etkinleştir":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>Mevcut bir abonelik için otomatik iletme ayarını Güncelleştir
Service Bus aboneliğiniz için **genel bakış** sayfasında, **görüntülenecek iletme iletileri** için geçerli değeri seçin. Aşağıdaki örnekte, geçerli değer **devre dışıdır**. **İletileri kuyruğa/konuya ilet** penceresinde, iletilerin iletilmesini istediğiniz kuyruğu veya konuyu seçebilirsiniz. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Mevcut bir abonelik için Otomatik iletmeyi etkinleştir":::

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
**Otomatik iletme özelliği etkinken bir sıra oluşturmak** için, [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) Bu komutu, `--forward-to` İletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

**Mevcut bir kuyruğun otomatik iletme ayarını güncelleştirmek** için, [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) Bu komutu, `--forward-to` İletilerin iletilmesini istediğiniz sıranın veya konunun adına ayarlayın. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


**Otomatik iletme özelliği etkinken bir konuya abonelik oluşturmak** için, [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) Bu komutu, `--forward-to` İletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

**Bir aboneliğe yönelik otomatik iletme ayarını güncelleştirmek** için, [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--forward-to` İletilerin iletilmesini istediğiniz sıranın veya konunun adına ayarla komutunu kullanın.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
**Otomatik iletme özelliği etkinken bir sıra oluşturmak** için, [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) Bu komutu, `-ForwardTo` İletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

**Mevcut bir sıranın otomatik iletme ayarını güncelleştirmek** için [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) Aşağıdaki örnekte gösterildiği gibi komutunu kullanın.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

**Otomatik iletme özelliği etkinken bir konu için abonelik oluşturmak** üzere, [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) Bu komutu, `-ForwardTo` İletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

**Mevcut bir aboneliğin otomatik iletme ayarını güncelleştirmek** için aşağıdaki örneğe bakın.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
**Otomatik iletme özellikli bir sıra oluşturmak** için, `forwardTo` kuyruk özellikleri bölümünde iletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/kuyruklar şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

**Otomatik iletme özellikli bir konu için abonelik oluşturmak** üzere, `forwardTo` kuyruk özellikleri bölümünde iletilerin iletilmesini istediğiniz sıranın veya konunun adı olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/konular/abonelikler şablon başvurusu](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```


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