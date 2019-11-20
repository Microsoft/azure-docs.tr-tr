---
title: Data Factory ile zamanlama ve yürütme
description: Azure Data Factory uygulama modelinin zamanlama ve yürütme yönlerini öğrenin.
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
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666124"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory zamanlama ve yürütme
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. işlem [hattı yürütme ve Tetikleyiciler](../concepts-pipeline-execution-triggers.md) makalesi.

Bu makalede Azure Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. Bu makalede etkinlik, işlem hatları, bağlı hizmetler ve veri kümeleri dahil olmak üzere Data Factory uygulama modeli kavramlarının temelleri anladığınızı varsaymaktadır. Azure Data Factory temel kavramları için aşağıdaki makalelere bakın:

* [Data Factory giriş](data-factory-introduction.md)
* [İşlem hatları](data-factory-create-pipelines.md)
* [Veri kümeleri](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>İşlem hattının başlangıç ve bitiş zamanları
İşlem hattı yalnızca **Başlangıç** saati ve **bitiş** saati arasında etkindir. Başlangıç zamanından veya bitiş zamanından önce yürütülmez. İşlem hattı duraklatıldığında, başlangıç ve bitiş zamanından bağımsız olarak yürütülmez. Bir işlem hattının çalışması için duraklatılmamalıdır. Bu ayarları işlem hattı tanımında (başlangıç, bitiş, duraklama) bulabilirsiniz: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Bu özellikler hakkında daha fazla bilgi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesi. 


## <a name="specify-schedule-for-an-activity"></a>Etkinlik için zamanlamayı belirtme
Yürütülen işlem hattı değildir. İşlem hattının genel bağlamında yürütülen etkinliklerdir. Etkinlik JSON ' un **Zamanlayıcı** bölümünü kullanarak bir etkinlik için yinelenen bir zamanlama belirtebilirsiniz. Örneğin, bir etkinliği saatlik olarak çalışacak şekilde zamanlamak için aşağıdaki adımları uygulayabilirsiniz:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Aşağıdaki diyagramda gösterildiği gibi, bir etkinlik için zamanlama belirtildiğinde işlem hattının başlangıç ve bitiş saatlerinde bir dizi pencere oluşturulur. Atlayan pencereler sabit boyutlu çakışmayan ve bitişik zaman aralıkları dizisidir. Etkinliğe yönelik bu mantıksal olarak verilen bu Windows, **etkinlik pencereleri**olarak adlandırılır.

![Etkinlik zamanlayıcı örneği](media/data-factory-scheduling-and-execution/scheduler-example.png)

Etkinliğin **Zamanlayıcı** özelliği isteğe bağlıdır. Bu özelliği belirtirseniz, etkinliğin çıkış veri kümesi tanımında belirttiğiniz temposunda ile eşleşmesi gerekir. Şu anda zamanlamayı çıktı veri kümesi yürütmektedir. Bu nedenle, etkinlik herhangi bir çıkış üretmese bile bir çıkış veri kümesi oluşturmanız gerekir. 

## <a name="specify-schedule-for-a-dataset"></a>Veri kümesi için zamanlamayı belirtin
Bir Data Factory işlem hattındaki etkinlik sıfır veya daha fazla giriş **veri kümesi** alabilir ve bir veya daha fazla çıkış veri kümesi oluşturabilir. Bir etkinlik için, giriş verilerinin kullanılabildiği temposunda veya çıktı verilerinin veri kümesi tanımlarının **kullanılabilirlik** bölümü kullanılarak üretilmekte olduğunu belirtebilirsiniz. 

**Kullanılabilirlik** bölümündeki **Sıklık** , zaman birimini belirtir. Sıklık için izin verilen değerler şunlardır: dakika, saat, gün, hafta ve ay. Kullanılabilirlik bölümünde **Interval** özelliği sıklık için bir çarpan belirtir. Örneğin: sıklık gün olarak ayarlanmışsa ve Interval bir çıkış veri kümesi için 1 olarak ayarlandıysa, çıkış verileri günlük olarak üretilir. Sıklığı dakika olarak belirtirseniz, aralığı 15 ' ten az olmayacak şekilde ayarlamanız önerilir. 

Aşağıdaki örnekte, giriş verileri saatlik olarak kullanılabilir ve çıkış verileri saatlik olarak (`"frequency": "Hour", "interval": 1`) üretilir. 

**Giriş veri kümesi:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Çıkış veri kümesi**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Şu anda, **çıktı veri kümesi zamanlamayı**yürütür. Diğer bir deyişle, çıkış veri kümesi için belirtilen zamanlama, çalışma zamanında bir etkinliği çalıştırmak için kullanılır. Bu nedenle, etkinlik herhangi bir çıkış üretmese bile bir çıkış veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz. 

Aşağıdaki işlem hattı tanımında **Zamanlayıcı** özelliği, etkinliğin zamanlamasını belirtmek için kullanılır. Bu özellik isteğe bağlıdır. Şu anda etkinlik için zamanlama, çıkış veri kümesi için belirtilen zamanlamaya uymalıdır.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Bu örnekte etkinlik, işlem hattının başlangıç ve bitiş zamanları arasında saat başı çalışır. Çıkış verileri üç saatlik pencereler için saatlik olarak üretilir (8, 10:00-10:00, 10:00-10:00 ve 10:00-11). 

Bir etkinlik çalıştırması tarafından tüketilen veya üretilen her veri birimi **veri dilimi**olarak adlandırılır. Aşağıdaki diyagramda bir giriş veri kümesi ve bir çıkış veri kümesi olan bir etkinliğin örneği gösterilmektedir: 

![Kullanılabilirlik Zamanlayıcı](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diyagramda giriş ve çıkış veri kümesi için saatlik veri dilimleri gösterilmektedir. Diyagramda, işleme için kullanılabilen üç giriş dilimi gösterilmektedir. 10-11 etkinliği devam ediyor, 10-11 ÖÖ çıkış dilimi üretiliyor. 

JSON veri kümesindeki geçerli dilimle ilişkili zaman aralığına şu değişkenleri kullanarak erişebilirsiniz: [Fedestart](data-factory-functions-variables.md#data-factory-system-variables) ve [Slice Eend](data-factory-functions-variables.md#data-factory-system-variables). Benzer şekilde, WindowStart ve WindowEnd ' i kullanarak bir etkinlik penceresiyle ilişkili zaman aralığına erişebilirsiniz. Etkinliğin zamanlaması, etkinliğin çıkış veri kümesinin zamanlamasıyla eşleşmelidir. Bu nedenle, sınıf, sırasıyla WindowStart ve WindowEnd değerleriyle aynıdır. Bu değişkenler hakkında daha fazla bilgi için bkz. [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md#data-factory-system-variables) makaleleri.  

Bu değişkenleri, etkinliğinizin JSON 'inizdeki farklı amaçlar için kullanabilirsiniz. Örneğin, zaman serisi verilerini temsil eden giriş ve çıkış veri kümelerinden verileri seçmek için bunları kullanabilirsiniz (örneğin: 8-9 ' a kadar). Bu örnek ayrıca, bir etkinliğin çalışması için ilgili verileri seçmek üzere **Windowstart** ve **windowend** ' i kullanır ve uygun **FolderPath**ile blob 'a kopyalar. **FolderPath** , her saat için ayrı bir klasöre sahip olacak şekilde parametrelendirilir.  

Yukarıdaki örnekte, girdi ve çıktı veri kümeleri için belirtilen zamanlama aynı (saatlik). Etkinliğin giriş veri kümesi farklı bir sıklıkta kullanılabilirse, 15 dakikada bir daha varsa, bu çıktı veri kümesini üreten etkinlik, çıkış veri kümesi, etkinlik zamanlamasını hangi şekilde yürüttüğünde bir saat sonra çalışır. Daha fazla bilgi için bkz. [farklı sıklıklarla model veri kümeleri](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Veri kümesi kullanılabilirliği ve ilkeleri
Sıklık ve Aralık özelliklerinin kullanımını, veri kümesi tanımının kullanılabilirlik bölümünde gördünüz. Etkinliğin planlanmasını ve yürütülmesini etkileyen diğer özelliklerden bazıları vardır. 

### <a name="dataset-availability"></a>Veri kümesi kullanılabilirliği 
Aşağıdaki tabloda **kullanılabilirlik** bölümünde kullanabileceğiniz özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| frequency |Veri kümesi dilimi üretiminin zaman birimini belirtir.<br/><br/><b>Desteklenen sıklık</b>: dakika, saat, gün, hafta, ay |Evet |NA |
| interval |Sıklık için bir çarpan belirtir<br/><br/>"Sıklık x Interval", dilimin ne sıklıkta üretildiğini belirler.<br/><br/>Veri kümesinin saatlik olarak dilimlendirilecekliğine ihtiyaç duyuyorsanız <b>Sıklık</b> değerini <b>Hour</b>ve <b>Interval</b> değerini <b>1</b>olarak ayarlarsınız.<br/><br/><b>Note</b>: sıklık değerini dakika olarak belirtirseniz, aralığı 15 ' ten az olmayacak şekilde ayarlamanız önerilir |Evet |NA |
| Biçim |Dilimin aralığın başlangıcında/sonunda üretilmesi gerekip gerekmediğini belirtir.<ul><li>StartOfInterval</li><li>Endofınterval</li></ul><br/><br/>Sıklık değeri month olarak ayarlanmışsa ve Style, Endofınterval olarak ayarlanırsa, dilim ayın son gününde oluşturulur. Stil StartOfInterval olarak ayarlandıysa, dilim ayın ilk gününde oluşturulur.<br/><br/>Sıklık, gün olarak ayarlanır ve stil, Endofınterval olarak ayarlanırsa, dilim günün son saati içinde oluşturulur.<br/><br/>Sıklık değeri Hour olarak ayarlanmışsa ve Style, Endofınterval olarak ayarlanırsa, dilim saatin sonunda üretilir. Örneğin, 1 PM – 2 dönemi için dilim için dilim 2 PM 'de oluşturulur. |Hayır |Endofınterval |
| anchorDateTime |Veri kümesi dilim sınırlarını hesaplamak için Zamanlayıcı tarafından kullanılan mutlak konumu tanımlar. <br/><br/><b>Note</b>: anchordatetime değerinin sıklığından daha ayrıntılı olan tarih bölümleri varsa, daha ayrıntılı parçalar yok sayılır. <br/><br/>Örneğin, <b>Aralık</b> <b>saatlik</b> (sıklık: Hour ve Interval: 1) ve <b>anchordatetime</b> değeri <b>dakika ve saniye</b>içeriyorsa, anchordatetime değerinin <b>dakika ve saniye</b> kısımları yok sayılır. |Hayır |01/01/0001 |
| konumu |Tüm veri kümesi dilimlerinin başlangıcını ve bitişini kaydırılan zaman aralığı. <br/><br/><b>Note</b>: hem anchordatetime hem de kaydır belirtilirse, sonuç Birleşik kaydırmadır. |Hayır |NA |

### <a name="offset-example"></a>fark örneği
Günlük (`"frequency": "Day", "interval": 1`) dilimleri varsayılan olarak 12: UTC saat (gece yarısı) ile başlar. Başlangıç saatinin 6 ' dan UTC zamanı olmasını istiyorsanız, sapmayı aşağıdaki kod parçacığında gösterildiği gibi ayarlayın: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime örneği
Aşağıdaki örnekte, veri kümesi her 23 saatte bir oluşturulur. İlk dilim, `2017-04-19T08:00:00` (UTC saati) olarak ayarlanan anchorDateTime tarafından belirtilen saatte başlar.

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>fark/stil örneği
Aşağıdaki veri kümesi, aylık bir veri kümesidir ve 8:00 (`3.08:00:00`) adresindeki her ay üzerinde üretilir:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Veri kümesi ilkesi
Bir veri kümesinde, bir dilim yürütmesi tarafından oluşturulan verilerin tüketimine hazırlanmadan nasıl doğrulanabileceğini belirten bir doğrulama ilkesi tanımlanmış olabilir. Bu gibi durumlarda, dilim yürütmeyi tamamladıktan sonra çıkış dilimi durumu, **doğrulamanın**bir alt durumu ile **bekliyor** olarak değiştirilir. Dilimler doğrulandıktan sonra dilimin durumu **Ready**olarak değişir. Bir veri dilimi üretildiyse ancak doğrulamayı geçirmediyseniz, bu dilime bağlı aşağı akış dilimleri için etkinlik çalıştırmaları işlenmez. İşlem [hatlarını izleyin ve yönetin](data-factory-monitor-manage-pipelines.md) , Data Factory veri dilimlerinin çeşitli durumlarını içerir.

DataSet tanımındaki **ilke** bölümü, veri kümesi dilimlerinin yerine getirilmesi gereken ölçütü veya koşulu tanımlar. Aşağıdaki tabloda, **ilke** bölümünde kullanabileceğiniz özellikler açıklanmaktadır:

| İlke Adı | Açıklama | Uygulanan | Gerekli | Varsayılan |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Bir **Azure blobundaki** verilerin en düşük boyut gereksinimlerini (megabayt cinsinden) karşıladığını doğrular. |Azure Blob |Hayır |NA |
| minimumRows | Bir **Azure SQL veritabanındaki** veya bir **Azure tablosundaki** verilerin en az sayıda satırı içerdiğini doğrular. |<ul><li>Azure SQL Veritabanı</li><li>Azure Tablosu</li></ul> |Hayır |NA |

#### <a name="examples"></a>Örnekler
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Bu özellikler ve örnekler hakkında daha fazla bilgi için bkz. [veri kümesi oluşturma](data-factory-create-datasets.md) makalesi. 

## <a name="activity-policies"></a>Etkinlik ilkeleri
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

Daha fazla bilgi için bkz. işlem [hatları](data-factory-create-pipelines.md) makalesi. 

## <a name="parallel-processing-of-data-slices"></a>Veri dilimlerinin paralel işlemesi
Ardışık düzen için başlangıç tarihini geçmişte ayarlayabilirsiniz. Bunu yaptığınızda, Data Factory, geçmişteki tüm veri dilimlerini otomatik olarak hesaplar (geri doldurur) ve bunları işlemeye başlar. Örneğin: başlangıç tarihi 2017-04-01 olan bir işlem hattı oluşturursanız ve geçerli tarih 2017-04-10 ' dir. Çıktı veri kümesinin temposunda günlük olması halinde, başlangıç tarihi geçmişte olduğundan Data Factory tüm dilimleri 2017-04-01 ' dan hemen 2017-04-09 ' e işlemeye başlar. Kullanılabilirlik bölümündeki Style özelliğinin değeri varsayılan olarak Endofınterval olduğundan, 2017-04-10 dilimi henüz işlenmedi. En eski dilim ilk olarak önce executionPriorityOrder 'ın varsayılan değeri Oldestvalue olarak işlenir. Style özelliğinin bir açıklaması için bkz. [DataSet AVAILABILITY](#dataset-availability) Section. ExecutionPriorityOrder bölümünün açıklaması için [etkinlik ilkeleri](#activity-policies) bölümüne bakın. 

Arka doldurulmuş veri dilimlerini, JSON etkinliğinin **ilke** bölümünde **eşzamanlılık** özelliğini ayarlayarak paralel olarak işlenecek şekilde yapılandırabilirsiniz. Bu özellik, farklı dilimlerde gerçekleşebileceğini paralel etkinlik yürütmelerinin sayısını belirler. Eşzamanlılık özelliği için varsayılan değer 1 ' dir. Bu nedenle, bir dilim varsayılan olarak bir seferde işlenir. En büyük değer 10 ' dur. Bir işlem hattının büyük bir kullanılabilir veri kümesiyle geçmesi gerektiğinde, daha büyük bir eşzamanlılık değeri olması veri işlemeyi hızlandırır. 

## <a name="rerun-a-failed-data-slice"></a>Başarısız bir veri dilimini yeniden çalıştır
Bir veri dilimini işlerken bir hata oluştuğunda, Azure portal Blade kullanarak bir dilimin işlenmesinin neden başarısız olduğunu bulabilir veya uygulamayı Izleyebilir ve yönetebilir. Ayrıntılar için Azure portal Blade veya [izleme ve yönetim uygulaması](data-factory-monitor-manage-app.md) [kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md) konusuna bakın.

İki etkinliği gösteren aşağıdaki örneği göz önünde bulundurun. Activity1 ve etkinlik 2. Activity1, DataSet1 dilimini kullanır ve son veri kümesinin bir dilimini oluşturmak için Activity2 tarafından giriş olarak tüketilen DataSet2 dilimini üretir.

![Başarısız dilim](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diyagramda, en son üç dilim, DataSet2 için 9-10 ÖÖ Slice üretilirken bir hata olduğunu gösterir. Data Factory, zaman serisi veri kümesinin bağımlılığını otomatik olarak izler. Sonuç olarak, 9-10 ile aşağı akış dilimi için etkinlik çalıştırmasını başlatmaz.

Data Factory izleme ve yönetim araçları, sorunun kök nedenini kolayca bulmak ve sorunu çözmesi için, başarısız olan dilimin tanılama günlüklerinin detayına gitmenizi sağlar. Sorunu düzelttikten sonra, başarısız olan dilimi oluşturmak için etkinliği çalıştırmayı kolayca başlatabilirsiniz. Veri dilimleri için durum geçişlerini yeniden çalıştırma ve anlama hakkında daha fazla bilgi için, bkz. Azure portal Blade veya [izleme ve yönetim uygulaması](data-factory-monitor-manage-app.md) [kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md) .

**DataSet2**için 9-10 ÖÖ dilimini yeniden çalıştırdıktan sonra Data Factory, son veri kümesinde 9-10 ' in bağımlı dilimi için çalıştırmaya başlar.

![Başarısız dilimi yeniden çalıştır](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Bir işlem hattında birden çok etkinlik
Bir işlem hattında birden fazla etkinliğiniz olabilir. Bir işlem hattında birden çok etkinliğiniz varsa ve bir etkinliğin çıkışı başka bir etkinliğin girişi değilse, etkinlikler için giriş veri dilimleri hazırsanız etkinlikler paralel olarak çalışabilir.

Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Etkinlikler aynı ardışık düzende veya farklı işlem hatlarında olabilir. İkinci etkinlik yalnızca ilki başarılı bir şekilde tamamlandığında yürütülür.

Örneğin, bir işlem hattının iki etkinliği olduğu aşağıdaki durumu göz önünde bulundurun:

1. Bu, bir dış giriş veri kümesi D1 gerektiren ve D2 çıkış veri kümesi üreten Activity.
2. D2 veri kümesinden giriş gerektiren ve çıkış veri kümesi D3 oluşturan etkinlik a2.

Bu senaryoda, a1 ve a2 etkinlikleri aynı ardışık düzen içinde bulunur. A1 etkinliği dış veriler kullanılabilir olduğunda ve zamanlanan kullanılabilirlik sıklığına ulaşıldığında çalışır. A2 etkinliği, D2 'den zamanlanan dilimler kullanılabilir hale geldiğinde ve zamanlanan kullanılabilirlik sıklığına ulaşıldığında çalışır. D2 veri kümesindeki dilimlerden birinde bir hata varsa, bu dilim kullanılabilir olana kadar a2 işlemi çalışmaz.

Aynı işlem hattındaki etkinliklerin bulunduğu Diyagram görünümü aşağıdaki diyagram gibi görünür:

![Aynı ardışık düzende zincirleme etkinlikleri](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Daha önce belirtildiği gibi, etkinlikler farklı işlem hatları halinde olabilir. Böyle bir senaryoda, Diyagram görünümü aşağıdaki diyagram gibi görünür:

![Etkinlikleri iki işlem hattı halinde zincirleme](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Bir örnek için ekin sıralı kopyalama bölümüne bakın.

## <a name="model-datasets-with-different-frequencies"></a>Farklı sıklıklarla model veri kümelerini modelleme
Örneklerde, girdi ve çıktı veri kümeleri ve etkinlik zamanlaması penceresi için sıklık aynı. Bazı senaryolar, bir veya daha fazla girişin sıklığından farklı bir sıklıkta çıkış üretme yeteneği gerektirir. Data Factory, bu senaryoların modellemesini destekler.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Örnek 1: her saat kullanılabilir olan giriş verileri için günlük çıkış raporu oluşturma
Azure Blob depolamada her saat için sensörlerden alınan giriş ölçümü verilerinin bulunduğu bir senaryoyu göz önünde bulundurun. [Data Factory Hive etkinliği](data-factory-hive-activity.md)ile gün için Ortalama, maksimum ve minimum gibi istatistiklerle günlük bir toplam rapor oluşturmak istiyorsunuz.

Bu senaryoyu Data Factory ile modelleyebilirsiniz:

**Giriş veri kümesi**

Saatlik giriş dosyaları belirtilen gün için klasöre bırakılır. Girişin kullanılabilirliği **saat** olarak ayarlanır (sıklık: saat, Aralık: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Çıkış veri kümesi**

Gün klasörünün her gününde bir çıkış dosyası oluşturulur. Çıkışın kullanılabilirliği **gün** olarak ayarlanır (sıklık: gün ve Aralık: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Etkinlik: bir ardışık düzende Hive etkinliği**

Hive betiği, aşağıdaki kod parçacığında gösterildiği gibi **Windowstart** değişkenini kullanan parametreler olarak uygun *Tarih saat* bilgilerini alır. Hive betiği, bu değişkeni, gün için doğru klasörden verileri yüklemek ve çıktıyı oluşturmak için toplamayı çalıştırmak için kullanır.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Aşağıdaki diyagramda, bir veri bağımlılığı görünümündeki senaryo gösterilmektedir.

![Veri bağımlılığı](./media/data-factory-scheduling-and-execution/data-dependency.png)

Her güne ait çıkış dilimi, giriş veri kümesinden alınan 24 saatlik dilimlere bağlıdır. Data Factory, üretilmekte olan çıkış dilimiyle aynı döneme denk gelen giriş veri dilimlerini tanımlayarak bu bağımlılıkları otomatik olarak hesaplar. 24 giriş dilimlerinin herhangi biri kullanılamıyorsa Data Factory, günlük etkinlik çalıştırmasına başlamadan önce giriş diliminin hazır olmasını bekler.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Örnek 2: ifadelerle ve Data Factory işlevlerle bağımlılığı belirtme
Başka bir senaryoyu ele alalım. İki giriş veri kümesini işleyen bir Hive etkinliğinizi olduğunu varsayalım. Bunlardan birinin günlük yeni verileri vardır, ancak biri her hafta yeni verileri alır. İki girdi arasında bir JOIN ve her gün bir çıktı üretmek istediğinizi varsayalım.

Çıkış verileri diliminin zaman dilimine göre işlem yapmak için Data Factory doğru giriş dilimlerinin otomatik olarak oluşturulduğu basit yaklaşım çalışmaz.

Her etkinliğin çalışması için Data Factory, haftalık giriş veri kümesi için geçen haftaki veri dilimini kullanması gerektiğini belirtmeniz gerekir. Bu davranışı uygulamak için aşağıdaki kod parçacığında gösterildiği gibi Azure Data Factory işlevlerini kullanırsınız.

**Input1: Azure blobu**

İlk giriş, günlük olarak güncelleştirilmekte olan Azure Blobun.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Azure blobu**

Input2, Azure Blobun haftalık olarak güncelleştiriliyor.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Çıkış: Azure blobu**

Günün her gününde bir çıkış dosyası oluşturulur. Çıkışın kullanılabilirliği **gün** olarak ayarlanır (sıklık: gün, Aralık: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Etkinlik: bir ardışık düzende Hive etkinliği**

Hive etkinliği iki girişi alır ve her gün bir çıkış dilimi üretir. Her günün çıkış dilimini, haftalık giriş için önceki haftaki giriş dilimine bağlı olarak aşağıdaki gibi belirtebilirsiniz.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Data Factory desteklediği işlevlerin ve sistem değişkenlerinin listesi için bkz. [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md) .

## <a name="appendix"></a>Ek

### <a name="example-copy-sequentially"></a>Örnek: sıralı olarak Kopyala
Birden çok kopyalama işlemini ardışık ve sıralı bir şekilde başka bir şekilde çalıştırmak mümkündür. Örneğin, aşağıdaki giriş verileri çıkış veri kümelerine sahip bir ardışık düzen (CopyActivity1 ve CopyActivity2) içinde iki kopyalama etkinliğine sahip olabilirsiniz:   

CopyActivity1

Giriş: veri kümesi. Çıkış: DataSet2.

CopyActivity2

Giriş: DataSet2.  Çıkış: Dataset3.

CopyActivity2 yalnızca CopyActivity1 başarıyla çalıştırılmışsa ve DataSet2 kullanılabiliyorsa çalışır.

Örnek ardışık düzen JSON şu şekildedir:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Örnekte, ilk kopyalama etkinliğinin çıkış veri kümesinin (DataSet2) ikinci etkinlik için giriş olarak belirtildiğine dikkat edin. Bu nedenle, ikinci etkinlik yalnızca ilk etkinliğin çıkış veri kümesi hazırlanışında çalışır.  

Örnekte, CopyActivity2 gibi farklı bir girişi olabilir, ancak CopyActivity2 'e giriş olarak DataSet2 belirtirseniz, bu nedenle CopyActivity1 bitene kadar etkinlik çalıştırılmaz. Örneğin:

CopyActivity1

Giriş: dataSet1. Çıkış: DataSet2.

CopyActivity2

Girişler: Dataset3, DataSet2. Çıkış: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Örnekte ikinci kopyalama etkinliği için iki giriş veri kümesi belirtildiğine dikkat edin. Birden çok giriş belirtildiğinde, verileri kopyalamak için yalnızca ilk giriş veri kümesi kullanılır, ancak diğer veri kümeleri de bağımlılıklar olarak kullanılır. CopyActivity2 yalnızca aşağıdaki koşullar karşılandıktan sonra başlayabilir:

* CopyActivity1 başarıyla tamamlandı ve DataSet2 kullanılabilir. Bu veri kümesi, Dataset4 'e veri kopyalanırken kullanılmaz. Yalnızca CopyActivity2 için bir zamanlama bağımlılığı işlevi görür.   
* Dataset3 kullanılabilir. Bu veri kümesi, hedefe kopyalanmış verileri temsil eder. 
