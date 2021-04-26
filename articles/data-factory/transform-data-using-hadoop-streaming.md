---
title: Hadoop akış etkinliğini kullanarak verileri dönüştürme
description: Hadoop akış programlarını bir Hadoop kümesinde çalıştırarak verileri dönüştürmek için Azure Data Factory 'de Hadoop akışı etkinliğinin nasıl kullanılacağını açıklar.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361446"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory 'de Hadoop akışı etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Güncel sürüm](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir [Data Factory işlem](concepts-pipelines-activities.md) hattındaki HDInsight akış etkinliği, [kendi kendinize](compute-linked-services.md#azure-hdinsight-linked-service) veya [Isteğe](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) bağlı HDInsight kümenizde Hadoop akış programlarını yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur.

Azure Data Factory yeni bir deyişle, [Azure Data Factory 'ye giriş](introduction.md) ile okuyun ve [öğreticiyi yapın:](tutorial-transform-data-spark-powershell.md) bu makaleyi okumadan önce verileri dönüştürün. 

## <a name="json-sample"></a>JSON örneği
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
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
| tür              | Hadoop akışı etkinliği için etkinlik türü Hdınsightstreaming olur | Yes      |
| linkedServiceName | Data Factory bağlı hizmet olarak kaydedilen HDInsight kümesine başvuru. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Yes      |
| Eşleyici            | Eşleyici yürütülebilir dosyasının adını belirtir | Yes      |
| reducer           | Reducer yürütülebilir dosyasının adını belirtir | Yes      |
| Birleştirici          | Birleştirici yürütülebilir dosyasının adını belirtir | No       |
| Dosya Linkedservice | Yürütülecek Eşleyici, birleştirici ve Reducer programlarını depolamak için kullanılan bir Azure depolama bağlı hizmetine başvuru. Burada yalnızca **[Azure Blob depolama](./connector-azure-blob-storage.md)** ve **[ADLS 2.](./connector-azure-data-lake-storage.md)** bağlı hizmetleri desteklenir. Bu bağlı hizmeti belirtmezseniz, HDInsight bağlı hizmetinde tanımlanan Azure depolama bağlı hizmeti kullanılır. | No       |
| Null          | FileLinkedService tarafından başvurulan Azure depolama alanında depolanan Mapper, birleştirici ve Reducer programlarının yolunu dizisini belirtin. Bu yol büyük/küçük harfe duyarlıdır. | Yes      |
| giriş             | Eşleyici için giriş dosyasının yer aldığı yolu belirtir. | Yes      |
| çıkış            | Reducer için çıkış dosyasının yer aldığı yolu belirtir. | Yes      |
| GetDebugInfo      | Günlük dosyalarının, HDInsight kümesi tarafından kullanılan (veya) scriptLinkedService tarafından belirtilen Azure depolama 'ya ne zaman kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: Hiçbiri. | No       |
| değişkenlerinden         | Bir Hadoop işi için bir bağımsız değişken dizisi belirtir. Bağımsız değişkenler her göreve komut satırı bağımsız değişkeni olarak geçirilir. | No       |
| tanımlar           | Hive betiği içinde başvurmak için parametreleri anahtar/değer çiftleri olarak belirtin. | Hayır       | 

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın: 

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasik) Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)