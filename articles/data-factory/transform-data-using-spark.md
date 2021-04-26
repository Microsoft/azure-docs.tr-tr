---
title: Spark etkinliğini kullanarak verileri dönüştürme
description: Spark etkinliğini kullanarak Spark programlarını bir Azure Data Factory işlem hattından çalıştırarak verileri dönüştürmeyi öğrenin.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e5c50d2cbd16ad2808dab485ad2b2870d6f3d350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392366"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-spark.md)
> * [Güncel sürüm](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir [Data Factory işlem](concepts-pipelines-activities.md) hattındaki Spark etkinliği, [kendi](compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  bağlı HDInsight kümeniz üzerinde bir Spark programı yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur. İsteğe bağlı Spark bağlı hizmeti kullandığınızda Data Factory, verileri işlemek için tam zamanında bir Spark kümesi oluşturur ve işlem tamamlandıktan sonra kümeyi siler. 


## <a name="spark-activity-properties"></a>Spark etkinlik özellikleri
Spark etkinliğinin örnek JSON tanımı aşağıda verilmiştir:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

Aşağıdaki tabloda JSON tanımında kullanılan JSON özellikleri açıklanmaktadır:

| Özellik              | Açıklama                              | Gerekli |
| --------------------- | ---------------------------------------- | -------- |
| name                  | İşlem hattındaki etkinliğin adı.    | Yes      |
| açıklama           | Etkinliğin ne yaptığını açıklayan metin.  | No       |
| tür                  | Spark etkinliği için etkinlik türü HDInsightSpark ' dir. | Yes      |
| linkedServiceName     | Spark programının çalıştığı HDInsight Spark bağlı hizmetinin adı. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Yes      |
| Mini iş Linkedservice | Spark iş dosyasını, bağımlılıklarını ve günlüklerini tutan Azure depolama bağlı hizmeti. Burada yalnızca **[Azure Blob depolama](./connector-azure-blob-storage.md)** ve **[ADLS 2.](./connector-azure-data-lake-storage.md)** bağlı hizmetleri desteklenir. Bu özellik için bir değer belirtmezseniz, HDInsight kümesiyle ilişkili depolama kullanılır. Bu özelliğin değeri yalnızca bir Azure depolama bağlı hizmeti olabilir. | No       |
| rootPath              | Spark dosyasını içeren Azure Blob kapsayıcısı ve klasörü. Dosya adı büyük/küçük harfe duyarlıdır. Bu klasörün yapısıyla ilgili ayrıntılar için klasör yapısı bölümüne (sonraki bölüm) bakın. | Yes      |
| entryFilePath         | Spark kodunun/paketinin kök klasörünün göreli yolu. Giriş dosyası bir Python dosyası ya da. jar dosyası olmalıdır. | Yes      |
| Sınıf             | Uygulamanın Java/Spark ana sınıfı      | No       |
| değişkenlerinden             | Spark programına yönelik komut satırı bağımsız değişkenlerinin listesi. | No       |
| proxyUser             | Spark programını yürütmek için kimliğe bürünmeye yönelik kullanıcı hesabı | No       |
| Mini yapılandırma           | Spark yapılandırma özellikleri için değerler belirtin: [Spark yapılandırma-uygulama özellikleri](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No       |
| GetDebugInfo          | Spark günlük dosyalarının, HDInsight kümesi tarafından kullanılan (veya) mini iş Linkedservice tarafından belirtilen Azure depolama 'ya ne zaman kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: Hiçbiri. | No       |

## <a name="folder-structure"></a>Klasör yapısı
Spark işleri Pig/Hive işlerinin daha genişdir. Spark işleri için, jar paketleri (Java SıNıFYOLUNA yerleştirilmiş), Python dosyaları (PYTHONPATH üzerine yerleştirilmiş) ve diğer tüm dosyalar gibi birden çok bağımlılığı sağlayabilirsiniz.

HDInsight bağlı hizmeti tarafından başvurulan Azure Blob depolama alanında aşağıdaki klasör yapısını oluşturun. Ardından, bağımlı dosyaları **Entryfilepath** tarafından temsil edilen kök klasördeki uygun alt klasörlere yükleyin. Örneğin, Python dosyalarını pyfiles alt klasörüne ve jar dosyalarını kök klasörün jar dosyaları dışındaki alt klasörüne yükleyin. Çalışma zamanında Data Factory hizmeti, Azure Blob depolamada aşağıdaki klasör yapısını bekliyor:     

| Yol                  | Açıklama                              | Gerekli | Tür   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` Asıl            | Depolama bağlı hizmetindeki Spark işinin kök yolu | Evet      | Klasör |
| &lt;Kullanıcı tanımlı &gt; | Spark işinin giriş dosyasını işaret eden yol | Yes      | Dosya   |
| ./jars                | Bu klasördeki tüm dosyalar, kümenin Java Sınıfyoluna yüklenir ve yerleştirilir | Hayır       | Klasör |
| ./pyFiles             | Bu klasördeki tüm dosyalar, kümenin PYTHONPATH yüklenir ve bu klasöre yerleştirilir | Hayır       | Klasör |
| ./Files               | Bu klasördeki tüm dosyalar, yürütücü çalışma dizinine yüklenir ve yerleştirilir | Hayır       | Klasör |
| ./Arşivler            | Bu klasördeki tüm dosyalar sıkıştırılmamış | Hayır       | Klasör |
| ./logs                | Spark kümesinden günlükleri içeren klasör. | Hayır       | Klasör |

HDInsight bağlı hizmeti tarafından başvurulan Azure Blob depolamada iki Spark iş dosyası içeren bir depolama örneği aşağıda verilmiştir.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
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
* [Azure Machine Learning Studio (klasik) Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
