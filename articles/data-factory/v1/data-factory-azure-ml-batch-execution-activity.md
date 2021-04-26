---
title: Azure Data Factory kullanarak tahmine dayalı veri işlem hatları oluşturma
description: Azure Data Factory ve Azure Machine Learning Studio kullanarak tahmine dayalı işlem hatları oluşturma işlemini açıklar (klasik)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2773ab2a7caa1eb4d198495a3ebe4ef0c14a5a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785521"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Azure Machine Learning Studio (klasik) ve Azure Data Factory kullanarak tahmine dayalı işlem hatları oluşturun

> [!div class="op_single_selector" title1="Dönüştürme etkinlikleri"]
> * [Hive etkinliği](data-factory-hive-activity.md)
> * [Pig etkinliği](data-factory-pig-activity.md)
> * [MapReduce etkinliği](data-factory-map-reduce.md)
> * [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
> * [Spark etkinliği](data-factory-spark.md)
> * [Azure Machine Learning Studio (klasik) Batch Yürütme Etkinliği](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (klasik) Kaynak Güncelleştirme Etkinliği](data-factory-azure-ml-update-resource-activity.md)
> * [Saklı Yordam Etkinliği](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL Etkinliği](data-factory-usql-activity.md)
> * [.NET özel etkinliği](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Giriş
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory makine öğrenimi kullanarak verileri dönüştürme](../transform-data-using-machine-learning.md).

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik)
[Azure Machine Learning Studio (klasik)](https://azure.microsoft.com/documentation/services/machine-learning/) tahmine dayalı analiz çözümleri oluşturmanıza, test etmenize ve dağıtmanıza olanak sağlar. Üst düzey bir görünüm noktasından üç adımda yapılır:

1. **Eğitim denemesi oluşturun**. Bu adımı Azure Machine Learning Studio (klasik) kullanarak yapabilirsiniz. Studio (klasik), eğitim verilerini kullanarak tahmine dayalı analiz modelini eğitme ve test etmek için kullandığınız işbirliğine dayalı bir görsel geliştirme ortamıdır.
2. Tahmine **dayalı bir Deneyiye dönüştürün**. Modelinize mevcut veriler ile eğitilirken ve yeni veri puanlaması için kullanmaya hazırsanız, Puanlama için denemenizi hazırlayın ve kolaylaştırın.
3. **Bunu bir Web hizmeti olarak dağıtın**. Puanlama denemenizin bir Azure Web hizmeti olarak yayımlanmasını sağlayabilirsiniz. Bu Web hizmeti uç noktası aracılığıyla modelinize veri gönderebilir ve bu modeli elde edebilir sonuç tahminleri alabilirsiniz.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory, verilerin **hareketini** ve **dönüştürülmesini** düzenleyen ve otomatikleştiren bulut tabanlı bir veri tümleştirme hizmetidir. Çeşitli veri depolarından veri alabilen, verileri dönüştürebilen/işleyecek ve sonuç verilerini veri depolarında yayımlayabilen Azure Data Factory kullanarak veri tümleştirme çözümleri oluşturabilirsiniz.

Data Factory hizmeti verileri taşıyıp dönüştüren ve ardından işlem hattını belirli bir zamanlamaya (saatlik, günlük, haftalık, vb.) göre çalıştıran veri işlem hatları oluşturmanıza imkan tanır. Ayrıca, veri işlem hatlarınız arasındaki çizgileri ve bağımlılıkları gösteren ve sorunları kolayca saptamak ve izleme uyarılarını ayarlamak üzere tek bir birleşik görünümden tüm veri işlem hatlarınızı izleyen zengin görsel öğeler sağlar.

Azure Data Factory hizmetini hızlıca kullanmaya başlamak için bkz. [Azure Data Factory giriş](data-factory-introduction.md) ve [ilk işlem hattı makalelerinizi oluşturma](data-factory-build-your-first-pipeline.md) .

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory ve Machine Learning Studio (klasik) birlikte
Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir [Azure Machine Learning Studio (klasik)][azure-machine-learning] Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. **Toplu yürütme etkinliğini** bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek Için bir Studio (klasik) Web hizmeti çağırabilirsiniz. Ayrıntılar için bkz. Batch yürütme etkinliği bölümünü kullanarak Azure Machine Learning Studio (klasik) Web hizmeti çağırma.

Zaman içinde, Studio (klasik) Puanlama denemeleri 'in tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Aşağıdaki adımları uygulayarak bir Data Factory işlem hattından bir Studio (klasik) modeli yeniden eğitebilirsiniz:

1. Eğitim denemenize (tahmine dayalı deneme) Web hizmeti olarak yayımlayın. Bu adımı, önceki senaryoda, tahmine dayalı denemeyi bir Web hizmeti olarak kullanıma sunabileceğiniz için Studio 'da (klasik) yapabilirsiniz.
2. Eğitim denemesinde Web hizmetini çağırmak için Studio (klasik) toplu yürütme etkinliğini kullanın. Temel olarak, hem eğitim Web hizmeti hem de Puanlama Web hizmetini çağırmak için Studio (klasik) toplu yürütme etkinliğini kullanabilirsiniz.

Yeniden eğitim elde ettikten sonra, **Azure Machine Learning Studio (klasik) güncelleştirme kaynak etkinliğini** kullanarak Puanlama Web hizmeti 'ni (Web hizmeti olarak sunulan tahmine dayalı deneme) yeni eğitilen modelle güncelleştirin. Ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak modelleri güncelleştirme](data-factory-azure-ml-update-resource-activity.md) .

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Toplu yürütme etkinliğini kullanarak bir Web hizmetini çağırma
Azure Data Factory kullanarak veri hareketini ve işlemeyi düzenleyebilir ve ardından Studio (klasik) kullanarak toplu yürütme gerçekleştirebilirsiniz. En üst düzey adımlar şunlardır:

1. Azure Machine Learning Studio (klasik) bağlı bir hizmet oluşturun. Aşağıdaki değerlere ihtiyacınız vardır:

   1. Batch yürütme API 'SI için **istek URI 'si** . Web Hizmetleri sayfasındaki **Batch yürütme** bağlantısına TıKLAYARAK istek URI 'sini bulabilirsiniz.
   2. Yayınlanan Studio (klasik) Web hizmeti için **API anahtarı** . Yayımladığınız Web hizmetine tıklayarak API anahtarını bulabilirsiniz.
   3. **AzureMLBatchExecution** etkinliğini kullanın.

      ![Machine Learning Studio (klasik) Pano](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI 'SI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Senaryo: Azure Blob depolama alanındaki verilere başvuran Web hizmeti girişlerini/çıkışlarını kullanarak denemeleri
Bu senaryoda, Studio (klasik) Web hizmeti, Azure Blob depolama alanındaki bir dosyadaki verileri kullanarak tahmine dayalı hale getirir ve tahmin sonuçlarını BLOB depolama alanında depolar. Aşağıdaki JSON, bir AzureMLBatchExecution etkinliği ile Data Factory işlem hattını tanımlar. Etkinlik veri kümesi **Decisiontreeınputblob** ' u output as Input ve **DecisionTreeResultBlob** olarak içerir. **Decisiontreeınputblob** , **WebServiceInput** JSON özelliği kullanılarak Web hizmetine giriş olarak geçirilir. **DecisionTreeResultBlob** , **webserviceçıktılar** JSON özelliği kullanılarak Web hizmetine çıkış olarak geçirilir.

> [!IMPORTANT]
> Web hizmeti birden çok giriş alırsa, WebServiceInput özelliğini **kullanmak** yerine **WebServiceInput** özelliğini kullanın. Webservicegirdilerle ilgili bir örnek için bkz. [Web hizmeti birden çok giriş gerektirir](#web-service-requires-multiple-inputs) bölümü.
>
> **WebServiceInput** / **webservicegirdilerden** ve **webserviceçıktılar** özelliklerinin ( **typeproperties** içinde) başvurduğu veri kümeleri de etkinlik **girişlerinde** ve **çıkışlarına** eklenmelidir.
>
> Studio (klasik) denemenize, Web hizmeti giriş ve çıkış bağlantı noktalarında ve genel parametrelerde özelleştirebilmeniz için varsayılan adlar ("input1", "input2") vardır. Webservicegirişlerinde, Webserviceçıktılar ve globalParameters ayarları için kullandığınız adlar, denemeleri adlarıyla tam olarak eşleşmelidir. Beklenen eşlemeyi doğrulamak için, Studio (klasik) uç noktanıza yönelik toplu yürütme Yardım sayfasında örnek istek yükünü görüntüleyebilirsiniz.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Yalnızca AzureMLBatchExecution etkinliğinin girişleri ve çıkışları Web hizmetine parametre olarak geçirilebilir. Örneğin, yukarıdaki JSON parçacığında, Decisiontreeınputblob, AzureMLBatchExecution etkinliğine yönelik bir giriştir ve bu, WebServiceInput parametresi aracılığıyla Web hizmetine giriş olarak geçirilir.
>
>

### <a name="example"></a>Örnek
Bu örnek, hem giriş hem de çıkış verilerini tutmak için Azure Storage kullanır.

Bu örneğe geçmeden önce [ilk işlem hattınızı Data Factory öğreticiyle derlemeyi][adf-build-1st-pipeline] öneririz. Bu örnekte Data Factory yapıtlar (bağlı hizmetler, veri kümeleri, işlem hattı) oluşturmak için Data Factory düzenleyicisini kullanın.

1. **Azure depolama alanı** için **bağlı bir hizmet** oluşturun. Giriş ve çıkış dosyaları farklı depolama hesaplarınızda ise, iki bağlı hizmet gerekir. Bir JSON örneği aşağıda verilmiştir:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. **Giriş** Azure Data Factory **veri kümesini** oluşturun. Diğer bazı Data Factory veri kümelerinden farklı olarak, bu veri kümelerinin her ikisi de **FolderPath** ve **filename** değerlerini içermesi gerekir. Her toplu iş yürütmesinin (her veri dilimi) benzersiz giriş ve çıkış dosyalarını işlemesini veya üretmesine neden olmak için bölümleme kullanabilirsiniz. Girişi CSV dosya biçimine dönüştürmek ve her dilim için depolama hesabına yerleştirmek üzere bazı yukarı akış etkinliklerini eklemeniz gerekebilir. Bu durumda, aşağıdaki örnekte gösterilen **dış** ve **externaldata** ayarlarını eklemeyin ve decisiontreeınputblob, farklı bir etkinliğin çıkış veri kümesi olur.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
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

   Giriş CSV dosyanız, sütun üst bilgisi satırına sahip olmalıdır. CSV 'yi blob depolamaya oluşturmak/taşımak için **kopyalama etkinliğini** kullanıyorsanız **blobwriteraddheader** havuz özelliğini **true** olarak ayarlamanız gerekir. Örnek:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   CSV dosyasında başlık satırı yoksa şu hatayı görebilirsiniz: **etkinlikte hata: dize okuma hatası. Beklenmeyen belirteç: StartObject. Yol ' ', satır 1, konum 1**.

3. **Çıktı** Azure Data Factory **veri kümesi** oluşturun. Bu örnek, her bir dilim yürütmesi için benzersiz bir çıkış yolu oluşturmak üzere bölümlendirme kullanır. Bölümleme olmadan etkinlik dosyanın üzerine yazar.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. API anahtarı ve modeli toplu yürütme URL 'SI sağlayan, **AzureMLLinkedService** türünde bir **bağlı hizmet** oluşturun.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Son olarak, **AzureMLBatchExecution** etkinliği içeren bir işlem hattı yazın. Çalışma zamanında, işlem hattı aşağıdaki adımları gerçekleştirir:

   1. Giriş veri kümelerinizde bulunan giriş dosyasının konumunu alır.
   2. Studio (klasik) Batch yürütme API 'sini çağırır
   3. Toplu yürütme çıkışını, çıkış veri kümenizde verilen bloba kopyalar.

      > [!NOTE]
      > AzureMLBatchExecution etkinliği sıfır veya daha fazla girişe ve bir veya daha fazla çıkışına sahip olabilir.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
            {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Hem **Başlangıç** hem de **bitiş** Tarih zamanları [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601)olmalıdır. Örneğin: 2014-10-14T16:32:41Z. **Bitiş** saati isteğe bağlıdır. **End** özelliği için değer belirtmezseniz, "**Başlat + 48 saat**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9999-09-09** olarak ayarlayın. JSON özellikleri hakkında ayrıntılı bilgi için bkz. [JSON Betik Oluşturma Başvurusu](/previous-versions/azure/dn835050(v=azure.100)).

      > [!NOTE]
      > AzureMLBatchExecution etkinliğinin girişinin belirtilmesi isteğe bağlıdır.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Senaryo: çeşitli depolama alanındaki verilere başvurmak için okuyucu/yazıcı modüllerini kullanma denemeleri
Studio (klasik) denemeleri oluşturma sırasında başka bir yaygın senaryo okuyucu ve yazıcı modüllerini kullanmaktır. Okuyucu modülü, bir denemenize veri yüklemek için kullanılır ve yazıcı modülü, verileri denemeleri veritabanından kaydetmeni. Okuyucu ve yazıcı modülleri hakkında daha fazla bilgi için MSDN Kitaplığı 'ndaki [okuyucu](/azure/machine-learning/studio-module-reference/import-data) ve [Yazıcı](/azure/machine-learning/studio-module-reference/export-data) konularına bakın.

Okuyucu ve yazıcı modüllerini kullanırken, bu okuyucu/yazıcı modüllerinin her özelliği için bir Web hizmeti parametresi kullanmak iyi bir uygulamadır. Bu Web parametreleri, çalışma zamanı sırasında değerleri yapılandırmanızı sağlar. Örneğin, bir Azure SQL veritabanı kullanan okuyucu modülü ile bir deneme oluşturabilirsiniz: XXX.database.windows.net. Web hizmeti dağıtıldıktan sonra, Web hizmetinin tüketicilerini YYY.database.windows.net adlı başka bir mantıksal SQL Server belirtmesini etkinleştirmek isteyebilirsiniz. Bu değerin yapılandırılmasına izin vermek için bir Web hizmeti parametresi kullanabilirsiniz.

> [!NOTE]
> Web hizmeti girişi ve çıkışı Web hizmeti parametrelerinden farklıdır. İlk senaryoda, bir giriş ve çıkışın bir Studio (klasik) Web hizmeti için nasıl belirtilebileceği gördünüz. Bu senaryoda, okuyucu/yazıcı modüllerinin özelliklerine karşılık gelen bir Web hizmeti için parametreleri geçitirsiniz.
>
>

Web hizmeti parametrelerini kullanmak için bir senaryoya göz atalım. Studio (klasik) tarafından desteklenen veri kaynaklarından birinden (örneğin, Azure SQL veritabanı) veri okumak için bir okuyucu modülü kullanan dağıtılmış bir Studio (klasik) Web hizmetiniz vardır. Toplu yürütme gerçekleştirildikten sonra, sonuçlar bir yazıcı modülü (Azure SQL veritabanı) kullanılarak yazılır.  Denemeleri içinde hiçbir Web hizmeti girişi ve çıkışı tanımlanmamıştır. Bu durumda, okuyucu ve yazıcı modülleri için ilgili Web hizmeti parametrelerini yapılandırmanızı öneririz. Bu yapılandırma, AzureMLBatchExecution etkinliği kullanılırken okuyucu/yazıcı modüllerinin yapılandırılmasını sağlar. Web hizmeti parametrelerini etkinlik JSON ' daki **Globalparameters** bölümünde aşağıdaki gibi belirtirsiniz.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Ayrıca, aşağıdaki örnekte gösterildiği gibi, Web hizmeti parametrelerinin değerlerini geçirerek [Data Factory işlevleri](data-factory-functions-variables.md) de kullanabilirsiniz:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web hizmeti parametreleri büyük/küçük harfe duyarlıdır; bu nedenle, JSON etkinliğinde belirttiğiniz adların Web hizmeti tarafından açığa çıkanlara eşleştiğinden emin olun.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Azure Blob 'da birden çok dosyadan veri okumak için okuyucu modülünü kullanma
Pig ve Hive gibi etkinliklere sahip büyük veri işlem hatları, uzantıları olmayan bir veya daha fazla çıktı dosyası üretebilir. Örneğin, bir dış Hive tablosu belirttiğinizde, dış Hive tablosu verileri, şu ad 000000_0 olan Azure Blob depolama alanında depolanabilir. Okuyucu modülünü, birden çok dosyayı okumak ve bunları tahmine dayalı olarak kullanmak için bir deneyde kullanabilirsiniz.

Okuyucu modülünü bir Studio (klasik) denemesinde kullanırken, Azure Blob 'U giriş olarak belirtebilirsiniz. Azure Blob Storage 'daki dosyalar, HDInsight üzerinde çalışan bir Pig ve Hive betiği tarafından üretilen çıkış dosyaları (örnek: 000000_0) olabilir. Okuyucu modülü, **kapsayıcı, Dizin/blob yolunu** yapılandırarak dosyaları okumanızı (uzantısız) sağlar. **Kapsayıcının yolu** , kapsayıcıyı ve **Dizin/blobu** , aşağıdaki görüntüde gösterildiği gibi dosyaları içeren klasörü işaret eder. Bir olan yıldız işareti \* ), **kapsayıcıdaki/klasördeki tüm dosyaların (yani, Data/aggregteddata/Year = 2014/month-6/ \* )** denemenin bir parçası olarak okunduğunu belirtir.

![Azure Blob özellikleri](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Örnek
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Web hizmeti parametreleriyle AzureMLBatchExecution etkinliği olan işlem hattı

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

Yukarıdaki JSON örneğinde:

* Dağıtılan Studio (klasik) Web hizmeti, bir Azure SQL veritabanından veri okumak/buradan veri yazmak için bir okuyucu ve bir yazıcı modülü kullanır. Bu Web hizmeti şu dört parametreyi kullanıma sunar: veritabanı sunucu adı, veritabanı adı, sunucu Kullanıcı hesabı adı ve sunucu Kullanıcı hesabı parolası.
* Hem **Başlangıç** hem de **bitiş** Tarih zamanları [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601)olmalıdır. Örneğin: 2014-10-14T16:32:41Z. **Bitiş** saati isteğe bağlıdır. **End** özelliği için değer belirtmezseniz, "**Başlat + 48 saat**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9999-09-09** olarak ayarlayın. JSON özellikleri hakkında ayrıntılı bilgi için bkz. [JSON Betik Oluşturma Başvurusu](/previous-versions/azure/dn835050(v=azure.100)).

### <a name="other-scenarios"></a>Diğer senaryolar
#### <a name="web-service-requires-multiple-inputs"></a>Web hizmeti birden çok giriş gerektiriyor
Web hizmeti birden çok giriş alırsa, WebServiceInput özelliğini **kullanmak** yerine **WebServiceInput** özelliğini kullanın. **Webservicegirişlerinin** başvurduğu veri kümeleri de etkinlik **girişlerinde** yer almalıdır.

Azure Machine Learning Studio (klasik) denemenize, Web hizmeti giriş ve çıkış bağlantı noktalarında ve genel parametrelerde özelleştirebileceğiniz varsayılan adlar ("input1", "input2") vardır. Webservicegirişlerinde, Webserviceçıktılar ve globalParameters ayarları için kullandığınız adlar, denemeleri adlarıyla tam olarak eşleşmelidir. Beklenen eşlemeyi doğrulamak için, Studio (klasik) uç noktanıza yönelik toplu yürütme Yardım sayfasında örnek istek yükünü görüntüleyebilirsiniz.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Web hizmeti bir giriş gerektirmez
Azure Machine Learning Studio (klasik) toplu yürütme Web Hizmetleri, herhangi bir giriş gerektirmeyen bir iş akışını (örneğin, R veya Python betikleri) çalıştırmak için kullanılabilir. Ya da deneme, herhangi bir GlobalParameters kullanıma sunmayan bir okuyucu modülü ile yapılandırılmış olabilir. Bu durumda, AzureMLBatchExecution etkinliği aşağıdaki şekilde yapılandırılır:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Web hizmeti bir giriş/çıkış gerektirmez
Azure Machine Learning Studio (klasik) toplu yürütme Web hizmetinde yapılandırılmış herhangi bir Web hizmeti çıkışı bulunmayabilir. Bu örnekte, Web hizmeti girişi veya çıkışı yoktur, ya da hiçbir GlobalParameters yapılandırılmaz. Etkinliğin kendisinde hala yapılandırılmış bir çıktı vardır, ancak bu bir webServiceOutput olarak verilmez.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web hizmeti okuyucuları ve yazarları kullanır ve etkinlik yalnızca diğer etkinlikler başarılı olduğunda çalışır
Azure Machine Learning Studio (klasik) Web hizmeti okuyucusu ve yazıcı modülleri, herhangi bir GlobalParameters ile veya olmadan çalışacak şekilde yapılandırılmış olabilir. Ancak, hizmet çağrılarını yalnızca bazı yukarı akış işlemleri tamamlandığında hizmeti çağırmak için veri kümesi bağımlılıklarını kullanan bir işlem hattına eklemek isteyebilirsiniz. Toplu yürütme tamamlandıktan sonra, bu yaklaşımı kullanarak bazı diğer eylemleri de tetikleyebilirsiniz. Bu durumda, etkinlik girişlerini ve çıkışları kullanarak bağımlılıkları, Web hizmeti girişleri veya çıkışları olarak adlandırmadan ifade edebilirsiniz.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Bu **, şunlardır:**

* Deneme uç noktanız bir WebServiceInput kullanıyorsa: bir blob veri kümesi tarafından temsil edilir ve etkinlik girişlerinde ve WebServiceInput özelliğinde bulunur. Aksi takdirde, WebServiceInput özelliği atlanır.
* Deneme uç noktanız webServiceOutput 'ları kullanıyorsa: Bunlar blob veri kümeleri tarafından temsil edilir ve etkinlik çıktılarına ve Webserviceçıktılar özelliğine dahil edilir. Etkinlik çıkışları ve Webserviceçıktılar, denemenin her çıktısının adı ile eşleştirilir. Aksi takdirde, Webserviceçıktılar özelliği atlanır.
* Deneme uç noktanız Globalparameters kullanıma sunuyorsa, bu değerler, etkinlik globalParameters özelliğinde anahtar, değer çiftleri olarak verilir. Aksi takdirde globalParameters özelliği atlanır. Anahtarlar büyük/küçük harfe duyarlıdır. [Azure Data Factory işlevler](data-factory-functions-variables.md) , değerlerde kullanılabilir.
* Ek veri kümeleri, etkinlik girdilerine ve çıkışlar özelliklerine, etkinlik türü özelliklerinde başvurulmadan dahil edilebilir. Bu veri kümeleri, dilim bağımlılıklarını kullanarak yürütmeyi yönetir, ancak başka bir AzureMLBatchExecution etkinliği tarafından yok sayılır.


## <a name="updating-models-using-update-resource-activity"></a>Kaynak güncelleştirme etkinliğini kullanarak modelleri güncelleştirme
Yeniden eğitim elde ettikten sonra, **Azure Machine Learning Studio (klasik) güncelleştirme kaynak etkinliğini** kullanarak Puanlama Web hizmeti 'ni (Web hizmeti olarak sunulan tahmine dayalı deneme) yeni eğitilen modelle güncelleştirin. Ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak modelleri güncelleştirme](data-factory-azure-ml-update-resource-activity.md) .

### <a name="reader-and-writer-modules"></a>Okuyucu ve yazıcı modülleri
Web hizmeti parametrelerinin kullanılmasına yönelik yaygın bir senaryo, Azure SQL okuyucuları ve yazarları kullanmaktır. Okuyucu modülü, Studio dışındaki veri yönetimi hizmetlerinden bir denemeye veri yüklemek için kullanılır (klasik). Yazıcı modülü, denemeleri 'ınızdan verileri Studio (klasik) dışında veri yönetimi hizmetlerine kaydetidir.

Azure Blob/Azure SQL okuyucusu/yazıcı hakkında daha fazla bilgi için MSDN Kitaplığı 'ndaki [okuyucu](/azure/machine-learning/studio-module-reference/import-data) ve [Yazıcı](/azure/machine-learning/studio-module-reference/export-data) konularına bakın. Önceki bölümdeki örnek, Azure Blob okuyucusunu ve Azure Blob yazıcısını kullandı. Bu bölümde Azure SQL okuyucu ve Azure SQL yazıcı kullanımı açıklanmaktadır.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular
**S:** Büyük veri ardışık düzenleri tarafından oluşturulan birden çok dosya var. AzureMLBatchExecution etkinliğini kullanarak tüm dosyalar üzerinde çalışma yapabilir miyim?

**Y:** Evet. Ayrıntılar için bkz. **Azure Blob 'da birden çok dosyadan veri okumak Için okuyucu kullanma modülü** bölümü.

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Azure Machine Learning Studio (klasik) toplu Işlem puanlama etkinliği
Azure Machine Learning Studio (klasik) ile tümleştirme için **AzureMLBatchScoring** etkinliğini kullanıyorsanız, en son **AzureMLBatchExecution** etkinliğini kullanmanızı öneririz.

AzureMLBatchExecution etkinliği, Azure SDK 'nın Ağustos 2015 sürümü ve Azure PowerShell kullanıma sunulmuştur.

AzureMLBatchScoring etkinliğini kullanmaya devam etmek istiyorsanız, bu bölümü okumaya devam edin.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Giriş/çıkış için Azure Storage kullanan Azure Machine Learning Studio (klasik) toplu Işlem puanlama etkinliği

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Web hizmeti parametreleri
Web hizmeti parametrelerinin değerlerini belirtmek için, aşağıdaki örnekte gösterildiği gibi JSON işlem hattı içindeki **AzureMLBatchScoringActivity** bölümüne bir **typeproperties** bölümü ekleyin:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Ayrıca, aşağıdaki örnekte gösterildiği gibi, Web hizmeti parametrelerinin değerlerini geçirerek [Data Factory işlevleri](data-factory-functions-variables.md) de kullanabilirsiniz:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web hizmeti parametreleri büyük/küçük harfe duyarlıdır; bu nedenle, JSON etkinliğinde belirttiğiniz adların Web hizmeti tarafından açığa çıkanlara eşleştiğinden emin olun.
>
>

## <a name="see-also"></a>Ayrıca Bkz.
* [Azure blog gönderisi: Azure Data Factory ve Azure Machine Learning kullanmaya başlama](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/