---
title: Azure Data Factory kullanarak tahmine dayalı veri işlem hatları oluşturma
description: Azure Data Factory ' de Azure Machine Learning Batch yürütme etkinliğini kullanarak tahmine dayalı bir Işlem hattı oluşturmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 24568940a0f6e550ae0fe7658b81ba1c3b3d3556
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683772"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Azure Machine Learning ve Azure Data Factory kullanarak tahmine dayalı işlem hatları oluşturun
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Geçerli sürüm](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) tahmine dayalı analiz çözümleri oluşturmanıza, test etmenize ve dağıtmanıza olanak sağlar. Üst düzey bir görünüm noktasından üç adımda yapılır:

1. **Eğitim denemesi oluşturun**. Bu adımı Azure Machine Learning Studio 'yu kullanarak yapabilirsiniz. Azure Machine Learning Studio, eğitim verilerini kullanarak tahmine dayalı analiz modelini eğitme ve test etmek için kullandığınız işbirliğine dayalı bir görsel geliştirme ortamıdır.
2. Tahmine **dayalı bir Deneyiye dönüştürün**. Modelinize mevcut veriler ile eğitilirken ve yeni veri puanlaması için kullanmaya hazırsanız, Puanlama için denemenizi hazırlayın ve kolaylaştırın.
3. **Bunu bir Web hizmeti olarak dağıtın**. Puanlama denemenizin bir Azure Web hizmeti olarak yayımlanmasını sağlayabilirsiniz. Bu Web hizmeti uç noktası aracılığıyla modelinize veri gönderebilir ve modelden sonuç tahminleri alabilirsiniz.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory ve Machine Learning birlikte
Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. **Toplu yürütme etkinliğini** bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek için bir Azure Machine Learning Studio Web hizmeti çağırabilirsiniz.

Zaman içinde, Azure Machine Learning Studio Puanlama denemeleri 'un tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Aşağıdaki adımları uygulayarak bir modeli Data Factory işlem hattından yeniden eğitebilirsiniz:

1. Eğitim denemenize (tahmine dayalı deneme) Web hizmeti olarak yayımlayın. Bu adımı, önceki senaryoda, tahmine dayalı denemeyi bir Web hizmeti olarak kullanıma sunabileceğiniz Azure Machine Learning Studio 'da yapabilirsiniz.
2. Eğitim denemesinde Web hizmetini çağırmak için Azure Machine Learning Studio Batch yürütme etkinliğini kullanın. Temel olarak, hem eğitim Web hizmeti hem de Puanlama Web hizmetini çağırmak için Azure Machine Learning Studio Batch yürütme etkinliğini kullanabilirsiniz.

Yeniden eğitim elde ettikten sonra, **Azure Machine Learning Studio güncelleştirme kaynak etkinliğini**kullanarak Puanlama Web hizmetini (bir Web hizmeti olarak sunulan tahmine dayalı deneme) yeni eğitilen modelle güncelleştirin. Ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak modelleri güncelleştirme](update-machine-learning-models.md) .

## <a name="azure-machine-learning-linked-service"></a>Bağlı hizmet Azure Machine Learning

Bir Azure Machine Learning Web hizmetini bir Azure Data Factory 'ye bağlamak için **Azure Machine Learning** bağlı bir hizmet oluşturursunuz. Bağlı hizmet Azure Machine Learning Batch yürütme etkinliği ve [kaynak güncelleştirme](update-machine-learning-models.md)etkinliği tarafından kullanılır.

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

JSON tanımındaki özellikler hakkında açıklamalar için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi.

Azure Machine Learning, tahmine dayalı denemenize yönelik hem klasik Web hizmetlerini hem de yeni Web hizmetlerini destekler. Data Factory kullanarak doğru olanı seçebilirsiniz. Azure Machine Learning bağlı hizmeti oluşturmak için gereken bilgileri almak için, tüm (yeni) Web hizmetlerinizin ve klasik Web hizmetlerinizin listelendiği https://services.azureml.netgidin. Erişmek istediğiniz Web **hizmetine tıklayın ve sayfayı kullan** ' a tıklayın. **Apikey** özelliği Için **birincil anahtarı** ve **mlEndpoint** özelliği için **Batch isteklerini** kopyalayın.

![Azure Machine Learning Web Hizmetleri](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning Batch yürütme etkinliği

Aşağıdaki JSON kod parçacığı bir Azure Machine Learning Batch yürütme etkinliği tanımlar. Etkinlik tanımı, daha önce oluşturduğunuz Azure Machine Learning bağlı hizmete bir başvuru içerir.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Özellik          | Açıklama                              | Gerekli |
| :---------------- | :--------------------------------------- | :------- |
| ad              | İşlem hattındaki etkinliğin adı     | Evet      |
| açıklama       | Etkinliğin ne yaptığını açıklayan metin.  | Hayır       |
| type              | Data Lake Analytics U-SQL etkinliği için etkinlik türü **AzureMLBatchExecution**' dir. | Evet      |
| linkedServiceName | Azure Machine Learning bağlı hizmetine bağlı hizmetler. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Evet      |
| Webservicegirişlerinde  | Azure Machine Learning Web hizmeti girişlerinin adlarını eşleyerek anahtar, değer çiftleri. Anahtar, yayımlanan Azure Machine Learning Web hizmetinde tanımlanan giriş parametreleriyle eşleşmelidir. Değer, giriş blobu konumlarını belirten bir Azure depolama bağlı hizmetleri ve FilePath Properties çiftidir. | Hayır       |
| Webserviceçıktılar | Azure Machine Learning Web hizmeti çıktılarının adlarını eşleyerek anahtar, değer çiftleri. Anahtar, yayımlanan Azure Machine Learning Web hizmetinde tanımlanan çıkış parametreleriyle eşleşmelidir. Değer, çıkış blob konumlarını belirten bir Azure depolama bağlı hizmetleri ve FilePath Properties çiftidir. | Hayır       |
| globalParameters  | Azure Machine Learning Studio Batch yürütme hizmeti uç noktasına geçirilecek anahtar, değer çiftleri. Anahtarların yayımlanan Azure Machine Learning Studio Web hizmetinde tanımlanan Web hizmeti parametrelerinin adlarıyla eşleşmesi gerekir. Değerler Azure Machine Learning Studio Batch yürütme isteğinin GlobalParameters özelliğinde geçirilir | Hayır       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Senaryo 1: Azure Blob depolama alanındaki verilere başvuran Web hizmeti girişlerini/çıkışlarını kullanarak denemeleri

Bu senaryoda Azure Machine Learning Web hizmeti, Azure Blob depolama alanındaki bir dosyadaki verileri kullanarak tahmine dayalı hale getirir ve tahmin sonuçlarını BLOB depolama alanında depolar. Aşağıdaki JSON, bir AzureMLBatchExecution etkinliği ile Data Factory işlem hattını tanımlar. Azure blog depolamada bulunan giriş ve çıkış verilerine LinkedName ve FilePath Pair kullanılarak başvurulur. Girişlerin ve çıktıların örnek bağlantılı hizmetinde farklılık farklıysa, doğru dosyaları alabilmesi ve Azure Machine Learning Studio Web hizmeti 'ne gönderebilmek için Data Factory yönelik her bir giriş/çıkış için farklı bağlı hizmetleri kullanabilirsiniz.

> [!IMPORTANT]
> Azure Machine Learning Studio denemeniz, Web hizmeti giriş ve çıkış bağlantı noktalarında ve genel parametrelerde, özelleştirebileceğiniz varsayılan adlar ("input1", "input2") vardır. Webservicegirişlerinde, Webserviceçıktılar ve globalParameters ayarları için kullandığınız adlar, denemeleri adlarıyla tam olarak eşleşmelidir. Beklenen eşlemeyi doğrulamak için Azure Machine Learning Studio uç noktanıza yönelik toplu yürütme Yardım sayfasında örnek istek yükünü görüntüleyebilirsiniz.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Senaryo 2: çeşitli depolama alanındaki verilere başvurmak için okuyucu/yazıcı modüllerini kullanma denemeleri
Azure Machine Learning Studio denemeleri oluştururken diğer bir yaygın senaryo, veri al ve çıkış veri modüllerini kullanmaktır. Veri Içeri aktarma modülü, bir denemeye veri yüklemek için kullanılır ve çıkış verileri modülü, verileri denemeleri. Veri ve çıkış veri modüllerini Içeri aktarma hakkında daha fazla bilgi için bkz. MSDN kitaplığındaki veri ve [Çıkış verilerini](https://msdn.microsoft.com/library/azure/dn905984.aspx) [içeri aktarma](https://msdn.microsoft.com/library/azure/dn905997.aspx) konuları.

Veri alma ve çıkış verileri modüllerini kullanırken, bu modüllerin her özelliği için bir Web hizmeti parametresi kullanmak iyi bir uygulamadır. Bu Web parametreleri, çalışma zamanı sırasında değerleri yapılandırmanızı sağlar. Örneğin, bir Azure SQL veritabanı kullanan bir Içeri aktarma verileri modülü ile bir deneme oluşturabilirsiniz: XXX.database.windows.net. Web hizmeti dağıtıldıktan sonra, Web hizmetinin tüketicilerini `YYY.database.windows.net`adlı başka bir Azure SQL Server belirtmesini etkinleştirmek isteyebilirsiniz. Bu değerin yapılandırılmasına izin vermek için bir Web hizmeti parametresi kullanabilirsiniz.

> [!NOTE]
> Web hizmeti girişi ve çıkışı Web hizmeti parametrelerinden farklıdır. İlk senaryoda, bir giriş ve çıkışın bir Azure Machine Learning Studio Web hizmeti için nasıl belirtilebileceği gördünüz. Bu senaryoda, veri al/çıkış veri modüllerinin özelliklerine karşılık gelen bir Web hizmeti için parametreler geçirirsiniz.
>
>

Web hizmeti parametrelerini kullanmak için bir senaryoya göz atalım. Azure Machine Learning tarafından desteklenen veri kaynaklarından birinden veri okumak için okuyucu modülü kullanan dağıtılmış bir Azure Machine Learning Web hizmetiniz vardır (örneğin: Azure SQL veritabanı). Toplu yürütme gerçekleştirildikten sonra, sonuçlar bir yazıcı modülü (Azure SQL veritabanı) kullanılarak yazılır.  Denemeleri içinde hiçbir Web hizmeti girişi ve çıkışı tanımlanmamıştır. Bu durumda, okuyucu ve yazıcı modülleri için ilgili Web hizmeti parametrelerini yapılandırmanızı öneririz. Bu yapılandırma, AzureMLBatchExecution etkinliği kullanılırken okuyucu/yazıcı modüllerinin yapılandırılmasını sağlar. Web hizmeti parametrelerini etkinlik JSON ' daki **Globalparameters** bölümünde aşağıdaki gibi belirtirsiniz.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Web hizmeti parametreleri büyük/küçük harfe duyarlıdır; bu nedenle, JSON etkinliğinde belirttiğiniz adların Web hizmeti tarafından açığa çıkanlara eşleştiğinden emin olun.
>

Yeniden eğitim elde ettikten sonra, **Azure Machine Learning Studio güncelleştirme kaynak etkinliğini**kullanarak Puanlama Web hizmetini (bir Web hizmeti olarak sunulan tahmine dayalı deneme) yeni eğitilen modelle güncelleştirin. Ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak modelleri güncelleştirme](update-machine-learning-models.md) .

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
