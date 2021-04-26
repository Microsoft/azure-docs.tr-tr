---
title: İleti oturumlarını Azure Service Bus etkinleştirme | Microsoft Docs
description: Bu makalede Azure portal, PowerShell, CLı ve programlama dillerini (C#, Java, Python ve JavaScript) kullanarak ileti oturumlarının nasıl etkinleştirileceği açıklanmaktadır
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755301"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Azure Service Bus kuyruğu veya abonelik için ileti oturumlarını etkinleştirme
Azure Service Bus oturumlar, sınırsız sayıda ilgili ileti dizisinin birleşme ve sıralı işlenmesini sağlar. Oturumlar **ilk içinde, ilk çıkar (FIFO)** ve **istek-yanıt** desenlerinde kullanılabilir. Daha fazla bilgi için bkz. [ileti oturumları](message-sessions.md). Bu makalede, Service Bus kuyruğu veya aboneliği için oturumları etkinleştirmenin farklı yolları gösterilmektedir. 

> [!IMPORTANT]
> - Service Bus temel katmanı oturumları desteklemez. Standart ve Premium katmanlar oturumları destekler. Bu katmanlar arasındaki farklar için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).
> - Kuyruk veya abonelik oluşturulduktan sonra ileti oturumlarını etkinleştiremez veya devre dışı bırakamıyoruz. Bunu yalnızca kuyruğu veya aboneliği oluşturma sırasında yapabilirsiniz. 

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal bir **sıra** oluştururken, aşağıdaki görüntüde gösterildiği gibi **oturumları etkinleştir** ' i seçin. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Kuyruğu sıra oluşturma sırasında etkinleştirin":::

Azure portal bir konu için abonelik oluştururken, aşağıdaki görüntüde gösterildiği gibi **oturumları etkinleştir** ' i seçin. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Abonelik oluşturma sırasında oturumu etkinleştir":::

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
**İleti oturumlarıyla etkin bir kuyruk oluşturmak** için [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) komutunu olarak ayarla ' yı kullanın `--enable-session` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

**İleti oturumlarının etkin olduğu bir konu için abonelik oluşturmak** üzere, seçeneğini [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) olarak ayarla komutunu kullanın `--enable-session` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
**İleti oturumlarıyla etkin bir kuyruk oluşturmak** için [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) komutunu olarak ayarla ' yı kullanın `-RequiresSession` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

**İleti oturumlarının etkin olduğu bir konu için abonelik oluşturmak** üzere, seçeneğini [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) olarak ayarla komutunu kullanın `-RequiresSession` `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
**İleti oturumları etkin olan bir kuyruk oluşturmak** için, `requiresSession` `true` kuyruk özellikleri bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/kuyruklar şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

**İleti oturumlarının etkin olduğu bir konu için abonelik oluşturmak** üzere, `requiresSession` `true` abonelik özellikleri bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/konular/abonelikler şablon başvurusu](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "requiresSession": true
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