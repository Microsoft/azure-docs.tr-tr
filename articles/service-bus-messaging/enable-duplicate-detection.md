---
title: Yinelenen ileti algılamayı etkinleştir-Azure Service Bus
description: Bu makalede Azure portal, PowerShell, CLı ve programlama dillerini (C#, Java, Python ve JavaScript) kullanarak yinelenen ileti algılamayı nasıl etkinleştireceğinizi açıklanmaktadır
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755302"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus kuyruğu veya konu başlığı için yinelenen ileti algılamayı etkinleştirme
Bir kuyruk veya konu için yinelenen saptamayı etkinleştirdiğinizde Azure Service Bus, bir süre için kuyruğa veya konuya gönderilen tüm iletilerin bir geçmişini tutar. Bu Aralık sırasında, kuyruğunuz veya konu başlığı yinelenen iletileri depolamaz. Bu özelliği etkinleştirmek, Kullanıcı tanımlı zaman dilimi boyunca tam olarak bir kez teslim garantisi sağlar. Daha fazla bilgi için bkz. [yinelenen algılama](duplicate-detection.md). Bu makalede, Service Bus kuyruğu veya konu başlığı için yinelenen ileti algılamayı etkinleştirmenin farklı yolları gösterilmektedir. 


> [!NOTE]
> - Service Bus temel katmanı yinelenen saptamayı desteklemez. Standart ve Premium katmanlar yinelenen saptamayı destekler. Bu katmanlar arasındaki farklar için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).
> - Sıra veya konu oluşturulduktan sonra yinelenen saptamayı etkinleştiremez veya devre dışı bırakabilirsiniz. Bunu yalnızca kuyruğu veya konuyu oluşturma sırasında yapabilirsiniz. 

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal bir **sıra** oluştururken, aşağıdaki görüntüde gösterildiği gibi **yinelenen saptamayı etkinleştir** ' i seçin. Bir kuyruk veya konu oluştururken yinelenen algılama penceresinin boyutunu yapılandırabilirsiniz. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Sıra oluşturma sırasında yinelenen algılamayı etkinleştir":::

Azure portal konu oluştururken, aşağıdaki görüntüde gösterildiği gibi **yinelenen saptamayı etkinleştir** ' i seçin. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Konu oluşturma sırasında yinelenen algılamayı etkinleştir":::

Bu ayarı, oluşturma sırasında yinelenen saptamayı etkinleştirdiyseniz, var olan bir kuyruk veya konu için de yapılandırabilirsiniz. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Var olan bir kuyruk veya konu başlığı için yinelenen algılama pencere boyutunu Güncelleştir
Var olan bir kuyruk veya konu başlığı için yinelenen algılama pencere boyutunu değiştirmek için, **genel bakış** sayfasında, **yinelenen algılama penceresi** için **Değiştir** ' i seçin.  

#### <a name="queue"></a>Kuyruk
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Sıra için yinelenen algılama pencere boyutunu ayarla":::

#### <a name="topic"></a>Konu
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Konu için yinelenen algılama pencere boyutunu ayarla":::


## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
**Yinelenen saptamayı etkin bir sıra oluşturmak** için [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) komutunu olarak ayarla ' yı kullanın `--enable-duplicate-detection` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

**Yinelenen saptamayı etkin bir konu oluşturmak** için, öğesini [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) olarak ayarla komutunu kullanın `--enable-duplicate-detection` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Yukarıdaki örneklerde, parametresini kullanarak yinelenen algılama penceresinin boyutu da ayarlanır `--duplicate-detection-history-time-window` . Pencere boyutu bir güne ayarlanır. Varsayılan değer 10 dakikadır ve izin verilen en büyük değer yedi gündür.

**Yeni bir algılama penceresi boyutuyla bir kuyruğu güncelleştirmek** için, [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) parametresiyle komutunu kullanın `--duplicate-detection-history-time-window` . Bu örnekte, pencere boyutu yedi güne güncelleştirilir. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Benzer şekilde, **Yeni bir algılama penceresi boyutuyla bir konuyu güncelleştirmek** için, [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) parametresiyle komutunu kullanın `--duplicate-detection-history-time-window` . Bu örnekte, pencere boyutu yedi güne güncelleştirilir.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
**Yinelenen saptamayı etkin bir sıra oluşturmak** için [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) komutunu olarak ayarla ' yı kullanın `-RequiresDuplicateDetection` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

**Yinelenen saptamayı etkin bir konu oluşturmak** için, öğesini [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) olarak ayarla komutunu kullanın `-RequiresDuplicateDetection` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

Yukarıdaki örneklerde, parametresini kullanarak yinelenen algılama penceresinin boyutu da ayarlanır `-DuplicateDetectionHistoryTimeWindow` . Pencere boyutu bir güne ayarlanır. Varsayılan değer 10 dakikadır ve izin verilen en büyük değer yedi gündür.

**Yeni bir algılama penceresi boyutuyla bir kuyruğu güncelleştirmek** için aşağıdaki örneğe bakın.  Bu örnekte, pencere boyutu yedi güne güncelleştirilir.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

**Yeni bir algılama penceresi boyutuyla bir konuyu güncelleştirmek** için aşağıdaki örneğe bakın. Bu örnekte, pencere boyutu yedi güne güncelleştirilir.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
**Yinelenen saptamayı etkin bir sıra oluşturmak** için, `requiresDuplicateDetection` `true` kuyruk özellikleri bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/kuyruklar şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). `duplicateDetectionHistoryTimeWindow`Yinelenen algılama penceresinin boyutunu ayarlamak için özelliği için bir değer belirtin. Aşağıdaki örnekte, bir gün olarak ayarlanır.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

**Yinelenen saptamayı etkin bir konu oluşturmak** için, `requiresDuplicateDetection` `true` konusunun Özellikler bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/konu şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
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