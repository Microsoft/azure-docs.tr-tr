---
title: Azure Data Factory-JSON betik başvurusu
description: Data Factory varlıkları için JSON şemaları sağlar.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b72be7026b0b8077cf5bf9f775d10fd03edd9118
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815632"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory-JSON betik başvurusu
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir.


Bu makalede, Azure Data Factory varlıkları (işlem hattı, etkinlik, veri kümesi ve bağlı hizmet) tanımlamak için JSON şemaları ve örnekler sağlanmıştır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>İşlem hattı
Bir işlem hattı tanımı için üst düzey yapı aşağıdaki gibidir:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Aşağıdaki tabloda, işlem hattı JSON tanımındaki özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli
-------- | ----------- | --------
| ad | İşlem hattının adı. Etkinliğin veya işlem hattının yapması için yapılandırıldığı eylemi temsil eden bir ad belirtin<br/><ul><li>En fazla karakter sayısı: 260</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterlere izin verilmez: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Evet |
| açıklama |Etkinliğin veya işlem hattının ne için kullanıldığını açıklayan metin | Hayır |
| etkinlikler | Etkinliklerin bir listesini içerir. | Evet |
| start |İşlem hattının başlangıç tarihi-saati. [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601)olmalıdır. Örneğin: 2014-10-14T16:32:41. <br/><br/>Bir yerel saat belirtmek mümkündür, örneğin bir saat. Örnek: `2016-02-27T06:00:00**-05:00`, bu 6 EST.<br/><br/>Başlangıç ve bitiş özellikleri, işlem hattının etkin dönemini belirtir. Çıkış dilimleri bu etkin dönemde yalnızca ile üretilir. |Hayır<br/><br/>End özelliği için bir değer belirtirseniz, Start özelliği için bir değer belirtmeniz gerekir.<br/><br/>Bir işlem hattı oluşturmak için başlangıç ve bitiş saatlerinin her ikisi de boş olabilir. İşlem hattının çalışması için etkin bir süre ayarlamak üzere her iki değeri de belirtmeniz gerekir. İşlem hattı oluştururken başlangıç ve bitiş zamanlarını belirtmezseniz, daha sonra set-Azdatafactorypıpelineactiveperiod cmdlet 'ini kullanarak bunları ayarlayabilirsiniz. |
| erer |İşlem hattının bitiş tarihi-saati. Belirtilmişse ISO biçiminde olmalıdır. Örneğin: 2014-10-14T17:32:41 <br/><br/>Bir yerel saat belirtmek mümkündür, örneğin bir saat. Örnek: `2016-02-27T06:00:00**-05:00`, bu 6 EST.<br/><br/>İşlem hattını süresiz olarak çalıştırmak için, End özelliğinin değeri olarak 9999-09-09 değerini belirtin. |Hayır <br/><br/>Start özelliği için bir değer belirtirseniz, End özelliği için bir değer belirtmeniz gerekir.<br/><br/>**Start** özelliği için notlara bakın. |
| isPaused |True olarak ayarlanırsa işlem hattı çalıştırılmaz. Varsayılan değer = false. Etkinleştirmek veya devre dışı bırakmak için bu özelliği kullanabilirsiniz. |Hayır |
| pipelineMode |İşlem hattı için zamanlama çalıştırmaları yöntemi. İzin verilen değerler: zamanlanmış (varsayılan), Onetime.<br/><br/>' Zamanlanmış ', işlem hattının etkin döneme (başlangıç ve bitiş saati) göre belirli bir zaman aralığında çalıştığını gösterir. ' Onetime ', işlem hattının yalnızca bir kez çalışacağını gösterir. Her seferinde bir kez oluşturulduktan sonra bir süre işlem hattı değiştirilemez/güncelleştirilemez. Kerelik ayarı hakkında ayrıntılı bilgi için bkz. [kerelik işlem hattı](data-factory-create-pipelines.md#onetime-pipeline) . |Hayır |
| expirationTime |Oluşturma sonrasında işlem hattının geçerli olduğu ve sağlanmış kalması gereken süre. Etkin, başarısız veya bekleyen çalıştırmaları yoksa, işlem hattı süre sonu zamanına ulaştığında otomatik olarak silinir. |Hayır |


## <a name="activity"></a>Etkinlik
Bir işlem hattı tanımı içindeki bir etkinliğin üst düzey yapısı (Activities öğesi) aşağıdaki gibidir:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

Aşağıdaki tabloda, etkinlik JSON tanımı içindeki özellikler açıklanır:

| Etiket | Açıklama | Gerekli |
| --- | --- | --- |
| ad |Etkinliğin adı. Etkinliğin yapması için yapılandırıldığı eylemi temsil eden bir ad belirtin<br/><ul><li>En fazla karakter sayısı: 260</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterlere izin verilmez: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Evet |
| açıklama |Etkinliğin ne için kullanıldığını açıklayan metin. |Hayır |
| type |Etkinliğin türünü belirtir. Farklı etkinlik türleri için [VERI depoları](#data-stores) ve [veri dönüştürme etkinlikleri](#data-transformation-activities) bölümlerine bakın. |Evet |
| girişi |Etkinlik tarafından kullanılan giriş tabloları<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Hdınsightstreaming ve SqlServerStoredProcedure etkinlikleri için Hayır <br/> <br/> Tüm diğerleri için Evet |
| çıkışı |Etkinlik tarafından kullanılan çıkış tabloları.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Evet |
| linkedServiceName |Etkinlik tarafından kullanılan bağlı hizmetin adı. <br/><br/>Bir etkinlik için gerekli işlem ortamına bağlanan bağlı hizmeti belirtmeniz gerekebilir. |HDInsight etkinlikleri, Azure Machine Learning etkinlikleri ve saklı yordam etkinliği için Evet. <br/><br/>Diğer tümü için hayır |
| typeProperties |TypeProperties bölümündeki özellikler etkinliğin türüne bağlıdır. |Hayır |
| ilke |Etkinliğin çalışma zamanı davranışını etkileyen ilkeler. Belirtilmemişse, varsayılan ilkeler kullanılır. |Hayır |
| leyiciyi |"Scheduler" özelliği, etkinlik için istenen zamanlamayı tanımlamak için kullanılır. Alt özellikleri, [bir veri kümesindeki kullanılabilirlik özelliğindeki](data-factory-create-datasets.md#dataset-availability)olanlarla aynıdır. |Hayır |

### <a name="policies"></a>İlkeler
İlkeler, özellikle bir tablonun dilimi işlendiğinde bir etkinliğin çalışma zamanı davranışını etkiler. Aşağıdaki tabloda ayrıntılar verilmektedir.

| Özellik | İzin verilen değerler | Varsayılan değer | Açıklama |
| --- | --- | --- | --- |
| Zamanlı |Tamsayı <br/><br/>En büyük değer: 10 |1 |Etkinliğin eşzamanlı yürütmelerinin sayısı.<br/><br/>Farklı dilimlerde gerçekleşebileceğini paralel etkinlik yürütmelerinin sayısını belirler. Örneğin, bir etkinliğin büyük bir kullanılabilir veri kümesiyle geçmesi gerekiyorsa daha büyük bir eşzamanlılık değeri, veri işlemeyi hızlandırır. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |İşlenmekte olan veri dilimlerinin sıralamasını belirler.<br/><br/>Örneğin, 2 dilimdeyse (4pm 'de bir diğeri, 5 pm 'de bir diğeri) ve her ikisi de yürütme bekleniyor. ExecutionPriorityOrder 'ı NewestFirst olarak ayarlarsanız, önce 5 PM 'deki dilim işlenir. Benzer şekilde, önce executionPriorityORder değerini Oldestolarak ayarlarsanız, 4 PM 'deki dilim işlenir. |
| retry |Tamsayı<br/><br/>En büyük değer 10 olabilir |0 |Dilim için veri işleme hatası olarak işaretlenmeden önce yeniden deneme sayısı. Bir veri dilimi için Etkinlik yürütme, belirtilen yeniden deneme sayısına yeniden denenir. Yeniden deneme, hatadan sonra mümkün olan en kısa sürede yapılır. |
| timeout |TimeSpan |00:00:00 |Etkinliğin zaman aşımı. Örnek: 00:10:00 (zaman aşımı 10 dakika)<br/><br/>Bir değer belirtilmemişse veya 0 ise, zaman aşımı sonsuz olur.<br/><br/>Bir dilimdeki veri işleme süresi, zaman aşımı değerini aşarsa, iptal edilir ve sistem işlemeyi yeniden denemeye çalışır. Yeniden deneme sayısı, retry özelliğine bağlıdır. Zaman aşımı oluştuğunda, durum zaman aşımına uğradı olarak ayarlanır. |
| ilir |TimeSpan |00:00:00 |Dilimin veri işleme başlamadan önce gecikme süresi belirtin.<br/><br/>Bir veri dilimi için etkinliğin yürütülmesi, gecikme beklenen yürütme zamanından sonra başlatılır.<br/><br/>Örnek: 00:10:00 (10 dakikalık gecikme süresi anlamına gelir) |
| longRetry |Tamsayı<br/><br/>En büyük değer: 10 |1 |Dilim yürütmesi başarısız olmadan önce uzun yeniden deneme girişimi sayısı.<br/><br/>longRetry denemeleri, Longretryınterval tarafından aralıklıdır. Bu nedenle, yeniden deneme girişimleri arasında bir saat belirtmeniz gerekiyorsa, longRetry kullanın. Hem yeniden dene hem de longRetry belirtilirse, her bir longRetry denemesi, yeniden deneme girişimleri içerir ve deneme sayısı üst sınırı * longRetry.<br/><br/>Örneğin, etkinlik ilkesinde aşağıdaki ayarlara sahipseniz:<br/>Yeniden dene: 3<br/>longRetry: 2<br/>Longretryınterval: 01:00:00<br/><br/>Yürütülecek yalnızca bir dilim olduğunu varsayalım (durum bekliyor) ve etkinliğin yürütülmesi her seferinde başarısız olur. Başlangıçta 3 ardışık yürütme denemesi vardır. Her denemeden sonra dilimin durumu yeniden denenecek. İlk 3 deneme bittikten sonra, dilim durumu LongRetry olur.<br/><br/>Bir saatten sonra (yani, longRetryInteval değeri), başka bir 3 ardışık yürütme denemesi kümesi olur. Bundan sonra, dilim durumu başarısız olur ve daha fazla yeniden deneme denenmelidir. Bu nedenle, genel 6 deneme yapılmıştır.<br/><br/>Herhangi bir yürütme başarılı olursa, dilim durumu kullanılmaya devam edilir ve daha fazla yeniden deneme denenmelidir.<br/><br/>longRetry, bağımlı verilerin belirleyici olmayan saatlerde ulaştığı durumlarda veya genel ortam, veri işlemenin gerçekleştiği bir düzmeme durumunda kullanılabilir. Bu gibi durumlarda, diğer bir süre sonra yeniden denemeler yapmak, istenen çıktının bir zaman aralığı ile sonuçlanmasından sonra bu işlemi yapmaya ve gerçekleşmeyebilir.<br/><br/>Uyarı sözcüğü: longRetry veya Longretryınterval için yüksek değerler ayarlamayın. Genellikle, daha yüksek değerler diğer systemik sorunlarını kapsıyor. |
| Longretryınterval |TimeSpan |00:00:00 |Uzun yeniden deneme girişimleri arasındaki gecikme |

### <a name="typeproperties-section"></a>typeProperties bölümü
TypeProperties bölümü her etkinlik için farklıdır. Dönüştürme etkinlikleri yalnızca tür özelliklerine sahiptir. Bir işlem hattındaki dönüştürme etkinliklerini tanımlayan JSON örnekleri için bu makaledeki [VERI dönüştürme etkinlikleri](#data-transformation-activities) bölümüne bakın.

**Kopyalama etkinliği** , typeproperties bölümünde iki alt bölüm içerir: **kaynak** ve **Havuz**. Veri deposunun kaynak ve/veya havuz olarak nasıl kullanılacağını gösteren JSON örnekleri için bu makaledeki [VERI depoları](#data-stores) bölümüne bakın.

### <a name="sample-copy-pipeline"></a>Örnek kopyalama işlem hattı
Aşağıdaki örnek işlem hattında, **Etkinlikler** bölümünde **Kopyalama** türünde olan bir etkinlik vardır. Bu örnekte, [kopyalama etkinliği](data-factory-data-movement-activities.md) verileri bir Azure Blob depolama ALANıNDAN Azure SQL veritabanına kopyalar.

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
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Aşağıdaki noktalara dikkat edin:

* Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır.
* Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır.
* **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir.

Veri deposunun kaynak ve/veya havuz olarak nasıl kullanılacağını gösteren JSON örnekleri için bu makaledeki [VERI depoları](#data-stores) bölümüne bakın.

Bu işlem hattını oluşturma hakkında ayrıntılı yönergeler için bkz. [öğretici: blob DEPOLAMADAN SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Örnek dönüştürme işlem hattı
Aşağıdaki örnek işlem hattında, **etkinlikler** bölümünde **HDInsightHive** türünde olan bir etkinlik vardır. Bu örnekte [HDInsight Hive etkinliği](data-factory-hive-activity.md), bir Azure HDInsight Hadoop kümesinde Hive betik dosyası çalıştırarak verileri bir Azure Blob depolamadan dönüştürür.

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
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Aşağıdaki noktalara dikkat edin:

* Etkinlikler bölümünde **türü** **HDInsightHive** olarak ayarlanmış yalnızca bir etkinlik vardır.
* **partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilen **AzureStorageLinkedService** adıyla) ve **adfgetstarted** kapsayıcısındaki **betik** klasöründe depolanır.
* **Tanımlar** bölümü, Hive betiğine Hive yapılandırma değerleri olarak geçirilen çalışma zamanı ayarlarını belirtmek için kullanılır (örneğin `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Bir işlem hattındaki dönüştürme etkinliklerini tanımlayan JSON örnekleri için bu makaledeki [VERI dönüştürme etkinlikleri](#data-transformation-activities) bölümüne bakın.

Bu işlem hattını oluşturma hakkında ayrıntılı yönergeler için bkz. [öğretici: Hadoop kümesi kullanarak verileri işlemek için ilk işlem hattınızı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Bağlı hizmet
Bağlı hizmet tanımı için üst düzey yapı aşağıdaki gibidir:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Aşağıdaki tabloda, etkinlik JSON tanımı içindeki özellikler açıklanır:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| ad | Bağlı hizmetin adı. | Evet |
| Özellikler-tür | Bağlı hizmetin türü. Örneğin: Azure Storage, Azure SQL veritabanı. |
| typeProperties | TypeProperties bölümünde her bir veri deposu veya işlem ortamı için farklı öğeler bulunur. Tüm işlem bağlantılı hizmetleri için tüm veri deposu bağlı hizmetleri ve [işlem ortamları](#compute-environments) için veri depoları bölümüne bakın |

## <a name="dataset"></a>Veri kümesi
Azure Data Factory bir veri kümesi aşağıdaki gibi tanımlanır:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

Aşağıdaki tabloda, yukarıdaki JSON 'daki özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| ad | Veri kümesinin adı. Bkz. adlandırma kuralları için [Azure Data Factory adlandırma kuralları](data-factory-naming-rules.md) . |Evet |NA |
| type | Veri kümesinin türü. Azure Data Factory tarafından desteklenen türlerden birini belirtin (örneğin: AzureBlob, Azuressqltable). Data Factory tarafından desteklenen tüm veri depoları ve veri kümesi türleri için [VERI depoları](#data-stores) bölümüne bakın. |
| yapısı | Veri kümesinin şeması. Sütunları, türleri, vb. içerir. | Hayır |NA |
| typeProperties | Seçili türe karşılık gelen özellikler. Desteklenen türler ve özellikleri için [VERI depoları](#data-stores) bölümüne bakın. |Evet |NA |
| external | Bir veri kümesinin bir Data Factory işlem hattı tarafından açıkça oluşturulup oluşturulmayacağını belirten Boole bayrağı. |Hayır |false |
| availability | Veri kümesi üretimi için işleme penceresini veya Dilimleme modelini tanımlar. Veri kümesi Dilimleme modeliyle ilgili ayrıntılar için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) makalesi. |Evet |NA |
| ilke |Veri kümesi dilimlerinin yerine getirilmesi gereken ölçütü veya koşulu tanımlar. <br/><br/>Ayrıntılar için bkz. veri kümesi Ilkesi bölümü. |Hayır |NA |

**Yapı** bölümündeki her sütun aşağıdaki özellikleri içerir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| ad |Sütunun adı. |Evet |
| type |Sütunun veri türü.  |Hayır |
| ayarı |Tür belirtildiğinde ve .NET Type `Datetime` veya `Datetimeoffset` olduğunda kullanılacak .NET tabanlı kültür. `en-us` varsayılan değerdir. |Hayır |
| formatını |Tür belirtildiğinde kullanılacak biçim dizesi, `Datetime` veya `Datetimeoffset` .NET türüdür. |Hayır |

Aşağıdaki örnekte, veri kümesinde `slicetimestamp`, `projectname`ve `pageviews` üç sütun bulunur ve bunlar sırasıyla: String, String ve Decimal türündedir.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Aşağıdaki tabloda **kullanılabilirlik** bölümünde kullanabileceğiniz özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| frequency |Veri kümesi dilimi üretiminin zaman birimini belirtir.<br/><br/><b>Desteklenen sıklık</b>: dakika, saat, gün, hafta, ay |Evet |NA |
| interval |Sıklık için bir çarpan belirtir<br/><br/>"Sıklık x Interval", dilimin ne sıklıkta üretildiğini belirler.<br/><br/>Veri kümesinin saatlik olarak dilimlendirilecekliğine ihtiyaç duyuyorsanız <b>Sıklık</b> değerini <b>Hour</b>ve <b>Interval</b> değerini <b>1</b>olarak ayarlarsınız.<br/><br/><b>Note</b>: sıklık değerini dakika olarak belirtirseniz, aralığı 15 ' ten az olmayacak şekilde ayarlamanız önerilir |Evet |NA |
| biçim |Dilimin aralığın başlangıcında/sonunda üretilmesi gerekip gerekmediğini belirtir.<ul><li>StartOfInterval</li><li>Endofınterval</li></ul><br/><br/>Sıklık değeri month olarak ayarlanmışsa ve Style, Endofınterval olarak ayarlanırsa, dilim ayın son gününde oluşturulur. Stil StartOfInterval olarak ayarlandıysa, dilim ayın ilk gününde oluşturulur.<br/><br/>Sıklık, gün olarak ayarlanır ve stil, Endofınterval olarak ayarlanırsa, dilim günün son saati içinde oluşturulur.<br/><br/>Sıklık değeri Hour olarak ayarlanmışsa ve Style, Endofınterval olarak ayarlanırsa, dilim saatin sonunda üretilir. Örneğin, 1 PM – 2 dönemi için dilim için dilim 2 PM 'de oluşturulur. |Hayır |Endofınterval |
| anchorDateTime |Veri kümesi dilim sınırlarını hesaplamak için Zamanlayıcı tarafından kullanılan mutlak konumu tanımlar. <br/><br/><b>Note</b>: anchordatetime değerinin sıklığından daha ayrıntılı olan tarih bölümleri varsa, daha ayrıntılı parçalar yok sayılır. <br/><br/>Örneğin, <b>Aralık</b> <b>saatlik</b> (sıklık: Hour ve Interval: 1) ve <b>anchordatetime</b> değeri <b>dakika ve saniye</b> içeriyorsa, anchordatetime değerinin <b>dakika ve saniye</b> kısımları yok sayılır. |Hayır |01/01/0001 |
| konumu |Tüm veri kümesi dilimlerinin başlangıcını ve bitişini kaydırılan zaman aralığı. <br/><br/><b>Note</b>: hem anchordatetime hem de kaydır belirtilirse, sonuç Birleşik kaydırmadır. |Hayır |NA |

Aşağıdaki kullanılabilirlik bölümü, çıkış veri kümesinin saatlik olarak (veya) giriş veri kümesinin kullanılabilir olduğunu belirtir:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

DataSet tanımındaki **ilke** bölümü, veri kümesi dilimlerinin yerine getirilmesi gereken ölçütü veya koşulu tanımlar.

| İlke Adı | Açıklama | Uygulanan | Gerekli | Varsayılan |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Bir **Azure blobundaki** verilerin en düşük boyut gereksinimlerini (megabayt cinsinden) karşıladığını doğrular. |Azure Blob |Hayır |NA |
| minimumRows |Bir **Azure SQL veritabanındaki** veya bir **Azure tablosundaki** verilerin en az sayıda satırı içerdiğini doğrular. |<ul><li>Azure SQL Veritabanı</li><li>Azure Tablosu</li></ul> |Hayır |NA |

**Örnek:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Bir veri kümesi Azure Data Factory tarafından üretilmediği sürece, **dış**olarak işaretlenmelidir. Bu ayar genellikle, etkinlik veya işlem hattı zinciri kullanılmadığı takdirde bir işlem hattının ilk etkinliğinin girişleri için geçerlidir.

| Ad | Açıklama | Gerekli | Varsayılan değer |
| --- | --- | --- | --- |
| dataDelay |Verilen dilim için dış verilerin kullanılabilirliğine ilişkin denetim için gecikme süresi. Örneğin, veriler saatlik olarak kullanılabilirse, dış verileri görmek için denetim kullanılabilir ve ilgili dilim, dataDelay kullanılarak geciktirilebilir.<br/><br/>Yalnızca geçerli süre için geçerlidir.  Örneğin, şu anda 1:00 PM ise ve bu değer 10 dakikadır, doğrulama 1:10 PM 'de başlar.<br/><br/>Bu ayar geçmişte dilimleri etkilemez (dilim bitiş zamanı + dataDelay < dilimler) herhangi bir gecikme olmadan işlenir.<br/><br/>`day.hours:minutes:seconds` biçim kullanılarak 23:59 saatten büyük bir süre belirtilmelidir. Örneğin, 24 saat belirtmek için 24:00:00 kullanmayın; Bunun yerine 1.00:00:00 kullanın. 24:00:00 kullanıyorsanız, 24 gün (24.00:00:00) olarak kabul edilir. 1 gün ve 4 saat için 1:04:00:00 belirtin. |Hayır |0 |
| RetryInterval |Bir hata ve sonraki yeniden deneme girişimi arasındaki bekleme süresi. Deneme başarısız olursa, sonraki deneme, RetryInterval 'dan sonra olur. <br/><br/>Şu an 1:00 PM ise ilk denemeye başladık. İlk doğrulama denetiminin tamamlanma süresi 1 dakikadır ve işlem başarısız olduysa, sonraki yeniden deneme 1:00 + 1 dakika (süre) + 1 dakika (yeniden deneme aralığı) = 1:02 PM olur. <br/><br/>Geçmişte dilimlerde bir gecikme yoktur. Yeniden deneme anında gerçekleşir. |Hayır |00:01:00 (1 dakika) |
| retryTimeout |Her yeniden deneme girişimi için zaman aşımı.<br/><br/>Bu özellik 10 dakikaya ayarlanırsa, doğrulamanın 10 dakika içinde tamamlanması gerekir. Doğrulama işlemini gerçekleştirmek için 10 dakikadan uzun sürerse, yeniden deneme zaman aşımına uğrar.<br/><br/>Tüm doğrulama denemeleri zaman aşımına uğrarsa, dilim zaman aşımı olarak işaretlenir. |Hayır |00:10:00 (10 dakika) |
| maximumRetry |Dış verilerin kullanılabilirliğine yönelik denetlenecek zaman sayısı. İzin verilen en büyük değer 10 ' dur. |Hayır |3 |


## <a name="data-stores"></a>VERI DEPOLARı
[Bağlı hizmet](#linked-service) bölümü, tüm bağlı hizmet türlerinde ortak olan JSON öğelerinin açıklamalarını sağladı. Bu bölüm her bir veri deposuna özgü JSON öğeleri hakkında ayrıntılar sağlar.

[DataSet](#dataset) bölümü, tüm veri kümeleri türlerinde ortak olan JSON öğelerinin açıklamalarını sağladı. Bu bölüm her bir veri deposuna özgü JSON öğeleri hakkında ayrıntılar sağlar.

[Etkinlik](#activity) bölümü, tüm etkinlik türleri için ortak olan JSON öğelerinin açıklamalarını sağladı. Bu bölüm, bir kopyalama etkinliğinde kaynak/havuz olarak kullanıldığında her bir veri deposuna özgü JSON öğeleri hakkında ayrıntılar sağlar.

Bağlı hizmet, veri kümesi ve kopyalama etkinliğinin kaynak/havuz için JSON şemalarını görmek üzere ilgilendiğiniz deponun bağlantısına tıklayın.

| Kategori | Veri deposu
|:--- |:--- |
| **Azure** |[Azure Blob Depolama](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Veritabanı](#azure-sql-database) |
| &nbsp; |[Azure SQL Veri Ambarı](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Bilişsel Arama](#azure-cognitive-search) |
| &nbsp; |[Azure Tablo Depolama](#azure-table-storage) |
| **Veritabanları** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Dosya** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Dosya Sistemi](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Diğer** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Web Tablosu |

## <a name="azure-blob-storage"></a>Azure Blob Depolama

### <a name="linked-service"></a>Bağlı hizmet
İki tür bağlı hizmet vardır: Azure Storage bağlı hizmeti ve Azure Storage SAS bağlı hizmeti.

#### <a name="azure-storage-linked-service"></a>Azure Storage Bağlı Hizmeti
Azure depolama hesabınızı, **hesap anahtarını**kullanarak bir veri fabrikasına bağlamak Için bir Azure depolama bağlı hizmeti oluşturun. Azure depolama bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **azurestorage**olarak ayarlayın. Ardından, aşağıdaki özellikleri **typeproperties** bölümünde belirtebilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| connectionString |ConnectionString özelliği için Azure depolama 'ya bağlanmak için gereken bilgileri belirtin. |Evet |

##### <a name="example"></a>Örnek

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS bağlı hizmeti
Azure Storage SAS bağlı hizmeti, bir Azure Depolama hesabını, paylaşılan erişim Imzası (SAS) kullanarak bir Azure Data Factory 'ye bağlayabilmeniz için izin verir. Veri fabrikasını depolama alanındaki tüm/belirli kaynaklara (blob/kapsayıcı) kısıtlı/zamana göre erişim ile sağlar. Paylaşılan erişim Imzasını kullanarak Azure depolama hesabınızı bir veri fabrikasına bağlamak için bir Azure Storage SAS bağlı hizmeti oluşturun. Azure Storage SAS bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **azurestorampasas**olarak ayarlayın. Ardından, aşağıdaki özellikleri **typeproperties** bölümünde belirtebilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| sasUri |Blob, kapsayıcı veya tablo gibi Azure depolama kaynakları için paylaşılan erişim Imzası URI 'SI belirtin. |Evet |

##### <a name="example"></a>Örnek

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Bu bağlı hizmetler hakkında daha fazla bilgi için bkz. [Azure Blob Storage Bağlayıcısı](data-factory-azure-blob-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure blob veri kümesi tanımlamak için, veri kümesinin **türünü** **AzureBlob**olarak ayarlayın. Ardından, **Typeproperties** bölümünde aşağıdaki Azure Blob 'a özgü özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |BLOB depolama alanındaki kapsayıcının ve klasörün yolu. Örnek: myblobcontainer\myblobfolder\ |Evet |
| fileName |Blobun adı. Dosya adı isteğe bağlıdır ve büyük/küçük harfe duyarlıdır.<br/><br/>Bir dosya adı belirtirseniz, etkinlik (kopyalama dahil) belirli bir Blobun üzerinde kullanılır.<br/><br/>Dosya adı belirtilmediğinde, Copy, giriş veri kümesi için folderPath içindeki tüm Blobları içerir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: `Data.<Guid>.txt` (örneğin:: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt |Hayır |
| partitionedBy |partitionedBy, isteğe bağlı bir özelliktir. Bu uygulamayı, zaman serisi verileri için dinamik bir folderPath ve filename belirtmek üzere kullanabilirsiniz. Örneğin, folderPath her saat veri için parametreleştirilebilirler. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Daha fazla bilgi için bkz. [Azure Blob Bağlayıcısı](data-factory-azure-blob-connector.md#dataset-properties) makalesi.

### <a name="blobsource-in-copy-activity"></a>Kopyalama etkinliğinde BlobSource
Verileri bir Azure Blob depolamadan kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **blobsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True (varsayılan değer), yanlış |Hayır |

#### <a name="example-blobsource"></a>Örnek: **Blobsource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>Kopyalama etkinliğinde BlobSink
Verileri bir Azure Blob depolama alanına kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **blobsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| copyBehavior |Kaynak BlobSource veya FileSystem olduğunda kopyalama davranışını tanımlar. |<b>Preservehierarchy</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu hedef dosyanın hedef klasöre göreli yolu ile aynıdır.<br/><br/><b>DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyindedir. Hedef dosyaların otomatik olarak üretilen adı var. <br/><br/><b>Mergefiles (varsayılan):</b> kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya/BLOB adı belirtilmişse, birleştirilmiş dosya adı belirtilen ad olur; Aksi takdirde, otomatik olarak oluşturulan dosya adı olacaktır. |Hayır |

#### <a name="example-blobsink"></a>Örnek: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure Blob Bağlayıcısı](data-factory-azure-blob-connector.md#copy-activity-properties) makalesi.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Bağlı hizmet
Azure Data Lake Store bağlı bir hizmet tanımlamak için, bağlantılı hizmetin türünü **AzureDataLakeStore**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **AzureDataLakeStore** olarak ayarlanmalıdır | Evet |
| dataLakeStoreUri | Azure Data Lake Store hesabı hakkındaki bilgileri belirtin. Şu biçimdedir: `https://[accountname].azuredatalakestore.net/webhdfs/v1` veya `adl://[accountname].azuredatalakestore.net/`. | Evet |
| subscriptionId | Data Lake Store ait olduğu Azure abonelik kimliği. | Havuz için gerekli |
| resourceGroupName | Data Lake Store ait olduğu Azure Kaynak grubu adı. | Havuz için gerekli |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Evet (hizmet sorumlusu kimlik doğrulaması için) |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. | Evet (hizmet sorumlusu kimlik doğrulaması için) |
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet (hizmet sorumlusu kimlik doğrulaması için) |
| yetkisi | **Data Factory düzenleyicisinde** **Yetkilendir** düğmesine tıklayın ve bu özelliğe OTOMATIK olarak üretilen yetkilendirme URL 'sini atayan kimlik bilgilerinizi girin. | Evet (Kullanıcı kimlik bilgisi kimlik doğrulaması için)|
| Kimliği | OAuth yetkilendirme oturumundan gelen OAuth oturum kimliği. Her oturum kimliği benzersizdir ve yalnızca bir kez kullanılabilir. Bu ayar Data Factory düzenleyicisini kullandığınızda otomatik olarak oluşturulur. | Evet (Kullanıcı kimlik bilgisi kimlik doğrulaması için) |

#### <a name="example-using-service-principal-authentication"></a>Örnek: hizmet sorumlusu kimlik doğrulamasını kullanma
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Örnek: Kullanıcı kimlik bilgisi kimlik doğrulaması kullanma
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure Data Lake Store Bağlayıcısı](data-factory-azure-datalake-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure Data Lake Store veri kümesi tanımlamak için, veri kümesinin **türünü** **AzureDataLakeStore**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| folderPath |Azure Data Lake deposundaki kapsayıcının ve klasörün yolu. |Evet |
| fileName |Azure Data Lake deposundaki dosyanın adı. Dosya adı isteğe bağlıdır ve büyük/küçük harfe duyarlıdır. <br/><br/>Bir dosya adı belirtirseniz, etkinlik (kopyalama dahil) belirli dosya üzerinde kullanılır.<br/><br/>Dosya adı belirtilmediğinde, Copy, giriş veri kümesi için folderPath içindeki tüm dosyaları içerir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: `Data.<Guid>.txt` (örneğin:: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt |Hayır |
| partitionedBy |partitionedBy, isteğe bağlı bir özelliktir. Bu uygulamayı, zaman serisi verileri için dinamik bir folderPath ve filename belirtmek üzere kullanabilirsiniz. Örneğin, folderPath her saat veri için parametreleştirilebilirler. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

#### <a name="example"></a>Örnek
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure Data Lake Store Bağlayıcısı](data-factory-azure-datalake-connector.md#dataset-properties) makalesi.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Kopyalama etkinliğinde Azure Data Lake Store kaynağı
Bir Azure Data Lake Store veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **AzureDataLakeStoreSource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

**AzureDataLakeStoreSource** , aşağıdaki özellikler **typeproperties** bölümünü destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True (varsayılan değer), yanlış |Hayır |

#### <a name="example-azuredatalakestoresource"></a>Örnek: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure Data Lake Store Bağlayıcısı](data-factory-azure-datalake-connector.md#copy-activity-properties) makalesi.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Kopyalama etkinliğinde havuz Azure Data Lake Store
Verileri bir Azure Data Lake Store kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **AzureDataLakeStoreSink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| copyBehavior |Kopyalama davranışını belirtir. |<b>Preservehierarchy</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu hedef dosyanın hedef klasöre göreli yolu ile aynıdır.<br/><br/><b>DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyinde oluşturulur. Hedef dosyalar otomatik olarak oluşturulan adla oluşturulur.<br/><br/><b>Mergefiles</b>: kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya/BLOB adı belirtilmişse, birleştirilmiş dosya adı belirtilen ad olur; Aksi takdirde, otomatik olarak oluşturulan dosya adı olacaktır. |Hayır |

#### <a name="example-azuredatalakestoresink"></a>Örnek: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure Data Lake Store Bağlayıcısı](data-factory-azure-datalake-connector.md#copy-activity-properties) makalesi.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Bağlı hizmet
Azure Cosmos DB bağlı bir hizmet tanımlamak için, bağlantılı hizmetin **türünü** **DocumentDb**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| connectionString |Azure Cosmos DB veritabanına bağlanmak için gereken bilgileri belirtin. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Daha fazla bilgi için bkz. [Azure Cosmos DB Bağlayıcısı](data-factory-azure-documentdb-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure Cosmos DB veri kümesi tanımlamak için, veri kümesinin **türünü** **documentdbcollection**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| Ma |Azure Cosmos DB koleksiyonunun adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Daha fazla bilgi için bkz. [Azure Cosmos DB Bağlayıcısı](data-factory-azure-documentdb-connector.md#dataset-properties) makalesi.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Kopyalama etkinliğinde koleksiyon kaynağı Azure Cosmos DB
Bir Azure Cosmos DB veri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **Documentdbcollectionsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için sorguyu belirtin. |Sorgu dizesi Azure Cosmos DB tarafından destekleniyor. <br/><br/>Örnek: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Hayır <br/><br/>Belirtilmemişse, çalıştırılan SQL ifadesini: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Belgenin iç içe olduğunu belirten özel karakter |Herhangi bir karakter. <br/><br/>Azure Cosmos DB, iç içe yapılara izin verilen JSON belgeleri için bir NoSQL deposudur. Azure Data Factory, kullanıcının, "." olan nestingSeparator aracılığıyla hiyerarşiyi görüntülemesine olanak sağlar. Yukarıdaki örneklerde. Ayırıcı ile kopyalama etkinliği, tablo tanımındaki "ad. First", "Name. Middle" ve "Name. Last" öğelerine göre Ilk olarak üç alt öğe içeren "ad" nesnesini oluşturur. |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Kopyalama etkinliğinde koleksiyon havuzunu Azure Cosmos DB
Azure Cosmos DB verileri kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **Documentdbcollectionsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| nestingSeparator |İç içe geçmiş belgenin gerekli olduğunu göstermek için kaynak sütun adında özel bir karakter. <br/><br/>Örneğin, yukarıdaki: çıktı tablosundaki `Name.First`, Cosmos DB belgesinde aşağıdaki JSON yapısını üretir:<br/><br/>"Ad": {<br/>    "İlk": "John"<br/>}, |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir.<br/><br/>Varsayılan değer `.` ' dır (nokta). |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. <br/><br/>Varsayılan değer `.` ' dır (nokta). |
| WriteBatchSize |Azure Cosmos DB hizmetine belge oluşturmak için paralel istek sayısı.<br/><br/>Bu özelliği kullanarak Azure Cosmos DB veri kopyalarken performansı ayarlayabilirsiniz. Azure Cosmos DB için daha fazla paralel istek gönderildiği için writeBatchSize ' i artırdığınızda daha iyi bir performans sağlayabilirsiniz. Bununla birlikte, hata iletisini oluşturabilecek azaltmaktan kaçınmanız gerekir: "Istek hızı çok büyük".<br/><br/>Daraltma, belge boyutu, belgelerdeki terim sayısı, hedef koleksiyonun dizin oluşturma ilkesi vb. dahil olmak üzere bir dizi etkene karar vermiştir. Kopyalama işlemleri için, en fazla üretilen iş (2.500 istek birimi/saniye) sağlamak üzere daha iyi bir koleksiyon (örneğin, S3) kullanabilirsiniz. |Tamsayı |Hayır (varsayılan: 5) |
| writeBatchTimeout |İşlemin zaman aşımına uğramadan önce tamamlanmasını bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Daha fazla bilgi için bkz. [Azure Cosmos DB Bağlayıcısı](data-factory-azure-documentdb-connector.md#copy-activity-properties) makalesi.

## <a name="azure-sql-database"></a>Azure SQL Veritabanı

### <a name="linked-service"></a>Bağlı hizmet
Azure SQL veritabanı bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **Azuressqldatabase**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| connectionString |ConnectionString özelliği için Azure SQL veritabanı örneğine bağlanmak için gereken bilgileri belirtin. |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure SQL veritabanı veri kümesi tanımlamak için, veri kümesinin **türünü** **Azurestabtable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure SQL veritabanı örneğindeki tablonun veya görünümün adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Daha fazla bilgi için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md#dataset-properties) makalesi.

### <a name="sql-source-in-copy-activity"></a>Kopyalama etkinliğinde SQL kaynağı
Verileri bir Azure SQL veritabanından kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **SQLSource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örnek: `select * from MyTable`. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Daha fazla bilgi için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md#copy-activity-properties) makalesi.

### <a name="sql-sink-in-copy-activity"></a>Kopyalama etkinliğinde SQL havuzu
Verileri Azure SQL veritabanı 'na kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **sqlsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |
| WriteBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Kopyalama etkinliği için, yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için kullanılan otomatik olarak oluşturulan dilim tanımlayıcısı ile doldurulacak bir sütun adı belirtin. |Binary (32) veri türüne sahip bir sütunun sütun adı. |Hayır |
| sqlWriterStoredProcedureName |Hedef tabloya veri (güncelleştirme/ekleme) içeren saklı yordamın adı. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |
| sqlWriterTableType |Saklı yordamda kullanılacak bir tablo türü adı belirtin. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilirler. |Tablo türü adı. |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md#copy-activity-properties) makalesi.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı

### <a name="linked-service"></a>Bağlı hizmet
Azure SQL veri ambarı bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **Azuresqldw**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| connectionString |ConnectionString özelliği için Azure SQL veri ambarı örneğine bağlanmak için gereken bilgileri belirtin. |Evet |



#### <a name="example"></a>Örnek

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL veri ambarı Bağlayıcısı](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure SQL veri ambarı veri kümesi tanımlamak için, veri kümesinin **türünü** **Azuresqldwtable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure SQL veri ambarı veritabanında tablonun veya görünümün adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL veri ambarı Bağlayıcısı](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) makalesi.

### <a name="sql-dw-source-in-copy-activity"></a>Kopyalama etkinliğinde SQL DW kaynağı
Azure SQL veri ambarı 'ndan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **sqldwsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL veri ambarı Bağlayıcısı](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) makalesi.

### <a name="sql-dw-sink-in-copy-activity"></a>Kopyalama etkinliğinde SQL DW havuzu
Verileri Azure SQL veri ambarı 'na kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **sqldwsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. |Sorgu ekstresi. |Hayır |
| allowPolyBase |BULKıNSERT mekanizması yerine PolyBase 'in (uygun olduğunda) kullanılıp kullanılmayacağını belirtir. <br/><br/> **PolyBase 'in kullanılması, verileri SQL veri ambarı 'na yüklemek için önerilen yoldur.** |True <br/>False (varsayılan) |Hayır |
| polyBaseSettings |**Allowpolybase** özelliği **true**olarak ayarlandığında belirtilenebilir bir özellik grubu. |&nbsp; |Hayır |
| rejectValue |Sorgu başarısız olmadan önce reddedilecek satırların sayısını veya yüzdesini belirtir. <br/><br/>[Dış tablo oluşturma (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) konusunun **bağımsız değişkenler** bölümünde PolyBase 'in reddetme seçenekleri hakkında daha fazla bilgi edinin. |0 (varsayılan), 1, 2,... |Hayır |
| rejectType |RejectValue seçeneğinin sabit değer değeri mi yoksa yüzde olarak mı belirtilmediğini belirtir. |Değer (varsayılan), yüzde |Hayır |
| rejectSampleValue |PolyBase reddedilen satırların yüzdesini yeniden hesaplamadan önce alınacak satır sayısını belirler. |1, 2,... |Evet, **rejectType** **ise** |
| useTypeDefault |PolyBase metin dosyasından verileri aldığında, sınırlandırılmış metin dosyalarında eksik değerlerin nasıl işleneceğini belirtir.<br/><br/>[Dış dosya biçimi oluşturma (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)Içindeki bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi edinin. |True, false (varsayılan) |Hayır |
| WriteBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure SQL veri ambarı Bağlayıcısı](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) makalesi.

## <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

### <a name="linked-service"></a>Bağlı hizmet
Azure Bilişsel Arama bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **azuresearch**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| url | Arama Hizmeti URL 'SI. | Evet |
| anahtar | Arama hizmeti için yönetici anahtarı. | Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure bilişsel Arama Bağlayıcısı](data-factory-azure-search-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure Bilişsel Arama veri kümesi tanımlamak için, veri kümesinin **türünü** **AzureSearchIndex**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| type | Type özelliği **AzureSearchIndex**olarak ayarlanmalıdır.| Evet |
| indexName | Arama dizininin adı. Data Factory dizini oluşturmaz. Dizinin Azure Bilişsel Arama mevcut olması gerekir. | Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Daha fazla bilgi için bkz. [Azure bilişsel Arama Bağlayıcısı](data-factory-azure-search-connector.md#dataset-properties) makalesi.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Kopyalama etkinliğinde Azure Bilişsel Arama Dizin havuzu
Verileri bir arama dizinine kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **AzureSearchIndexSink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| -------- | ----------- | -------------- | -------- |
| writeBehavior | Dizinde bir belgenin zaten mevcut olduğu zaman birleştirilip birleştirilmeyeceğini veya değiştirip edilmeyeceğini belirtir. | Birleştir (varsayılan)<br/>Karşıya Yükle| Hayır |
| WriteBatchSize | Arabellek boyutu writeBatchSize ulaştığında verileri arama dizinine yükler. | 1 ile 1.000 arasında. Varsayılan değer 1000 ' dir. | Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Azure bilişsel Arama Bağlayıcısı](data-factory-azure-search-connector.md#copy-activity-properties) makalesi.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Bağlı hizmet
İki tür bağlı hizmet vardır: Azure Storage bağlı hizmeti ve Azure Storage SAS bağlı hizmeti.

#### <a name="azure-storage-linked-service"></a>Azure Storage Bağlı Hizmeti
Azure depolama hesabınızı, **hesap anahtarını**kullanarak bir veri fabrikasına bağlamak Için bir Azure depolama bağlı hizmeti oluşturun. Azure depolama bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **azurestorage**olarak ayarlayın. Ardından, aşağıdaki özellikleri **typeproperties** bölümünde belirtebilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Type özelliği: **Azurestorage** olarak ayarlanmalıdır |Evet |
| connectionString |ConnectionString özelliği için Azure depolama 'ya bağlanmak için gereken bilgileri belirtin. |Evet |

**Örnek:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS bağlı hizmeti
Azure Storage SAS bağlı hizmeti, bir Azure Depolama hesabını, paylaşılan erişim Imzası (SAS) kullanarak bir Azure Data Factory 'ye bağlayabilmeniz için izin verir. Veri fabrikasını depolama alanındaki tüm/belirli kaynaklara (blob/kapsayıcı) kısıtlı/zamana göre erişim ile sağlar. Paylaşılan erişim Imzasını kullanarak Azure depolama hesabınızı bir veri fabrikasına bağlamak için bir Azure Storage SAS bağlı hizmeti oluşturun. Azure Storage SAS bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **azurestorampasas**olarak ayarlayın. Ardından, aşağıdaki özellikleri **typeproperties** bölümünde belirtebilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Type özelliği: **Azurestorampasas** olarak ayarlanmalıdır |Evet |
| sasUri |Blob, kapsayıcı veya tablo gibi Azure depolama kaynakları için paylaşılan erişim Imzası URI 'SI belirtin. |Evet |

**Örnek:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Bu bağlı hizmetler hakkında daha fazla bilgi için bkz. [Azure Tablo depolama Bağlayıcısı](data-factory-azure-table-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Azure Tablo veri kümesi tanımlamak için, veri kümesinin **türünü** **AzureTable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure Tablo veritabanı örneğindeki tablonun adı. |Evet. Bir tableName, azureTableSourceQuery olmadan belirtildiğinde, tablodaki tüm kayıtlar hedefe kopyalanır. Bir azureTableSourceQuery belirtilmişse, sorguyu karşılayan tablodaki kayıtlar da hedefe kopyalanır. |

#### <a name="example"></a>Örnek

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Bu bağlı hizmetler hakkında daha fazla bilgi için bkz. [Azure Tablo depolama Bağlayıcısı](data-factory-azure-table-connector.md#dataset-properties) makalesi.

### <a name="azure-table-source-in-copy-activity"></a>Kopyalama etkinliğinde Azure tablo kaynağı
Azure Tablo depolamadan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **AzureTableSource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| azureTableSourceQuery |Verileri okumak için özel sorguyu kullanın. |Azure tablo sorgu dizesi. Sonraki bölümde örneklere bakın. |Hayır. Bir tableName, azureTableSourceQuery olmadan belirtildiğinde, tablodaki tüm kayıtlar hedefe kopyalanır. Bir azureTableSourceQuery belirtilmişse, sorguyu karşılayan tablodaki kayıtlar da hedefe kopyalanır. |
| azureTableSourceIgnoreTableNotFound |Swallow tablosunun özel durumunun mevcut olup olmadığını belirtin. |DEĞERI<br/>YANLÝÞ |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Bu bağlı hizmetler hakkında daha fazla bilgi için bkz. [Azure Tablo depolama Bağlayıcısı](data-factory-azure-table-connector.md#copy-activity-properties) makalesi.

### <a name="azure-table-sink-in-copy-activity"></a>Kopyalama etkinliğinde Azure Tablo havuzu
Azure Tablo depolama birimine veri kopyalıyorsanız, kopyalama etkinliğinin **Havuz türünü** **AzureTableSink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Havuz tarafından kullanılabilen varsayılan bölüm anahtarı değeri. |Bir dize değeri. |Hayır |
| azureTablePartitionKeyName |Değerleri bölüm anahtarları olarak kullanılan sütunun adını belirtin. Belirtilmemişse, bölüm anahtarı olarak AzureTableDefaultPartitionKeyValue kullanılır. |Bir sütun adı. |Hayır |
| azureTableRowKeyName |Sütun değerleri satır anahtarı olarak kullanılan sütunun adını belirtin. Belirtilmezse, her satır için bir GUID kullanın. |Bir sütun adı. |Hayır |
| azureTableInsertType |Azure tablosuna veri ekleme modu.<br/><br/>Bu özellik, çıkış tablosunda eşleşen bölüm ve satır anahtarlarının değerlerinin değiştirilmesini veya birleştirildiğini denetler. <br/><br/>Bu ayarların (birleştirme ve değiştirme) nasıl çalıştığını öğrenmek için bkz. [varlık ekleme veya birleştirme](https://msdn.microsoft.com/library/azure/hh452241.aspx) ve [varlık ekleme veya değiştirme](https://msdn.microsoft.com/library/azure/hh452242.aspx) konuları. <br/><br> Bu ayar tablo düzeyinde değil, satır düzeyinde uygulanır ve hiçbir seçenek, girişte bulunmayan çıkış tablosundaki satırları silmez. |Birleştir (varsayılan)<br/>değiştirin |Hayır |
| WriteBatchSize |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler |TimeSpan<br/><br/>Örnek: "00:20:00" (20 dakika) |Hayır (varsayılan olarak, depolama istemcisi varsayılan zaman aşımı değeri 90 sn) |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Bu bağlı hizmetler hakkında daha fazla bilgi için bkz. [Azure Tablo depolama Bağlayıcısı](data-factory-azure-table-connector.md#copy-activity-properties) makalesi.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Bağlı hizmet
Amazon Redshift bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **AmazonRedshift**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |Amazon Redshift sunucusunun IP adresi veya ana bilgisayar adı. |Evet |
| port |Amazon Redshift sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası sayısı. |Hayır, varsayılan değer: 5439 |
| veritabanı |Amazon Redshift veritabanının adı. |Evet |
| kullanıcı adı |Veritabanına erişimi olan kullanıcının adı. |Evet |
| password |Kullanıcı hesabı için parola. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Daha fazla bilgi için bkz. Amazon Redshift Connector makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Amazon Redshift veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Amazon Redshift veritabanındaki bağlı hizmetin başvurduğu tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |


#### <a name="example"></a>Örnek

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Daha fazla bilgi için bkz. Amazon Redshift Connector makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Amazon Redshift 'tan veri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Daha fazla bilgi için bkz. Amazon Redshift Connector makalesi.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Bağlı hizmet
IBM DB2 bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **OnPremisesDB2**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |DB2 sunucusunun adı. |Evet |
| veritabanı |DB2 veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. Şema adı büyük/küçük harfe duyarlıdır. |Hayır |
| authenticationType |DB2 veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi DB2 veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Daha fazla bilgi için bkz. IBM DB2 bağlayıcı makalesi.

### <a name="dataset"></a>Veri kümesi
Bir DB2 veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu DB2 veritabanı örneğindeki tablonun adı. TableName, büyük/küçük harfe duyarlıdır. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse)

#### <a name="example"></a>Örnek
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. IBM DB2 bağlayıcı makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
IBM DB2 'den veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `"query": "select * from "MySchema"."MyTable""`. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

#### <a name="example"></a>Örnek
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Daha fazla bilgi için bkz. IBM DB2 bağlayıcı makalesi.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Bağlı hizmet
MySQL bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **OnPremisesMySql**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |MySQL sunucusunun adı. |Evet |
| veritabanı |MySQL veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. |Hayır |
| authenticationType |MySQL veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler: `Basic`. |Evet |
| Nitelen |MySQL veritabanına bağlanmak için Kullanıcı adını belirtin. |Evet |
| password |Belirttiğiniz kullanıcı hesabı için parola belirtin. |Evet |
| gatewayName |Data Factory hizmetinin şirket içi MySQL veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. [MySQL Bağlayıcısı](data-factory-onprem-mysql-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir MySQL veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlantılı hizmetin başvurduğu MySQL veritabanı örneğindeki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Daha fazla bilgi için bkz. [MySQL Bağlayıcısı](data-factory-onprem-mysql-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir MySQL veritabanından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |


#### <a name="example"></a>Örnek
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [MySQL Bağlayıcısı](data-factory-onprem-mysql-connector.md#copy-activity-properties) makalesi.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Bağlı hizmet
Bir Oracle bağlantılı hizmeti tanımlamak için, bağlantılı hizmetin **türünü** **OnPremisesOracle**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| driverType | Oracle Database verileri kopyalamak için kullanılacak sürücüyü belirtin. İzin verilen değerler **Microsoft** veya **ODP** (varsayılan). Sürücü ayrıntılarında desteklenen sürüm ve yükleme bölümüne bakın. | Hayır |
| connectionString | ConnectionString özelliği için Oracle Database örneğine bağlanmak için gereken bilgileri belirtin. | Evet |
| gatewayName | Şirket içi Oracle sunucusuna bağlanmak için kullanılan ağ geçidinin adı |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Oracle Bağlayıcısı](data-factory-onprem-oracle-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Oracle veri kümesi tanımlamak için, veri kümesinin **türünü** **oracletable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Oracle Database tablonun adı. |Hayır ( **oracleReaderQuery** of **oraclesource** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Daha fazla bilgi için bkz. [Oracle Bağlayıcısı](data-factory-onprem-oracle-connector.md#dataset-properties) makalesi.

### <a name="oracle-source-in-copy-activity"></a>Kopyalama etkinliğinde Oracle kaynağı
Bir Oracle veritabanından veri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **oraclesource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| oracleReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin, `select * from MyTable` <br/><br/>Belirtilmemişse, çalıştırılan SQL ifadesini: `select * from MyTable` |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Oracle Bağlayıcısı](data-factory-onprem-oracle-connector.md#copy-activity-properties) makalesi.

### <a name="oracle-sink-in-copy-activity"></a>Kopyalama etkinliğinde Oracle havuzu
İ. i Oracle veritabanına veri kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **oraclesink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: 00:30:00 (30 dakika). |Hayır |
| WriteBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 100) |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için kullanılan otomatik olarak oluşturulan dilim tanımlayıcısı ile doldurulacak kopyalama etkinliğinin sütun adını belirtin. |Binary (32) veri türüne sahip bir sütunun sütun adı. |Hayır |

#### <a name="example"></a>Örnek
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Daha fazla bilgi için bkz. [Oracle Bağlayıcısı](data-factory-onprem-oracle-connector.md#copy-activity-properties) makalesi.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Bağlı hizmet
PostgreSQL bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **OnPremisesPostgreSql**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |PostgreSQL sunucusunun adı. |Evet |
| veritabanı |PostgreSQL veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. Şema adı büyük/küçük harfe duyarlıdır. |Hayır |
| authenticationType |PostgreSQL veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi PostgreSQL veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Daha fazla bilgi için bkz. [PostgreSQL Bağlayıcısı](data-factory-onprem-postgresql-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir PostgreSQL veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu PostgreSQL veritabanı örneğindeki tablonun adı. TableName, büyük/küçük harfe duyarlıdır. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

#### <a name="example"></a>Örnek
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Daha fazla bilgi için bkz. [PostgreSQL Bağlayıcısı](data-factory-onprem-postgresql-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir PostgreSQL veritabanından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: "Query": "select * from \"MySchema\".\"MyTable\"". |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [PostgreSQL Bağlayıcısı](data-factory-onprem-postgresql-connector.md#copy-activity-properties) makalesi.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Bağlı hizmet
SAP Business Warehouse (siyah beyaz) bağlı hizmeti tanımlamak için, bağlı hizmetin **türünü** **sapta**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
sunucu | SAP BW örneğinin bulunduğu sunucunun adı. | string | Evet
systemNumber | SAP BW sisteminin sistem numarası. | Dize olarak temsil edilen iki basamaklı ondalık sayı. | Evet
ClientID | SAP W sistemindeki istemcinin istemci KIMLIĞI. | Dize olarak temsil edilen üç basamaklı ondalık sayı. | Evet
kullanıcı adı | SAP sunucusuna erişimi olan kullanıcının adı | string | Evet
password | Kullanıcının parolası. | string | Evet
gatewayName | Data Factory hizmetinin şirket içi SAP BW örneğine bağlanmak için kullanması gereken ağ geçidinin adı. | string | Evet
encryptedCredential | Şifrelenmiş kimlik bilgisi dizesi. | string | Hayır

#### <a name="example"></a>Örnek

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Daha fazla bilgi için bkz. [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
SAP BW bir veri kümesi tanımlamak için veri kümesinin **türünü** **relationaltable**olarak ayarlayın. **Relationaltable**türünde SAP BW veri kümesi için desteklenen türe özgü özellik yok.

#### <a name="example"></a>Örnek

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Daha fazla bilgi için bkz. [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
SAP Business Warehouse 'dan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu | SAP BW örneğinden verileri okumak için MDX sorgusunu belirtir. | MDX sorgusu. | Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) makalesi.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Bağlı hizmet
SAP HANA bağlı bir hizmet tanımlamak için, bağlı hizmetin **türünü** **saphana**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
sunucu | SAP HANA örneğinin bulunduğu sunucunun adı. Sunucunuz özelleştirilmiş bir bağlantı noktası kullanıyorsa `server:port` belirtin. | string | Evet
authenticationType | Kimlik doğrulama türü. | dizisinde. "Temel" veya "Windows" | Evet
kullanıcı adı | SAP sunucusuna erişimi olan kullanıcının adı | string | Evet
password | Kullanıcının parolası. | string | Evet
gatewayName | Data Factory hizmetinin şirket içi SAP HANA örneğine bağlanmak için kullanması gereken ağ geçidinin adı. | string | Evet
encryptedCredential | Şifrelenmiş kimlik bilgisi dizesi. | string | Hayır

#### <a name="example"></a>Örnek

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Daha fazla bilgi için bkz. [SAP HANA Bağlayıcısı](data-factory-sap-hana-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
SAP HANA bir veri kümesi tanımlamak için veri kümesinin **türünü** **relationaltable**olarak ayarlayın. **Relationaltable**türünde SAP HANA veri kümesi için desteklenen türe özgü özellik yok.

#### <a name="example"></a>Örnek

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Daha fazla bilgi için bkz. [SAP HANA Bağlayıcısı](data-factory-sap-hana-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Verileri bir SAP HANA veri deposundan kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu | SAP HANA örneğinden verileri okumak için SQL sorgusunu belirtir. | SQL sorgusu. | Evet |


#### <a name="example"></a>Örnek


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [SAP HANA Bağlayıcısı](data-factory-sap-hana-connector.md#copy-activity-properties) makalesi.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Bağlı hizmet
Şirket içi SQL Server veritabanını bir veri fabrikasına bağlamak için **OnPremisesSqlServer** türünde bağlı bir hizmet oluşturursunuz. Aşağıdaki tabloda, şirket içi SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama sağlanmaktadır.

Aşağıdaki tabloda SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesSqlServer**olarak ayarlanmalıdır. |Evet |
| connectionString |SQL kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak şirket içi SQL Server veritabanına bağlanmak için gerekli connectionString bilgilerini belirtin. |Evet |
| gatewayName |Data Factory hizmetinin şirket içi SQL Server veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |
| kullanıcı adı |Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. Örnek: **domainname\\Kullanıcı adı**. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |

**New-AzDataFactoryEncryptValue** cmdlet 'ini kullanarak kimlik bilgilerini şifreleyebilir ve aşağıdaki örnekte gösterildiği gibi bunları bağlantı dizesinde kullanabilirsiniz (**encryptedcredential** özelliği):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Örnek: SQL kimlik doğrulaması kullanmak için JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Örnek: Windows kimlik doğrulamasını kullanmak için JSON

Kullanıcı adı ve parola belirtilmişse, ağ geçidi, şirket içi SQL Server veritabanına bağlanmak üzere belirtilen kullanıcı hesabının kimliğine bürünmek için bunları kullanır. Aksi takdirde, ağ geçidi, doğrudan ağ geçidinin güvenlik bağlamı (başlangıç hesabı) ile SQL Server bağlanır.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir SQL Server veri kümesi tanımlamak için, veri kümesinin **türünü** **sqlservertable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu SQL Server veritabanı örneğindeki tablonun veya görünümün adı. |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#dataset-properties) makalesi.

### <a name="sql-source-in-copy-activity"></a>Kopyalama etkinliğinde SQL kaynağı
SQL Server veritabanından veri kopyalıyorsanız kopyalama etkinliğinin **kaynak türünü** **SQLSource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. Giriş veri kümesi tarafından başvurulan veritabanından birden fazla tabloya başvurabilir. Belirtilmemişse, çalıştırılan SQL deyiminizi seçin: tablom arasından seçim yapın. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

SqlSource için **Sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak için bu sorguyu SQL Server veritabanı kaynağına göre çalıştırır.

Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, yapı bölümünde tanımlanan sütunlar SQL Server veritabanında çalıştırılacak bir seçme sorgusu oluşturmak için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

> [!NOTE]
> **SqlReaderStoredProcedureName**kullandığınızda JSON veri kümesindeki **TableName** özelliği için de bir değer belirtmeniz gerekir. Ancak bu tabloda gerçekleştirilen hiçbir doğrulama yoktur.


#### <a name="example"></a>Örnek
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Bu örnekte, SqlSource için **Sqlreaderquery** belirtilir. Kopyalama etkinliği, verileri almak için bu sorguyu SQL Server veritabanı kaynağına göre çalıştırır. Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz. SqlReaderQuery, giriş veri kümesi tarafından başvurulan veritabanı içindeki birden çok tabloya başvurabilir. Yalnızca DataSet 'in tableName typeProperty olarak ayarlanan tablo ile sınırlı değildir.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, yapı bölümünde tanımlanan sütunlar SQL Server veritabanında çalıştırılacak bir seçme sorgusu oluşturmak için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#copy-activity-properties) makalesi.

### <a name="sql-sink-in-copy-activity"></a>Kopyalama etkinliğinde SQL havuzu
Verileri bir SQL Server veritabanına kopyalıyorsunuz, kopyalama etkinliğinin **Havuz türünü** **sqlsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |
| WriteBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| sqlWriterCleanupScript |Belirli bir dilim verilerinin temizlenmesi gibi, yürütülecek kopyalama etkinliğinin sorgusunu belirtin. Daha fazla bilgi için bkz. yinelenebilirlik Section. |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için kullanılan otomatik olarak oluşturulan dilim tanımlayıcısı ile doldurulacak kopyalama etkinliğinin sütun adını belirtin. Daha fazla bilgi için bkz. yinelenebilirlik Section. |Binary (32) veri türüne sahip bir sütunun sütun adı. |Hayır |
| sqlWriterStoredProcedureName |Hedef tabloya veri (güncelleştirme/ekleme) içeren saklı yordamın adı. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |
| sqlWriterTableType |Saklı yordamda kullanılacak tablo türü adını belirtin. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilirler. |Tablo türü adı. |Hayır |

#### <a name="example"></a>Örnek
İşlem hattı, bu giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqlsink**olarak ayarlanır.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#copy-activity-properties) makalesi.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Bağlı hizmet
Bir Sybase bağlı hizmeti tanımlamak için, bağlı hizmetin **türünü** **OnPremisesSybase**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |Sybase sunucusunun adı. |Evet |
| veritabanı |Sybase veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. |Hayır |
| authenticationType |Sybase veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi Sybase veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Sybase Bağlayıcısı](data-factory-onprem-sybase-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Sybase veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |İlişkili hizmetin başvurduğu Sybase veritabanı örneğindeki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Sybase Bağlayıcısı](data-factory-onprem-sybase-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir Sybase veritabanından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [Sybase Bağlayıcısı](data-factory-onprem-sybase-connector.md#copy-activity-properties) makalesi.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Bağlı hizmet
Bir Teradata bağlı hizmeti tanımlamak için, bağlantılı hizmetin **türünü** **OnPremisesTeradata**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |Teradata sunucusunun adı. |Evet |
| authenticationType |Teradata veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi Teradata veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Teradata Bağlayıcısı](data-factory-onprem-teradata-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Teradata blob veri kümesi tanımlamak için veri kümesinin **türünü** **relationaltable**olarak ayarlayın. Şu anda Teradata veri kümesi için desteklenen hiçbir tür özelliği yok.

#### <a name="example"></a>Örnek
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Teradata Bağlayıcısı](data-factory-onprem-teradata-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir Teradata veritabanından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Daha fazla bilgi için bkz. [Teradata Bağlayıcısı](data-factory-onprem-teradata-connector.md#copy-activity-properties) makalesi.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Bağlı hizmet
Cassandra bağlı hizmetini tanımlamak için, bağlantılı hizmetin **türünü** **OnPremisesCassandra**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| konağının |Cassandra sunucularının bir veya daha fazla IP adresi veya ana bilgisayar adı.<br/><br/>Aynı anda tüm sunuculara bağlanmak için IP adreslerinin veya ana bilgisayar adlarının virgülle ayrılmış bir listesini belirtin. |Evet |
| port |Cassandra sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. |Hayır, varsayılan değer: 9042 |
| authenticationType |Temel veya anonim |Evet |
| kullanıcı adı |Kullanıcı hesabı için Kullanıcı adını belirtin. |Evet, authenticationType temel olarak ayarlandıysa. |
| password |Kullanıcı hesabı için parola belirtin. |Evet, authenticationType temel olarak ayarlandıysa. |
| gatewayName |Şirket içi Cassandra veritabanına bağlanmak için kullanılan ağ geçidinin adı. |Evet |
| encryptedCredential |Ağ Geçidi tarafından şifrelenen kimlik bilgileri. |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Cassandra bağlayıcı](data-factory-onprem-cassandra-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Cassandra veri kümesini tanımlamak için, veri kümesinin **türünü** **Cassandratable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| anahtar alanı |Cassandra veritabanında anahtar alanının veya şemanın adı. |Evet ( **Cassandrasource** **sorgusu** tanımlanmamışsa). |
| tableName |Cassandra veritabanındaki tablonun adı. |Evet ( **Cassandrasource** **sorgusu** tanımlanmamışsa). |

#### <a name="example"></a>Örnek

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Cassandra bağlayıcı](data-factory-onprem-cassandra-connector.md#dataset-properties) makalesi.

### <a name="cassandra-source-in-copy-activity"></a>Copy etkinliğinde Cassandra kaynağı
Cassandra 'dan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **Cassandrasource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL-92 sorgusu veya CQL sorgusu. Bkz. [CQL başvurusu](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL sorgusu kullanırken, sorgulamak istediğiniz tabloyu temsil etmek için **anahtar alanı Name. Table adını** belirtin. |Hayır (veri kümesinde TableName ve anahtar alanı tanımlanmışsa). |
| Bu düzey |Tutarlılık düzeyi, istemci uygulamasına veri döndürmeden önce bir okuma isteğine kaç yinelemenin yanıt vereceğini belirtir. Cassandra, okuma isteğini karşılamak üzere verilerin belirtilen sayıda çoğaltmasını denetler. |BIR, IKI, ÜÇ, ÇEKIRDEK, TÜMÜ, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Ayrıntılar için bkz. [veri tutarlılığını yapılandırma](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Hayır. Varsayılan değer BIR. |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Cassandra bağlayıcı](data-factory-onprem-cassandra-connector.md#copy-activity-properties) makalesi.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Bağlı hizmet
MongoDB bağlı hizmeti tanımlamak için, bağlantılı hizmetin **türünü** **OnPremisesMongoDB**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| sunucu |MongoDB sunucusunun IP adresi veya ana bilgisayar adı. |Evet |
| port |MongoDB sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. |İsteğe bağlı, varsayılan değer: 27017 |
| authenticationType |Temel veya anonim. |Evet |
| kullanıcı adı |MongoDB 'ye erişmek için Kullanıcı hesabı. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| password |Kullanıcının parolası. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| authSource |Kimlik doğrulaması için kimlik bilgilerinizi denetlemek üzere kullanmak istediğiniz MongoDB veritabanının adı. |İsteğe bağlı (temel kimlik doğrulaması kullanılıyorsa). Varsayılan: yönetici hesabını ve databaseName özelliği kullanılarak belirtilen veritabanını kullanır. |
| Dosyasında |Erişmek istediğiniz MongoDB veritabanının adı. |Evet |
| gatewayName |Veri deposuna erişen ağ geçidinin adı. |Evet |
| encryptedCredential |Ağ Geçidi tarafından şifrelenen kimlik bilgileri. |İsteğe bağlı |

#### <a name="example"></a>Örnek

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. [MongoDB bağlayıcı makalesi](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Veri kümesi
MongoDB veri kümesini tanımlamak için, veri kümesinin **türünü** **Mongodbcollection**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Ma |MongoDB veritabanındaki koleksiyonun adı. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Daha fazla bilgi için bkz. [MongoDB bağlayıcı makalesi](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Kopyalama etkinliğinde MongoDB kaynağı
MongoDB 'den veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **Mongodbsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL-92 sorgu dizesi. Örneğin: `select * from MyTable`. |Hayır ( **veri kümesi** **KoleksiyonAdı** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [MongoDB bağlayıcı makalesi](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Bağlı hizmet
Amazon S3 bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **Awsaccesskey**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| Accesskeyıd |Gizli dizi erişim anahtarının KIMLIĞI. |string |Evet |
| secretAccessKey |Gizli dizi erişim anahtarı. |Şifrelenmiş gizli dizi |Evet |

#### <a name="example"></a>Örnek
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Amazon S3 bağlayıcı makalesi](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Veri kümesi
Amazon S3 veri kümesini tanımlamak için, veri kümesinin **türünü** **AmazonS3**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| bucketName |S3 demet adı. |Dize |Evet |
| anahtar |S3 nesne anahtarı. |Dize |Hayır |
| koy |S3 nesne anahtarı için ön ek. Anahtarları bu önek ile başlayan nesneler seçilidir. Yalnızca anahtar boş olduğunda geçerlidir. |Dize |Hayır |
| version |S3 sürümü etkinse S3 nesnesinin sürümü. |Dize |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır | |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır | |


> [!NOTE]
> bucketName + Key, bu, Bucket 'ın S3 nesneleri için kök kapsayıcı olduğu ve anahtarın S3 nesnesinin tam yolu olduğu S3 nesnesinin konumunu belirtir.

#### <a name="example-sample-dataset-with-prefix"></a>Örnek: ön ek içeren örnek veri kümesi

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Örnek: örnek veri kümesi (sürümüyle birlikte)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Örnek: S3 için dinamik yollar
Örnekte, Amazon S3 veri kümesindeki Key ve bucketName özellikleri için sabit değerler kullanacağız.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Data Factory anahtar ve bucketName, çalışma zamanında dinamik olarak bir şekilde hesaplamanız için, bkz.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Bir Amazon S3 veri kümesi önek özelliği için aynısını yapabilirsiniz. Desteklenen işlevlerin ve değişkenlerin listesi için bkz. [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md) .

Daha fazla bilgi için bkz. [Amazon S3 bağlayıcı makalesi](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi kaynağı
Amazon S3 ' den veri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **filesystemsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:


| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |S3 nesnelerinin dizin altında yinelemeli olarak kaydedilip edilmeyeceğini belirtir. |doğru/yanlış |Hayır |


#### <a name="example"></a>Örnek


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [Amazon S3 bağlayıcı makalesi](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Dosya Sistemi


### <a name="linked-service"></a>Bağlı hizmet
Şirket içi **dosya sunucusu** bağlı hizmeti Ile bir Azure Data Factory 'ye şirket içi dosya sistemi bağlayabilirsiniz. Aşağıdaki tabloda, şirket Içi dosya sunucusu bağlantılı hizmetine özgü JSON öğelerine yönelik açıklamalar sağlanmaktadır.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliğinin **OnPremisesFileServer**olarak ayarlandığından emin olun. |Evet |
| konağının |Kopyalamak istediğiniz klasörün kök yolunu belirtir. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları. |Evet |
| UserID |Sunucuya erişimi olan kullanıcının KIMLIĞINI belirtin. |Hayır (encryptedCredential öğesini seçerseniz) |
| password |Kullanıcının parolasını belirtin (Kullanıcı kimliği). |Hayır (encryptedCredential seçeneğini belirlerseniz |
| encryptedCredential |New-AzDataFactoryEncryptValue cmdlet 'ini çalıştırarak alabileceğiniz şifrelenmiş kimlik bilgilerini belirtin. |Hayır (Kullanıcı kimliği ve parolayı düz metin olarak belirtmeyi seçerseniz) |
| gatewayName |Data Factory şirket içi dosya sunucusuna bağlanmak için kullanması gereken ağ geçidinin adını belirtir. |Evet |

#### <a name="sample-folder-path-definitions"></a>Örnek klasör yolu tanımları

| Senaryo | Bağlı hizmet tanımında ana bilgisayar | veri kümesi tanımında folderPath |
| --- | --- | --- |
| Veri Yönetimi ağ geçidi makinesindeki yerel klasör: <br/><br/>Örnekler: D:\\\* veya D:\folder\alt klasörü\\\* |D:\\\\ (Veri Yönetimi Gateway 2,0 ve üzeri sürümler için) <br/><br/> localhost (Veri Yönetimi Gateway 'den önceki sürümlerde 2,0) |.\\\\ veya klasör\\\\alt ağ (Veri Yönetimi Gateway 2,0 ve üzeri sürümleri için) <br/><br/>D:\\\\ veya D:\\\\klasörü\\\\alt klasörü (2,0 altındaki Ağ Geçidi sürümü için) |
| Uzak paylaşılan klasör: <br/><br/>Örnekler: \\\\sunucum\\paylaşma\\\* veya \\\\sunucum\\alt klasörü\\\\\\\* |\\\\\\\\sunucum\\\\paylaşma |.\\\\ veya klasör\\\\alt klasör |


#### <a name="example-using-username-and-password-in-plain-text"></a>Örnek: salt metin olarak Kullanıcı adı ve parola kullanma

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Örnek: encryptedcredential kullanma

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. [dosya sistemi Bağlayıcısı makalesi](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Veri kümesi
Bir dosya sistemi veri kümesi tanımlamak için, veri kümesinin **türünü** **FileShare**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün alt yol belirtir. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Evet |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimdedir: <br/><br/>`Data.<Guid>.txt` (örnek: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt) |Hayır |
| fileFilter |Tüm dosyalar yerine folderPath içindeki dosyaların bir alt kümesini seçmek için kullanılacak bir filtre belirtin. <br/><br/>İzin verilen değerler: `*` (birden fazla karakter) ve `?` (tek karakter).<br/><br/>Örnek 1: "fileFilter": "*. log"<br/>Örnek 2: "fileFilter": 2016-1-?. txt<br/><br/>FileFilter 'nin bir giriş FileShare veri kümesi için geçerli olduğunu unutmayın. |Hayır |
| partitionedBy |Zaman serisi verileri için dinamik bir folderPath/fileName belirtmek üzere partitionedBy ' i kullanabilirsiniz. Her saat veri için folderPath parametreli bir örnektir. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**; ve desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. [Azure Data Factory dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md#compression-support)bakın. |Hayır |

> [!NOTE]
> Dosya adı ve fileFilter 'yi aynı anda kullanamazsınız.

#### <a name="example"></a>Örnek

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [dosya sistemi Bağlayıcısı makalesi](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi kaynağı
Dosya sisteminden veri kopyalıyorsanız kopyalama etkinliğinin **kaynak türünü** **filesystemsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Daha fazla bilgi için bkz. [dosya sistemi Bağlayıcısı makalesi](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi havuzu
Dosya sistemine veri kopyalıyorsanız kopyalama etkinliğinin **Havuz türünü** **filesystemmsink**olarak ayarlayın ve **Havuz** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| copyBehavior |Kaynak BlobSource veya FileSystem olduğunda kopyalama davranışını tanımlar. |**Preservehierarchy:** Hedef klasördeki dosya hiyerarşisini korur. Diğer bir deyişle, kaynak dosyanın kaynak klasöre göreli yolu hedef klasöre hedef dosyanın göreli yoluyla aynıdır.<br/><br/>**DÜZEDEN hiyerarşisi:** Kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyinde oluşturulur. Hedef dosyalar, otomatik olarak oluşturulan bir adla oluşturulur.<br/><br/>**Mergefiles:** Kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya adı/blob adı belirtilmişse, birleştirilmiş dosya adı belirtilen addır. Aksi takdirde, otomatik olarak oluşturulan bir dosya adıdır. |Hayır |

Otomobil

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [dosya sistemi Bağlayıcısı makalesi](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Bağlı hizmet
Bir FTP bağlı hizmeti tanımlamak için, bağlı hizmetin **türünü** **FtpServer**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| konağının |FTP sunucusunun adı veya IP adresi |Evet |&nbsp; |
| authenticationType |Kimlik doğrulaması türünü belirtme |Evet |Temel, anonim |
| kullanıcı adı |FTP sunucusuna erişimi olan Kullanıcı |Hayır |&nbsp; |
| password |Kullanıcı için parola (Kullanıcı adı) |Hayır |&nbsp; |
| encryptedCredential |FTP sunucusuna erişmek için şifrelenmiş kimlik bilgisi |Hayır |&nbsp; |
| gatewayName |Şirket içi FTP sunucusuna bağlanmak için Veri Yönetimi ağ geçidinin adı |Hayır |&nbsp; |
| port |FTP sunucusunun dinlediği bağlantı noktası |Hayır |21 |
| enableSsl |SSL/TLS kanalı üzerinden FTP kullanıp kullanmayacağınızı belirtin |Hayır |doğru |
| enableServerCertificateValidation |SSL/TLS kanalı üzerinden FTP kullanırken sunucu SSL sertifika doğrulamasının etkinleştirilip etkinleştirilmeyeceğini belirtin |Hayır |doğru |

#### <a name="example-using-anonymous-authentication"></a>Örnek: anonim kimlik doğrulaması kullanma

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Örnek: temel kimlik doğrulaması için Kullanıcı adı ve parolayı düz metin olarak kullanma

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Örnek: bağlantı noktası, enableSsl, enableServerCertificateValidation kullanma

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Örnek: kimlik doğrulaması ve ağ geçidi için encryptedCredential kullanma

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Daha fazla bilgi için bkz. [FTP Bağlayıcısı](data-factory-ftp-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir FTP veri kümesi tanımlamak için, veri kümesinin **türünü** **FileShare**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün alt yolu. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Evet
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: <br/><br/>`Data.<Guid>.txt` (örnek: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt) |Hayır |
| fileFilter |Tüm dosyalar yerine folderPath içindeki dosyaların bir alt kümesini seçmek için kullanılacak bir filtre belirtin.<br/><br/>İzin verilen değerler: `*` (birden fazla karakter) ve `?` (tek karakter).<br/><br/>Örnekler 1: `"fileFilter": "*.log"`<br/>Örnek 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter, bir giriş FileShare veri kümesi için geçerlidir. Bu özellik,. |Hayır |
| partitionedBy |partitionedBy, zaman serisi verilerine yönelik bir dinamik folderPath, filename belirtmek için kullanılabilir. Örneğin, her saat veri için folderPath parametreli parametrelenir. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**; ve desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |
| useBinaryTransfer |Ikili aktarım modunu kullanıp kullanmayacağınızı belirtin. İkili mod ve yanlış ASCII için true. Varsayılan değer: true. Bu özellik yalnızca, ilişkili bağlı hizmet türü: FtpServer türünde olduğunda kullanılabilir. |Hayır |

> [!NOTE]
> filename ve fileFilter aynı anda kullanılamaz.

#### <a name="example"></a>Örnek

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Daha fazla bilgi için bkz. [FTP Bağlayıcısı](data-factory-ftp-connector.md#dataset-properties) makalesi.

### <a name="file-system-source-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi kaynağı
Bir FTP sunucusundan veri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **filesystemsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [FTP Bağlayıcısı](data-factory-ftp-connector.md#copy-activity-properties) makalesi.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Bağlı hizmet
Bir IBir ıbağlantılı hizmet tanımlamak için, bağlı hizmetin **türünü** I1 olarak **ayarlayın**ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type **özelliği:,** olarak ayarlanmalıdır |Evet |
| Url |Bu URL 'nin URL 'si |Evet |
| authenticationType |Anonim veya Windows. <br><br> Bir bağlantı **kimliği Için Kerberos kimlik doğrulaması** kullanmak üzere, şirket içi ortamınızı uygun şekilde ayarlamak için bu bölüme bakın. |Evet |
| Nitelen |Windows kimlik doğrulaması için Kullanıcı adı. |Evet (Windows kimlik doğrulaması için) |
| password |Windows kimlik doğrulaması için parola. |Evet (Windows kimlik doğrulaması için) |
| gatewayName |Data Factory hizmetinin, bir hizmetin bir bağlantı kurmak için kullanması gereken ağ geçidinin adı. |Evet |
| encryptedCredential |Erişim kimlik bilgisinin [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) çıkışı. |Hayır |

#### <a name="example-using-anonymous-authentication"></a>Örnek: anonim kimlik doğrulaması kullanma

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Örnek: Windows kimlik doğrulamasını kullanma

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. bağlantı makalesi makalesi.

### <a name="dataset"></a>Veri kümesi
Bir IBir ıveri kümesi tanımlamak için, veri kümesinin **türünü** **FileShare**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün yolu. Örnek: `myfolder`<br/><br/>Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örneğin: folder\alt klasörü için klasör\\\\alt klasörü ve d:\samplefolder için, d:\\\\samplefolder belirtin.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Evet |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: <br/><br/>`Data.<Guid>.txt` (örneğin:: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt |Hayır |
| partitionedBy |partitionedBy, zaman serisi verilerine yönelik bir dinamik folderPath, filename belirtmek için kullanılabilir. Örnek: her saat veri için folderPath parametreli parametrelenir. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

> [!NOTE]
> filename ve fileFilter aynı anda kullanılamaz.

#### <a name="example"></a>Örnek

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Daha fazla bilgi için bkz. bağlantı makalesi makalesi.

### <a name="file-system-source-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi kaynağı
Bir sunucudan verileri kopyalıyorsunuz, kopyalama etkinliğinin **kaynak türünü** **filesystemsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

**Filesystemsource** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. bağlantı makalesi makalesi.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Bağlı hizmet
SFTP bağlı hizmetini tanımlamak için, bağlantılı hizmetin **türünü** **SFTP**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| konağının | SFTP sunucusunun adı veya IP adresi. |Evet |
| port |SFTP sunucusunun dinlediği bağlantı noktası. Varsayılan değer: 21 |Hayır |
| authenticationType |Kimlik doğrulama türünü belirtin. İzin verilen değerler: **temel**, **sshpublickey**. <br><br> Temel kimlik doğrulamasını kullanma ve sırasıyla daha fazla özellik ve JSON örnekleri üzerinde [SSH ortak anahtar kimlik doğrulama bölümlerini kullanma](#using-ssh-public-key-authentication) bölümüne bakın. |Evet |
| skipHostKeyValidation | Konak anahtarı doğrulamanın atlanıp atlanmayacağını belirtin. | Hayır. Varsayılan değer: false |
| hostKeyFingerprint | Ana bilgisayar anahtarının parmak yazdırma türünü belirtin. | `skipHostKeyValidation` false olarak ayarlandıysa Evet.  |
| gatewayName |Şirket içi bir SFTP sunucusuna bağlanmak için Veri Yönetimi ağ geçidinin adı. | Şirket içi bir SFTP sunucusundan veri kopyalandıysanız Evet. |
| encryptedCredential | SFTP sunucusuna erişmek için şifrelenmiş kimlik bilgileri. Kopyalama sihirbazında veya ClickOnce açılan iletişim kutusunda temel kimlik doğrulaması (Kullanıcı adı + parola) veya SshPublicKey kimlik doğrulaması (Kullanıcı adı + özel anahtar yolu veya içerik) belirttiğinizde otomatik olarak üretilir. | Hayır. Yalnızca şirket içi bir SFTP sunucusundan veri kopyalarken geçerlidir. |

#### <a name="example-using-basic-authentication"></a>Örnek: temel kimlik doğrulaması kullanma

Temel kimlik doğrulaması kullanmak için `authenticationType` ' ı `Basic` olarak ayarlayın ve en son bölümde tanıtılan SFTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| kullanıcı adı | SFTP sunucusuna erişimi olan kullanıcı. |Evet |
| password | Kullanıcı için parola (Kullanıcı adı). | Evet |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Örnek: **şifreli kimlik bilgileri Ile temel kimlik doğrulaması**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**SSH ortak anahtar kimlik doğrulamasını kullanma:**

Temel kimlik doğrulaması kullanmak için `authenticationType` ' ı `SshPublicKey` olarak ayarlayın ve en son bölümde tanıtılan SFTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| kullanıcı adı |SFTP sunucusuna erişimi olan Kullanıcı |Evet |
| privateKeyPath | Ağ geçidinin erişebileceği özel anahtar dosyasının mutlak yolunu belirtin. | `privateKeyPath` ya da `privateKeyContent`belirtin. <br><br> Yalnızca şirket içi bir SFTP sunucusundan veri kopyalarken geçerlidir. |
| privateKeyContent | Özel anahtar içeriğinin seri hale getirilmiş dizesi. Kopyalama Sihirbazı özel anahtar dosyasını okuyabilir ve özel anahtar içeriğini otomatik olarak ayıklayabilir. Başka bir aracı/SDK kullanıyorsanız, bunun yerine privateKeyPath özelliğini kullanın. | `privateKeyPath` ya da `privateKeyContent`belirtin. |
| Deyimi | Anahtar dosyası bir pass ifadesi tarafından korunuyorsa, özel anahtarın şifresini çözmek için geçiş tümceciğini/parolayı belirtin. | Özel anahtar dosyası bir pass ifadesi tarafından korunuyorsa Evet. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Örnek: **özel anahtar içeriğini kullanarak Sshpublickey kimlik doğrulaması**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Daha fazla bilgi için bkz. [SFTP bağlayıcı](data-factory-sftp-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir SFTP veri kümesi tanımlamak için, veri kümesinin **türünü** **FileShare**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün alt yolu. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Evet |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: <br/><br/>`Data.<Guid>.txt` (örnek: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt) |Hayır |
| fileFilter |Tüm dosyalar yerine folderPath içindeki dosyaların bir alt kümesini seçmek için kullanılacak bir filtre belirtin.<br/><br/>İzin verilen değerler: `*` (birden fazla karakter) ve `?` (tek karakter).<br/><br/>Örnekler 1: `"fileFilter": "*.log"`<br/>Örnek 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter, bir giriş FileShare veri kümesi için geçerlidir. Bu özellik,. |Hayır |
| partitionedBy |partitionedBy, zaman serisi verilerine yönelik bir dinamik folderPath, filename belirtmek için kullanılabilir. Örneğin, her saat veri için folderPath parametreli parametrelenir. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |
| useBinaryTransfer |Ikili aktarım modunu kullanıp kullanmayacağınızı belirtin. İkili mod ve yanlış ASCII için true. Varsayılan değer: true. Bu özellik yalnızca, ilişkili bağlı hizmet türü: FtpServer türünde olduğunda kullanılabilir. |Hayır |

> [!NOTE]
> filename ve fileFilter aynı anda kullanılamaz.

#### <a name="example"></a>Örnek

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Daha fazla bilgi için bkz. [SFTP bağlayıcı](data-factory-sftp-connector.md#dataset-properties) makalesi.

### <a name="file-system-source-in-copy-activity"></a>Kopyalama etkinliğinde dosya sistemi kaynağı
Bir SFTP kaynağından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **filesystemsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |Hayır |



#### <a name="example"></a>Örnek

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [SFTP bağlayıcı](data-factory-sftp-connector.md#copy-activity-properties) makalesi.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Bağlı hizmet
HTTP bağlantılı hizmetini tanımlamak için, bağlantılı hizmetin **türünü** **http**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| url | Web sunucusunun temel URL 'SI | Evet |
| authenticationType | Kimlik doğrulama türünü belirtir. İzin verilen değerler: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Daha fazla özellik ve bu kimlik doğrulama türleri için JSON örnekleri üzerinde bu tablonun altındaki bölümlere bakın. | Evet |
| enableServerCertificateValidation | Kaynak HTTPS Web sunucusu ise sunucu SSL sertifika doğrulamasının etkinleştirilip etkinleştirilmeyeceğini belirtin | Hayır, varsayılan değer doğru |
| gatewayName | Şirket içi HTTP kaynağına bağlanmak için Veri Yönetimi ağ geçidinin adı. | Şirket içi HTTP kaynağından veri kopyalandıysanız Evet. |
| encryptedCredential | HTTP uç noktasına erişmek için şifrelenmiş kimlik bilgileri. Kopyalama Sihirbazı 'nda veya ClickOnce açılan iletişim kutusunda kimlik doğrulama bilgilerini yapılandırdığınızda otomatik olarak üretilir. | Hayır. Yalnızca şirket içi HTTP sunucusundan veri kopyalarken geçerlidir. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Örnek: Basic, Digest veya Windows kimlik doğrulamasını kullanma
`authenticationType` `Basic`, `Digest`veya `Windows`olarak ayarlayın ve yukarıda tanıtılan HTTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| kullanıcı adı | HTTP uç noktasına erişmek için Kullanıcı adı. | Evet |
| password | Kullanıcı için parola (Kullanıcı adı). | Evet |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Örnek: ClientCertificate kimlik doğrulamasını kullanma

Temel kimlik doğrulamasını kullanmak için, `authenticationType` ' ı `ClientCertificate` olarak ayarlayın ve yukarıdaki HTTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| embeddedCertData | Kişisel bilgi değişimi (PFX) dosyasının ikili verilerinin Base64 ile kodlanmış içeriği. | `embeddedCertData` ya da `certThumbprint`belirtin. |
| Certparmak Izi | Ağ Geçidi makinenizin sertifika deposunda yüklü olan sertifikanın parmak izi. Yalnızca şirket içi HTTP kaynağından veri kopyalarken geçerlidir. | `embeddedCertData` ya da `certThumbprint`belirtin. |
| password | Sertifikayla ilişkili parola. | Hayır |

Kimlik doğrulaması için `certThumbprint` kullanırsanız ve sertifika yerel bilgisayarın Kişisel deposunda yüklüyse, ağ geçidi hizmetine okuma izni vermeniz gerekir:

1. Microsoft Yönetim Konsolu 'Nu (MMC) başlatın. **Yerel bilgisayarı**hedefleyen **Sertifikalar** ek bileşenini ekleyin.
2. **Sertifikalar**, **Kişisel**ve **Sertifikalar**' ı genişletin.
3. Kişisel mağazadan sertifikaya sağ tıklayın ve özel anahtarları Yönet->**Tüm görevler** ' i seçin **...**
3. **Güvenlik** sekmesinde, veri yönetimi ağ geçidi ana bilgisayar hizmetinin altında, sertifikaya yönelik okuma erişimiyle birlikte çalıştığı kullanıcı hesabını ekleyin.

**Örnek: istemci sertifikası kullanma:** Bu bağlı hizmet, veri fabrikasını şirket içi HTTP Web sunucusuna bağlar. Veri Yönetimi ağ geçidi yüklü olan makinede yüklü bir istemci sertifikası kullanır.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Örnek: bir dosyada istemci sertifikası kullanma
Bu bağlı hizmet, veri fabrikasını şirket içi HTTP Web sunucusuna bağlar. Veri Yönetimi ağ geçidi yüklü olan makinede bir istemci sertifika dosyası kullanır.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Daha fazla bilgi için bkz. [http Bağlayıcısı](data-factory-http-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir HTTP veri kümesi tanımlamak için, veri kümesinin **türünü** **http**olarak ayarlayın ve aşağıdaki özellikleri **typeproperties** bölümünde belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| relativeUrl 'Si | Verileri içeren kaynağın göreli URL 'SI. Yol belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. <br><br> Dinamik URL oluşturmak için [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md)kullanabilirsiniz, örnek: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Hayır |
| requestMethod | Http yöntemi. İzin verilen değerler **Al** veya **Postala**. | Hayır. `GET` varsayılan değerdir. |
| additionalHeaders | Ek HTTP istek üstbilgileri. | Hayır |
| Istek gövdesi | HTTP isteğinin gövdesi. | Hayır |
| formatını | Verileri ayrıştırmadan olduğu **gıbı HTTP uç** noktasından yalnızca almak istiyorsanız bu biçim ayarlarını atlayın. <br><br> Kopyalama sırasında HTTP yanıt içeriğini ayrıştırmak istiyorsanız, şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

#### <a name="example-using-the-get-default-method"></a>Örnek: GET (varsayılan) yöntemini kullanma

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Örnek: POST yöntemini kullanma

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Daha fazla bilgi için bkz. [http Bağlayıcısı](data-factory-http-connector.md#dataset-properties) makalesi.

### <a name="http-source-in-copy-activity"></a>Kopyalama etkinliğinde HTTP kaynağı
Bir HTTP kaynağından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **httpsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı (TimeSpan). Yanıt verilerinin okunması için zaman aşımı değil, yanıt almak için zaman aşımı. | Hayır. Varsayılan değer: 00:01:40 |


#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [http Bağlayıcısı](data-factory-http-connector.md#copy-activity-properties) makalesi.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Bağlı hizmet
OData bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **OData**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| url |OData hizmetinin URL 'si. |Evet |
| authenticationType |OData kaynağına bağlanmak için kullanılan kimlik doğrulaması türü. <br/><br/> Bulut OData için, olası değerler anonim, temel ve OAuth ' dir (Azure Data Factory Şu anda yalnızca Azure Active Directory tabanlı OAuth 'ı destekler). <br/><br/> Şirket içi OData için, olası değerler anonim, temel ve Windows ' dir. |Evet |
| kullanıcı adı |Temel kimlik doğrulaması kullanıyorsanız Kullanıcı adını belirtin. |Evet (yalnızca temel kimlik doğrulaması kullanıyorsanız) |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Evet (yalnızca temel kimlik doğrulaması kullanıyorsanız) |
| authorizedCredential |OAuth kullanıyorsanız, Data Factory kopyalama Sihirbazı veya düzenleyicide **Yetkilendir** düğmesine tıklayın ve kimlik bilgilerinizi girin, ardından bu özelliğin değeri otomatik olarak oluşturulur. |Evet (yalnızca OAuth kimlik doğrulaması kullanıyorsanız) |
| gatewayName |Data Factory hizmetinin şirket içi OData hizmetine bağlanmak için kullanması gereken ağ geçidinin adı. Yalnızca şirket içi OData kaynağından veri kopyalıyorsanız belirtin. |Hayır |

#### <a name="example---using-basic-authentication"></a>Örnek-temel kimlik doğrulaması kullanma
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Örnek-anonim kimlik doğrulaması kullanma

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Örnek-şirket içi OData kaynağına erişme Windows kimlik doğrulaması kullanma

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Örnek-Cloud OData kaynağına erişme OAuth kimlik doğrulaması kullanma
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Daha fazla bilgi için bkz. [OData Bağlayıcısı](data-factory-odata-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir OData veri kümesi tanımlamak için, veri kümesinin **türünü** **ODataResource**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Yolun |OData kaynağının yolu |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Daha fazla bilgi için bkz. [OData Bağlayıcısı](data-factory-odata-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir OData kaynağından veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Örnek | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |"? $select = ad, açıklama & $top = 5" |Hayır |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [OData Bağlayıcısı](data-factory-odata-connector.md#copy-activity-properties) makalesi.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Bağlı hizmet
ODBC bağlı hizmetini tanımlamak için, bağlı hizmetin **türünü** **OnPremisesOdbc**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| connectionString |Bağlantı dizesinin erişim dışı kimlik bilgisi kısmı ve isteğe bağlı olarak şifrelenmiş kimlik bilgileri. Aşağıdaki bölümlerde bulunan örneklere bakın. |Evet |
| kimlik bilgisi |Sürücüye özgü özellik-değer biçiminde belirtilen bağlantı dizesinin erişim kimlik bilgisi kısmı. Örnek: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Hayır |
| authenticationType |ODBC veri deposuna bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim ve temel. |Evet |
| kullanıcı adı |Temel kimlik doğrulaması kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin ODBC veri deposuna bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

#### <a name="example---using-basic-authentication"></a>Örnek-temel kimlik doğrulaması kullanma

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Örnek-şifreli kimlik bilgileriyle temel kimlik doğrulaması kullanma
[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet 'ini kullanarak kimlik bilgilerini şifreleyebilirsiniz.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Örnek: anonim kimlik doğrulaması kullanma

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Daha fazla bilgi için bkz. [ODBC Bağlayıcısı](data-factory-odbc-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
ODBC veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |ODBC veri deposundaki tablonun adı. |Evet |


#### <a name="example"></a>Örnek

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [ODBC Bağlayıcısı](data-factory-odbc-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Bir ODBC veri deposundan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `select * from MyTable`. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Daha fazla bilgi için bkz. [ODBC Bağlayıcısı](data-factory-odbc-connector.md#copy-activity-properties) makalesi.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Bağlı hizmet
Salesforce bağlantılı hizmeti tanımlamak için, bağlantılı hizmetin **türünü** **Salesforce**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| environmentUrl | Salesforce örneğinin URL 'sini belirtin. <br><br> -Varsayılan: "https:\//login.salesforce.com". <br> -Korumalı verileri veri kopyalamak için "https://test.salesforce.com" belirtin. <br> -Özel etki alanından veri kopyalamak için, örneğin "https://[Domain]. My. Salesforce. com" belirtin. |Hayır |
| kullanıcı adı |Kullanıcı hesabı için bir Kullanıcı adı belirtin. |Evet |
| password |Kullanıcı hesabı için bir parola belirtin. |Evet |
| securityToken |Kullanıcı hesabı için bir güvenlik belirteci belirtin. Güvenlik belirtecini sıfırlama/alma hakkında yönergeler için bkz. [güvenlik belirteci alma](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Genel olarak güvenlik belirteçleri hakkında daha fazla bilgi edinmek için bkz. [güvenlik ve API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Daha fazla bilgi için bkz. [Salesforce Bağlayıcısı](data-factory-salesforce-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Salesforce veri kümesi tanımlamak için, veri kümesinin **türünü** **relationaltable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Salesforce 'daki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

#### <a name="example"></a>Örnek

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Salesforce Bağlayıcısı](data-factory-salesforce-connector.md#dataset-properties) makalesi.

### <a name="relational-source-in-copy-activity"></a>Kopyalama etkinliğinde ilişkisel kaynak
Salesforce 'tan veri kopyalıyorsanız, kopyalama etkinliğinin **kaynak türünü** **relationalsource**olarak ayarlayın ve **kaynak** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |Bir SQL-92 sorgusu veya [Salesforce nesne sorgulama dili (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) sorgusu. Örneğin:  `select * from MyTable__c`. |Hayır ( **veri kümesinin** **tabloadı** belirtilmişse) |

#### <a name="example"></a>Örnek



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Tüm özel nesneler için API adının "__c" kısmı gereklidir.

Daha fazla bilgi için bkz. [Salesforce Bağlayıcısı](data-factory-salesforce-connector.md#copy-activity-properties) makalesi.

## <a name="web-data"></a>Web verileri

### <a name="linked-service"></a>Bağlı hizmet
Web bağlantılı hizmet tanımlamak için, bağlantılı hizmetin **türünü** **Web**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Url |Web kaynağının URL 'SI |Evet |
| authenticationType |Deðeri. |Evet |


#### <a name="example"></a>Örnek


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Daha fazla bilgi için bkz. [Web tablosu Bağlayıcısı](data-factory-web-table-connector.md#linked-service-properties) makalesi.

### <a name="dataset"></a>Veri kümesi
Bir Web veri kümesi tanımlamak için, veri kümesinin **türünü** **webtable**olarak ayarlayın ve **typeproperties** bölümünde aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Veri kümesinin türü. **Webtable** olarak ayarlanmalıdır |Evet |
| Yolun |Tabloyu içeren kaynağın göreli URL 'SI. |Hayır. Yol belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. |
| indeks |Kaynaktaki tablonun dizini. HTML sayfasındaki bir tablonun dizinini alma adımları için bkz. HTML sayfasındaki tablonun dizinini alma bölümü. |Evet |

#### <a name="example"></a>Örnek

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Daha fazla bilgi için bkz. [Web tablosu Bağlayıcısı](data-factory-web-table-connector.md#dataset-properties) makalesi.

### <a name="web-source-in-copy-activity"></a>Kopyalama etkinliğinde Web kaynağı
Bir Web tablosundan veri kopyalıyorsanız kopyalama etkinliğinin **kaynak türünü** **websource**olarak ayarlayın. Şu anda, Copy etkinliğinin kaynağı **Websource**türünde olduğunda ek özellikler desteklenmez.

#### <a name="example"></a>Örnek

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Daha fazla bilgi için bkz. [Web tablosu Bağlayıcısı](data-factory-web-table-connector.md#copy-activity-properties) makalesi.

## <a name="compute-environments"></a>IŞLEM ORTAMLARı
Aşağıdaki tabloda, Data Factory tarafından desteklenen işlem ortamları ve bunlar üzerinde çalışabilen dönüştürme etkinlikleri listelenmektedir. Bir Data Factory 'ye bağlamak üzere bağlantılı hizmetin JSON şemalarını görmek için ilgilendiğiniz işlem bağlantısına tıklayın.

| İşlem ortamı | Etkinlikler |
| --- | --- |
| [İsteğe bağlı HDInsight kümesi](#on-demand-azure-hdinsight-cluster) veya [kendi HDInsight kümeniz](#existing-azure-hdinsight-cluster) |[.NET özel etkinliği](#net-custom-activity), [Hive etkinliği](#hdinsight-hive-activity), [Pig etkinliği](#hdinsight-pig-activity), [MapReduce etkinliği](#hdinsight-mapreduce-activity), Hadoop akış etkinliği, [Spark etkinliği](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET özel etkinliği](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Toplu yürütme etkinliğini Machine Learning](#machine-learning-batch-execution-activity) [Machine Learning kaynak etkinliğini Güncelleştir](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL veritabanı](#azure-sql-database), [Azure SQL veri ambarı](#azure-sql-data-warehouse), [SQL Server](#sql-server-stored-procedure) |[Saklı Yordam](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>İsteğe bağlı Azure HDInsight kümesi
Azure Data Factory hizmeti, verileri işlemek için otomatik olarak Windows/Linux tabanlı bir isteğe bağlı HDInsight kümesi oluşturabilir. Küme, kümeyle ilişkili depolama hesabı (JSON 'daki linkedServiceName özelliği) ile aynı bölgede oluşturulur. Şu dönüştürme etkinliklerini bu bağlı hizmette çalıştırabilirsiniz: [.NET özel etkinliği](#net-custom-activity), [Hive etkinliği](#hdinsight-hive-activity), [Pig etkinliği](#hdinsight-pig-activity), [MapReduce etkinliği](#hdinsight-mapreduce-activity), Hadoop akış etkinliği, [Spark etkinliği](#hdinsight-spark-activity).

### <a name="linked-service"></a>Bağlı hizmet
Aşağıdaki tabloda, isteğe bağlı HDInsight bağlı hizmetinin Azure JSON tanımında kullanılan özellikler için açıklamalar verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği **hdınsightondemand**olarak ayarlanmalıdır. |Evet |
| clusterSize |Kümedeki çalışan/veri düğümlerinin sayısı. HDInsight kümesi, bu özellik için belirttiğiniz çalışan düğümü sayısıyla birlikte 2 baş düğüm ile oluşturulur. Düğümler 4 çekirdeğe sahip Standard_D3 boyutlardır, bu nedenle 4 çalışan düğümü kümesi 24 çekirdek alır (çalışan düğümleri için 4\*4 = 16 çekirdek, ve baş düğümler için 2\*4 = 8 çekirdek). Standard_D3 katmanı hakkında ayrıntılar için bkz. [HDInsight 'Ta Linux tabanlı Hadoop kümeleri oluşturma](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . |Evet |
| timeToLive |İsteğe bağlı HDInsight kümesi için izin verilen boşta kalma süresi. Kümede başka bir etkin iş yoksa, bir etkinlik çalıştırıldıktan sonra, isteğe bağlı HDInsight kümesinin ne kadar süreyle etkin kalacağını belirtir.<br/><br/>Örneğin, bir etkinlik çalıştırması 6 dakika sürüyorsa ve TimeToLive, 5 dakika olarak ayarlanırsa, etkinlik çalıştırmasının sonunda 6 dakikadan sonra küme, 5 dakika boyunca etkin kalır. Başka bir etkinlik çalıştırması 6 dakikalık bir pencere ile yürütülürse aynı küme tarafından işlenir.<br/><br/>İsteğe bağlı HDInsight kümesi oluşturma işlemi pahalı bir işlemdir, bu nedenle isteğe bağlı HDInsight kümesini yeniden kullanarak bir veri fabrikasının performansını artırmak için bu ayarı gereken şekilde kullanın.<br/><br/>TimeToLive değerini 0 olarak ayarlarsanız, etkinlik işlenmiş olarak çalıştırıldığında küme silinir. Öte yandan, yüksek bir değer ayarlarsanız, küme gereksiz bir şekilde yüksek maliyetlerle ortaya çıkabilir. Bu nedenle, gereksinimlerinize göre uygun değeri ayarlamanız önemlidir.<br/><br/>TimeToLive Özellik değeri uygun şekilde ayarlandıysa, birden çok işlem hattı, isteğe bağlı HDInsight kümesinin aynı örneğini paylaşabilir |Evet |
| version |HDInsight kümesinin sürümü. Ayrıntılar için [Azure Data Factory desteklenen HDInsight sürümleri](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)bölümüne bakın. |Hayır |
| linkedServiceName |Verileri depolamak ve işlemek için isteğe bağlı küme tarafından kullanılacak Azure depolama bağlı hizmeti. <p>Şu anda depolama olarak bir Azure Data Lake Store kullanan isteğe bağlı bir HDInsight kümesi oluşturamazsınız. Bir Azure Data Lake Store HDInsight işlemeden elde edilen sonuç verilerini depolamak istiyorsanız, verileri Azure Blob depolama alanından Azure Data Lake Store kopyalamak için bir kopyalama etkinliği kullanın.</p>  | Evet |
| additionalLinkedServiceNames |Data Factory hizmeti tarafından sizin adınıza kaydettirilebilmeleri için HDInsight bağlı hizmeti için ek depolama hesapları belirtir. |Hayır |
| osType |İşletim sisteminin türü. İzin verilen değerler: Windows (varsayılan) ve Linux |Hayır |
| hcatalogLinkedServiceName |HCatalog veritabanına işaret eden Azure SQL bağlı hizmetinin adı. İsteğe bağlı HDInsight kümesi, Azure SQL veritabanı, meta veri deposu olarak kullanılarak oluşturulur. |Hayır |

### <a name="json-example"></a>JSON örneği
Aşağıdaki JSON, Linux tabanlı isteğe bağlı HDInsight bağlı hizmetini tanımlar. Data Factory hizmeti bir veri dilimini işlerken otomatik olarak bir **Linux tabanlı** HDInsight kümesi oluşturur.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Daha fazla bilgi için bkz. [işlem bağlı hizmetleri](data-factory-compute-linked-services.md) makalesi.

## <a name="existing-azure-hdinsight-cluster"></a>Mevcut Azure HDInsight kümesi
Kendi HDInsight kümenizi Data Factory kaydetmek için bir Azure HDInsight bağlı hizmeti oluşturabilirsiniz. Şu veri dönüştürme etkinliklerini bu bağlı hizmette çalıştırabilirsiniz: [.NET özel etkinliği](#net-custom-activity), [Hive etkinliği](#hdinsight-hive-activity), [Pig etkinliği](#hdinsight-pig-activity), [MapReduce etkinliği](#hdinsight-mapreduce-activity), Hadoop akış etkinliği, [Spark etkinliği](#hdinsight-spark-activity).

### <a name="linked-service"></a>Bağlı hizmet
Aşağıdaki tabloda, Azure HDInsight bağlı hizmeti 'nin Azure JSON tanımında kullanılan özellikler için açıklamalar sağlanmaktadır.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği **HDInsight**olarak ayarlanmalıdır. |Evet |
| clusterUri |HDInsight kümesinin URI 'SI. |Evet |
| kullanıcı adı |Mevcut bir HDInsight kümesine bağlanmak için kullanılacak kullanıcının adını belirtin. |Evet |
| password |Kullanıcı hesabı için parola belirtin. |Evet |
| linkedServiceName | HDInsight kümesi tarafından kullanılan Azure Blob depolama alanına başvuran Azure depolama bağlı hizmetinin adı. <p>Şu anda bu özellik için Azure Data Lake Store bağlı bir hizmet belirtemezsiniz. HDInsight kümesinin Data Lake Store erişimi varsa Hive/Pig betiklerinden Azure Data Lake Store verilere erişebilirsiniz. </p>  |Evet |

Desteklenen HDInsight kümelerinin sürümleri için bkz. [desteklenen HDInsight sürümleri](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON örneği

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Bir veri fabrikasına sahip sanal makinelerin (VM 'Ler) Batch havuzunu kaydetmek için Azure Batch bağlantılı bir hizmet oluşturabilirsiniz. .NET özel etkinliklerini, Azure Batch veya Azure HDInsight kullanarak çalıştırabilirsiniz. Bu bağlı hizmette [.NET özel etkinliğini](#net-custom-activity) çalıştırabilirsiniz.

### <a name="linked-service"></a>Bağlı hizmet
Aşağıdaki tabloda Azure Batch bağlı bir hizmetin Azure JSON tanımında kullanılan özellikler için açıklamalar verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği **AzureBatch**olarak ayarlanmalıdır. |Evet |
| Adı |Azure Batch hesabının adı. |Evet |
| accessKey |Azure Batch hesabı için erişim anahtarı. |Evet |
| poolName |Sanal makine havuzunun adı. |Evet |
| linkedServiceName |Bu Azure Batch bağlı hizmetiyle ilişkili Azure depolama bağlı hizmetinin adı. Bu bağlı hizmet, etkinliği çalıştırmak ve Etkinlik yürütme günlüklerini depolamak için gereken hazırlama dosyaları için kullanılır. |Evet |


#### <a name="json-example"></a>JSON örneği

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Bir veri fabrikasına sahip Machine Learning Batch Puanlama uç noktasını kaydetmek için Azure Machine Learning bağlı bir hizmet oluşturursunuz. Bu bağlı hizmet üzerinde çalışabilen iki veri dönüştürme etkinliği: [Batch yürütme etkinliği Machine Learning](#machine-learning-batch-execution-activity) [Machine Learning kaynak güncelleştirme](#machine-learning-update-resource-activity)etkinliği.

### <a name="linked-service"></a>Bağlı hizmet
Aşağıdaki tabloda Azure Machine Learning bağlı bir hizmetin Azure JSON tanımında kullanılan özellikler için açıklamalar verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Tür |Type özelliği: **AzureML**olarak ayarlanmalıdır. |Evet |
| mlEndpoint |Toplu işlem Puanlama URL 'SI. |Evet |
| apiKey |Yayımlanan çalışma alanı modelinin API 'SI. |Evet |

#### <a name="json-example"></a>JSON örneği

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Bir işlem hattındaki [Data Lake Analytics U-SQL etkinliğini](data-factory-usql-activity.md) kullanmadan önce Azure Data Lake Analytics işlem hizmetini bir Azure Data Factory 'ye bağlamak için **Azure Data Lake Analytics** bağlı bir hizmet oluşturursunuz.

### <a name="linked-service"></a>Bağlı hizmet

Aşağıdaki tabloda, Azure Data Lake Analytics bağlı bir hizmetin JSON tanımında kullanılan özellikler için açıklamalar verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Tür |Type özelliği: **AzureDataLakeAnalytics**olarak ayarlanmalıdır. |Evet |
| Adı |Azure Data Lake Analytics hesap adı. |Evet |
| Datalakeanaliz Ticsurı |Azure Data Lake Analytics URI 'SI. |Hayır |
| yetkisi |Yetkilendirme kodu, Data Factory düzenleyicisinde **Yetkilendir** düğmesine tıkladıktan sonra ve OAuth oturum açma işlemini tamamladıktan sonra otomatik olarak alınır. |Evet |
| subscriptionId |Azure abonelik kimliği |Hayır (belirtilmemişse, Veri Fabrikası aboneliği kullanılır). |
| resourceGroupName |Azure kaynak grubu adı |Hayır (belirtilmemişse, veri fabrikasının kaynak grubu kullanılır). |
| Kimliği |OAuth yetkilendirme oturumundan oturum kimliği. Her oturum kimliği benzersizdir ve yalnızca bir kez kullanılabilir. Data Factory düzenleyicisini kullandığınızda bu KIMLIK otomatik olarak üretilir. |Evet |


#### <a name="json-example"></a>JSON örneği
Aşağıdaki örnek, Azure Data Lake Analytics bağlı bir hizmet için JSON tanımı sağlar.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server saklı yordamı

SQL Server bağlı bir hizmet oluşturur ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](data-factory-stored-proc-activity.md) birlikte kullanırsınız.

### <a name="linked-service"></a>Bağlı hizmet
Şirket içi SQL Server veritabanını bir veri fabrikasına bağlamak için **OnPremisesSqlServer** türünde bağlı bir hizmet oluşturursunuz. Aşağıdaki tabloda, şirket içi SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama sağlanmaktadır.

Aşağıdaki tabloda SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesSqlServer**olarak ayarlanmalıdır. |Evet |
| connectionString |SQL kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak şirket içi SQL Server veritabanına bağlanmak için gerekli connectionString bilgilerini belirtin. |Evet |
| gatewayName |Data Factory hizmetinin şirket içi SQL Server veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |
| kullanıcı adı |Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. Örnek: **domainname\\Kullanıcı adı**. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |

**New-AzDataFactoryEncryptValue** cmdlet 'ini kullanarak kimlik bilgilerini şifreleyebilir ve aşağıdaki örnekte gösterildiği gibi bunları bağlantı dizesinde kullanabilirsiniz (**encryptedcredential** özelliği):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Örnek: SQL kimlik doğrulaması kullanmak için JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Örnek: Windows kimlik doğrulamasını kullanmak için JSON

Kullanıcı adı ve parola belirtilmişse, ağ geçidi, şirket içi SQL Server veritabanına bağlanmak üzere belirtilen kullanıcı hesabının kimliğine bürünmek için bunları kullanır. Aksi takdirde, ağ geçidi, doğrudan ağ geçidinin güvenlik bağlamı (başlangıç hesabı) ile SQL Server bağlanır.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#linked-service-properties) makalesi.

## <a name="data-transformation-activities"></a>VERI DÖNÜŞTÜRME ETKINLIKLERI

Etkinlik | Açıklama
-------- | -----------
[HDInsight Hive etkinliği](#hdinsight-hive-activity) | Bir Data Factory işlem hattının HDInsight Hive etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hive sorguları yürütür.
[HDInsight Pig etkinliği](#hdinsight-pig-activity) | Bir Data Factory işlem hattındaki HDInsight Pig etkinliği, kendi veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizdeki Pig sorgularını yürütür.
[HDInsight MapReduce Etkinliği](#hdinsight-mapreduce-activity) | Bir Data Factory işlem hattındaki HDInsight MapReduce etkinliği, MapReduce programlarını kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde yürütür.
[HDInsight Akış Etkinliği](#hdinsight-streaming-activity) | Bir Data Factory işlem hattının HDInsight akış etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hadoop akış programlarını yürütür.
[HDInsight Spark Etkinliği](#hdinsight-spark-activity) | Bir Data Factory işlem hattının HDInsight Spark etkinliği kendi HDInsight kümenizdeki Spark programlarını yürütür.
[Machine Learning Batch Yürütme Etkinliği](#machine-learning-batch-execution-activity) | Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir Azure Machine Learning Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. Toplu yürütme etkinliğini bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek için bir Machine Learning Web hizmeti çağırabilirsiniz.
[Machine Learning Kaynak Güncelleştirme Etkinliği](#machine-learning-update-resource-activity) | Zaman içinde, Machine Learning Puanlama denemeleri 'un tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Yeniden eğitim ile işiniz bittiğinde, Puanlama Web hizmetini geri çekme Machine Learning modeliyle güncelleştirmek istersiniz. Web hizmetini yeni eğitilen modelle güncelleştirmek için kaynak güncelleştirme etkinliğini kullanabilirsiniz.
[Saklı Yordam Etkinliği](#stored-procedure-activity) | Bir Data Factory işlem hattındaki saklı yordam etkinliğini aşağıdaki veri depolarından birinde bir saklı yordam çağırmak için kullanabilirsiniz: Azure SQL veritabanı, Azure SQL veri ambarı, kuruluşunuzdaki veya bir Azure VM 'de SQL Server veritabanı.
[Data Lake Analytics U-SQL etkinliği](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL etkinliği Azure Data Lake Analytics kümesinde bir U-SQL betiği çalıştırır.
[.NET özel etkinliği](#net-custom-activity) | Data Factory tarafından desteklenmeyen bir şekilde veri dönüştürmeniz gerekiyorsa, kendi veri işleme mantığınızla özel bir etkinlik oluşturabilir ve etkinliği ardışık düzende kullanabilirsiniz. Özel .NET etkinliğini bir Azure Batch hizmeti veya Azure HDInsight kümesi kullanarak çalışacak şekilde yapılandırabilirsiniz.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive Etkinliği
Hive etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **hdınsighthive**olmalıdır. Önce bir HDInsight bağlı hizmeti oluşturmanız ve bunun adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü Hdınsighthive olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| betiğini çalıştırın |Hive betiğini satır içi olarak belirt |Hayır |
| betik yolu |Hive betiğini bir Azure Blob depolama alanında depolayın ve dosyanın yolunu sağlayın. ' Script ' veya ' scriptPath ' özelliğini kullanın. İkisi birlikte kullanılamaz. Dosya adı büyük/küçük harfe duyarlıdır. |Hayır |
| tanımlar |' Hiveconf ' kullanarak Hive betiği içinde başvurmak için bir anahtar/değer çiftleri olarak parametre belirtin |Hayır |

Bu tür özellikleri Hive etkinliğine özeldir. Diğer Özellikler (typeProperties bölümünün dışında) tüm etkinlikler için desteklenir.

### <a name="json-example"></a>JSON örneği
Aşağıdaki JSON bir işlem hattının HDInsight Hive etkinliğini tanımlar.

```json
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

Daha fazla bilgi için bkz. [Hive etkinlik](data-factory-hive-activity.md) makalesi.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig Etkinliği
Bir Pig Activity JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **HDInsightPig**olmalıdır. Önce bir HDInsight bağlı hizmeti oluşturmanız ve bunun adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü HDInsightPig olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| betiğini çalıştırın |Pig betiğini satır içi olarak belirt |Hayır |
| betik yolu |Pig betiğini bir Azure Blob depolama alanına depolayın ve dosyanın yolunu sağlayın. ' Script ' veya ' scriptPath ' özelliğini kullanın. İkisi birlikte kullanılamaz. Dosya adı büyük/küçük harfe duyarlıdır. |Hayır |
| tanımlar |Pig betiği içinde başvurmak için parametreleri anahtar/değer çiftleri olarak belirtin |Hayır |

Bu tür özellikleri Pig etkinliğine özeldir. Diğer Özellikler (typeProperties bölümünün dışında) tüm etkinlikler için desteklenir.

### <a name="json-example"></a>JSON örneği

```json
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

Daha fazla bilgi için bkz. Pig etkinlik makalesi.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce Etkinliği
Bir MapReduce etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **HDInsightMapReduce**olmalıdır. Önce bir HDInsight bağlı hizmeti oluşturmanız ve bunun adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü HDInsightMapReduce olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| jarLinkedService | JAR dosyasını içeren Azure depolama için bağlı hizmetin adı. | Evet |
| jarFilePath | Azure depolama 'daki JAR dosyasının yolu. | Evet |
| className | JAR dosyasındaki ana sınıfın adı. | Evet |
| değişkenlerinden | MapReduce programı için virgülle ayrılmış bağımsız değişkenlerin bir listesi. Çalışma zamanında, MapReduce çerçevesinden birkaç ek bağımsız değişken (örneğin: MapReduce. job. Tag) görürsünüz. Bağımsız değişkenlerinizi MapReduce bağımsız değişkenleriyle ayırt etmek için, aşağıdaki örnekte gösterildiği gibi her iki seçeneği ve değeri bağımsız değişkenler olarak kullanmayı düşünün (-s,--Input,--Output vb.) | Hayır |

### <a name="json-example"></a>JSON örneği

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
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
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Daha fazla bilgi için bkz. [MapReduce etkinlik](data-factory-map-reduce.md) makalesi.

## <a name="hdinsight-streaming-activity"></a>HDInsight Akış Etkinliği
Bir Hadoop akış etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **hdınsightstreaming**olmalıdır. Önce bir HDInsight bağlı hizmeti oluşturmanız ve bunun adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü Hdınsightstreaming olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama |
| --- | --- |
| Eşleyici | Eşleyici yürütülebilir dosyasının adı. Örnekte, Cat. exe Eşleyici yürütülebilirdir.|
| reducer | Reducer yürütülebilir dosyasının adı. Örnekte, WC. exe, Reducer çalıştırılabilir. |
| giriş | Eşleyici için giriş dosyası (konum dahil). Örnek: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample blob kapsayıcısıdır, example/Data/Gutenberg klasörüdür ve DaVinci. txt blob olur. |
| çıkış | Reducer için çıkış dosyası (konum dahil). Hadoop akış işinin çıktısı, bu özellik için belirtilen konuma yazılır. |
| FilePaths parametresine | Eşleyici ve Reducer Yürütülebilirler için yollar. Örnek: "adfsample/example/Apps/WC. exe", adfsample blob kapsayıcısıdır, örnek/uygulamalar klasördür ve WC. exe yürütülebilir bir dosyadır. |
| Dosya Linkedservice | FilePaths bölümünde belirtilen dosyaları içeren Azure Storage 'ı temsil eden Azure depolama bağlı hizmeti. |
| değişkenlerinden | MapReduce programı için virgülle ayrılmış bağımsız değişkenlerin bir listesi. Çalışma zamanında, MapReduce çerçevesinden birkaç ek bağımsız değişken (örneğin: MapReduce. job. Tag) görürsünüz. Bağımsız değişkenlerinizi MapReduce bağımsız değişkenleriyle ayırt etmek için, aşağıdaki örnekte gösterildiği gibi her iki seçeneği ve değeri bağımsız değişkenler olarak kullanmayı düşünün (-s,--Input,--Output vb.) |
| GetDebugInfo | İsteğe bağlı bir öğe. Hata olarak ayarlandığında Günlükler yalnızca hata durumunda indirilir. Tümü olarak ayarlandığında, yürütme durumu ne olursa olsun Günlükler her zaman indirilir. |

> [!NOTE]
> **Çıktılar** özelliği Için Hadoop akış etkinliği için bir çıkış veri kümesi belirtmeniz gerekir. Bu veri kümesi, yalnızca işlem hattı zamanlamasını (saatlik, günlük, vb.) yönlendirmek için gereken bir kukla veri kümesi olabilir. Etkinlik bir giriş gerçekleştirmezse, **girişler** özelliği için etkinlik için bir giriş veri kümesi belirtmeyi atlayabilirsiniz.

## <a name="json-example"></a>JSON örneği

```json
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
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
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
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Daha fazla bilgi için bkz. [Hadoop akış etkinliği](data-factory-hadoop-streaming-activity.md) makalesi.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark Etkinliği
Spark etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **HDInsightSpark**olmalıdır. Önce bir HDInsight bağlı hizmeti oluşturmanız ve bunun adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü HDInsightSpark olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| rootPath | Spark dosyasını içeren Azure Blob kapsayıcısı ve klasörü. Dosya adı büyük/küçük harfe duyarlıdır. | Evet |
| entryFilePath | Spark kodunun/paketinin kök klasörünün göreli yolu. | Evet |
| className | Uygulamanın Java/Spark ana sınıfı | Hayır |
| değişkenlerinden | Spark programına yönelik komut satırı bağımsız değişkenlerinin listesi. | Hayır |
| proxyUser | Spark programını yürütmek için kimliğe bürünmeye yönelik kullanıcı hesabı | Hayır |
| Mini yapılandırma | Spark yapılandırma özellikleri. | Hayır |
| GetDebugInfo | Spark günlük dosyalarının, HDInsight kümesi tarafından kullanılan (veya) mini iş Linkedservice tarafından belirtilen Azure depolama 'ya ne zaman kopyalanacağını belirtir. İzin verilen değerler: None, Always veya Failure. Varsayılan değer: yok. | Hayır |
| Mini iş Linkedservice | Spark iş dosyasını, bağımlılıklarını ve günlüklerini tutan Azure depolama bağlı hizmeti.  Bu özellik için bir değer belirtmezseniz, HDInsight kümesiyle ilişkili depolama kullanılır. | Hayır |

### <a name="json-example"></a>JSON örneği

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Aşağıdaki noktalara dikkat edin:

- **Type** özelliği **HDInsightSpark**olarak ayarlanır.
- **RootPath** **adfspark\\pyfiles** olarak ayarlanır; burada Adfspark, Azure Blob kapsayıcısı ve pyfiles ise bu kapsayıcıda iyi bir klasördür. Bu örnekte, Azure Blob depolama, Spark kümesiyle ilişkili olan bir örnektir. Dosyayı farklı bir Azure depolama birimine yükleyebilirsiniz. Bunu yaparsanız, bu depolama hesabını veri fabrikasına bağlamak için bir Azure depolama bağlı hizmeti oluşturun. Daha sonra, bağlı hizmetin adını, **mini Iş linkedservice** özelliği için bir değer olarak belirtin. Bu özellik ve Spark etkinliğinin desteklediği diğer özellikler hakkında daha fazla bilgi için bkz. Spark etkinlik özellikleri.
- **Entryfilepath** , Python dosyası olan **test.py**öğesine ayarlanır.
- **GetDebugInfo** özelliği **her zaman**olarak ayarlanır; Bu, günlük dosyalarının her zaman oluşturulduğu anlamına gelir (başarılı veya başarısız).

    > [!IMPORTANT]
    > Bir sorun gidermediğiniz takdirde bu özelliği her zaman bir üretim ortamında ayarlamanıza tavsiye ederiz.
- **Çıktılar** bölümünde bir çıkış veri kümesi vardır. Spark programı herhangi bir çıkış üretmese bile bir çıkış veri kümesi belirtmeniz gerekir. Çıktı veri kümesi, işlem hattı için zamanlamayı (saatlik, günlük, vb.) yürütür.

Etkinlik hakkında daha fazla bilgi için bkz. [Spark etkinlik](data-factory-spark.md) makalesi.

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning Batch Yürütme Etkinliği
Azure Machine Learning Studio Batch yürütme etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **AzureMLBatchExecution**olmalıdır. Önce Azure Machine Learning bağlı bir hizmet oluşturmanız ve bu adın adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü AzureMLBatchExecution olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
Projede WebServiceInput | Azure Machine Learning Studio Web hizmeti için giriş olarak geçirilecek veri kümesi. Bu veri kümesi, etkinliğin girişlerinde de yer almalıdır. |WebServiceInput veya Webservicegirdilerden birini kullanın. |
Webservicegirişlerinde | Azure Machine Learning Studio Web hizmeti için giriş olarak geçirilecek veri kümelerini belirtin. Web hizmeti birden çok giriş alırsa, WebServiceInput özelliğini kullanmak yerine Webservicegirdileri özelliğini kullanın. **Webservicegirişlerinin** başvurduğu veri kümeleri de etkinlik **girişlerinde**yer almalıdır. | WebServiceInput veya Webservicegirdilerden birini kullanın. |
Webserviceçıktılar | Azure Machine Learning Studio Web hizmeti için çıkış olarak atanan veri kümeleri. Web hizmeti bu veri kümesindeki çıktı verilerini döndürür. | Evet |
globalParameters | Bu bölümdeki Web hizmeti parametrelerinin değerlerini belirtin. | Hayır |

### <a name="json-example"></a>JSON örneği
Bu örnekte, etkinliğin **Mlsqlinput** as Input ve Output as **MLSqlOutput** veri kümesi vardır. **Mlsqlinput** , **WebServiceInput** JSON özelliği kullanılarak Web hizmetine giriş olarak geçirilir. **MLSqlOutput** , **webserviceçıktılar** JSON özelliği kullanılarak Web hizmetine çıkış olarak geçirilir.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

JSON örneğinde, dağıtılan Azure Machine Learning Web hizmeti, verileri bir Azure SQL veritabanından okumak/içine yazmak için bir okuyucu ve bir yazıcı modülü kullanır. Bu Web hizmeti şu dört parametreyi kullanıma sunar: veritabanı sunucu adı, veritabanı adı, sunucu Kullanıcı hesabı adı ve sunucu Kullanıcı hesabı parolası.

> [!NOTE]
> Yalnızca AzureMLBatchExecution etkinliğinin girişleri ve çıkışları Web hizmetine parametre olarak geçirilebilir. Örneğin, yukarıdaki JSON parçacığında, MLSqlInput, AzureMLBatchExecution etkinliğine yönelik bir giriştir ve bu, WebServiceInput parametresi aracılığıyla Web hizmetine giriş olarak geçirilir.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning Kaynak Güncelleştirme Etkinliği
Azure Machine Learning Studio güncelleştirme kaynak etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **AzureMLUpdateResource**olmalıdır. Önce Azure Machine Learning bağlı bir hizmet oluşturmanız ve bu adın adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Etkinlik türünü AzureMLUpdateResource olarak ayarladığınızda **typeproperties** bölümünde aşağıdaki özellikler desteklenir:

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
Traınedmodelname | Geri çekme modelinin adı. | Evet |
Traınedmodeldatasetname | Yeniden eğitme işleminin döndürdüğü ilearner dosyasına işaret eden veri kümesi. | Evet |

### <a name="json-example"></a>JSON örneği
İşlem hattının iki etkinliği vardır: **AzureMLBatchExecution** ve **AzureMLUpdateResource**. Azure Machine Learning Studio Batch yürütme etkinliği, eğitim verilerini giriş olarak alır ve çıkış olarak bir iLearner dosyası üretir. Etkinlik, eğitim Web hizmetini (bir Web hizmeti olarak sunulan eğitim denemesine) giriş eğitim verileriyle çağırır ve IBU dosya hizmetten ilearner dosyasını alır. PlaceholderBlob, işlem hattını çalıştırmak için Azure Data Factory hizmetinin gerektirdiği yalnızca bir kukla çıkış veri kümesidir.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL Etkinliği
Aşağıdaki özellikleri bir U-SQL etkinliği JSON tanımında belirtebilirsiniz. Etkinliğin Type özelliği: **Datalakeanalticsu-SQL**olmalıdır. Azure Data Lake Analytics bağlı bir hizmet oluşturmanız ve bu adın adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Aşağıdaki özellikler, etkinlik türünü Datalakeanalticsu-SQL olarak ayarladığınızda **Typeproperties** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| scriptPath |U-SQL betiğini içeren klasörün yolu. Dosyanın adı büyük/küçük harfe duyarlıdır. |Hayır (komut dosyası kullanıyorsanız) |
| scriptLinkedService |Betiği içeren depolamayı Data Factory 'ye bağlayan bağlantılı hizmet |Hayır (komut dosyası kullanıyorsanız) |
| betiğini çalıştırın |ScriptPath ve scriptLinkedService belirtmek yerine satır içi betiği belirtin. Örneğin: "komut dosyası": "VERITABANı testi oluşturma". |Hayır (scriptPath ve scriptLinkedService kullanıyorsanız) |
| Analyticsunits |İşi çalıştırmak için eşzamanlı olarak kullanılan en fazla düğüm sayısı. |Hayır |
| Priority |Önce kuyruğa alınan tüm işlerin ne kadar önce çalıştırılacağını belirler. Sayı ne kadar düşükse öncelik o kadar yüksektir. |Hayır |
| parametreler |U-SQL betiği için parametreler |Hayır |

### <a name="json-example"></a>JSON örneği

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Daha fazla bilgi için bkz. [U-SQL etkinliği Data Lake Analytics](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Saklı Yordam Etkinliği
Saklı yordam etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **Sqlserverstoredprocedure**olmalıdır. Aşağıdaki bağlı hizmetlerden birini oluşturmanız ve bağlantılı hizmetin adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir:

- SQL Server
- Azure SQL Veritabanı
- Azure SQL Veri Ambarı

Aşağıdaki özellikler, etkinlik türünü SqlServerStoredProcedure olarak ayarladığınızda **Typeproperties** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| storedProcedureName |Azure SQL veritabanı veya Azure SQL veri ambarı 'nda, çıkış tablosunun kullandığı bağlı hizmet tarafından temsil edilen saklı yordamın adını belirtin. |Evet |
| storedProcedureParameters |Saklı yordam parametrelerinin değerlerini belirtin. Bir parametre için null değer geçirmeniz gerekiyorsa, "Param1": null (tümü küçük harf) sözdizimini kullanın. Bu özelliği kullanma hakkında bilgi edinmek için aşağıdaki örneğe bakın. |Hayır |

Bir giriş veri kümesi belirtirseniz, saklı yordam etkinliğinin çalışması için (' hazır ' durumunda) kullanılabilir olmalıdır. Giriş veri kümesi, saklı yordamda parametre olarak tüketilemiyor. Yalnızca saklı yordam etkinliğini başlatmadan önce bağımlılığı denetlemek için kullanılır. Saklı yordam etkinliği için bir çıkış veri kümesi belirtmeniz gerekir.

Çıktı veri kümesi, saklı yordam etkinliğinin **zamanlamasını** belirtir (saatlik, haftalık, aylık, vb.). Çıktı veri kümesi, bir Azure SQL veritabanı veya Azure SQL veri ambarı ya da saklı yordamın çalıştırılmasını istediğiniz bir SQL Server veritabanına başvuran **bağlı bir hizmet** kullanmalıdır. Çıktı veri kümesi, işlem hattındaki başka bir etkinlik ([zincirleme etkinlikleri](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) tarafından sonraki işleme için saklı yordamın sonucunu geçirmenin bir yolu olarak görev yapabilir. Ancak, Data Factory saklı yordamın çıkışını bu veri kümesine otomatik olarak yazmaz. Bu, çıktı veri kümesinin işaret ettiği bir SQL tablosuna yazan saklı yordamdır. Bazı durumlarda, çıkış veri kümesi yalnızca saklı yordam etkinliğini çalıştırmaya yönelik zamanlamayı belirtmek için kullanılan bir **kukla veri kümesi**olabilir.

### <a name="json-example"></a>JSON örneği

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Daha fazla bilgi için bkz. [saklı yordam etkinliği](data-factory-stored-proc-activity.md) makalesi.

## <a name="net-custom-activity"></a>.NET özel etkinliği
.NET özel etkinliği JSON tanımında aşağıdaki özellikleri belirtebilirsiniz. Etkinliğin Type özelliği: **Dotnetactivity**olmalıdır. Azure HDInsight bağlı hizmeti veya Azure Batch bağlı hizmeti oluşturmanız ve bağlantılı hizmetin adını **Linkedservicename** özelliği için bir değer olarak belirtmeniz gerekir. Aşağıdaki özellikler, etkinlik türünü DotNetActivity olarak ayarladığınızda **Typeproperties** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| AssemblyName | Derlemenin adı. Örnekte,: **MyDotnetActivity. dll**' dir. | Evet |
| Noktası |Idotnetactivity arabirimini uygulayan sınıfın adı. Örnekte,: **MyDotNetActivityNS. MyDotNetActivity** burada MyDotNetActivityNS ad alanıdır ve MyDotNetActivity sınıfıdır.  | Evet |
| PackageLinkedService | Özel etkinlik ZIP dosyasını içeren BLOB depolama alanına işaret eden Azure depolama bağlı hizmetinin adı. Örnekte: **AzureStorageLinkedService**.| Evet |
| PackageFile | ZIP dosyasının adı. Örnekte, bu: **customactivitycontainer/MyDotNetActivity. zip**' dir. | Evet |
| extendedProperties | Tanımlayabilir ve .NET koduna geçirebilmeniz için genişletilmiş özellikler. Bu örnekte,, **festart sistem** değişkenine göre bir değere ayarlanır. | Hayır |

### <a name="json-example"></a>JSON örneği

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Ayrıntılı bilgi için bkz. [Data Factory makalesinde özel etkinlikleri kullanma](data-factory-use-custom-activities.md) .

## <a name="next-steps"></a>Sonraki Adımlar
Aşağıdaki öğreticilere bakın:

- [Öğretici: kopyalama etkinliği ile işlem hattı oluşturma](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Öğretici: Hive etkinliği ile işlem hattı oluşturma](data-factory-build-your-first-pipeline.md)
