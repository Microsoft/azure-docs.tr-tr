---
title: Azure Data Factory 'den MapReduce programını çağır
description: Azure Data Factory 'deki bir Azure HDInsight kümesinde MapReduce programlarını çalıştırarak verileri nasıl işleyebileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 6d32cd12989262ebeaafee60a02bb7ea2a9b1e32
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139289"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Data Factory 'den MapReduce programlarını çağırma
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
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory MapReduce etkinliğini kullanarak verileri dönüştürme](../transform-data-using-hadoop-map-reduce.md).


Bir Data Factory işlem hattındaki HDInsight MapReduce [](data-factory-create-pipelines.md) etkinliği, MapReduce programlarını [kendi kendinize](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe bağlı](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux tabanlı HDInsight kümenizde yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) makalesinde oluşturulur.

> [!NOTE] 
> Azure Data Factory yeni kullanıyorsanız, Azure Data Factory ve Öğreticiyi bir [şekilde](data-factory-introduction.md) okuyun: Bu makaleyi okumadan önce [ilk veri işlem hattınızı oluşturun](data-factory-build-your-first-pipeline.md) .  

## <a name="introduction"></a>Giriş
Azure Data Factory 'deki bir işlem hattı bağlı işlem hizmetlerini kullanarak bağlı depolama hizmetlerindeki verileri işler. Her etkinliğin belirli bir işleme işlemi gerçekleştirdiği bir etkinlik dizisi içerir. Bu makalede, HDInsight MapReduce etkinliğinin kullanımı açıklanmaktadır.

HDInsight Pig ve Hive etkinliklerini kullanarak bir işlem hattından Windows/Linux tabanlı HDInsight kümesinde Pig/Hive betikleri çalıştırmaya ilişkin ayrıntılar için bkz. [Pig](data-factory-pig-activity.md) and [Hive](data-factory-hive-activity.md) . 

## <a name="json-for-hdinsight-mapreduce-activity"></a>HDInsight MapReduce etkinliği için JSON
HDInsight etkinliğinin JSON tanımında: 

1. **Etkinliğin** **türünü** **HDInsight**olarak ayarlayın.
2. **ClassName** özelliği için sınıfın adını belirtin.
3. **JarFilePath** özelliği için dosya adı da dahil olmak üzere jar dosyasının yolunu belirtin.
4. **JarLinkedService** ÖZELLIĞI için jar dosyasını Içeren Azure Blob depolama 'ya başvuran bağlı hizmeti belirtin.   
5. **Bağımsız değişkenler** bölümünde MapReduce programı için herhangi bir bağımsız değişken belirtin. Çalışma zamanında, MapReduce çerçevesinden birkaç ek bağımsız değişken (örneğin: MapReduce. job. Tag) görürsünüz. Bağımsız değişkenlerinizi MapReduce bağımsız değişkenleriyle ayırt etmek için, aşağıdaki örnekte gösterildiği gibi her iki seçeneği ve değeri bağımsız değişken olarak kullanmayı düşünün (-s,--Input,--Output vb.), bu seçenekler hemen arkasından değerleri izler.

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   HDInsight MapReduce etkinliğini bir HDInsight kümesinde herhangi bir MapReduce jar dosyası çalıştırmak için kullanabilirsiniz. Aşağıdaki örnek bir işlem hattının JSON tanımında, HDInsight etkinliği Mahout JAR dosyasını çalıştıracak şekilde yapılandırılmıştır.

## <a name="sample-on-github"></a>GitHub üzerinde örnek
HDInsight MapReduce etkinliğinin kullanılması için bir örnek indirebilirsiniz: [GitHub 'Daki örnekleri Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Sözcük sayısı programını çalıştırma
Bu örnekteki işlem hattı, Azure HDInsight kümenizdeki Word Count Map/azaltma programını çalıştırır.   

### <a name="linked-services"></a>Bağlı Hizmetler
İlk olarak, Azure HDInsight kümesi tarafından kullanılan Azure depolama alanını Azure Data Factory 'ye bağlamak için bağlı bir hizmet oluşturursunuz. Aşağıdaki kodu kopyalayıp yapıştırırsanız, **Hesap adı** ve **hesap anahtarı** 'nı Azure depolamanın adı ve anahtarıyla değiştirmeyi unutmayın. 

#### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti

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
Ardından, Azure HDInsight kümenizi Azure Data Factory 'ye bağlamak için bağlı bir hizmet oluşturursunuz. Aşağıdaki kodu kopyalayıp yapıştırırsanız, **HDInsight kümesi adını** HDInsight kümenizin adıyla değiştirin ve Kullanıcı adı ve parola değerlerini değiştirin.   

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
Bu örnekteki işlem hattı herhangi bir giriş yapmaz. HDInsight MapReduce etkinliği için bir çıkış veri kümesi belirtirsiniz. Bu veri kümesi, yalnızca işlem hattı zamanlamasını sağlamak için gerekli olan bir kukla veri kümesidir.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>İşlem hattı
Bu örnekteki işlem hattının şu türde yalnızca bir etkinliği vardır: HDInsightMapReduce. JSON 'daki önemli özelliklerden bazıları şunlardır: 

| Özellik | Notlar |
|:--- |:--- |
| type |Tür **HDInsightMapReduce**olarak ayarlanmalıdır. |
| Sınıf |Sınıfın adı: **WORDCOUNT** |
| jarFilePath |Sınıfını içeren jar dosyasının yolu. Aşağıdaki kodu kopyalayıp yapıştırırsanız, kümenin adını değiştirmeyi unutmayın. |
| jarLinkedService |Jar dosyasını içeren Azure depolama bağlı hizmeti. Bu bağlı hizmet, HDInsight kümesiyle ilişkili depolamayı ifade eder. |
| arguments |WORDCOUNT programı iki bağımsız değişken alır, bir giriş ve çıkış. Giriş dosyası DaVinci. txt dosyasıdır. |
| frequency/interval |Bu özelliklerin değerleri, çıkış veri kümesiyle eşleşir. |
| linkedServiceName |daha önce oluşturduğunuz HDInsight bağlı hizmetini ifade eder. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark programlarını çalıştırma
MapReduce etkinliğini kullanarak HDInsight Spark kümenizde Spark programları çalıştırabilirsiniz. Ayrıntılar için bkz. [Azure Data Factory’den Spark programlarını çağırma](data-factory-spark.md).  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Ayrıca Bkz.
* [Hive etkinliği](data-factory-hive-activity.md)
* [Pig etkinliği](data-factory-pig-activity.md)
* [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
* [Spark programlarını çağırma](data-factory-spark.md)
* [R betiklerini çağırma](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

