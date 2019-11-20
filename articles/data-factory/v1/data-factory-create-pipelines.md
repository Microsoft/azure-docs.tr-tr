---
title: Data Factory için işlem hatları oluşturma/zamanlama, zincir etkinlikleri
description: Verileri taşımak ve dönüştürmek için Azure Data Factory bir veri işlem hattı oluşturmayı öğrenin. Kullanıma hazırlık bilgileri üretmek için veri odaklı bir iş akışı oluşturun.
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
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682734"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory işlem hatları ve Etkinlikler
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-create-pipelines.md)
> * [Sürüm 2 (geçerli sürüm)](../concepts-pipelines-activities.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Işlem hatları](../concepts-pipelines-activities.md).

Bu makale, Azure Data Factory’de işlem hatlarını ve etkinlikleri anlamanıza ve veri hareketi ile veri işleme senaryolarınız için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur.

> [!NOTE]
> Bu makalede, [Azure Data Factory giriş](data-factory-introduction.md)yaptığınızda geçmiş olduğunu varsaymaktadır. Veri Fabrikası oluşturmaya yönelik uygulamalı deneyim sahibi değilseniz, [veri dönüştürme öğreticisini](data-factory-build-your-first-pipeline.md) ve/veya [veri taşıma öğreticisini](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kullanarak bu makaleyi daha iyi anlamanıza yardımcı olur.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı, bir araya geldiğinde bir görev gerçekleştiren mantıksal etkinlik grubudur. Bir işlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Örneğin, kopyalama etkinliğini kullanarak şirket içi SQL Server’dan Azure Blob Depolama’ya veri kopyalayabilirsiniz. Ardından bir Azure HDInsight kümesinde Hive betiği çalıştıran bir Hive etkinliği kullanıp blob depolamadaki verileri işleyerek/dönüştürerek çıktı verileri üretebilirsiniz. Son olarak, ikinci bir kopyalama etkinliği kullanarak üzerinde iş zekası (BI) raporlama çözümlerinin oluşturulduğu bir Azure SQL Veri Ambarı’na çıktı verilerini kopyalayabilirsiniz.

Bir etkinliğin sıfır veya sıfırdan çok giriş [veri kümesi](data-factory-create-datasets.md) olabilir ve her etkinlik bir veya birden çok çıkış [veri kümesi](data-factory-create-datasets.md) oluşturabilir. Aşağıdaki diyagramda, Data Factory içindeki işlem hattı, etkinlik ve veri kümesi arasındaki ilişki gösterilmektedir:

![İşlem hattı, etkinlik ve veri kümesi arasındaki ilişki](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

İşlem hattı etkinlikleri tek tek yerine bir küme olarak yönetmenizi sağlar. Örneğin, işlem hattındaki etkinliklerle bağımsız olarak bir işlem hattı dağıtabilir, zamanlayabilir, askıya alabilir ve sürdürebilirsiniz.

Data Factory iki tür etkinliği destekler: veri taşıma etkinlikleri ve veri dönüştürme etkinlikleri. Her etkinlik sıfır veya daha fazla giriş [veri kümesine](data-factory-create-datasets.md) sahip olabilir ve bir veya daha fazla çıktı veri kümesi oluşturabilir.

Giriş veri kümesi, veri işlem hattındaki bir etkinlik için girişi ve çıktı veri kümesi, etkinliğin çıktısını temsil eder. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Bir veri kümesi oluşturduktan sonra, bu kümeyi bir işlem hattındaki etkinliklerle birlikte kullanabilirsiniz. Örneğin, veri kümesi bir Kopyalama Etkinliğinin veya HDInsightHive Etkinliğinin giriş/çıkış veri kümesi olabilir. Veri kümeleri hakkında daha fazla bilgi için [Azure Data Factory'de Veri Kümeleri](data-factory-create-datasets.md) makalesine bakın.

### <a name="data-movement-activities"></a>Veri taşıma etkinlikleri
Data Factory’deki Kopyalama Etkinliği bir kaynak veri deposundan havuz veri deposuna verileri kopyalar. Data Factory aşağıdaki veri depolarını destekler. Herhangi bir kaynaktan gelen veriler herhangi bir havuza yazılabilir. Bir depoya veya depodan veri kopyalama hakkında bilgi edinmek için veri deposuna tıklayın.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* taşıyan veri depoları şirket içi veya Azure IaaS üzerinde olabilir ve bir şirket içi/Azure IaaS makinesine [Veri Yönetimi Ağ Geçidi](data-factory-data-management-gateway.md) yüklemenizi gerektirir.

Daha fazla bilgi için [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md) makalesine bakın.

### <a name="data-transformation-activities"></a>Veri dönüştürme etkinlikleri
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Daha fazla bilgi için [Veri Dönüştürme Etkinlikleri](data-factory-data-transformation-activities.md) makalesine bakın.

### <a name="custom-net-activities"></a>Özel .NET etkinlikleri
Kopyalama etkinliğinin desteklemediği bir veri deposuna/kaynağından veri taşımanız ya da kendi mantığınızı kullanarak verileri dönüştürmeniz gerekirse **özel bir .net etkinliği**oluşturun. Özel bir etkinlik oluşturma ve kullanma hakkında ayrıntılı bilgi için bkz. [Azure Data Factory işlem hattında özel etkinlikler kullanma](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>İşlem hatlarını zamanlama
İşlem hattı yalnızca **Başlangıç** saati ve **bitiş** saati arasında etkindir. Başlangıç zamanından veya bitiş zamanından önce yürütülmez. İşlem hattı duraklatılmışsa, başlangıç ve bitiş zamanından bağımsız olarak yürütülmez. Bir işlem hattının çalışması için duraklatılmamalıdır. Azure Data Factory planlama ve yürütmenin nasıl çalıştığını anlamak için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) .

## <a name="pipeline-json"></a>İşlem Hattı JSON
Bir işlem hattının JSON biçiminde nasıl tanımlandığına daha yakından bakalım. Bir işlem hattının genel yapısı şu şekilde görünür:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Etiket | Açıklama | Gerekli |
| --- | --- | --- |
| ad |İşlem hattının adı. İşlem hattının gerçekleştirdiği eylemi temsil eden bir ad belirtin. <br/><ul><li>En fazla karakter sayısı: 260</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterlere izin verilmez: ".", "+", "?", "/", "<", ">", "\*", "%", "&", ":", "\\"</li></ul> |Evet |
| açıklama | İşlem hattının ne için kullanıldığını açıklayan metni belirtin. |Evet |
| etkinlikler | **Etkinlikler** bölümünde tanımlanmış bir veya daha fazla etkinlik olabilir. Etkinlikler JSON öğesiyle ilgili ayrıntılar için sonraki bölüme bakın. | Evet |
| start | İşlem hattının başlangıç tarihi-saati. [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601)olmalıdır. Örneğin: `2016-10-14T16:32:41Z`. <br/><br/>Bir yerel saat belirtmek mümkündür, örneğin bir saat. İşte bir örnek: `2016-02-27T06:00:00-05:00`", bu 6 EST.<br/><br/>Başlangıç ve bitiş özellikleri, işlem hattının etkin dönemini belirtir. Çıkış dilimleri bu etkin dönemde yalnızca ile üretilir. |Hayır<br/><br/>End özelliği için bir değer belirtirseniz, Start özelliği için bir değer belirtmeniz gerekir.<br/><br/>Bir işlem hattı oluşturmak için başlangıç ve bitiş saatlerinin her ikisi de boş olabilir. İşlem hattının çalışması için etkin bir süre ayarlamak üzere her iki değeri de belirtmeniz gerekir. İşlem hattı oluştururken başlangıç ve bitiş zamanlarını belirtmezseniz, daha sonra set-Azdatafactorypıpelineactiveperiod cmdlet 'ini kullanarak bunları ayarlayabilirsiniz. |
| erer | İşlem hattının bitiş tarihi-saati. Belirtilmişse ISO biçiminde olmalıdır. Örneğin, `2016-10-14T17:32:41Z` <br/><br/>Bir yerel saat belirtmek mümkündür, örneğin bir saat. Örnek: `2016-02-27T06:00:00-05:00`, bu 6 EST.<br/><br/>İşlem hattını süresiz olarak çalıştırmak için, End özelliğinin değeri olarak 9999-09-09 değerini belirtin. <br/><br/> İşlem hattı yalnızca başlangıç saati ve bitiş saati arasında etkindir. Başlangıç zamanından veya bitiş zamanından önce yürütülmez. İşlem hattı duraklatılmışsa, başlangıç ve bitiş zamanından bağımsız olarak yürütülmez. Bir işlem hattının çalışması için duraklatılmamalıdır. Azure Data Factory planlama ve yürütmenin nasıl çalıştığını anlamak için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) . |Hayır <br/><br/>Start özelliği için bir değer belirtirseniz, End özelliği için bir değer belirtmeniz gerekir.<br/><br/>**Start** özelliği için notlara bakın. |
| isPaused | True olarak ayarlanırsa işlem hattı çalıştırılmaz. Bu, duraklatılmış durumda. Varsayılan değer = false. Bu özelliği, bir işlem hattını etkinleştirmek veya devre dışı bırakmak için kullanabilirsiniz. |Hayır |
| pipelineMode | İşlem hattı için zamanlama çalıştırmaları yöntemi. İzin verilen değerler: zamanlanmış (varsayılan), Onetime.<br/><br/>' Zamanlanmış ', işlem hattının etkin döneme (başlangıç ve bitiş saati) göre belirli bir zaman aralığında çalıştığını gösterir. ' Onetime ', işlem hattının yalnızca bir kez çalışacağını gösterir. Her seferinde bir kez oluşturulduktan sonra bir süre işlem hattı değiştirilemez/güncelleştirilemez. Kerelik ayarı hakkında ayrıntılı bilgi için bkz. [kerelik işlem hattı](#onetime-pipeline) . |Hayır |
| expirationTime | Oluşturulduktan sonra, [tek seferlik işlem hattının](#onetime-pipeline) geçerli olduğu ve sağlanması gereken süre. Etkin, başarısız veya bekleyen çalıştırmaları yoksa, işlem hattı süre sonu zamanına ulaştığında otomatik olarak silinir. Varsayılan değer: `"expirationTime": "3.00:00:00"`|Hayır |
| kümelerinin |İşlem hattında tanımlanan etkinlikler tarafından kullanılacak veri kümelerinin listesi. Bu özellik, bu işlem hattına özgü ve Veri Fabrikası içinde tanımlanmayan veri kümelerini tanımlamak için kullanılabilir. Bu işlem hattı içinde tanımlanan veri kümeleri yalnızca bu işlem hattı tarafından kullanılabilir ve paylaştırılamaz. Ayrıntılar için bkz. [kapsamlı veri kümeleri](data-factory-create-datasets.md#scoped-datasets) . |Hayır |

## <a name="activity-json"></a>Etkinlik JSON
**Etkinlikler** bölümünde tanımlanmış bir veya daha fazla etkinlik olabilir. Her etkinlik aşağıdaki en üst düzey yapıya sahiptir:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

Aşağıdaki tabloda, etkinlik JSON tanımındaki özellikler açıklamaktadır:

| Etiket | Açıklama | Gerekli |
| --- | --- | --- |
| ad | Etkinliğin adı. Etkinliğin gerçekleştirdiği eylemi temsil eden bir ad belirtin. <br/><ul><li>En fazla karakter sayısı: 260</li><li>Bir harf numarası veya alt çizgi (\_) ile başlamalıdır</li><li>Şu karakterlere izin verilmez: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Evet |
| açıklama | Etkinliğin ne olduğunu veya ne için kullanıldığını açıklayan metin |Evet |
| type | Etkinliğin türü. Farklı etkinlik türleri için [veri taşıma etkinlikleri](#data-movement-activities) ve [veri dönüştürme etkinlikleri](#data-transformation-activities) bölümlerine bakın. |Evet |
| girişi |Etkinlik tarafından kullanılan giriş tabloları<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Evet |
| çıkışı |Etkinlik tarafından kullanılan çıkış tabloları.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Evet |
| linkedServiceName |Etkinlik tarafından kullanılan bağlı hizmetin adı. <br/><br/>Bir etkinlik için gerekli işlem ortamına bağlanan bağlı hizmeti belirtmeniz gerekebilir. |HDInsight etkinliği için Evet ve Azure Machine Learning Batch puanlama etkinliği <br/><br/>Diğer tümü için hayır |
| typeProperties |**Typeproperties** bölümündeki özellikler etkinliğin türüne bağlıdır. Bir etkinliğin tür özelliklerini görmek için önceki bölümde verilen etkinlik bağlantılarına tıklayın. | Hayır |
| ilke |Etkinliğin çalışma zamanı davranışını etkileyen ilkeler. Belirtilmemişse, varsayılan ilkeler kullanılır. |Hayır |
| leyiciyi | "Scheduler" özelliği, etkinlik için istenen zamanlamayı tanımlamak için kullanılır. Alt özellikleri, [bir veri kümesindeki kullanılabilirlik özelliğindeki](data-factory-create-datasets.md#dataset-availability)olanlarla aynıdır. |Hayır |

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

## <a name="sample-copy-pipeline"></a>Örnek kopyalama işlem hattı
Aşağıdaki örnek işlem hattında, **Etkinlikler** bölümünde **Kopyalama** türünde olan bir etkinlik vardır. Bu örnekte [kopyalama etkinliği](data-factory-data-movement-activities.md), verileri Azure Blob depolama alanından Azure SQL veritabanına kopyalar.

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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Aşağıdaki noktalara dikkat edin:

* Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır.
* Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır. JSON biçiminde veri kümeleri tanımlamak için [Veri Kümeleri](data-factory-create-datasets.md) makalesine bakın.
* **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. Veri [taşıma etkinlikleri](#data-movement-activities) bölümünde, veri deposuna/veri deposundan veri taşıma hakkında daha fazla bilgi edinmek için kaynak veya havuz olarak kullanmak istediğiniz veri deposuna tıklayın.

Bu işlem hattını oluşturma hakkında ayrıntılı yönergeler için bkz. [öğretici: blob DEPOLAMADAN SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Örnek dönüştürme işlem hattı
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Aşağıdaki noktalara dikkat edin:

* Etkinlikler bölümünde **türü** **HDInsightHive** olarak ayarlanmış yalnızca bir etkinlik vardır.
* **partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilen **AzureStorageLinkedService** adıyla) ve **adfgetstarted** kapsayıcısındaki **betik** klasöründe depolanır.
* `defines` bölümü, Hive betiğine Hive yapılandırma değerleri olarak geçirilen çalışma zamanı ayarlarını belirtmek için kullanılır (örn. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

**TypeProperties** bölümü her bir dönüştürme etkinliği için farklıdır. Bir dönüştürme etkinliği için desteklenen tür özellikleri hakkında bilgi edinmek için [veri dönüştürme etkinlikleri](#data-transformation-activities) tablosunda dönüştürme etkinliğine tıklayın.

Bu işlem hattını oluşturma hakkında ayrıntılı yönergeler için bkz. [öğretici: Hadoop kümesi kullanarak verileri işlemek için ilk işlem hattınızı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Bir işlem hattında birden çok etkinlik
Önceki iki örnekte işlem hatları yalnızca bir etkinlik içeriyordu. Bir işlem hattında birden fazla etkinliğiniz olabilir.

Bir işlem hattında birden çok etkinliğiniz varsa ve bir etkinliğin çıkışı başka bir etkinliğin girişi değilse, etkinlikler için giriş veri dilimleri hazırsanız etkinlikler paralel olarak çalışabilir.

Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak bulundurarak iki etkinliği zincirleyebilirsiniz. İkinci etkinlik yalnızca ilk bir kez başarıyla tamamlandığında yürütülür.

![Aynı ardışık düzende zincirleme etkinlikleri](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Bu örnekte, işlem hattının iki etkinliği vardır: Activity1 ve Activity2. Activity1, DataSet1 bir girdi olarak alır ve bir çıktı DataSet2 üretir. Etkinlik, DataSet2 bir giriş olarak alır ve bir çıktı Dataset3 üretir. Activity1 (DataSet2) çıkışı Activity2 girişi olduğundan, Activity2 yalnızca etkinlik başarıyla tamamlandıktan sonra ve DataSet2 dilimi üretirse çalışır. Activity1 bir nedenden dolayı başarısız olursa ve DataSet2 dilimini oluşturmazsa, etkinlik 2 bu dilim için çalışmaz (örneğin: 9-10 ' a).

Ayrıca, farklı işlem hatlarında olan etkinlikleri zincirleyebilirsiniz.

![Etkinlikleri iki işlem hattı halinde zincirleme](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Bu örnekte, Pipeline1 giriş olarak DataSet1 alan ve bir çıktı olarak DataSet2 üreten yalnızca bir etkinliğe sahiptir. Ayrıca Pipeline2, bir giriş olarak DataSet2 ve bir çıkış olarak Dataset3 alan yalnızca bir etkinliğe sahiptir.

Daha fazla bilgi için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>İşlem hatları oluşturma ve izleme
Bu araçlardan veya SDK 'Lardan birini kullanarak işlem hatları oluşturabilirsiniz.

- Kopyalama Sihirbazı
- Visual Studio
- Azure PowerShell
- Azure Resource Manager şablonu
- REST API
- .NET API’si

Bu araçlardan veya SDK 'Lardan birini kullanarak işlem hattı oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticilere bakın.

- [Veri dönüştürme etkinliğine sahip işlem hattı oluşturma](data-factory-build-your-first-pipeline.md)
- [Veri taşıma etkinliği ile işlem hattı oluşturma](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

İşlem hattı oluşturulduktan/dağıtıldıktan sonra, Azure portal dikey pencereleri kullanarak, işlem hatlarınızı yönetebilir ve izleyebilirsiniz ve uygulamayı Izleyebilir ve yönetebilirsiniz. Adım adım yönergeler için aşağıdaki konulara bakın.

- [Azure Portal Blade kullanarak işlem hatlarını izleyin ve yönetin](data-factory-monitor-manage-pipelines.md).
- [Izleme ve yönetme uygulamasını kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Onetime işlem hattı
İşlem hattı tanımında belirttiğiniz başlangıç ve bitiş saatlerinde düzenli aralıklarla çalışacak bir işlem hattı oluşturabilir ve zamanlayabilirsiniz (örneğin: saatlik veya günlük). Ayrıntılar için bkz. zamanlama etkinlikleri. Yalnızca bir kez çalışan bir işlem hattı da oluşturabilirsiniz. Bunu yapmak için, aşağıdaki JSON örneğinde gösterildiği gibi işlem hattı tanımındaki **PipelineMode** özelliğini **kerelik** olarak ayarlarsınız. Bu özellik için varsayılan değer **zamanlandı**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Şunlara dikkat edin:

* İşlem hattının **Başlangıç** ve **bitiş** zamanları belirtilmedi.
* Giriş ve çıkış veri kümelerinin **kullanılabilirliği** (**Sıklık** ve **Aralık**), Data Factory değerleri kullanmasa bile.
* Diyagram görünümü bir kerelik işlem hatlarını göstermez. Bu davranış tasarım gereğidir.
* Tek seferlik işlem hatları güncelleştirilemez. Tek seferlik bir işlem hattını kopyalayabilir, yeniden adlandırabilir, özellikleri güncelleştirebilir ve bir tane oluşturmak için dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- Veri kümeleri hakkında daha fazla bilgi için bkz. [veri kümesi oluşturma](data-factory-create-datasets.md) makalesi.
- İşlem hatlarının nasıl zamanlandığını ve yürütüldüğü hakkında daha fazla bilgi için [Azure Data Factory makalesinde zamanlama ve yürütme](data-factory-scheduling-and-execution.md) konusuna bakın.
