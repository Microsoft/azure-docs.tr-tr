---
title: Yakalama etkinken bir olay hub 'ı oluşturma-Azure Event Hubs | Microsoft Docs
description: Bir olay hub'ı ile bir Azure Event Hubs ad alanı oluşturma ve Azure Resource Manager şablonu kullanarak Yakalamayı etkinleştirme
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 5d980cefa5aa953a3fb9683adb589bf202e73250
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826290"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Olay hub’ı ile bir ad alanı oluşturma ve şablon kullanarak Yakalamayı etkinleştirm

Bu makalede, bir olay hub’ı örneği ile [Event Hubs](event-hubs-what-is-event-hubs.md) ad alanı oluşturan ve olay hub’ında [Yakalama özelliğini](event-hubs-capture-overview.md) etkinleştiren Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir. Makalede, hangi kaynakların dağıtıldığının ve dağıtım yürütülürken belirtilen parametrelerin nasıl tanımlanacağı açıklanmaktadır. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz.

Bu makalede ayrıca seçtiğiniz hedefe göre olayları Azure Storage Bloblarında veya bir Azure Data Lake Store’da yakalamayı belirteceğiniz gösterilmektedir.

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma][Authoring Azure Resource Manager templates]. Bir şablonda kullanılacak JSON sözdizimi ve özellikler için bkz. [Microsoft. EventHub kaynak türleri](/azure/templates/microsoft.eventhub/allversions).

Azure kaynakları adlandırma kurallarına ilişkin desenler ve uygulamalar hakkında daha fazla bilgi için bkz. [Azure kaynakları adlandırma kuralları][Azure Resources naming conventions].

Tam şablonlar için aşağıdaki GitHub bağlantılarına tıklayın:

- [Olay Hub 'ı ve depolama şablonu yakalamayı etkinleştir][Event Hub and enable Capture to Storage template] 
- [Olay Hub 'ı ve Azure Data Lake Store şablona yakalamayı etkinleştir][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> En son şablonları denetlemek için [Azure hızlı başlangıç şablonları][Azure Quickstart Templates] Galerisi ' ni ziyaret edin ve Event Hubs arayın.
> 
> 

## <a name="what-will-you-deploy"></a>Ne dağıtacaksınız?

Bu şablonu kullanarak bir olay hub’ı ile Event Hubs ad alanı dağıtır ve aynı zamanda [Event Hubs Yakalama](event-hubs-capture-overview.md) özelliğini etkinleştirirsiniz. Event Hubs Yakalama özelliği, tercih ettiğiniz bir süre veya boyut aralığı içinde Event Hubs’dan Azure Blob depolama alanına veya Azure Data Lake Store’a akış verilerini otomatik olarak iletmenizi sağlar. Event Hubs Yakalama özelliğini Azure Depolamada etkinleştirmek için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtma](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Event Hubs Yakalama özelliğini Azure Data Lake Store’da etkinleştirmek için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtma](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parametreler

Azure Resource Manager sayesinde, şablon dağıtıldığında belirtmek istediğiniz değerlerin parametrelerini siz tanımlarsınız. Şablon, tüm parametre değerlerini içeren `Parameters` adlı bir bölüm içerir. Dağıtmakta olduğunuz projeye veya dağıtım yaptığınız ortama göre değişen değerler için bir parametre tanımlamanız gerekir. Her zaman aynı kalan değerler için parametre tanımlamayın. Her parametre değeri, dağıtılan kaynakları tanımlamak için şablonda kullanılır.

Şablon aşağıdaki parametreleri tanımlar.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Oluşturulacak Event Hubs ad alanının adı.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Event Hubs ad alanında oluşturulan olay hub’ının adı.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

İletilerin olay hub'ında tutulacağı gün sayısı. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Olay hub'ında oluşturulacak bölüm sayısı.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Olay hub’ında Yakalama özelliğini etkinleştirir.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Olay verilerini seri hale getirmek için belirttiğiniz kodlama biçimi.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Event Hubs Yakalama özelliğinin veri yakalamaya başladığı zaman aralığı.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Yakalama özelliğinin veri yakalamaya başladığı boyut aralığı.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Event Hubs Yakalama özelliği tarafından Avro dosyalarını yazmak için kullanılan ad biçimi. Yakalama adı biçimi `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` ve `{Second}` alanlarını içermelidir. Bu, herhangi bir sırada olabilir, sınırlayıcılar tercihe bağlıdır.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

Şablonun API sürümü.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Hedef olarak Azure Depolama’yı seçerseniz aşağıdaki parametreleri kullanın.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Yakalama özelliğinin istediğiniz Depolama hesabında yakalamayı etkinleştirmesi için bir Azure Depolama hesabı kaynak kimliği gereklidir.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Olay verilerinin yakalanacağı blob kapsayıcısı.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Hedef olarak Azure Data Lake Store’u seçerseniz aşağıdaki parametreleri kullanın. Olayı yakalamak istediğiniz Data Lake Store yolunuzda izinleri ayarlamanız gerekir. İzinleri ayarlamak için [bu makaleye](event-hubs-capture-enable-through-portal.md#capture-data-to-an-azure-data-lake-store-account) bakın.

### <a name="subscriptionid"></a>subscriptionId

Azure Data Lake Store ve Event Hubs ad alanı için abonelik kimliği. Bu iki kaynağın aynı abonelik kimliği altında olması gerekir

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

Yakalanan olaylar için Azure Data Lake Store adı.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Yakalanan olaylar için hedef klasör yolu. Bu klasör, yakalama işlemi sırasında olayların gönderileceği Data Lake Store klasörüdür. Bu klasörün izinlerini ayarlamak için bkz. [Event Hubs verilerini almak için Azure Data Lake Store kullanma](../data-lake-store/data-lake-store-archive-eventhub-capture.md).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Yakalanan olaylar için hedef olarak Azure Depolama kullanıldığında dağıtılacak kaynaklar

Bir olay hub’ı ile **EventHub** türünde bir ad alanı oluşturur ve aynı zamanda Azure Blob Depolama’ya Yakalama özelliğini etkinleştirir.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Hedef olarak Azure Data Lake Store kullanıldığında dağıtılacak kaynaklar

Bir olay hub’ı ile **EventHub** türünde bir ad alanı oluşturur ve aynı zamanda Azure Data Lake Store’a Yakalama özelliğini etkinleştirir.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> **Skipemptyarchives** özelliğini kullanarak yakalama penceresinde hiçbir olay gerçekleşirken boş dosyaları yaymayı etkinleştirebilir veya devre dışı bırakabilirsiniz. 

## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Depolamada Event Hubs Yakalama özelliğini etkinleştirmek için şablonunuzu dağıtın:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Azure Data Lake Store’da Event Hubs Yakalama özelliğini etkinleştirmek için şablonunuzu dağıtın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Hedef olarak Azure Blob Depolama:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Hedef olarak Azure Data Lake Store:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs Yakalama özelliğini [Azure portalı](https://portal.azure.com) üzerinden de yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure portalını kullanarak Event Hubs Yakalama özelliğini etkinleştirme](event-hubs-capture-enable-through-portal.md).

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış](event-hubs-what-is-event-hubs.md)
* [Olay Hub’ı oluşturma](event-hubs-create.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
