---
title: Data Factory Kaynak Yöneticisi şablonları kullanma
description: Data Factory varlıkları oluşturmak için Azure Resource Manager şablonlarını oluşturma ve kullanma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d9ee060b7d1f7a443357c7116c01e8aaf972e666
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682529"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Azure Data Factory varlıkları oluşturmak için şablonları kullanma
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. 

## <a name="overview"></a>Genel Bakış
Veri tümleştirme gereksinimleriniz için Azure Data Factory kullanırken, kendinizi farklı ortamlarda aynı kalıbı yeniden kullanmayı veya aynı çözüm içinde aynı görev kaldı uygulamayı de bulabilirsiniz. Şablonlar, bu senaryoları kolay bir şekilde uygulamanıza ve yönetmenize yardımcı olur. Azure Data Factory şablonlar, yeniden kullanılabilirlik ve yineleme gerektiren senaryolar için idealdir.

Bir kuruluşun dünya genelinde 10 üretim bitkiler olduğu durumu göz önünde bulundurun. Her bir bitki günlüğü ayrı bir şirket içi SQL Server veritabanında depolanır. Şirket, geçici analiz için bulutta tek bir veri ambarı oluşturmak istiyor. Ayrıca, geliştirme, test ve üretim ortamları için aynı mantığa ancak farklı yapılandırmalara sahip olmak istiyor.

Bu durumda, bir görevin aynı ortamda tekrarlanması gerekir, ancak her bir üretim tesisi için 10 veri fabrikasının içinde farklı değerler vardır. Aslında, **yineleme** vardır. Şablon oluşturma, bu genel akışın soyutlamasını (yani, her veri fabrikasında aynı etkinliklere sahip olan işlem hatları) sağlar, ancak her üretim tesisi için ayrı bir parametre dosyası kullanır.

Ayrıca, kuruluş bu 10 veri fabrikalarını farklı ortamlarda birden çok kez dağıtmak istediğinde, Şablonlar geliştirme, test ve üretim ortamları için ayrı parametre dosyalarından yararlanarak bu **yeniden kullanılabilirliği** kullanabilir.

## <a name="templating-with-azure-resource-manager"></a>Azure Resource Manager ile şablon oluşturma
[Azure Resource Manager şablonlar](../../azure-resource-manager/template-deployment-overview.md) , Azure Data Factory şablon oluşturma elde etmenin harika bir yoludur. Kaynak Yöneticisi şablonları bir JSON dosyası aracılığıyla Azure çözümünüzün altyapısını ve yapılandırmasını tanımlar. Azure Resource Manager şablonlar tüm/Azure hizmetleriyle çalıştığı için, Azure varlıklarınızın tüm kaynaklarını kolayca yönetmek için yaygın olarak kullanılabilir. Genel olarak Kaynak Yöneticisi şablonları hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager şablonları yazma](../../azure-resource-manager/resource-group-authoring-templates.md) .

## <a name="tutorials"></a>Öğreticiler
Kaynak Yöneticisi şablonları kullanarak Data Factory varlıkları oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticilere bakın:

* [Öğretici: Azure Resource Manager şablonu kullanarak verileri kopyalamak için bir işlem hattı oluşturma](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Öğretici: Azure Resource Manager şablonu kullanarak verileri işlemek için bir işlem hattı oluşturma](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>GitHub 'da Data Factory şablonları
GitHub 'da aşağıdaki Azure hızlı başlangıç şablonlarına göz atın:

* [Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalamak için bir veri fabrikası oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Azure HDInsight kümesinde Hive etkinliği ile bir veri fabrikası oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Salesforce 'tan Azure Bloblarına veri kopyalamak için bir veri fabrikası oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Etkinlikleri zincirlendirmek için bir veri fabrikası oluşturun: bir FTP sunucusundan Azure Blob 'larına veri kopyalar, verileri dönüştürmek için isteğe bağlı HDInsight kümesinde bir Hive betiği çağırır ve sonucu Azure SQL veritabanı 'na kopyalar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Azure Data Factory şablonlarınızı [Azure hızlı başlangıç](https://azure.microsoft.com/documentation/templates/)aşamasında paylaşabilirsiniz. Bu depo aracılığıyla paylaşılabilen şablonlar geliştirirken [katkı kılavuzuna](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) bakın.

Aşağıdaki bölümlerde, Kaynak Yöneticisi şablonunda Data Factory kaynaklarını tanımlama hakkında ayrıntılar sağlanmaktadır.

## <a name="defining-data-factory-resources-in-templates"></a>Şablonlarda Data Factory kaynaklarını tanımlama
Veri Fabrikası tanımlamak için en üst düzey şablon:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Veri fabrikası tanımlama
Resource Manager şablonunda bir veri fabrikasını aşağıdaki örnekte gösterildiği gibi tanımlayın:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
DataFactoryName şu şekilde "değişkenler" içinde tanımlanmıştır:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Bağlı Hizmetleri tanımlama

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Dağıtmak istediğiniz belirli bağlı hizmetin JSON özellikleri hakkında ayrıntılı bilgi için bkz. [depolama bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service) veya [işlem bağlantılı hizmetleri](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . "Bağımlıdson" parametresi karşılık gelen veri fabrikasının adını belirtir. Azure depolama için bağlı bir hizmet tanımlama örneği aşağıdaki JSON tanımında gösterilmektedir:

### <a name="define-datasets"></a>Veri kümelerini tanımlama

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Dağıtmak istediğiniz belirli veri kümesi türünün JSON özellikleri hakkındaki ayrıntılar için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) ' na bakın. "Bağımlıdson" parametresi karşılık gelen veri fabrikası ve depolama bağlı hizmetinin adını belirtir. Azure Blob Storage 'ın veri kümesi türünü tanımlama örneği aşağıdaki JSON tanımında gösterilmektedir:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>İşlem hatlarını tanımlama

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Dağıtmak istediğiniz belirli işlem hattını ve etkinlikleri tanımlamaya yönelik JSON özellikleriyle ilgili ayrıntılar için işlem [hatlarını tanımlama](data-factory-create-pipelines.md#pipeline-json) bölümüne bakın. "Bağımlıdson" parametresi, veri fabrikasının adını ve buna karşılık gelen bağlı hizmetleri ya da veri kümelerini belirtir. Azure Blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattı örneği aşağıdaki JSON kod parçacığında gösterilmektedir:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Data Factory şablonu parametrize etme
Parametreleştirmede en iyi uygulamalar için bkz. [Azure Resource Manager şablonları oluşturmak Için en iyi uygulamalar](../../azure-resource-manager/resource-manager-template-best-practices.md). Genellikle, bunun yerine değişkenlerin kullanılabilmesi için parametre kullanımı simge durumuna küçültülmüş olmalıdır. Yalnızca aşağıdaki senaryolarda parametreleri sağlayın:

* Ayarlar ortama göre farklılık gösterir (örnek: geliştirme, test ve üretim)
* Gizli dizileri (parolalar gibi)

Şablonları kullanarak Azure Data Factory varlıkları dağıttığınızda [Azure Key Vault](../../key-vault/key-vault-overview.md) gizli dizileri çekmeniz gerekiyorsa, aşağıdaki örnekte gösterildiği gibi **Anahtar Kasası** ve **gizli adı** belirtin:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Mevcut veri fabrikaları için şablonları dışarı aktarma işlemi henüz desteklenmediğinden, bu, çalışıyor.
>
>
