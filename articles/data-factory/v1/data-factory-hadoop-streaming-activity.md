---
title: Hadoop akış etkinliğini kullanarak verileri dönüştürme-Azure
description: Bir Azure Data Factory 'de Hadoop akışı programlarını isteğe bağlı/kendi HDInsight kümeniz üzerinde çalıştırarak verileri dönüştürmek için nasıl kullanabileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c56961b28750f3ba6450c26c897c1ef6c54b3ed8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667497"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory 'de Hadoop akışı etkinliğini kullanarak verileri dönüştürme
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
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory Hadoop akışını kullanarak verileri dönüştürme](../transform-data-using-hadoop-streaming.md).


Bir Azure Data Factory işlem hattından Hadoop akış işi çağırma HDInsightStreamingActivity etkinliğini kullanabilirsiniz. Aşağıdaki JSON kod parçacığında, bir işlem hattı JSON dosyasında HDInsightStreamingActivity kullanımı için sözdizimi gösterilmektedir. 

Bir Data Factory işlem [hattının](data-factory-create-pipelines.md) HDInsight akış etkinliği, [kendi kendinize](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe bağlı](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux tabanlı HDInsight kümenizde Hadoop akış programlarını yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) makalesinde oluşturulur.

> [!NOTE] 
> Azure Data Factory yeni kullanıyorsanız, [Azure Data Factory 'ye giriş](data-factory-introduction.md) ile okuyun ve öğreticiyi yapın: Bu makaleyi okumadan önce [ilk veri Işlem hattınızı oluşturun](data-factory-build-your-first-pipeline.md) . 

## <a name="json-sample"></a>JSON örneği
HDInsight kümesi, örnek programlarla (WC. exe ve Cat. exe) ve veriler (DaVinci. txt) ile otomatik olarak doldurulur. Varsayılan olarak, HDInsight kümesi tarafından kullanılan kapsayıcının adı kümenin kendisidir. Örneğin, Kümenizin adı myhdicluster ise, ilişkili blob kapsayıcısının adı myhdicluster olacaktır. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Aşağıdaki noktalara dikkat edin:

1. **Linkedservicename** ' i, akış MapReduce Işinin çalıştırıldığı HDInsight kümenize işaret eden bağlı hizmetin adı olarak ayarlayın.
2. Etkinliğin türünü **hdınsightstreaming**olarak ayarlayın.
3. **Eşleyici** özelliği için Eşleyici yürütülebilirinin adını belirtin. Örnekte, Cat. exe Eşleyici yürütülebilirdir.
4. **Reducer** özelliği için Reducer yürütülebilir dosyasının adını belirtin. Örnekte, WC. exe, Reducer çalıştırılabilir.
5. **Giriş** türü özelliği için Eşleyici için giriş dosyasını (konum dahil) belirtin. Örnek: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample blob kapsayıcısıdır, example/Data/Gutenberg klasörüdür ve DaVinci. txt blob olur.
6. **Çıktı** türü özelliği için, Reducer için çıkış dosyasını (konum dahil) belirtin. Hadoop akış işinin çıktısı, bu özellik için belirtilen konuma yazılır.
7. **FilePaths** bölümünde Eşleyici ve Reducer Yürütülebilirler için yolları belirtin. Örnek: "adfsample/example/Apps/WC. exe", adfsample blob kapsayıcısıdır, örnek/uygulamalar klasördür ve WC. exe yürütülebilir bir dosyadır.
8. **Filelinkedservice** özelliği Için, filePaths bölümünde belirtilen dosyaları içeren Azure depolama 'yı temsil eden Azure depolama bağlı hizmetini belirtin.
9. **Arguments** özelliği için akış işinin bağımsız değişkenlerini belirtin.
10. **GetDebugInfo** özelliği, isteğe bağlı bir öğedir. Hata olarak ayarlandığında Günlükler yalnızca hata durumunda indirilir. Her zaman olarak ayarlandığında, yürütme durumu ne olursa olsun Günlükler her zaman indirilir.

> [!NOTE]
> Örnekte gösterildiği gibi, **çıktılar** özelliği Için Hadoop akış etkinliği için bir çıktı veri kümesi belirtirsiniz. Bu veri kümesi, yalnızca işlem hattı zamanlamasını sağlamak için gerekli olan bir kukla veri kümesidir. **Girişler** özelliği için etkinlik için herhangi bir giriş veri kümesi belirtmeniz gerekmez.  
> 
> 

## <a name="example"></a>Örnek
Bu izlenecek işlem hattı, Azure HDInsight kümenizdeki sözcük sayısı akış Haritası/azaltma programını çalıştırır. 

### <a name="linked-services"></a>Bağlı hizmetler
#### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti
İlk olarak, Azure HDInsight kümesi tarafından kullanılan Azure depolama alanını Azure Data Factory 'ye bağlamak için bağlı bir hizmet oluşturursunuz. Aşağıdaki kodu kopyalayıp yapıştırırsanız, hesap adı ve hesap anahtarı 'nı Azure depolamanın adı ve anahtarıyla değiştirmeyi unutmayın. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti
Ardından, Azure HDInsight kümenizi Azure Data Factory 'ye bağlamak için bağlı bir hizmet oluşturursunuz. Aşağıdaki kodu kopyalayıp yapıştırırsanız, HDInsight kümesi adını HDInsight kümenizin adıyla değiştirin ve Kullanıcı adı ve parola değerlerini değiştirin. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Veri kümeleri
#### <a name="output-dataset"></a>Çıkış veri kümesi
Bu örnekteki işlem hattı herhangi bir giriş yapmaz. HDInsight akış etkinliği için bir çıkış veri kümesi belirtirsiniz. Bu veri kümesi, yalnızca işlem hattı zamanlamasını sağlamak için gerekli olan bir kukla veri kümesidir. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>İşlem hattı
Bu örnekteki işlem hattının şu türde yalnızca bir etkinliği vardır: **hdınsightstreaming**. 

HDInsight kümesi, örnek programlarla (WC. exe ve Cat. exe) ve veriler (DaVinci. txt) ile otomatik olarak doldurulur. Varsayılan olarak, HDInsight kümesi tarafından kullanılan kapsayıcının adı kümenin kendisidir. Örneğin, Kümenizin adı myhdicluster ise, ilişkili blob kapsayıcısının adı myhdicluster olacaktır.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Ayrıca Bkz.
* [Hive etkinliği](data-factory-hive-activity.md)
* [Pig etkinliği](data-factory-pig-activity.md)
* [MapReduce etkinliği](data-factory-map-reduce.md)
* [Spark programlarını çağırma](data-factory-spark.md)
* [R betiklerini çağırma](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

