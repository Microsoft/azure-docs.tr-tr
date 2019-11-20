---
title: Azure Data Factory Pig etkinliğini kullanarak verileri dönüştürme
description: Bir Azure Data Factory 'de Pig etkinliğini kullanarak bir isteğe bağlı/kendi HDInsight kümeniz üzerinde Pig betikleri nasıl çalıştırabileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a6861a22f853bb0b48eb7c9832adb251bc3467ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682388"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Azure Data Factory Pig etkinliğini kullanarak verileri dönüştürme
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
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory Pig etkinliğini kullanarak verileri dönüştürme](../transform-data-using-hadoop-pig.md).


Bir [Data Factory işlem](data-factory-create-pipelines.md) hattındaki HDInsight Pig etkinliği, [kendi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) veya [isteğe bağlı](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux tabanlı HDInsight kümenizdeki Pig sorgularını yürütür. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) makalesinde oluşturulur.

> [!NOTE] 
> Azure Data Factory yeni kullanıyorsanız, [Azure Data Factory 'ye giriş](data-factory-introduction.md) ile okuyun ve öğreticiyi yapın: Bu makaleyi okumadan önce [ilk veri Işlem hattınızı oluşturun](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Sözdizimi

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
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
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Söz dizimi ayrıntıları

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| ad |Etkinliğin adı |Evet |
| açıklama |Etkinliğin ne için kullanıldığını açıklayan metin |Hayır |
| type |HDinsightPig |Evet |
| girişi |Pig etkinliği tarafından tüketilen bir veya daha fazla giriş |Hayır |
| çıkışı |Pig etkinliği tarafından üretilen bir veya daha fazla çıkış |Evet |
| linkedServiceName |Data Factory bağlı hizmet olarak kaydedilen HDInsight kümesine başvuru |Evet |
| betiğini çalıştırın |Pig betiğini satır içi olarak belirt |Hayır |
| scriptPath |Pig betiğini bir Azure Blob depolama alanına depolayın ve dosyanın yolunu sağlayın. ' Script ' veya ' scriptPath ' özelliğini kullanın. İkisi birlikte kullanılamaz. Dosya adı büyük/küçük harfe duyarlıdır. |Hayır |
| tanımlar |Pig betiği içinde başvurmak için parametreleri anahtar/değer çiftleri olarak belirtin |Hayır |

## <a name="example"></a>Örnek
Bir oyun günlüğü analizinin bir örneğini, şirketiniz tarafından başlatılan oyuncuların oynatılması için harcadığı süreyi belirlemek istediğiniz yere göz atalım.

Aşağıdaki örnek oyun günlüğü, virgülle (,) ayrılmış bir dosyadır. Aşağıdaki alanları içerir: ProfileId, SessionStart, Duration, Srcıaddress ve GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Bu verileri işlemek için **Pig betiği** :

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Bu Pig betiğini bir Data Factory işlem hattında yürütmek için aşağıdaki adımları uygulayın:

1. [Kendi HDInsight işlem kümenizi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) kaydetmek veya [isteğe bağlı HDInsight işlem kümesini](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)yapılandırmak için bağlı bir hizmet oluşturun. Bu bağlı hizmeti **HDInsightLinkedService**arayalım.
2. Verileri barındıran Azure Blob depolama ile bağlantıyı yapılandırmak için [bağlı bir hizmet](data-factory-azure-blob-connector.md) oluşturun. Bu bağlı hizmeti **StorageLinkedService**arayalım.
3. Girişe ve çıkış verilerine işaret eden veri [kümeleri](data-factory-create-datasets.md) oluşturun. **Pigsamplein** giriş veri kümesini ve çıkış veri kümesi **Pigsampleout**' ı çağıralım.
4. Pig sorgusunu, #2 adımında yapılandırılan Azure Blob depolama alanındaki bir dosyaya kopyalayın. Verileri barındıran Azure depolama, sorgu dosyasını barındıran bilgisayardan farklıysa, ayrı bir Azure depolama bağlı hizmeti oluşturun. Etkinlik yapılandırmasındaki bağlantılı hizmete bakın. **ScriptPath** kullanarak Pig betik dosyası ve **scriptlinkedservice**yolunu belirtin. 
   
   > [!NOTE]
   > **Betik** özelliğini kullanarak etkinlik tanımında Pig betiğini satır içi olarak da sağlayabilirsiniz. Ancak, betikteki tüm özel karakterlerin kaçışması ve hata ayıklama sorunlarına neden olabileceği için bu yaklaşımı önermiyoruz. En iyi Yöntem #4 adımı takip etmek olacaktır.
   >
   >
5. HDInsightPig etkinliğiyle işlem hattını oluşturun. Bu etkinlik, HDInsight kümesinde Pig betiği çalıştırarak giriş verilerini işler.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. İşlem hattını dağıtın. Ayrıntılar için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesi. 
7. Veri Fabrikası izleme ve yönetim görünümlerini kullanarak işlem hattını izleyin. Ayrıntılar için [Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md) makalesine bakın.

## <a name="specifying-parameters-for-a-pig-script"></a>Pig betiği için parametreleri belirtme
Aşağıdaki örneği göz önünde bulundurun: oyun günlükleri Azure Blob depolamada günlük olarak alınır ve Tarih ve saate göre bölümlenmiş bir klasörde depolanır. Pig betiğini parametreleştirmek ve giriş klasörü konumunu çalışma zamanında dinamik olarak geçirmek ve ayrıca tarih ve saat ile bölümlenen çıktıyı oluşturmak istersiniz.

Parametreli Pig betiğini kullanmak için şunları yapın:

* **Tanımlar**içindeki parametreleri tanımlayın.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* Pig komut dosyasında, aşağıdaki örnekte gösterildiği gibi, ' **$ParameterName**' kullanarak parametrelere başvurun:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Ayrıca Bkz.
* [Hive etkinliği](data-factory-hive-activity.md)
* [MapReduce etkinliği](data-factory-map-reduce.md)
* [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
* [Spark programlarını çağırma](data-factory-spark.md)
* [R betiklerini çağırma](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
