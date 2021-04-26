---
title: Azure Service Bus sıralarında ve konularında bölümlemeyi etkinleştir
description: Bu makalede, Azure portal, PowerShell, CLı ve programlama dillerini (C#, Java, Python ve JavaScript) kullanarak Azure Service Bus sıralarında ve konularda bölümlemenin nasıl etkinleştirileceği açıklanır.
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755299"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus kuyruğu veya konu başlığı için bölümleme özelliğini etkinleştirme
Service Bus bölümler, birden çok ileti aracılarına ve mesajlaşma depolarına göre bölümlenmesi için kuyrukları ve konuları veya mesajlaşma varlıklarını etkinleştirir. Bölümlendirme, bölümlenmiş bir varlığın genel üretilen işinin artık tek bir ileti aracısının veya mesajlaşma deposunun performansıyla sınırlı olmadığı anlamına gelir. Buna ek olarak, bir mesajlaşma deposunun geçici kesilmesi bölümlenmiş bir kuyruğu veya konuyu işlemez. Bölümlenmiş kuyruklar ve konular, işlemler ve oturumlar için destek gibi tüm gelişmiş Service Bus özellikleri içerebilir. Daha fazla bilgi için bkz. [bölümlenmiş kuyruklar ve konular](service-bus-partitioning.md). Bu makalede, Service Bus kuyruğu veya konu başlığı için yinelenen ileti algılamayı etkinleştirmenin farklı yolları gösterilmektedir. 

> [!IMPORTANT]
> - Bölümlendirme, temel veya standart SKU 'lardaki tüm kuyruklar ve konular için varlık oluşturma bölümünde kullanılabilir. Premium mesajlaşma SKU 'SU için kullanılamaz, ancak Premium ad alanlarında daha önce varolan bölümlenmiş varlıklar beklendiği gibi çalışmaya devam eder.
> - Var olan herhangi bir kuyrukta veya konuda bölümleme seçeneğini değiştirmek mümkün değildir. Seçeneği yalnızca bir kuyruk veya konu oluşturduğunuzda ayarlayabilirsiniz. 

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal bir **sıra** oluştururken, aşağıdaki görüntüde gösterildiği gibi **bölümlemeyi etkinleştir** ' i seçin. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Sıra oluşturma sırasında bölümlemeyi etkinleştir":::

Azure portal konu oluştururken, aşağıdaki görüntüde gösterildiği gibi **bölümlemeyi etkinleştir** ' i seçin. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Konu oluşturma sırasında bölümlemeyi etkinleştir":::

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma
**Bölümlendirme etkin olan bir kuyruk oluşturmak** için [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) komutunu olarak ayarla ' yı kullanın `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

**Bölümlendirme etkinken bir konu oluşturmak** için [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) komutunu olarak ayarla ' yı kullanın `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma
**Bölümlendirme etkin olan bir kuyruk oluşturmak** için [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) komutunu olarak ayarla ' yı kullanın `-EnablePartitioning` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

**Bölümlendirme etkinken bir konu oluşturmak** için [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) komutunu olarak ayarla ' yı kullanın `-EnablePartitioning` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
**Bölümlendirme etkin olan bir kuyruk oluşturmak** için, `enablePartitioning` `true` kuyruk özellikleri bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/kuyruklar şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

**Yinelenen saptamayı etkin bir konu oluşturmak** için, `enablePartitioning` `true` konusunun Özellikler bölümünde olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft. ServiceBus ad alanları/konu şablonu başvurusu](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "enablePartitioning": true
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