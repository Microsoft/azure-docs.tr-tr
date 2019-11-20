---
title: Data Factory Işlevleri ve sistem değişkenleri
description: Azure Data Factory işlevlerinin ve sistem değişkenlerinin bir listesini sağlar
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667661"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory Işlevleri ve sistem değişkenleri
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory sistem değişkenleri](../control-flow-system-variables.md).

Bu makalede, Azure Data Factory tarafından desteklenen işlevler ve değişkenler hakkında bilgi sağlanır.

## <a name="data-factory-system-variables"></a>Data Factory sistem değişkenleri

| Değişken adı | Açıklama | Nesne kapsamı | JSON kapsamı ve kullanım örnekleri |
| --- | --- | --- | --- |
| WindowStart |Geçerli etkinlik çalıştırma penceresi için zaman aralığı başlangıcı |etkinlik |<ol><li>Veri seçimi sorgularını belirtin. [Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde başvurulan bağlayıcı makalelerine bakın.</li> |
| WindowEnd |Geçerli etkinlik çalıştırma penceresi için zaman aralığı sonu |etkinlik |WindowStart ile aynı. |
| Slicestart 'taki |Üretilen veri dilimi için zaman aralığı başlangıcı |etkinlik<br/>Veri kümesi |<ol><li>[Azure Blob](data-factory-azure-blob-connector.md) ve [dosya sistemi veri kümeleri](data-factory-onprem-file-system-connector.md)ile çalışırken dinamik klasör yollarını ve dosya adlarını belirtin.</li><li>Etkinlik girişleri koleksiyonundaki Data Factory işlevleriyle giriş bağımlılıklarını belirtin.</li></ol> |
| Dilimleyicebitişi |Geçerli veri dilimi için zaman aralığı sonu. |etkinlik<br/>Veri kümesi |Dilimleyicestart ile aynı. |

> [!NOTE]
> Şu anda Data Factory, etkinlikte belirtilen zamanlamanın, çıkış veri kümesinin kullanılabilirliği olarak belirtilen zamanlama ile tam olarak eşleştiğinden emin olmanızı gerektirir. Bu nedenle, WindowStart, WindowEnd ve Slice Estart ve Slice Eend her zaman aynı zaman dilimine ve tek bir çıkış dilimine eşlenir.
> 

### <a name="example-for-using-a-system-variable"></a>Bir sistem değişkeni kullanma örneği
Aşağıdaki örnekte, **Monthestart** 'ın Year, month, Day ve Time, **FolderPath** ve **filename** özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory işlevleri
Veri fabrikasındaki işlevleri, sistem değişkenleriyle birlikte aşağıdaki amaçlarla kullanabilirsiniz:

1. Veri seçimi sorgularını belirtme ( [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinin başvurduğu bağlayıcı makalelerine bakın.
   
   Data Factory işlevini çağırma söz dizimi: **$$\<işlevi** , veri seçim sorguları ve etkinlik ve veri kümelerinde diğer özellikler için >.  
2. Etkinlik girişleri koleksiyonundaki Data Factory işlevleriyle giriş bağımlılıklarını belirtme.
   
    $ $ giriş bağımlılığı ifadelerini belirtmek için gerekli değildir.     

Aşağıdaki örnekte, bir JSON dosyasındaki **Sqlreaderquery** özelliği `Text.Format` işlevi tarafından döndürülen bir değere atanır. Bu örnek ayrıca, etkinlik çalıştırma penceresinin başlangıç saatini temsil eden **Windowstart**adlı bir sistem değişkeni kullanır.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Kullanabileceğiniz farklı biçimlendirme seçeneklerini açıklayan [özel tarih ve saat biçim dizeleri](https://msdn.microsoft.com/library/8kb3ddd4.aspx) konusuna bakın (örneğin: ay vs. yyyy). 

### <a name="functions"></a>İşlevler
Aşağıdaki tablolar Azure Data Factory içindeki tüm işlevleri listeler:

| Kategori | İşlev | Parametreler | Açıklama |
| --- | --- | --- | --- |
| Zaman |AddHours (X, Y) |X: DateTime <br/><br/>Y: int |Verilen saat X 'e Y saat ekler. <br/><br/>Örnek: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Zaman |AddMinutes (X, Y) |X: DateTime <br/><br/>Y: int |X 'e Y dakika ekler.<br/><br/>Örnek: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Zaman |Saat başı (X) |X: DateTime |X 'in saat bileşeniyle temsil edilen saatin başlangıç saatini alır. <br/><br/>Örnek: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Tarih |AddDays (X, Y) |X: DateTime<br/><br/>Y: int |X 'e Y gün ekler. <br/><br/>Örnek: 9/15/2013 12:00:00 PM + 2 gün = 9/17/2013 12:00:00 PM.<br/><br/>Y 'yi negatif bir sayı olarak belirterek gün sayısı çıkarabilirsiniz.<br/><br/>Örnek: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Tarih |Addaylar (X, Y) |X: DateTime<br/><br/>Y: int |X 'e Y ayları ekler.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Y 'yi negatif bir sayı olarak belirterek ayı de çıkarabilirsiniz.<br/><br/>Örnek: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Tarih |Addçeyrekler (X, Y) |X: DateTime <br/><br/>Y: int |X ' e * 3 ay ekler.<br/><br/>Örnek: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Tarih |Addhafta (X, Y) |X: DateTime<br/><br/>Y: int |X için Y * 7 gün ekler<br/><br/>Örnek: 9/15/2013 12:00:00 PM + 1 hafta = 9/22/2013 12:00:00 PM<br/><br/>Y 'yi negatif bir sayı olarak belirterek haftalar çıkarabilirsiniz.<br/><br/>Örnek: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Tarih |Addyıllar (X, Y) |X: DateTime<br/><br/>Y: int |X 'e Y yılları ekler.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Y 'yi negatif bir sayı olarak belirterek yıl çıkarabilirsiniz.<br/><br/>Örnek: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Tarih |Gün (X) |X: DateTime |X ' in gün bileşenini alır.<br/><br/>Örnek: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Tarih |DayOfWeek (X) |X: DateTime |X 'in haftanın günü bileşenini alır.<br/><br/>Örnek: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Tarih |DayOfYear (X) |X: DateTime |X 'in yıl bileşeni ile temsil edilen yılın gününü alır.<br/><br/>Örnekler:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Tarih |DaysInMonth (X) |X: DateTime |X parametresinin month bileşeniyle temsil edilen aydaki günleri alır.<br/><br/>Örnek: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Tarih |EndOfDay (X) |X: DateTime |X 'in günün sonunu (gün bileşeni) temsil eden tarih ve saati alır.<br/><br/>Örnek: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Tarih |EndOfMonth (X) |X: DateTime |X parametresinin aya göre temsil edilen ayın sonunu alır. <br/><br/>Örnek: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (Eylül ayının sonunu temsil eden tarih saat) |
| Tarih |StartOfDay (X) |X: DateTime |X parametresinin gün bileşeni tarafından temsil edilen günün başlangıcını alır.<br/><br/>Örnek: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |(X) kaynağından |X: dize |X dizesini bir tarih saatine ayrıştırır. |
| DateTime |Ticks (X) |X: DateTime |X parametresinin ticks özelliğini alır. Tek bir değer 100 nanosaniye değerine eşittir. Bu özelliğin değeri 12:00:00 gece yarısından beri geçen saat sayısını temsil eder, 1 Ocak 0001. |
| Metin |Biçim (X) |X: String değişkeni |Metni biçimlendirir (`\\'` birleşimini kaçış `'` karakteriyle kullanın).|

> [!IMPORTANT]
> Bir işlevi başka bir işlev içinde kullanırken, iç işlev için **$$** ön eki kullanmanız gerekmez. Örneğin: $ $Text. Format (' PartitionKey EQ \\' my_pkey_filter_value\\' ve RowKey Ge \\' {0: YYYY-AA-GG SS: DD: ss}\\' ', Time. AddHours (Bölüm Estart,-6)). Bu örnekte, **$$** ön eki **zaman. AddHours** işlevi için kullanılmadığını unutmayın. 

#### <a name="example"></a>Örnek
Aşağıdaki örnekte, Hive etkinliğinin giriş ve çıkış parametreleri `Text.Format` işlevi ve Festart sistem değişkeni kullanılarak belirlenir. 

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

### <a name="example-2"></a>Örnek 2

Aşağıdaki örnekte, saklı yordam etkinliğinin tarih saat parametresi metin kullanılarak belirlenir. Format işlevi ve Daestart değişkeni. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Örnek 3
Aşağıdaki örnekte gösterildiği gibi önceki gündeki verileri bir önceki günden okumak için AddDays işlevini kullanın: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Kullanabileceğiniz farklı biçimlendirme seçeneklerini açıklayan [özel tarih ve saat biçim dizeleri](https://msdn.microsoft.com/library/8kb3ddd4.aspx) konusuna bakın (örneğin: yy vs. yyyy). 

