---
title: Azure Data Factory kullanarak makine öğrenimi modellerini güncelleştirme
description: Azure Data Factory ve makine öğrenimi kullanarak tahmine dayalı işlem hatları oluşturma işlemini açıklar.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 3313c9c362a9b82cf7ed8db63479aaa5cf0c777e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683251"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Kaynak güncelleştirme etkinliğini kullanarak Azure Machine Learning modellerini güncelleştirme
Bu makalede, [Azure Machine Learning ve Azure Data Factory kullanarak tahmine dayalı işlem hatları oluşturma](transform-data-using-machine-learning.md)ana Azure Data Factory Azure Machine Learning tümleştirme makalesini tamamlar. Daha önce yapmadıysanız, bu makaleyi okumadan önce ana makaleyi gözden geçirin.

## <a name="overview"></a>Genel Bakış
Azure Machine Learning modellerini işlemin bir parçası olarak modelinize eğitim ve kaydedilir. Daha sonra bunu, tahmine dayalı bir Web hizmeti oluşturmak için kullanırsınız. Web hizmeti daha sonra Web sitelerinde, panolarda ve mobil uygulamalarda tüketilebilir.

Machine Learning kullanarak oluşturduğunuz modeller genellikle statik değildir. Yeni veriler kullanılabilir hale geldiğinde veya API 'nin tüketicisi kendi verilerine sahip olduğunda, modelin geri çekilmesi gerekir. Azure Machine Learning bir modeli nasıl yeniden eğitebilmeniz hakkındaki ayrıntılar için [Machine Learning modelini yeniden eğitme](../machine-learning/machine-learning-retrain-machine-learning-model.md) bölümüne bakın.

Yeniden eğitim sık gerçekleşebilir. Toplu yürütme etkinliği ve kaynak güncelleştirme etkinliği sayesinde, Data Factory kullanarak tahmine dayalı Web hizmetini yeniden eğitmek ve güncelleştirmek Azure Machine Learning modelini çalıştırabilirsiniz.

Aşağıdaki resimde, eğitim ve tahmine dayalı Web Hizmetleri arasındaki ilişki gösterilmektedir.

![Web Hizmetleri](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning kaynak etkinliğini Güncelleştir

Aşağıdaki JSON kod parçacığı bir Azure Machine Learning Batch yürütme etkinliği tanımlar.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Özellik                      | Açıklama                              | Gerekli |
| :---------------------------- | :--------------------------------------- | :------- |
| ad                          | İşlem hattındaki etkinliğin adı     | Evet      |
| açıklama                   | Etkinliğin ne yaptığını açıklayan metin.  | Hayır       |
| type                          | Azure Machine Learning kaynak güncelleştirme etkinliği için etkinlik türü **AzureMLUpdateResource**' dir. | Evet      |
| linkedServiceName             | UpdateResourceEndpoint özelliği içeren bağlı hizmet Azure Machine Learning. | Evet      |
| Traınedmodelname              | Web hizmeti denemesinde güncellenmek üzere eğitilen model modülünün adı | Evet      |
| Traınedmodellinkedservicename | Güncelleştirme işlemi tarafından karşıya yüklenen ilearner dosyasını tutan Azure depolama bağlı hizmetinin adı | Evet      |
| Traınedmodelfilepath          | Güncelleştirme işlemi tarafından karşıya yüklenen ilearner dosyasını temsil etmek için Traınedmodellinkedservice içindeki göreli dosya yolu | Evet      |

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı

İşlemin tamamına bir modeli yeniden eğitmek ve tahmine dayalı Web hizmetlerini güncelleştirmek, aşağıdaki adımları içerir:

- **Toplu yürütme etkinliğini**kullanarak **eğitim Web hizmetini** çağırın. Eğitim Web hizmetini çağırmak, [Azure Machine Learning ve toplu yürütme etkinliğini Data Factory kullanarak tahmine dayalı işlem hatları oluşturma](transform-data-using-machine-learning.md)bölümünde açıklanan bir tahmine dayalı Web hizmeti çağırma ile aynıdır. Eğitim Web hizmetinin çıktısı, tahmine dayalı Web hizmetini güncelleştirmek için kullanabileceğiniz bir iLearner dosyasıdır.
- Web hizmetini yeni eğitilen modelle güncelleştirmek için, **Kaynağı Güncelleştir etkinliğini** kullanarak tahmine **dayalı Web hizmetinin** **kaynak güncelleştirme uç noktasını** çağırın.

## <a name="azure-machine-learning-linked-service"></a>Bağlı hizmet Azure Machine Learning

Yukarıda bahsedilen uçtan uca iş akışının çalışması için iki Azure Machine Learning bağlantılı hizmet oluşturmanız gerekir:

1. Eğitim Web hizmetine Azure Machine Learning bağlı bir hizmet olan bu bağlı hizmet, toplu yürütme etkinliği tarafından, [Azure Machine Learning ve Data Factory Batch yürütmesi kullanarak tahmine dayalı işlem hatları oluşturma konusunda bahsedilen özellikler ile aynı şekilde kullanılır Etkinlik](transform-data-using-machine-learning.md). Fark, eğitim Web hizmetinin çıkışı, daha sonra tahmine dayalı Web hizmetini güncelleştirmek için kaynak güncelleştirme etkinliği tarafından kullanılan bir iLearner dosyasıdır.
2. Tahmine dayalı Web hizmetinin güncelleştirme kaynak uç noktasına Azure Machine Learning bağlı bir hizmet. Bu bağlı hizmet, yukarıdaki adımdan döndürülen iLearner dosyasını kullanarak tahmine dayalı Web hizmetini güncelleştirmek için kaynak güncelleştirme etkinliği tarafından kullanılır.

İkinci Azure Machine Learning bağlı hizmeti için, Azure Machine Learning Web hizmetiniz klasik bir Web hizmeti veya yeni bir Web hizmeti olduğunda yapılandırma farklıdır. Farklar aşağıdaki bölümlerde ayrı olarak ele alınmıştır.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web hizmeti yeni Azure Resource Manager Web hizmeti

Web hizmeti, Azure Resource Manager uç noktası sunan yeni bir Web hizmeti türüdür, ikinci **varsayılan olmayan** uç noktayı eklemeniz gerekmez. Bağlantılı hizmette bulunan **Updateresourceendpoint** şu biçimdedir:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Web hizmetini [Azure Machine Learning Web Hizmetleri portalında](https://services.azureml.net/)sorgularken URL 'deki yer tutucuları için değerler alabilirsiniz.

Güncelleştirme kaynak uç noktasının yeni türü hizmet sorumlusu kimlik doğrulaması gerektirir. Hizmet sorumlusu kimlik doğrulamasını kullanmak için, bir uygulama varlığını Azure Active Directory (Azure AD) olarak kaydedin ve aboneliğin veya Web hizmetinin ait olduğu kaynak grubunun **katkıda** bulunan veya **sahip** rolü verin. [Azure kaynağını yönetmek için hizmet sorumlusu oluşturma ve izinleri atama](../active-directory/develop/howto-create-service-principal-portal.md)bölümüne bakın. Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

- Uygulama Kimliği
- Uygulama anahtarı
- Kiracı Kimliği

Örnek bir bağlı hizmet tanımı aşağıda verilmiştir:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Aşağıdaki senaryo daha fazla ayrıntı sağlar. Bir Azure Data Factory işlem hattından Azure Machine Learning Studio modellerini yeniden eğitim ve güncelleştirme için bir örnektir.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Örnek: Azure Machine Learning modeli yeniden eğitim ve güncelleştirme

Bu bölüm, bir modeli yeniden eğitmek için **Azure Machine Learning Studio Batch yürütme etkinliğini** kullanan bir örnek işlem hattı sağlar. İşlem hattı Ayrıca, Puanlama Web hizmetindeki modeli güncelleştirmek için **Azure Machine Learning Studio güncelleştirme kaynağı etkinliğini** kullanır. Bu bölüm, örnekteki tüm bağlı hizmetler, veri kümeleri ve işlem hattı için JSON parçacıkları da sağlar.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob depolama bağlı hizmeti:
Azure depolama aşağıdaki verileri içerir:

* eğitim verileri. Azure Machine Learning Studio eğitim Web hizmeti için giriş verileri.
* iLearner dosyası. Azure Machine Learning Studio eğitim Web hizmeti 'nin çıktısı. Bu dosya aynı zamanda kaynak güncelleştirme etkinliğine giriştir.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Azure Machine Learning Studio eğitim uç noktası için bağlı hizmet
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

**Azure Machine Learning Studio**'Da, **MlEndpoint** ve **apikey**değerlerini almak için aşağıdakileri yapın:

1. Sol taraftaki menüden **Web Hizmetleri** ' ne tıklayın.
2. Web hizmetleri listesinde **eğitim Web hizmeti** ' ne tıklayın.
3. **API anahtarı** metin kutusu ' nun yanındaki Kopyala ' ya tıklayın. Panodaki anahtarı Data Factory JSON düzenleyicisine yapıştırın.
4. **Azure Machine Learning Studio**'DA **toplu yürütme** bağlantısı ' na tıklayın.
5. İstek **URI** 'sini **istek** bölümünden kopyalayın ve Data Factory JSON düzenleyicisine yapıştırın.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Azure Machine Learning Studio güncelleştirilebilir Puanlama uç noktası için bağlı hizmet:
Aşağıdaki JSON kod parçacığı, Puanlama Web hizmeti 'nin güncelleştirilebilir uç noktasını işaret eden Azure Machine Learning bağlı bir hizmeti tanımlar.

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

### <a name="pipeline"></a>İşlem hattı
İşlem hattının iki etkinliği vardır: **AzureMLBatchExecution** ve **AzureMLUpdateResource**. Toplu yürütme etkinliği eğitim verilerini giriş olarak alır ve çıkış olarak bir iLearner dosyası üretir. Kaynağı Güncelleştir etkinliği daha sonra bu iLearner dosyasını alır ve tahmine dayalı Web hizmetini güncelleştirmek için onu kullanır.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın:

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
