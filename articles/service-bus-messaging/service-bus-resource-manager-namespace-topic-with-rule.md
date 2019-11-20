---
title: Azure Resource Manager şablonu kullanarak Azure Service Bus konu aboneliği ve kuralı oluşturma | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak konu, abonelik ve kuralla Service Bus ad alanı oluşturma
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 6631528c9d1f2dfc6dcd3e9d6da81115a9ece637
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832073"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak konu, abonelik ve kuralla Service Bus ad alanı oluşturma

Bu makalede, bir konu, abonelik ve kural (filtre) ile bir Service Bus ad alanı oluşturan Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir. Makalesinde hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağı açıklanmaktadır. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma][Authoring Azure Resource Manager templates].

Azure kaynakları adlandırma kurallarına ilişkin uygulama ve desenler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için önerilen adlandırma kuralları][Recommended naming conventions for Azure resources].

Tüm şablon için [konu, abonelik ve kural şablonuyla Service Bus ad alanına][Service Bus namespace with topic, subscription, and rule] bakın.

> [!NOTE]
> Aşağıdaki Azure Resource Manager şablonları indirme ve dağıtım için kullanılabilir.
> 
> * [Kuyruk ve yetkilendirme kuralıyla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-auth-rule.md)
> * [Sıraya sahip bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace.md)
> * [Konu ve abonelikle bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic.md)
> 
> En son şablonları denetlemek için [Azure hızlı başlangıç şablonları][Azure Quickstart Templates] Galerisi ' ni ziyaret edin ve Service Bus arayın.
> 
> 

## <a name="what-do-you-deploy"></a>Ne dağıtırsınız?

Bu şablonla, konu, abonelik ve kural (filtre) ile bir Service Bus ad alanı dağıtırsınız.

[Service Bus konular ve abonelikler](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) , *Yayımla/abone ol* düzeninde bire çok bir iletişim biçimi sağlar. Konular ve abonelikler kullanılırken, dağıtılmış bir uygulamanın bileşenleri birbirleriyle doğrudan iletişim kurmazlar; bunun yerine bir aracı gibi davranan konu aracılığıyla iletileri değiş tokuş ederler. Bir konuya abonelik, konuya gönderilen iletilerin kopyalarını alan sanal kuyruğa benzer. Abonelik filtresi, bir konuya gönderilen iletileri belirli bir konu aboneliği içinde hangi mesajların belirebilir belirtmenize olanak sağlar.

## <a name="what-are-rules-filters"></a>Kurallar (filtreler) nedir?

Birçok senaryoda, belirli özelliklere sahip olan iletiler farklı yollarla işlenmelidir. Bu özel işlemeyi etkinleştirmek için, belirli özelliklere sahip iletileri bulmak üzere abonelikleri yapılandırabilir ve ardından bu özelliklerde değişiklikler yapabilirsiniz. Service Bus abonelikler konuya gönderilen tüm iletileri görebilse de, bu iletilerin bir alt kümesini yalnızca sanal abonelik kuyruğuna kopyalayabilirsiniz. Abonelik filtreleri kullanılarak gerçekleştirilir. Kurallar (filtreler) hakkında daha fazla bilgi için bkz. [kurallar ve eylemler](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtma](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametreler

Azure Resource Manager, şablon dağıtıldığında belirtmek istediğiniz değerler için parametreleri tanımlayın. Şablon, tüm parametre değerlerini içeren `Parameters` adlı bir bölüm içerir. Dağıttığınız projeye göre veya dağıttığınız ortama göre değişen değerler için bir parametre tanımlayın. Her zaman aynı kalan değerler için parametre tanımlamayın. Her parametre değeri, dağıtılan kaynakları tanımlamak için şablonda kullanılır.

Şablon aşağıdaki parametreleri tanımlar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Oluşturulacak Service Bus ad alanının adı.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Service Bus ad alanında oluşturulan konunun adı.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Service Bus ad alanında oluşturulan aboneliğin adı.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Service Bus ad alanında oluşturulan kuralın (filtre) adı.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
Şablonun Service Bus API sürümü.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Dağıtılacak kaynaklar
**İleti**türünde, konu ve abonelik ve kurallarla bir standart Service Bus ad alanı oluşturur.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

JSON sözdizimi ve özellikleri için bkz. [ad alanları](/azure/templates/microsoft.servicebus/namespaces), [konular](/azure/templates/microsoft.servicebus/namespaces/topics), [abonelikler](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)ve [kurallar](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Sonraki adımlar
Abonelik filtresi oluşturmayı gösteren aşağıdaki konuya bakın: bir [Azure Resource Manager şablonu kullanarak konu, abonelik ve kuralla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic-with-rule.md)

Şu makaleleri görüntüleyerek bu kaynakları yönetmeyi öğrenin:

* [Azure Service Bus Yönet](service-bus-management-libraries.md)
* [Service Bus’ı PowerShell ile yönetme](service-bus-manage-with-ps.md)
* [Service Bus Gezgini ile Service Bus kaynaklarını yönetme](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

