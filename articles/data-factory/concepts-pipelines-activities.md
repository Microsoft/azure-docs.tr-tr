---
title: Azure Data Factory’de işlem hatları ve etkinlikler
description: Azure Data Factory’de işlem hatları ve etkinlikler hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: bed81633b27d5d0f89cb7e3d7a6e0975de4b6772
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681454"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory’de işlem hatları ve etkinlikler
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-create-pipelines.md)
> * [Geçerli sürüm](concepts-pipelines-activities.md)

Bu makale, Azure Data Factory’de işlem hatlarını ve etkinlikleri anlamanıza ve veri hareketi ile veri işleme senaryolarınız için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur.

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı, bir araya geldiğinde bir görev gerçekleştiren mantıksal etkinlik grubudur. Örneğin, bir işlem hattı günlük verilerini alıp temizleyen ve sonra günlük verilerini analiz etmek üzere bir HDInsight kümesinde Spark işi başlatan bir dizi etkinlik içerebilir. İşlem hattının avantajı, etkinliklerin her birini tek tek yönetmek yerine bir küme olarak yönetmenize olanak tanımasıdır. Örneğin, etkinlikleri bağımsız olarak dağıtmak ve zamanlamak yerine işlem hattını dağıtabilir ve zamanlayabilirsiniz.

Bir işlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Örneğin, kopyalama etkinliğini kullanarak şirket içi SQL Server’dan Azure Blob Depolama’ya veri kopyalayabilirsiniz. Ardından bir Azure HDInsight kümesinde Hive betiği çalıştıran bir Hive etkinliği kullanıp blob depolamadaki verileri işleyerek/dönüştürerek çıktı verileri üretebilirsiniz. Son olarak, ikinci bir kopyalama etkinliği kullanarak üzerinde iş zekası (BI) raporlama çözümlerinin oluşturulduğu bir Azure SQL Veri Ambarı’na çıktı verilerini kopyalayabilirsiniz.

Data Factory üç tür etkinliği destekler: [veri taşıma etkinlikleri](copy-activity-overview.md), [veri dönüştürme etkinlikleri](transform-data.md) ve [denetim etkinlikleri](control-flow-web-activity.md). Bir etkinliğin sıfır veya sıfırdan çok giriş [veri kümesi](concepts-datasets-linked-services.md) olabilir ve her etkinlik bir veya birden çok çıkış [veri kümesi](concepts-datasets-linked-services.md) oluşturabilir. Aşağıdaki diyagramda, Data Factory içindeki işlem hattı, etkinlik ve veri kümesi arasındaki ilişki gösterilmektedir:

![Veri kümesi, etkinlik ve işlem hattı arasındaki ilişki](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Giriş veri kümesi, veri işlem hattındaki bir etkinlik için girişi ve çıktı veri kümesi, etkinliğin çıktısını temsil eder. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Bir veri kümesi oluşturduktan sonra, bu kümeyi bir işlem hattındaki etkinliklerle birlikte kullanabilirsiniz. Örneğin, veri kümesi bir Kopyalama Etkinliğinin veya HDInsightHive Etkinliğinin giriş/çıkış veri kümesi olabilir. Veri kümeleri hakkında daha fazla bilgi için [Azure Data Factory'de Veri Kümeleri](concepts-datasets-linked-services.md) makalesine bakın.

## <a name="data-movement-activities"></a>Veri taşıma etkinlikleri
Data Factory’deki Kopyalama Etkinliği bir kaynak veri deposundan havuz veri deposuna verileri kopyalar. Data Factory bu bölümdeki tabloda listelenen veri depolarını destekler. Herhangi bir kaynaktan gelen veriler herhangi bir havuza yazılabilir. Bir depoya veya depodan veri kopyalama hakkında bilgi edinmek için veri deposuna tıklayın.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Daha fazla bilgi için [Kopyalama Etkinliği - Genel Bakış](copy-activity-overview.md) makalesine bakın.

## <a name="data-transformation-activities"></a>Veri dönüştürme etkinlikleri
Azure Data Factory, işlem hatlarına tek tek veya başka bir etkinlikle zincirleme halinde eklenebilecek aşağıdaki dönüştürme etkinliklerini destekler.

Veri dönüştürme etkinliği | İşlem ortamı
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Akışı](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning etkinlikleri: Toplu Yürütme ve Kaynak Güncelleştirme](transform-data-using-machine-learning.md) | Azure VM
[Saklı Yordam](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Veri Ambarı veya SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Özel Kod](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Not Defteri](transform-data-databricks-notebook.md) | Azure Databricks
[Databricks jar etkinliği](transform-data-databricks-jar.md) | Azure Databricks
[Databricks Python etkinliği](transform-data-databricks-python.md) | Azure Databricks

Daha fazla bilgi için [veri dönüştürme etkinlikleri](transform-data.md) makalesine bakın.

## <a name="control-activities"></a>Denetim etkinlikleri
Aşağıdaki denetim akışı etkinlikleri desteklenir:

Denetim etkinliği | Açıklama
---------------- | -----------
[İşlem Hattı Yürütme Etkinliği](control-flow-execute-pipeline-activity.md) | İşlem Hattı Yürütme etkinliği bir Data Factory işlem hattının başka bir işlem hattını çağırmasını sağlar.
[ForEachActivity](control-flow-for-each-activity.md) | ForEach Etkinliği, işlem hattınızda yinelenen bir denetim akışını tanımlar. Bu etkinlik bir koleksiyon üzerinde yinelemek için kullanılır ve bir döngüde belirtilen etkinlikleri yürütür. Bu etkinliğin döngü uygulaması, programlama dillerindeki Foreach döngü yapısına benzer.
[WebActivity](control-flow-web-activity.md) | Web Etkinliği bir Data Factory işlem hattından özel bir REST uç noktasını çağırmak için kullanılabilir. Etkinlik tarafından kullanılacak ve erişilecek veri kümelerini ve bağlı hizmetleri geçirebilirsiniz.
[Arama Etkinliği](control-flow-lookup-activity.md) | Arama Etkinliği herhangi bir dış kaynaktan bir record/ table name/ değerini okumak veya aramak için kullanılabilir. Sonraki etkinliklerde bu çıktıya daha fazla başvurulabilir.
[Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md) | GetMetadata etkinliği, Azure Data Factory içindeki herhangi bir verinin meta verilerini almak için kullanılabilir.
[Bitiş Etkinliği](control-flow-until-activity.md) | Programlama dillerindeki Do-Until döngü yapısına benzer bir Do-Until döngüsü uygular. Etkinlikle ilişkilendirilmiş olan koşul doğru sonucunu verene kadar bir dizi etkinliği döngüsel olarak yürütür. Data Factory'de bitiş etkinliği için bir zaman aşımı değeri belirtebilirsiniz.
[If Koşulu Etkinliği](control-flow-if-condition-activity.md) | If Koşulu, doğru veya yanlış sonucunu vermesi temelinde dallanmak için kullanılabilir. If Koşulu etkinliği, programlama dilerindeki If deyimiyle aynı işlevselliği sağlar. Koşul `true` sonucunu verdiğinde bir dizi etkinliği, `false` sonucu verdiğinde ise başka bir dizi etkinliği değerlendirmeye alır.
[Bekleme Etkinliği](control-flow-wait-activity.md) | İşlem hattında Bekleme etkinliğini kullandığınızda, işlem hattı izleyen etkinlikleri yürütmeye devam etmeden önce belirtilen süre kadar bekler.

## <a name="pipeline-json"></a>İşlem Hattı JSON
JSON biçiminde işlem hattı şöyle tanımlanır:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Etiket | Açıklama | Tür | Gerekli
--- | ----------- | ---- | --------
ad | İşlem hattının adı. İşlem hattının gerçekleştirdiği eylemi temsil eden bir ad belirtin. <br/><ul><li>En fazla karakter sayısı: 140</li><li>Bir harf, sayı veya alt çizgi ile başlamalıdır (\_)</li><li>Şu karakterler kullanılamaz: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\”</li></ul> | Dize | Evet
açıklama | İşlem hattının ne için kullanıldığını açıklayan metni belirtin. | Dize | Hayır
etkinlikler | **Etkinlikler** bölümünde tanımlanmış bir veya daha fazla etkinlik olabilir. Etkinliklerin JSON öğesi hakkında ayrıntılı bilgi için [Etkinlik JSON](#activity-json) bölümüne bakın. | Dizi | Evet
parametreler | **Parametreler** bölümü, işlem hattınızı yeniden kullanım için esnek hale getiren, işlem hattında tanımlanmış bir veya daha fazla parametreyi içerebilir. | Liste | Hayır

## <a name="activity-json"></a>Etkinlik JSON
**Etkinlikler** bölümünde tanımlanmış bir veya daha fazla etkinlik olabilir. İki temel etkinlik türü vardır: Yürütme ve Denetim Etkinlikleri.

### <a name="execution-activities"></a>Yürütme etkinlikleri
Yürütme etkinlikleri [veri taşıma](#data-movement-activities) ve [veri dönüştürme etkinliklerini](#data-transformation-activities) içerir. Aşağıdaki üst düzey yapıya sahiptir:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

Aşağıdaki tabloda, etkinlik JSON tanımındaki özellikler açıklamaktadır:

Etiket | Açıklama | Gerekli
--- | ----------- | ---------
ad | Etkinliğin adı. Etkinliğin gerçekleştirdiği eylemi temsil eden bir ad belirtin. <br/><ul><li>En fazla karakter sayısı: 55</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterler kullanılamaz: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Evet</li></ul>
açıklama | Etkinliğin ne olduğunu veya ne için kullanıldığını açıklayan metin | Evet
type | Etkinliğin türü. Farklı etkinlik türleri için [Veri Taşıma Etkinlikleri](#data-movement-activities), [Veri Dönüştürme Etkinlikleri](#data-transformation-activities) ve [Denetim Etkinlikleri](#control-activities) bölümlerine bakın. | Evet
linkedServiceName | Etkinlik tarafından kullanılan bağlı hizmetin adı.<br/><br/>Bir etkinlik için gerekli işlem ortamına bağlanan bağlı hizmeti belirtmeniz gerekebilir. | HDInsight Etkinliği, Azure Machine Learning Toplu İşlem Puanlandırma Etkinliği, Saklı Yordam Etkinliği için evet. <br/><br/>Diğer tümü için hayır
typeProperties | typeProperties bölümündeki özellikler her bir etkinlik türüne bağlıdır. Bir etkinliğin tür özelliklerini görmek için önceki bölümde verilen etkinlik bağlantılarına tıklayın. | Hayır
ilke | Etkinliğin çalışma zamanı davranışını etkileyen ilkeler. Bu özellik zaman aşımı ve yeniden deneme davranışını içerir. Belirtilmemişse, varsayılan değerler kullanılır. Daha fazla bilgi için [Etkinlik İlkesi](#activity-policy) bölümüne bakın. | Hayır
dependsOn | Bu özellik etkinlik bağımlılıklarını ve sonraki etkinliklerin önceki etkinliklere ne kadar bağımlı olduğunu tanımlamak için kullanılır. Daha fazla bilgi için bkz. [Etkinlik bağımlılığı](#activity-dependency) | Hayır

### <a name="activity-policy"></a>Etkinlik ilkesi
İlkeler bir etkinliğin çalışma zamanı davranışını etkiler ve yapılandırma seçenekleri sunar. Etkinlik İlkeleri yalnızca yürütme etkinlikleri için kullanılabilir.

### <a name="activity-policy-json-definition"></a>Etkinlik ilkesi JSON tanımı

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

JSON adı | Açıklama | İzin Verilen Değerler | Gerekli
--------- | ----------- | -------------- | --------
timeout | Çalıştırılacak etkinliğinin zaman aşımını belirtir. | Timespan | Hayır. Varsayılan zaman aşımı süresi 7 gündür.
retry | En fazla yeniden deneme sayısı | Tamsayı | Hayır. Varsayılan değer 0'dır
retryIntervalInSeconds | Yeniden deneme girişimleri arasında saniye cinsinden gecikme | Tamsayı | Hayır. Varsayılan değer 30 saniyedir
secureOutput | true olarak ayarlandığında etkinlik çıkışı güvenli olarak kabul edilir ve izleme amacıyla günlüğe alınmaz. | Boole | Hayır. Varsayılan değer false’tur.

### <a name="control-activity"></a>Denetim etkinliği
Denetim etkinlikleri aşağıdaki üst düzey yapıya sahiptir:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Etiket | Açıklama | Gerekli
--- | ----------- | --------
ad | Etkinliğin adı. Etkinliğin gerçekleştirdiği eylemi temsil eden bir ad belirtin.<br/><ul><li>En fazla karakter sayısı: 55</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterler kullanılamaz: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Evet</li><ul>
açıklama | Etkinliğin ne olduğunu veya ne için kullanıldığını açıklayan metin | Evet
type | Etkinliğin türü. Farklı etkinlik türleri için [veri taşıma etkinlikleri](#data-movement-activities), [veri dönüştürme etkinlikleri](#data-transformation-activities) ve [denetim etkinlikleri](#control-activities) bölümlerine bakın. | Evet
typeProperties | typeProperties bölümündeki özellikler her bir etkinlik türüne bağlıdır. Bir etkinliğin tür özelliklerini görmek için önceki bölümde verilen etkinlik bağlantılarına tıklayın. | Hayır
dependsOn | Bu özellik Etkinlik Bağımlılığını ve sonraki etkinliklerin önceki etkinliklere ne kadar bağımlı olduğunu tanımlamak için kullanılır. Daha fazla bilgi için bkz. [etkinlik bağımlılığı](#activity-dependency). | Hayır

### <a name="activity-dependency"></a>Etkinlik bağımlılığı
Etkinlik Bağımlılığı, sonraki etkinliklerin önceki etkinliklere nasıl bağımlı olduğunu tanımlar ve sonraki görevi yürütmeye devam edilip edilmeyeceğine yönelik koşulu belirler. Bir etkinlik farklı bağımlılık koşullarıyla daha önceki bir veya birden çok etkinliğe bağımlı olabilir.

Farklı bağımlılık koşulları şunlardır: Başarılı, Başarısız, Atlandı, Tamamlandı.

Örneğin, bir işlem hattında Etkinlik A -> Etkinlik B ise oluşabilecek farklı senaryolar şunlardır:

- Etkinlik B, Etkinlik A’ya **başarılı** koşuluyla bağımlıdır: Etkinlik B yalnızca Etkinlik A’nın son durumu başarılı ise çalışır
- Etkinlik B, Etkinlik A’ya **başarısız** koşuluyla bağımlıdır: Etkinlik B yalnızca Etkinlik A’nın son durumu başarısız ise çalışır
- Etkinlik B, Etkinlik A’ya **tamamlandı** koşuluyla bağımlıdır: Etkinlik B yalnızca Etkinlik A’nın son durumu başarılı veya başarısız ise çalışır
- Etkinlik B, Etkinlik A’ya **atlandı** koşuluyla bağımlıdır: Etkinlik B, Etkinlik A’nın son durumu atlandı ise çalışır. Atlandı koşulu, her bir etkinliğin yalnızca önceki etkinlik başarılı olursa çalıştığı Etkinlik X -> Etkinlik Y -> Etkinlik Z senaryosunda gerçekleşir. Etkinlik X başarısız olursa, Etkinlik Y hiçbir zaman yürütülmeyeceği için durumu “Atlandı” olur. Benzer şekilde, Etkinlik Z’nin durumu da "Atlandı" olur.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Örnek: Etkinlik 2, Etkinlik 1’in başarılı olmasına bağlıdır

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Örnek kopyalama işlem hattı
Aşağıdaki örnek işlem hattında, **Etkinlikler** bölümünde **Kopyalama** türünde olan bir etkinlik vardır. Bu örnekte [kopyalama etkinliği](copy-activity-overview.md), verileri Azure Blob depolama alanından Azure SQL veritabanına kopyalar.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Aşağıdaki noktalara dikkat edin:

- Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır.
- Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır. JSON biçiminde veri kümeleri tanımlamak için [Veri Kümeleri](concepts-datasets-linked-services.md) makalesine bakın.
- **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. [Veri taşıma etkinlikleri](#data-movement-activities) bölümünde, verileri veri deposuna/veri deposundan taşıma hakkında daha fazla bilgi almak için kaynak veya havuz olarak kullanmak istediğiniz veri deposuna tıklayın.

Bu işlem hattını oluşturmak üzere izlenecek tam yol için bkz. [Hızlı Başlangıç: veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Örnek dönüştürme işlem hattı
Aşağıdaki örnek işlem hattında, **etkinlikler** bölümünde **HDInsightHive** türünde olan bir etkinlik vardır. Bu örnekte [HDInsight Hive etkinliği](transform-data-using-hadoop-hive.md), bir Azure HDInsight Hadoop kümesinde Hive betik dosyası çalıştırarak verileri bir Azure Blob depolamadan dönüştürür.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Aşağıdaki noktalara dikkat edin:

- Etkinlikler bölümünde **türü** **HDInsightHive** olarak ayarlanmış yalnızca bir etkinlik vardır.
- **partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilen AzureStorageLinkedService adıyla) ve `adfgetstarted` kapsayıcısındaki betik klasöründe depolanır.
- `defines` bölümü, hive betiğine Hive yapılandırma değerleri olarak (örn `{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`) geçirilen çalışma zamanı ayarlarını belirtmek için kullanılır.

**TypeProperties** bölümü her bir dönüştürme etkinliği için farklıdır. Bir dönüştürme etkinliği için desteklenen tür özellikleri hakkında bilgi edinmek için [Veri dönüştürme etkinlikleri](#data-transformation-activities) içindeki dönüştürme etkinliklerine tıklayın.

Bu işlem hattını oluşturmak üzere izlenecek tam yol için bkz. [Öğretici: Spark kullanarak veri dönüştürme](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Bir işlem hattında birden çok etkinlik
Önceki iki örnekte işlem hatları yalnızca bir etkinlik içeriyordu. Bir işlem hattında birden fazla etkinliğiniz olabilir. Bir işlem hattında birden fazla etkinlik varsa ve sonraki etkinlikler önceki etkinliklere bağımlı değilse, etkinlikler paralel olarak çalışabilir.

Sonraki etkinliklerin önceki etkinliklere nasıl bağımlı olduğunu tanımlayan ve sonraki görevi yürütmeye devam edilip edilmeyeceğine yönelik koşulu belirleyen [etkinlik bağımlılığını](#activity-dependency) kullanarak iki etkinliği zincirleyebilirsiniz. Bir etkinlik farklı bağımlılık koşullarıyla daha önceki bir veya daha çok etkinliğe bağımlı olabilir.

## <a name="scheduling-pipelines"></a>İşlem hatlarını zamanlama
İşlem hatları tetikleyiciler tarafından zamanlanır. Farklı türde tetikleyiciler vardır (işlem hatlarının duvar saat zamanlamasıyla tetiklenmesine olanak tanıyan zamanlayıcı tetikleyici ve işlem hatlarını talep üzerine tetikleyen el ile tetikleyici). Tetikleyiciler hakkında daha fazla bilgi için [işlem hattı yürütme ve tetikleyicileri](concepts-pipeline-execution-triggers.md) makalesine bakın.

Tetikleyicinizin bir işlem hattı çalıştırmasını başlatması için tetikleyici tanımındaki belirli işlem hattının işlem hattı başvurusunu eklemeniz gerekir. İşlem hatları ve tetikleyiciler n-m ilişkisine sahiptir. Birden çok tetikleyici tek bir işlem hattını, bir tetikleyici de birden fazla işlem hattını başlatabilir. Tetikleyici tanımlandıktan sonra işlem hattını tetiklemesini başlatmak için tetikleyiciyi başlatmanız gerekir. Tetikleyiciler hakkında daha fazla bilgi için [işlem hattı yürütme ve tetikleyicileri](concepts-pipeline-execution-triggers.md) makalesine bakın.

Örneğin, "MyCopyPipeline" işlem hattımı başlatmak istediğim "Tetikleyici A" adlı zamanlayıcı tetikleyiciye sahip olduğumu varsayalım. Tetikleyiciyi aşağıdaki örnekte gösterildiği gibi tanımlayın:

### <a name="trigger-a-definition"></a>Tetikleyici A tanımı

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Sonraki adımlar
Etkinliklerle işlem hatları oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticilere bakın:

- [Kopyalama etkinliği içeren işlem hattı oluşturma](quickstart-create-data-factory-powershell.md)
- [Veri dönüştürme etkinliğine sahip işlem hattı oluşturma](tutorial-transform-data-spark-powershell.md)
