---
title: Azure Data Factory kullanarak Azure Machine Learning Studio (klasik) modelleri güncelleştirme
description: Azure Data Factory ve Azure Machine Learning Studio kullanarak tahmine dayalı işlem hatları oluşturmayı açıklar (klasik)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 5eac6f939ffcc39961f508a993ca17757221503f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783107"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Kaynak güncelleştirme etkinliğini kullanarak Azure Machine Learning Studio (klasik) modellerini güncelleştirme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makale, ana Azure Data Factory Azure Machine Learning Studio (klasik) tümleştirme makalesini tamamlar: [Azure Machine Learning Studio (klasik) ve Azure Data Factory kullanarak tahmine dayalı işlem hatları oluşturun](transform-data-using-machine-learning.md). Daha önce yapmadıysanız, bu makaleyi okumadan önce ana makaleyi gözden geçirin.

## <a name="overview"></a>Genel Bakış
İşlem Azure Machine Learning Studio (klasik) modellerinin bir parçası olarak modelinize eğitim ve kaydedilir. Daha sonra bunu, tahmine dayalı bir Web hizmeti oluşturmak için kullanırsınız. Web hizmeti daha sonra Web sitelerinde, panolarda ve mobil uygulamalarda tüketilebilir.

Azure Machine Learning Studio (klasik) kullanarak oluşturduğunuz modeller genellikle statik değildir. Yeni veriler kullanılabilir hale geldiğinde veya API 'nin tüketicisi kendi verilerine sahip olduğunda, modelin geri çekilmesi gerekir. 

Yeniden eğitim sık gerçekleşebilir. Toplu yürütme etkinliği ve kaynak güncelleştirme etkinliği sayesinde, Azure Machine Learning Studio (klasik) modeli yeniden eğitimi ve Data Factory kullanarak tahmine dayalı Web hizmetini güncelleştirme Işlemini gerçekleştirebilirsiniz.

Aşağıdaki resimde, eğitim ve tahmine dayalı Web Hizmetleri arasındaki ilişki gösterilmektedir.

![Web hizmetleri](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Azure Machine Learning Studio (klasik) kaynak güncelleştirme etkinliği

Aşağıdaki JSON kod parçacığı Azure Machine Learning Studio (klasik) toplu yürütme etkinliğini tanımlar.

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
| name                          | İşlem hattındaki etkinliğin adı     | Yes      |
| açıklama                   | Etkinliğin ne yaptığını açıklayan metin.  | No       |
| tür                          | Azure Machine Learning Studio (klasik) kaynak güncelleştirme etkinliği için etkinlik türü  **AzureMLUpdateResource**' dir. | Yes      |
| linkedServiceName             | UpdateResourceEndpoint özelliği içeren Azure Machine Learning Studio (klasik) bağlı hizmet. | Yes      |
| Traınedmodelname              | Web hizmeti denemesinde güncellenmek üzere eğitilen model modülünün adı | Yes      |
| Traınedmodellinkedservicename | Güncelleştirme işlemi tarafından karşıya yüklenen ilearner dosyasını tutan Azure depolama bağlı hizmetinin adı | Yes      |
| Traınedmodelfilepath          | Güncelleştirme işlemi tarafından karşıya yüklenen ilearner dosyasını temsil etmek için Traınedmodellinkedservice içindeki göreli dosya yolu | Yes      |

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı

İşlemin tamamına bir modeli yeniden eğitmek ve tahmine dayalı Web hizmetlerini güncelleştirmek, aşağıdaki adımları içerir:

- **Toplu yürütme etkinliğini** kullanarak **eğitim Web hizmetini** çağırın. Eğitim Web hizmetini çağırmak, [Azure Machine Learning Studio (klasik) ve Data Factory toplu yürütme etkinliğini kullanarak tahmine dayalı işlem hatları oluşturma](transform-data-using-machine-learning.md)bölümünde açıklanan tahmine dayalı bir Web hizmetini çağırmaya benzer. Eğitim Web hizmetinin çıktısı, tahmine dayalı Web hizmetini güncelleştirmek için kullanabileceğiniz bir iLearner dosyasıdır.
- Web hizmetini yeni eğitilen modelle güncelleştirmek için, **Kaynağı Güncelleştir etkinliğini** kullanarak tahmine **dayalı Web hizmetinin** **kaynak güncelleştirme uç noktasını** çağırın.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasik) bağlı hizmet

Yukarıda bahsedilen uçtan uca iş akışının çalışması için iki Azure Machine Learning Studio (klasik) bağlantılı hizmet oluşturmanız gerekir:

1. Eğitim Web hizmetine bir Azure Machine Learning Studio (klasik) bağlı hizmeti olan bu bağlı hizmet, toplu yürütme etkinliği tarafından, [Azure Machine Learning Studio (klasik) ve Data Factory toplu yürütme etkinliğini kullanarak tahmine dayalı işlem hatları oluşturma](transform-data-using-machine-learning.md)konusunda bahsedilen özellikler ile aynı şekilde kullanılır. Fark, eğitim Web hizmetinin çıkışı, daha sonra tahmine dayalı Web hizmetini güncelleştirmek için kaynak güncelleştirme etkinliği tarafından kullanılan bir iLearner dosyasıdır.
2. Tahmine dayalı Web hizmetinin güncelleştirme kaynak uç noktasına Azure Machine Learning Studio (klasik) bağlantılı hizmet. Bu bağlı hizmet, yukarıdaki adımdan döndürülen iLearner dosyasını kullanarak tahmine dayalı Web hizmetini güncelleştirmek için kaynak güncelleştirme etkinliği tarafından kullanılır.

İkinci Azure Machine Learning Studio (klasik) bağlı hizmeti için, Azure Machine Learning Studio (klasik) Web hizmetiniz klasik bir Web hizmeti veya yeni bir Web hizmeti olduğunda yapılandırma farklıdır. Farklar aşağıdaki bölümlerde ayrı olarak ele alınmıştır.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web hizmeti yeni Azure Resource Manager Web hizmeti

Web hizmeti, Azure Resource Manager uç noktası sunan yeni bir Web hizmeti türüdür, ikinci **varsayılan olmayan** uç noktayı eklemeniz gerekmez. Bağlantılı hizmette bulunan **Updateresourceendpoint** şu biçimdedir:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Web hizmetini [Azure Machine Learning Studio (klasik) Web Hizmetleri portalında](https://services.azureml.net/)sorgularken URL 'deki yer tutucuları için değerler alabilirsiniz.

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

Aşağıdaki senaryo daha fazla ayrıntı sağlar. Bir Azure Data Factory işlem hattından yeniden eğitim ve Azure Machine Learning Studio (klasik) modellerini güncelleştirme örneği vardır.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Örnek: Azure Machine Learning Studio (klasik) modeli yeniden eğitim ve güncelleştirme

Bu bölüm, bir modeli yeniden eğitmek için **Azure Machine Learning Studio (klasik) toplu yürütme etkinliğini** kullanan bir örnek işlem hattı sağlar. İşlem hattı Ayrıca, Puanlama Web hizmetindeki modeli güncelleştirmek için **Azure Machine Learning Studio (klasik) kaynak güncelleştirme etkinliğini** kullanır. Bu bölüm, örnekteki tüm bağlı hizmetler, veri kümeleri ve işlem hattı için JSON parçacıkları da sağlar.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob depolama bağlı hizmeti:
Azure depolama aşağıdaki verileri içerir:

* eğitim verileri. Azure Machine Learning Studio (klasik) eğitim Web hizmeti için giriş verileri.
* iLearner dosyası. Azure Machine Learning Studio (klasik) eğitim Web hizmetinden alınan çıkış. Bu dosya aynı zamanda kaynak güncelleştirme etkinliğine giriştir.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Azure Machine Learning Studio (klasik) eğitim uç noktası için bağlı hizmet
Aşağıdaki JSON kod parçacığı, eğitim Web hizmetinin varsayılan uç noktasını işaret eden Azure Machine Learning Studio (klasik) bağlı bir hizmeti tanımlar.

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

**Azure Machine Learning Studio (klasik)** Içinde, **MlEndpoint** ve **apikey** değerlerini almak için aşağıdakileri yapın:

1. Sol taraftaki menüden **Web Hizmetleri** ' ne tıklayın.
2. Web hizmetleri listesinde **eğitim Web hizmeti** ' ne tıklayın.
3. **API anahtarı** metin kutusu ' nun yanındaki Kopyala ' ya tıklayın. Panodaki anahtarı Data Factory JSON düzenleyicisine yapıştırın.
4. **Azure Machine Learning Studio (klasik)** ÖĞESINDE **toplu yürütme** bağlantısı ' na tıklayın.
5. İstek **URI** 'sini **istek** bölümünden kopyalayın ve Data Factory JSON düzenleyicisine yapıştırın.

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Azure Machine Learning Studio (klasik) güncelleştirilebilir Puanlama uç noktası için bağlı hizmet:
Aşağıdaki JSON kod parçacığı, Puanlama Web hizmetinin güncelleştirilebilir uç noktasını işaret eden Azure Machine Learning Studio (klasik) bağlı bir hizmeti tanımlar.

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

### <a name="pipeline"></a>İşlem Hattı
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
