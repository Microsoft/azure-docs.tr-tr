---
title: Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme
description: Spark etkinliğini kullanarak bir Azure Data Factory ardışık düzeninde Spark programları çalıştırarak verileri dönüştürmeyi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 5f3bb88d3a2e43abe1776a4b46e4ab35490db8ec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683756"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory Spark etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-spark.md)
> * [Geçerli sürüm](transform-data-using-spark.md)

Bir [Data Factory işlem](concepts-pipelines-activities.md) hattındaki Spark etkinliği, [kendi](compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) bağlı HDInsight kümeniz üzerinde bir Spark programı yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur. İsteğe bağlı Spark bağlı hizmeti kullandığınızda Data Factory, verileri işlemek için tam zamanında bir Spark kümesi oluşturur ve işlem tamamlandıktan sonra kümeyi siler. 


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
| ad                  | İşlem hattındaki etkinliğin adı.    | Evet      |
| açıklama           | Etkinliğin ne yaptığını açıklayan metin.  | Hayır       |
| type                  | Spark etkinliği için etkinlik türü HDInsightSpark ' dir. | Evet      |
| linkedServiceName     | Spark programının çalıştığı HDInsight Spark bağlı hizmetinin adı. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Evet      |
| Mini iş Linkedservice | Spark iş dosyasını, bağımlılıklarını ve günlüklerini tutan Azure depolama bağlı hizmeti.  Bu özellik için bir değer belirtmezseniz, HDInsight kümesiyle ilişkili depolama kullanılır. Bu özelliğin değeri yalnızca bir Azure depolama bağlı hizmeti olabilir. | Hayır       |
| rootPath              | Spark dosyasını içeren Azure Blob kapsayıcısı ve klasörü. Dosya adı büyük/küçük harfe duyarlıdır. Bu klasörün yapısıyla ilgili ayrıntılar için klasör yapısı bölümüne (sonraki bölüm) bakın. | Evet      |
| entryFilePath         | Spark kodunun/paketinin kök klasörünün göreli yolu. Giriş dosyası bir Python dosyası ya da. jar dosyası olmalıdır. | Evet      |
| className             | Uygulamanın Java/Spark ana sınıfı      | Hayır       |
| Değişkenlerinden             | Spark programına yönelik komut satırı bağımsız değişkenlerinin listesi. | Hayır       |
| proxyUser             | Spark programını yürütmek için kimliğe bürünmeye yönelik kullanıcı hesabı | Hayır       |
| Mini yapılandırma           | Spark yapılandırma özellikleri için değerler belirtin: [Spark yapılandırma-uygulama özellikleri](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Hayır       |
| GetDebugInfo          | Spark günlük dosyalarının, HDInsight kümesi tarafından kullanılan (veya) mini iş Linkedservice tarafından belirtilen Azure depolama 'ya ne zaman kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: yok. | Hayır       |

## <a name="folder-structure"></a>Klasör yapısı
Spark işleri Pig/Hive işlerinin daha genişdir. Spark işleri için, jar paketleri (Java SıNıFYOLUNA yerleştirilmiş), Python dosyaları (PYTHONPATH üzerine yerleştirilmiş) ve diğer tüm dosyalar gibi birden çok bağımlılığı sağlayabilirsiniz.

HDInsight bağlı hizmeti tarafından başvurulan Azure Blob depolama alanında aşağıdaki klasör yapısını oluşturun. Ardından, bağımlı dosyaları **Entryfilepath**tarafından temsil edilen kök klasördeki uygun alt klasörlere yükleyin. Örneğin, Python dosyalarını pyfiles alt klasörüne ve jar dosyalarını kök klasörün jar dosyaları dışındaki alt klasörüne yükleyin. Çalışma zamanında Data Factory hizmeti, Azure Blob depolamada aşağıdaki klasör yapısını bekliyor:     

| Yol                  | Açıklama                              | Gerekli | Tür   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (kök)            | Depolama bağlı hizmetindeki Spark işinin kök yolu | Evet      | Klasör |
| &lt;Kullanıcı tanımlı &gt; | Spark işinin giriş dosyasını işaret eden yol | Evet      | Dosya   |
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
* [Machine Learning Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
