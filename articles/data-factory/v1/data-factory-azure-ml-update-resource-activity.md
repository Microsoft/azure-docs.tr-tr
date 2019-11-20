---
title: Azure Data Factory kullanarak Machine Learning modellerini güncelleştirme
description: Azure Data Factory ve Azure Machine Learning kullanarak tahmine dayalı işlem hatları oluşturma işlemini açıklar.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 190a4e704b002a4d6d4876d048c693a5fffe0114
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683129"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Kaynak güncelleştirme etkinliğini kullanarak Azure Machine Learning modellerini güncelleştirme

> [!div class="op_single_selector" title1="Dönüştürme etkinlikleri"]
> * [Hive etkinliği](data-factory-hive-activity.md) 
> * [Pig etkinliği](data-factory-pig-activity.md)
> * [MapReduce etkinliği](data-factory-map-reduce.md)
> * [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
> * [Spark etkinliği](data-factory-spark.md)
> * [Machine Learning Batch Yürütme Etkinliği](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Kaynak Güncelleştirme Etkinliği](data-factory-azure-ml-update-resource-activity.md)
> * [Saklı Yordam Etkinliği](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL Etkinliği](data-factory-usql-activity.md)
> * [.NET özel etkinliği](data-factory-use-custom-activities.md)


> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory makine öğrenimi modellerini güncelleştirme](../update-machine-learning-models.md).

Bu makalede, [Azure Machine Learning ve Azure Data Factory kullanarak tahmine dayalı işlem hatları oluşturma](data-factory-azure-ml-batch-execution-activity.md)ana Azure Data Factory Azure Machine Learning tümleştirme makalesini tamamlar. Daha önce yapmadıysanız, bu makaleyi okumadan önce ana makaleyi gözden geçirin. 

## <a name="overview"></a>Genel Bakış
Zaman içinde, Azure ML Puanlama denemeleri 'in tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Yeniden eğitimle işiniz bittiğinde, Puanlama Web hizmetini geri çekme ML modeliyle güncelleştirmek istiyorsunuz. Web Hizmetleri aracılığıyla yeniden eğitime ve Azure ML modellerini güncelleştirmeye yönelik tipik adımlar şunlardır:

1. [Azure ML Studio](https://studio.azureml.net)bir deneme oluşturun.
2. Modelden memnun olduğunuzda, hem **eğitim** denemesi hem de Puanlama/tahmine**dayalı deneme**için Web hizmetleri yayımlamak üzere Azure ML Studio kullanın.

Aşağıdaki tabloda, bu örnekte kullanılan Web Hizmetleri açıklanmaktadır.  Ayrıntılar için bkz. [Machine Learning modellerini programlama yoluyla yeniden eğitme](../../machine-learning/machine-learning-retrain-models-programmatically.md) .

- **Eğitim Web hizmeti** -eğitim verilerini alır ve eğitilen modeller üretir. Yeniden eğitimin çıktısı bir Azure Blob depolama alanındaki. ilearner dosyasıdır. **Varsayılan uç nokta** , Eğitim denemesini bir Web hizmeti olarak yayımladığınızda sizin için otomatik olarak oluşturulur. Daha fazla uç nokta oluşturabilirsiniz, ancak örnek yalnızca varsayılan uç noktayı kullanır.
- **Puanlama Web hizmeti** -etiketsiz veri örneklerini alır ve tahminler yapar. Tahmin çıkışının, deneme yapılandırmasına bağlı olarak, bir Azure SQL veritabanında. csv dosyası veya satırları gibi çeşitli formları olabilir. Varsayılan uç nokta, tahmine dayalı denemeyi Web hizmeti olarak yayımladığınızda sizin için otomatik olarak oluşturulur. 

Aşağıdaki resimde, Azure ML 'deki eğitim ve Puanlama uç noktaları arasındaki ilişki gösterilmektedir.

![Web Hizmetleri](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Azure ML Batch yürütme etkinliğini**kullanarak **eğitim Web hizmetini** çağırabilirsiniz. Eğitim Web hizmetini çağırmak, Puanlama verileri için bir Azure ML Web hizmeti 'ni (Puanlama Web hizmeti) çağırmada de aynıdır. Yukarıdaki bölümlerde, bir Azure ML Web hizmetinin Azure Data Factory bir işlem hattından ayrıntılı bir şekilde nasıl çağrılcağı ele alınmaktadır. 

Web hizmetini yeni eğitilen modelle güncelleştirmek için **Azure ML güncelleştirme kaynağı etkinliğini** kullanarak **Puanlama Web hizmetini** çağırabilirsiniz. Aşağıdaki örnekler bağlı hizmet tanımları sağlar: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Puanlama Web hizmeti, klasik bir Web hizmetidir
Puanlama Web hizmeti **Klasik bir Web hizmeti**ise, Azure Portal kullanarak ikinci **varsayılan olmayan ve güncelleştirilebilir uç noktası** oluşturun. Adımlar için bkz. [uç nokta oluşturma](../../machine-learning/machine-learning-create-endpoint.md) makalesi. Varsayılan olmayan güncelleştirilebilir uç noktasını oluşturduktan sonra aşağıdaki adımları uygulayın:

* **MlEndpoint** JSON ÖZELLIĞI için URI değerini almak üzere **Batch yürütmesi** ' ne tıklayın.
* **Updateresourceendpoint** JSON ÖZELLIĞI için URI değerini almak üzere **kaynak bağlantısını Güncelleştir** ' e tıklayın. API anahtarı uç nokta sayfasının kendisindedir (sağ alt köşede).

![güncelleştirilebilir uç nokta](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Aşağıdaki örnek, AzureML bağlantılı hizmeti için örnek bir JSON tanımı sağlar. Bağlı hizmet, kimlik doğrulaması için apiKey kullanır.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Puanlama Web hizmeti Azure Resource Manager Web hizmeti 
Web hizmeti, Azure Resource Manager uç noktası sunan yeni bir Web hizmeti türüdür, ikinci **varsayılan olmayan** uç noktayı eklemeniz gerekmez. Bağlantılı hizmette bulunan **Updateresourceendpoint** şu biçimdedir: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Web hizmetini [Azure Machine Learning Web Hizmetleri portalında](https://services.azureml.net/)sorgularken URL 'deki yer tutucuları için değerler alabilirsiniz. Güncelleştirme kaynak uç noktasının yeni türü bir AAD (Azure Active Directory) belirteci gerektirir. AzureML bağlantılı hizmetinde **Serviceprincipalıd** ve **servicesprincipalkey**' i belirtin. [Azure kaynağını yönetmek için hizmet sorumlusu oluşturma ve izinleri atama](../../active-directory/develop/howto-create-service-principal-portal.md)bölümüne bakın. Örnek bir AzureML bağlantılı hizmet tanımı aşağıda verilmiştir: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Aşağıdaki senaryo daha fazla ayrıntı sağlar. Azure ML modellerini bir Azure Data Factory işlem hattından yeniden eğitim ve güncelleştirme için bir örnektir.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Senaryo: bir Azure ML modelini yeniden eğitim ve güncelleştirme
Bu bölüm, bir modeli yeniden eğitmek için **Azure ML Batch yürütme etkinliğini** kullanan bir örnek işlem hattı sağlar. İşlem hattı, Puanlama Web hizmetindeki modeli güncelleştirmek için **Azure ML güncelleştirme kaynağı etkinliğini** de kullanır. Bu bölüm, örnekteki tüm bağlı hizmetler, veri kümeleri ve işlem hattı için JSON parçacıkları da sağlar.

Örnek işlem hattının Diyagram görünümü aşağıdadır. Gördüğünüz gibi, Azure ML Batch yürütme etkinliği eğitim girişini alır ve bir eğitim çıkışı (iLearner dosyası) oluşturur. Azure ML güncelleştirme kaynağı etkinliği bu eğitim çıktısını alır ve modeli Puanlama Web hizmeti uç noktasında güncelleştirir. Kaynak güncelleştirme etkinliği herhangi bir çıktı oluşturmaz. PlaceholderBlob, işlem hattını çalıştırmak için Azure Data Factory hizmetinin gerektirdiği yalnızca bir kukla çıkış veri kümesidir.

![ardışık düzen diyagramı](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob depolama bağlı hizmeti:
Azure depolama aşağıdaki verileri içerir:

* eğitim verileri. Azure ML eğitimi Web hizmeti için giriş verileri.  
* iLearner dosyası. Azure ML eğitimi Web hizmeti 'nin çıktısı. Bu dosya aynı zamanda kaynak güncelleştirme etkinliğine giriştir.  

Bağlı hizmetin örnek JSON tanımı aşağıda verilmiştir:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Eğitim girişi veri kümesi:
Aşağıdaki veri kümesi, Azure ML eğitimi Web hizmeti için giriş eğitim verilerini temsil eder. Azure ML Batch yürütme etkinliği bu veri kümesini giriş olarak alır.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Eğitim çıkış veri kümesi:
Aşağıdaki veri kümesi, Azure ML eğitimi Web hizmetinden çıkış iLearner dosyasını temsil eder. Azure ML Batch yürütme etkinliği bu veri kümesini oluşturur. Bu veri kümesi aynı zamanda Azure ML güncelleştirme kaynağı etkinliğine giriştir.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML eğitim uç noktası için bağlı hizmet
Aşağıdaki JSON kod parçacığı, eğitim Web hizmetinin varsayılan uç noktasını işaret eden Azure Machine Learning bağlı bir hizmeti tanımlar.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

**Azure ML Studio**'de **MlEndpoint** ve **apikey**değerlerini almak için aşağıdakileri yapın:

1. Sol taraftaki menüden **Web Hizmetleri** ' ne tıklayın.
2. Web hizmetleri listesinde **eğitim Web hizmeti** ' ne tıklayın.
3. **API anahtarı** metin kutusu ' nun yanındaki Kopyala ' ya tıklayın. Panodaki anahtarı Data Factory JSON düzenleyicisine yapıştırın.
4. **Azure ML Studio**'DA **Batch yürütme** bağlantısı ' na tıklayın.
5. İstek **URI** 'sini **istek** bölümünden kopyalayın ve Data Factory JSON düzenleyicisine yapıştırın.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML güncelleştirilebilir Puanlama uç noktası için bağlı hizmet:
Aşağıdaki JSON kod parçacığı, Puanlama Web hizmetinin varsayılan olmayan güncelleştirilebilir uç noktasına işaret eden Azure Machine Learning bağlı bir hizmeti tanımlar.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Yer tutucu çıkış veri kümesi:
Azure ML güncelleştirme kaynağı etkinliği herhangi bir çıktı oluşturmaz. Ancak, Azure Data Factory bir işlem hattının zamanlamasını sağlamak için bir çıktı veri kümesi gerekir. Bu nedenle, bu örnekte bir kukla/PlaceHolder veri kümesi kullanırız.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>İşlem hattı
İşlem hattının iki etkinliği vardır: **AzureMLBatchExecution** ve **AzureMLUpdateResource**. Azure ML Batch yürütme etkinliği eğitim verilerini giriş olarak alır ve çıkış olarak bir iLearner dosyası üretir. Etkinlik, eğitim Web hizmetini (bir Web hizmeti olarak sunulan eğitim denemesine) giriş eğitim verileriyle çağırır ve IBU dosya hizmetten ilearner dosyasını alır. PlaceholderBlob, işlem hattını çalıştırmak için Azure Data Factory hizmetinin gerektirdiği yalnızca bir kukla çıkış veri kümesidir.

![ardışık düzen diyagramı](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
