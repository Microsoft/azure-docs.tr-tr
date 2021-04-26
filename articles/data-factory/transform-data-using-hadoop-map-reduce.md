---
title: Hadoop MapReduce etkinliğini kullanarak verileri dönüştürme
description: Azure Data Factory 'deki bir Azure HDInsight kümesinde Hadoop MapReduce programlarını çalıştırarak verileri nasıl işleyebileceğinizi öğrenin.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: f03906586d6226c92cfa69e1a139d4c876cbf723
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375893"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Azure Data Factory Hadoop MapReduce etkinliğini kullanarak verileri dönüştürme

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-map-reduce.md)
> * [Güncel sürüm](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir Data Factory işlem [hattının](concepts-pipelines-activities.md) HDInsight MapReduce etkinliği, [kendi](compute-linked-services.md#azure-hdinsight-linked-service) veya [Isteğe](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) bağlı HDInsight kümenizdeki MapReduce programını çağırır. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur.

Azure Data Factory yeni bir deyişle, [Azure Data Factory 'ye giriş](introduction.md) yapın ve öğreticiyi yapın: Öğretici: Bu makaleyi okumadan önce [verileri dönüştürün](tutorial-transform-data-spark-powershell.md) .

HDInsight Pig ve Hive etkinliklerini kullanarak bir işlem hattından HDInsight kümesinde Pig/Hive betikleri çalıştırmaya ilişkin ayrıntılar için bkz. [Pig](transform-data-using-hadoop-pig.md) and [Hive](transform-data-using-hadoop-hive.md) .

## <a name="syntax"></a>Syntax

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Söz dizimi ayrıntıları

| Özellik          | Açıklama                              | Gerekli |
| ----------------- | ---------------------------------------- | -------- |
| name              | Etkinliğin adı                     | Yes      |
| açıklama       | Etkinliğin ne için kullanıldığını açıklayan metin | No       |
| tür              | MapReduce etkinliği için etkinlik türü HDinsightMapReduce | Yes      |
| linkedServiceName | Data Factory bağlı hizmet olarak kaydedilen HDInsight kümesine başvuru. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Yes      |
| Sınıf         | Yürütülecek sınıfın adı         | Yes      |
| jarLinkedService  | Jar dosyalarını depolamak için kullanılan bir Azure depolama bağlı hizmetine başvuru. Burada yalnızca **[Azure Blob depolama](./connector-azure-blob-storage.md)** ve **[ADLS 2.](./connector-azure-data-lake-storage.md)** bağlı hizmetleri desteklenir. Bu bağlı hizmeti belirtmezseniz, HDInsight bağlı hizmetinde tanımlanan Azure depolama bağlı hizmeti kullanılır. | No       |
| jarFilePath       | JarLinkedService tarafından başvurulan Azure Storage 'da depolanan jar dosyalarının yolunu belirtin. Dosya adı büyük/küçük harfe duyarlıdır. | Yes      |
| jarlibs           | JarLinkedService içinde tanımlanan Azure depolama alanında depolanan iş tarafından başvurulan jar kitaplık dosyalarının yolunun dize dizisi. Dosya adı büyük/küçük harfe duyarlıdır. | No       |
| GetDebugInfo      | Günlük dosyalarının HDInsight kümesi tarafından kullanılan Azure depolama 'ya (veya) jarLinkedService tarafından belirtilen şekilde kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: Hiçbiri. | No       |
| değişkenlerinden         | Bir Hadoop işi için bir bağımsız değişken dizisi belirtir. Bağımsız değişkenler her göreve komut satırı bağımsız değişkeni olarak geçirilir. | No       |
| tanımlar           | Hive betiği içinde başvurmak için parametreleri anahtar/değer çiftleri olarak belirtin. | No       |



## <a name="example"></a>Örnek
HDInsight MapReduce etkinliğini bir HDInsight kümesinde herhangi bir MapReduce jar dosyası çalıştırmak için kullanabilirsiniz. Aşağıdaki örnek bir işlem hattının JSON tanımında, HDInsight etkinliği Mahout JAR dosyasını çalıştıracak şekilde yapılandırılmıştır.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
**Bağımsız değişkenler** bölümünde MapReduce programı için herhangi bir bağımsız değişken belirtebilirsiniz. Çalışma zamanında, MapReduce çerçevesinden birkaç ek bağımsız değişken (örneğin: MapReduce. job. Tag) görürsünüz. Bağımsız değişkenlerinizi MapReduce bağımsız değişkenleriyle ayırt etmek için, aşağıdaki örnekte gösterildiği gibi her iki seçeneği ve değeri bağımsız değişken olarak kullanmayı düşünün (-s,--Input,--Output vb.).

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın:

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasik) Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)