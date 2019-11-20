---
title: Azure Data Factory Hadoop MapReduce etkinliğini kullanarak verileri dönüştürme
description: Azure Data Factory 'deki bir Azure HDInsight kümesinde Hadoop MapReduce programlarını çalıştırarak verileri nasıl işleyebileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 49e00d9a47f92fb30a29e7051cba35f54bde3700
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683854"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Azure Data Factory Hadoop MapReduce etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-map-reduce.md)
> * [Geçerli sürüm](transform-data-using-hadoop-map-reduce.md)

Bir Data Factory işlem [hattının](concepts-pipelines-activities.md) HDInsight MapReduce etkinliği, [kendi](compute-linked-services.md#azure-hdinsight-linked-service) veya [Isteğe](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) bağlı HDInsight kümenizdeki MapReduce programını çağırır. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur.

Azure Data Factory yeni bir deyişle, [Azure Data Factory 'ye giriş](introduction.md) yapın ve öğreticiyi yapın: Öğretici: Bu makaleyi okumadan önce [verileri dönüştürün](tutorial-transform-data-spark-powershell.md) .

HDInsight Pig ve Hive etkinliklerini kullanarak bir işlem hattından HDInsight kümesinde Pig/Hive betikleri çalıştırmaya ilişkin ayrıntılar için bkz. [Pig](transform-data-using-hadoop-pig.md) and [Hive](transform-data-using-hadoop-hive.md) .

## <a name="syntax"></a>Sözdizimi

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
| ad              | Etkinliğin adı                     | Evet      |
| açıklama       | Etkinliğin ne için kullanıldığını açıklayan metin | Hayır       |
| type              | MapReduce etkinliği için etkinlik türü HDinsightMapReduce | Evet      |
| linkedServiceName | Data Factory bağlı hizmet olarak kaydedilen HDInsight kümesine başvuru. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Evet      |
| className         | Yürütülecek sınıfın adı         | Evet      |
| jarLinkedService  | Jar dosyalarını depolamak için kullanılan bir Azure depolama bağlı hizmetine başvuru. Bu bağlı hizmeti belirtmezseniz, HDInsight bağlı hizmetinde tanımlanan Azure depolama bağlı hizmeti kullanılır. | Hayır       |
| jarFilePath       | JarLinkedService tarafından başvurulan Azure Storage 'da depolanan jar dosyalarının yolunu belirtin. Dosya adı büyük/küçük harfe duyarlıdır. | Evet      |
| jarlibs           | JarLinkedService içinde tanımlanan Azure depolama alanında depolanan iş tarafından başvurulan jar kitaplık dosyalarının yolunun dize dizisi. Dosya adı büyük/küçük harfe duyarlıdır. | Hayır       |
| GetDebugInfo      | Günlük dosyalarının HDInsight kümesi tarafından kullanılan Azure depolama 'ya (veya) jarLinkedService tarafından belirtilen şekilde kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: yok. | Hayır       |
| Değişkenlerinden         | Bir Hadoop işi için bir bağımsız değişken dizisi belirtir. Bağımsız değişkenler her göreve komut satırı bağımsız değişkeni olarak geçirilir. | Hayır       |
| tanımlar           | Hive betiği içinde başvurmak için parametreleri anahtar/değer çiftleri olarak belirtin. | Hayır       |



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
**Bağımsız değişkenler** bölümünde MapReduce programı için herhangi bir bağımsız değişken belirtebilirsiniz. Çalışma zamanında, MapReduce çerçevesinden birkaç ek bağımsız değişken (örneğin: MapReduce. job. Tag) görürsünüz. Bağımsız değişkenlerinizi MapReduce bağımsız değişkenleriyle ayırt etmek için, aşağıdaki örnekte gösterildiği gibi her iki seçeneği ve değeri bağımsız değişken olarak kullanmayı düşünün (-s,--Input,--Output vb.), bu seçenekler hemen arkasından değerleri izler.

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın:

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
