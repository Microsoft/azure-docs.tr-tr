---
title: Azure Resource Manager şablonu kullanarak Service Bus yetkilendirme kuralı oluşturma | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak ad alanı ve sıra için Service Bus yetkilendirme kuralı oluşturma
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8769979fe3e5107e4ca788f65ff1e721b266776b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013073"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak ad alanı ve sıra için Service Bus yetkilendirme kuralı oluşturma

Bu makalede, bir Service Bus ad alanı ve kuyruğu için [Yetkilendirme kuralı](service-bus-authentication-and-authorization.md#shared-access-signature) oluşturan Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir. Makalesinde hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağı açıklanmaktadır. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz.

Şablon oluşturma hakkında daha fazla bilgi için lütfen bkz. [Azure Resource Manager şablonları yazma][Authoring Azure Resource Manager templates].

Tüm şablon için GitHub 'da [Service Bus yetkilendirme kuralı şablonu][Service Bus auth rule template] ' na bakın.

> [!NOTE]
> Aşağıdaki Azure Resource Manager şablonları indirme ve dağıtım için kullanılabilir.
> 
> * [Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace.md)
> * [Sıraya sahip bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-queue.md)
> * [Konu ve abonelikle bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic.md)
> * [Konu, abonelik ve kuralla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> En son şablonları denetlemek için [Azure hızlı başlangıç şablonları][Azure Quickstart Templates] Galerisi ' ni ziyaret edin ve **Service Bus**arayın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Ne dağıtacaksınız?

Bu şablonla, bir ad alanı ve mesajlaşma varlığı (Bu durumda bir kuyruk) için Service Bus yetkilendirme kuralı dağıtırsınız.

Bu şablon, kimlik doğrulaması için [paylaşılan erişim imzasını (SAS)](service-bus-sas.md) kullanır. SAS, uygulamaların ad alanında yapılandırılmış bir erişim anahtarı kullanarak veya belirli hakların ilişkilendirildiği mesajlaşma varlığında (kuyruk veya konu) Service Bus kimlik doğrulamasını sağlar. Daha sonra bu anahtarı, istemcilerin Service Bus kimlik doğrulaması için kullanabileceği bir SAS belirteci oluşturmak için kullanabilirsiniz.

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtma](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametreler

Azure Resource Manager sayesinde, şablon dağıtıldığında belirtmek istediğiniz değerlerin parametrelerini siz tanımlarsınız. Şablon, tüm parametre değerlerini içeren `Parameters` adlı bir bölümü içerir. Dağıttığınız projeye göre veya dağıttığınız ortama göre farklılık gösteren değerler için bir parametre tanımlamalısınız. Her zaman aynı kalacak değerler için parametre tanımlamayın. Her parametre değeri, dağıtılan kaynakları tanımlamak için şablonda kullanılır.

Şablon aşağıdaki parametreleri tanımlar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Oluşturulacak Service Bus ad alanının adı.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Ad alanı için yetkilendirme kuralının adı.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>Servicebussıraadı
Service Bus ad alanındaki kuyruğun adı.

```json
"serviceBusQueueName": {
"type": "string"
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
**Mesajlaşma**türünde bir standart Service Bus ad alanı ve ad alanı ve varlık için bir Service Bus yetkilendirme kuralı oluşturur.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

JSON sözdizimi ve özellikleri için bkz. [ad alanları](/azure/templates/microsoft.servicebus/namespaces), [Kuyruklar](/azure/templates/microsoft.servicebus/namespaces/queues)ve [authorizationrules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure Resource Manager kullanarak kaynak oluşturup dağıttığınıza göre, şu makaleleri görüntüleyerek bu kaynakları yönetmeyi öğrenin:

* [Service Bus’ı PowerShell ile yönetme](service-bus-powershell-how-to-provision.md)
* [Service Bus Gezgini ile Service Bus kaynaklarını yönetme](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus kimlik doğrulaması ve yetkilendirme](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
