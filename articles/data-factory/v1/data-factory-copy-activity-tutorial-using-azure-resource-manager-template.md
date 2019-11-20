---
title: 'Öğretici: Kaynak Yöneticisi şablonu kullanarak işlem hattı oluşturma '
description: Bu öğreticide, Azure Resource Manager şablonu kullanarak bir Azure Data Factory işlem hattı oluşturacaksınız. İşlem hattı, verileri Azure blob depolama alanlarından Azure SQL veritabanlarına kopyalar.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a4f1c2d128e41e13a6e57732e8f97ff4c250d0a1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682899"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Öğretici: Verileri kopyalamak üzere bir Data Factory işlem hattı oluşturmak için Azure Resource Manager şablonunu kullanma 
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 

Bu öğreticide, Azure Resource Manager şablonu kullanarak bir Azure veri fabrikasını nasıl oluşturacağınız ve izleyeceğiniz gösterilmektedir. Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Çıkış verileri üretmek için giriş verilerini dönüştürmez. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md).

Bu öğreticide, içinde bir etkinlik olan işlem hattı oluşturursunuz: Kopyalama Etkinliği. Kopyalama etkinliği, verileri, desteklenen bir veri deposundan desteklenen bir havuz veri deposuna kopyalar. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama Etkinliği hakkında daha fazla bilgi için bkz. [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md).

Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [bir işlem hattında birden fazla etkinlik](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Öğreticiye Genel Bakış ve Ön Koşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) bölümünü inceleyin ve **ön koşul** adımlarını tamamlayın.
* Bilgisayarınıza Azure PowerShell’in en son sürümünü yüklemek için [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview) makalesindeki yönergeleri izleyin. Bu öğreticide PowerShell’i kullanarak Data Factory varlıklarını dağıtırsınız. 
* (isteğe bağlı) Azure Resource Manager şablonları hakkında bilgi için bkz. [Azure Resource Manager Şablonları Yazma](../../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="in-this-tutorial"></a>Bu öğreticide
Bu öğreticide, aşağıdaki Data Factory varlıklarıyla bir veri fabrikası oluşturursunuz:

| Varlık | Açıklama |
| --- | --- |
| Azure Storage bağlı hizmeti |Azure Storage hesabınızı veri fabrikasına bağlar. Öğreticideki kopyalama etkinliği için Azure Storage kaynak veri deposu, Azure SQL veritabanı ise havuz veri deposudur. Kopyalama etkinliği için giriş verilerini içeren depolama hesabını belirtir. |
| Azure SQL Veritabanı bağlı hizmeti |Azure SQL veritabanınızı veri fabrikasına bağlar. Kopyalama etkinliği için çıktı verilerini tutan Azure SQL veritabanını belirtir. |
| Azure Blob giriş veri kümesi |Azure Storage bağlı hizmetini ifade eder. Bağlı hizmet bir Azure Storage hesabını belirtirken, Azure Blob veri kümesi girdi verilerini tutan depolama birimindeki kapsayıcı, klasör ve dosya adını belirtir. |
| Azure SQL çıktı veri kümesi |Azure SQL bağlı hizmetini ifade eder. Azure SQL bağlı hizmeti bir Azure SQL sunucusunu, Azure SQL veri kümesi ise çıktı verilerini tutan tablonun adını belirtir. |
| Veri işlem hattı |İşlem hattı, Azure blob veri kümesini girdi, Azure SQL veri kümesini ise çıktı olarak alan Kopyalama türünde bir etkinliğe sahiptir. Kopyalama etkinliği, verileri bir Azure blob’undan Azure SQL veritabanındaki tabloya kopyalar. |

Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. İki tür etkinlik mevcuttur: [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) ve [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md). Bu öğreticide bir etkinlik (kopyalama etkinliği) ile işlem hattı oluşturursunuz.

![Azure Blob’u Azure SQL Veritabanına kopyalama](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

Aşağıdaki bölümde, öğreticiyi hızlıca geçip şablonu test etmeniz için Data Factory varlıklarını tanımlamaya yönelik tam bir Resource Manager şablonu verilmektedir. Her bir Data Factory varlığının nasıl tanımlandığını anlamak için [Şablondaki Data Factory varlıkları](#data-factory-entities-in-the-template) bölümüne bakın.

## <a name="data-factory-json-template"></a>Data Factory JSON şablonu
Bir veri fabrikasını tanımlamaya yönelik en üst düzey Resource Manager şablonu şudur: 

```json
{
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
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
**C:\ADFGetStarted** klasöründe aşağıdaki içeriklerle birlikte **ADFCopyTutorialARM.json** adlı bir JSON dosyası oluşturun:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
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
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parametreler JSON
Azure Resource Manager şablonuna yönelik parametreleri içeren **ADFCopyTutorialARM-Parameters.json** adlı bir JSON dosyası oluşturun. 

> [!IMPORTANT]
> storageAccountName ve storageAccountKey parametreleri için Azure Depolama hesabınızın adını ve anahtarını belirtin.  
> 
> sqlServerName, databaseName, sqlServerUserName ve sqlServerPassword parametreleri için Azure SQL sunucusunu, veritabanını, kullanıcıyı ve parolayı belirtin.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> Aynı Data Factory JSON şablonuyla birlikte kullanabileceğiniz geliştirme, test ve üretim ortamları için ayrı parametre JSON dosyalarına sahip olabilirsiniz. Bir Power Shell komut dosyası kullanarak, bu ortamlarda Data Factory varlıklarını dağıtmayı otomatik hale getirebilirsiniz.  
> 
> 

## <a name="create-data-factory"></a>Veri fabrikası oluşturma
1. **Azure PowerShell**’i başlatın ve aşağıdaki komutu çalıştırın:
   * Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin.
   
    ```PowerShell
    Connect-AzAccount       
    ```  
   * Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın.
   
    ```PowerShell
    Get-AzSubscription
    ```   
   * Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın.
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. 1 Adımda oluşturduğunuz Resource Manager şablonunu kullanarak Data Factory varlıklarını dağıtmak için aşağıdaki komutu çalıştırın.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>İşlem hattını izleme

1. Azure hesabınızı kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol menüdeki **Veri fabrikaları**’na tıklayın (veya) **INTELLIGENCE + ANALYTICS** kategorisi altındaki **Tüm hizmetler** ve **Veri fabrikaları** öğelerine tıklayın.
   
    ![Veri fabrikaları menüsü](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. **Veri fabrikaları** sayfasında veri fabrikanızı (AzureBlobToAzureSQLDatabaseDF) bulun. 
   
    ![Veri fabrikası arama](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Azure veri fabrikanıza tıklayın. Veri fabrikasının giriş sayfasını görürsünüz.
   
    ![Veri fabrikasının giriş sayfası](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
6. Bu öğreticide oluşturduğunuz işlem hattını ve veri kümelerini izlemek için [Veri kümelerini ve işlem hatlarını izleme](data-factory-monitor-manage-pipelines.md) makalesindeki yönergeleri izleyin. Visual Studio şu anda Data Factory işlem hatlarını izlemeyi desteklememektedir.
7. Bir dilim **Hazır** durumundayken verilerin Azure SQL veritabanındaki **emp** tablosuna kopyalandığını doğrulayın.


Bu öğreticide oluşturduğunuz işlem hattını ve veri kümelerini izlemek üzere Azure portalı dikey pencerelerinin kullanımına ilişkin daha fazla bilgi için bkz. [Veri kümelerini ve işlem hatlarını izleme](data-factory-monitor-manage-pipelines.md).

Veri işlem hatlarınızı izlemek üzere İzleme ve Yönetme uygulamasının kullanımına ilişkin daha fazla bilgi için bkz. [İzleme Uygulamasını kullanarak Azure Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md).

## <a name="data-factory-entities-in-the-template"></a>Şablondaki Data Factory varlıkları
### <a name="define-data-factory"></a>Veri fabrikası tanımlama
Resource Manager şablonunda bir veri fabrikasını aşağıdaki örnekte gösterildiği gibi tanımlayın:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```

dataFactoryName aşağıdaki gibi tanımlanır: 

```json
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

Bu değer, kaynak grubu kimliğini temel alan benzersiz bir dizedir.  

### <a name="defining-data-factory-entities"></a>Data Factory varlıklarını tanımlama
Aşağıdaki Data Factory varlıkları JSON şablonunda tanımlanır: 

1. [Azure Storage bağlı hizmeti](#azure-storage-linked-service)
2. [Azure SQL bağlı hizmeti](#azure-sql-database-linked-service)
3. [Azure blob veri kümesi](#azure-blob-dataset)
4. [Azure SQL veri kümesi](#azure-sql-dataset)
5. [Kopyalama etkinliği içeren bir veri işlem hattı](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti
AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bir kapsayıcı oluşturup verileri [önkoşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak bu depolama hesabına yüklediniz. Bu bölümde Azure depolama hesabınızın adını ve anahtarını belirtirsiniz. Bir Azure Storage bağlı hizmetini tanımlamak için kullanılan JSON özelliklerine ilişkin ayrıntılar için bkz. [Azure Storage bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

ConnectionString, storageAccountName ve storageAccountKey parametrelerini kullanır. Bu parametrelerin değerleri bir yapılandırma dosyası kullanılarak geçirilir. Tanım ayrıca şablonda tanımlanan değişkenleri kullanır: azureStorageLinkedService ve dataFactoryName. 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti
AzureSqlLinkedService, Azure SQL veritabanınızı veri fabrikasına bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki emp tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz. Bu bölümde Azure SQL sunucu adı, veritabanı adı, kullanıcı adı ve kullanıcı parolasını belirtirsiniz. Bir Azure SQL bağlı hizmetini tanımlamak için kullanılan JSON özelliklerine ilişkin ayrıntılar için bkz. [Azure SQL bağlı hizmeti](data-factory-azure-sql-connector.md#linked-service-properties).  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString; değerleri bir yapılandırma dosyası kullanılarak geçirilen sqlServerName, databaseName, sqlServerUserName ve sqlServerPassword parametrelerini kullanır. Tanımda ayrıca şablondaki şu değişkenler kullanılır: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Azure blob veri kümesi
Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Azure blob veri kümesi tanımında blob kapsayıcısını, klasörü ve giriş verilerini içeren dosyanın adını belirtirsiniz. Bir Azure Blob veri kümesini tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar için bkz. [Azure Blob veri kümesi özellikleri](data-factory-azure-blob-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL veri kümesi
Azure SQL veritabanında Azure Blob depolama biriminden kopyalanan verileri tutan tablonun adını belirtirsiniz. Bir Azure SQL veri kümesini tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar için bkz. [Azure SQL veri kümesi özellikleri](data-factory-azure-sql-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Veri işlem hattı
Verileri Azure blob veri kümesinden Azure SQL veri kümesine kopyalayan bir işlem hattı tanımlayın. Bu örnekte bir işlem hattı tanımlamak için kullanılan JSON öğelerinin açıklamaları için bkz. [İşlem Hattı JSON](data-factory-create-pipelines.md#pipeline-json). 

```json
{
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
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>Şablonu yeniden kullanma
Bu öğreticide, Data Factory varlıkları tanımlamaya yönelik bir şablon ve parametrelerin değerlerini geçirmeye yönelik bir şablon oluşturdunuz. İşlem hattı, verileri bir Azure Storage hesabından parametreler aracılığıyla belirtilen bir Azure SQL veritabanına kopyalar. Data Factory varlıklarını farklı ortamlara dağıtmak üzere aynı şablonu kullanmak için, her bir ortama yönelik bir parametre dosyası oluşturur ve bu ortama dağıtırken kullanırsınız.     

Örnek:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Birinci komut geliştirme ortamına, ikinci komut test ortamına ve üçüncü komut üretim ortamına yönelik parametre dosyasını kullanır.  

Ayrıca tekrarlanan görevleri gerçekleştirmek için şablonu yeniden kullanabilirsiniz. Örneğin, aynı mantığı uygulamasına karşın her veri fabrikasının farklı Depolama ve SQL Veritabanı hesaplarını kullandığı bir veya daha fazla işlem hattı ile çok sayıda veri fabrikası oluşturmanız gerekir. Bu senaryoda, aynı şablonu veri fabrikaları oluşturmaya yönelik farklı parametre dosyaları ile aynı ortamda (geliştirme, test veya üretim) kullanırsınız.   

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir kopyalama işleminde kaynak veri deposu olarak Azure blob depolama alanını ve hedef veri deposu olarak Azure SQL veritabanını kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynaklar ve hedefler olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Veri deposundan/veri deposuna veri kopyalama hakkında bilgi edinmek için tablodaki veri deposunun bağlantısına tıklayın.
