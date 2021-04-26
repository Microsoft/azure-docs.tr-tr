---
title: Hive etkinliğini kullanarak verileri dönüştürme-Azure
description: Bir isteğe bağlı/kendi HDInsight kümeniz üzerinde Hive sorguları çalıştırmak için Azure Data Factory v1 'de Hive etkinliğini nasıl kullanabileceğinizi öğrenin.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63e726c98922b789977a884bf747f12186707d57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782716"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory Hive etkinliğini kullanarak verileri dönüştürme 
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

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory Hive etkinliğini kullanarak verileri dönüştürme](../transform-data-using-hadoop-hive.md).

Bir Data Factory işlem [hattının](data-factory-create-pipelines.md) HDInsight Hive etkinliği, [kendi kendinize](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe bağlı](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux tabanlı HDInsight kümenizde Hive sorguları yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) makalesinde oluşturulur.

> [!NOTE] 
> Azure Data Factory yeni kullanıyorsanız, [Azure Data Factory 'ye giriş](data-factory-introduction.md) ile okuyun ve öğreticiyi yapın: Bu makaleyi okumadan önce [ilk veri Işlem hattınızı oluşturun](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Söz dizimi ayrıntıları
| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| name |Etkinliğin adı |Yes |
| açıklama |Etkinliğin ne için kullanıldığını açıklayan metin |No |
| tür |Hdınsighthive |Yes |
| girişi |Hive etkinliği tarafından tüketilen girişler |No |
| çıkışı |Hive etkinliği tarafından oluşturulan çıktılar |Yes |
| linkedServiceName |Data Factory bağlı hizmet olarak kaydedilen HDInsight kümesine başvuru |Yes |
| betik |Hive betiğini satır içi olarak belirt |No |
| scriptPath |Hive betiğini bir Azure Blob depolama alanında depolayın ve dosyanın yolunu sağlayın. ' Script ' veya ' scriptPath ' özelliğini kullanın. İkisi birlikte kullanılamaz. Dosya adı büyük/küçük harfe duyarlıdır. |No |
| tanımlar |' Hiveconf ' kullanarak Hive betiği içinde başvurmak için bir anahtar/değer çiftleri olarak parametre belirtin |No |

## <a name="example"></a>Örnek
Kullanıcıların, şirketiniz tarafından başlatılan oyunları oynatılması için harcadığı zamanı belirlemek istediğiniz oyun günlüğü analizinin bir örneğini ele alalım. 

Aşağıdaki günlük, virgülle () ayrılmış bir oyun günlüğü örneğidir `,` ve şu alanları içerir: ProfileId, SessionStart, Duration, Srcıaddress ve GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Bu verileri işlemek için **Hive betiği** :

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Bu Hive betiğini bir Data Factory işlem hattında yürütmek için, aşağıdakileri yapmanız gerekir

1. [Kendi HDInsight işlem kümenizi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) kaydetmek veya [isteğe bağlı HDInsight işlem kümesini](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)yapılandırmak için bağlı bir hizmet oluşturun. "HDInsightLinkedService" bağlantılı hizmetini arayalım.
2. Verileri barındıran Azure Blob depolama ile bağlantıyı yapılandırmak için [bağlı bir hizmet](data-factory-azure-blob-connector.md) oluşturun. "StorageLinkedService" bağlantılı hizmetini arayalım
3. Girişe ve çıkış verilerine işaret eden veri [kümeleri](data-factory-create-datasets.md) oluşturun. "HiveSampleIn" giriş veri kümesini ve "HiveSampleOut" çıkış veri kümesini arayalım
4. Hive sorgusunu bir dosya olarak, #2 adımında yapılandırılmış Azure Blob depolama alanına kopyalayın. verilerin barındırılmasına yönelik depolama alanı, bu sorgu dosyasını barındırmakla farklıysa, ayrı bir Azure depolama bağlı hizmeti oluşturun ve etkinliğin içinde başvurun. Komut dosyası dosyasını içeren Azure depolama alanını belirtmek üzere Hive sorgu dosyası ve **Scriptlinkedservice** yolunu belirtmek Için **ScriptPath** kullanın. 
   
   > [!NOTE]
   > Ayrıca, **komut dosyası** özelliğini kullanarak, Hive betiğini etkinlik tanımında satır içi olarak da sağlayabilirsiniz. JSON belgesi içindeki betikteki tüm özel karakterlerin kaçışması ve hata ayıklama sorunlarına neden olabileceği için bu yaklaşımı önermiyoruz. En iyi Yöntem #4 adımı takip etmek olacaktır.
   > 
   > 
5. Hdınsighthive etkinliğiyle bir işlem hattı oluşturun. Etkinlik, verileri işler/dönüştürür.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. İşlem hattını dağıtın. Ayrıntılar için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesi. 
7. Veri Fabrikası izleme ve yönetim görünümlerini kullanarak işlem hattını izleyin. Ayrıntılar için [Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md) makalesine bakın. 

## <a name="specifying-parameters-for-a-hive-script"></a>Hive betiği için parametreleri belirtme
Bu örnekte, oyun günlükleri Azure Blob depolama alanında günlük olarak alınır ve Tarih ve saat ile bölümlenen bir klasörde depolanır. Hive betiğini parametreleştirmek ve giriş klasörü konumunu çalışma zamanında dinamik olarak geçirmek ve ayrıca tarih ve saat ile bölümlenen çıktıyı oluşturmak istersiniz.

Parametreli Hive betiğini kullanmak için şunları yapın

* **Tanımlar** içindeki parametreleri tanımlayın.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Hive betiğinde, **$ {hiveconf: ParameterName}** öğesini kullanarak parametreye bakın. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Ayrıca Bkz.
* [Pig etkinliği](data-factory-pig-activity.md)
* [MapReduce etkinliği](data-factory-map-reduce.md)
* [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
* [Spark programları çağırma](data-factory-spark.md)
* [R betikleri çağırma](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

