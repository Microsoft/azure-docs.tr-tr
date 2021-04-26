---
title: Azure Service Bus kuyrukları ve abonelikleri için atılacak etkin
description: Bu makalede Azure portal, PowerShell, CLı ve programlama dillerini (C#, Java, Python ve JavaScript) kullanarak kuyruklar ve abonelikler için atılacak kullanım işlemlerinin nasıl etkinleştirileceği açıklanır.
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819406"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus kuyrukları ve abonelikleri için ileti süre sonundan ölü kullanım süresini etkinleştirme
Konular için Azure Service Bus kuyrukları ve abonelikleri, atılacak ileti sırası (DLQ) adlı ikincil bir alt sıra sağlar. Atılacak ileti sırasının açık bir şekilde oluşturulması ve silinemediği veya ana varlıktan bağımsız olarak yönetilebilmesi gerekmez. Atılacak ileti sırasının amacı, hiçbir alıcıya teslim edilemeyen iletileri veya işlenemeyen iletileri tutmaktır. Daha fazla bilgi için bkz. [Service Bus atılacak ileti sıralarına genel bakış](service-bus-dead-letter-queues.md). Bu makalede, Service Bus kuyrukları ve abonelikleri için geçersiz kılmak için farklı yollar sunulmaktadır. 

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal konu başlığı için bir **kuyruk** veya **abonelik** oluştururken, aşağıdaki örneklerde gösterildiği gibi **ileti süre sonu üzerinde atılacak kullanım süresini etkinleştir** ' i seçin. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Etkin olmayan bir kuyruk oluşturma etkin
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Sıra oluşturma sırasında atılacak süreyi etkinleştirin":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Etkin olmayan bir abonelik oluşturma
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Abonelik oluşturma sırasında atılacak süreyi etkinleştirin":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Mevcut bir sıra için ileti süre sonu ayarını etkin değil olarak güncelleştir
Service Bus kuyruğunuzun **genel bakış** sayfasında, ileti süre sonu için **atılacak** ayarı için geçerli değeri seçin. Aşağıdaki örnekte, geçerli değer **devre dışıdır**. Açılan penceredeki ileti süre sonu için atılacak kullanım süresini etkinleştirebilir veya devre dışı bırakabilirsiniz. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Mevcut bir sıra için ileti süre sonundan atılacak kullanım süresini etkinleştir":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Mevcut bir abonelik için ileti sona erme tarihi ayarını güncelleştirme
Service Bus aboneliğinizin **genel bakış** sayfasında, ileti süre sonu için **atılacak** ayarı için geçerli değeri seçin. Aşağıdaki örnekte, geçerli değer **devre dışıdır**. Açılan penceredeki ileti süre sonu için atılacak kullanım süresini etkinleştirebilir veya devre dışı bırakabilirsiniz. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Mevcut bir abonelik için ileti süre sonu için atılacak kullanım süresini etkinleştir":::

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
**İleti süre sonu etkin olan bir kuyruk oluşturmak** için [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) komutunu olarak ayarla ' yı kullanın `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

**Var olan bir kuyruk için ileti süre sonu ayarını geçersiz kılmak** için, [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) komutunu olarak ayarla ' yı kullanın `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


**İleti kullanım süresi etkinken etkin olmayan bir konuya abonelik oluşturmak** için, [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) komutunu olarak ayarla ' yı kullanın `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Bir **konuyla ilgili bir abonelik için ileti süre sonu ayarını geçersiz kılmak** için, [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) set ile komutunu kullanın `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
**İleti süre sonu etkin olan bir kuyruk oluşturmak** için [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) komutunu olarak ayarla ' yı kullanın `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

**Mevcut bir kuyruğun ileti süre sonu ayarını geçersiz kılmak** için [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) Aşağıdaki örnekte gösterildiği gibi komutunu kullanın.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

**İleti süre sonu etkin olan bir konuyla ilgili bir abonelik oluşturmak** için, öğesini [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-DeadLetteringOnMessageExpiration` olarak ayarla komutunu kullanın `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

**Mevcut bir abonelik için ileti süre sonu ayarını geçersiz kılmak** için aşağıdaki örneğe bakın.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
**İleti süre sonu etkin olan bir kuyruk oluşturmak** için, `deadLetteringOnMessageExpiration` kuyruk özellikleri bölümünde olarak ayarlayın `true` . Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/kuyruklar şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

**İleti süre sonu etkin olan bir konuyla ilgili abonelik oluşturmak** için, `deadLetteringOnMessageExpiration` kuyruk özellikleri bölümünde olarak ayarlayın `true` . Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/konular/abonelikler şablon başvurusu](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "deadLetteringOnMessageExpiration": true
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