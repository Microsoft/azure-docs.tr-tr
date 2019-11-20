---
title: Azure Data Factory'de işlem hattı çalıştırma ve tetikleyiciler
description: Bu makalede Azure Data Factory'de istek üzerine veya tetikleyici oluşturarak işlem hattı yürütme konusunda bilgi sağlanır.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: 20a5a9c5513c165cd5add2e97f019a741dfd0b03
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681480"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory'de işlem hattı çalıştırma ve tetikleyiciler
> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmetinin sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-scheduling-and-execution.md)
> * [Geçerli sürüm](concepts-pipeline-execution-triggers.md)

Azure Data Factory'de _işlem hattı çalıştırması_, bir işlem hattı yürütme örneğini tanımlar. Örneğin, 08.00, 09.00 ve 10.00'da çalışan bir işlem hattınız olduğunu kabul edelim. Bu durumda, işlem hattının üç ayrı çalıştırması (işlem hattı çalıştırması) olacaktır. Her işlem hattı çalıştırması benzersiz bir işlem hattı çalıştırma kimliğine sahiptir. Çalıştırma kimliği, belirli bir işlem hattı çalıştırmasını benzersiz bir şekilde tanımlayan GUID’dir.

İşlem hattı çalıştırmaları örneği genelde bağımsız değişkenlerin işlem hatlarında tanımladığınız parametrelere iletilmesiyle oluşturulur. İşlem hattını el ile veya bir _tetikleyici_ kullanarak çalıştırabilirsiniz. Bu makalede her iki işlem hattı çalıştırma yöntemiyle ilgili ayrıntılı bilgiler yer almaktadır.

## <a name="manual-execution-on-demand"></a>El ile yürütme (isteğe bağlı)
Bir işlem hattının el ile yürütülmesine _isteğe bağlı_ yürütme de denir.

Örneğin çalıştırmak istediğiniz **copyPipeline** adlı temel bir işlem hattına sahip olduğunuzu düşünelim. Bu işlem hattı, bir Azure Blob depolama kaynak klasöründen aynı depolama alanı içindeki hedef bir klasöre kopyalama yapan tek bir etkinlik içerir. Aşağıdaki JSON tanımında bu örnek işlem hattı gösterilmektedir:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

JSON tanımında, işlem hattı iki parametre kabul eder: **sourceBlobContainer** ve **sinkBlobContainer**. Çalışma zamanında bu parametrelere değer verirsiniz.

Aşağıdaki yöntemlerden birini kullanarak işlem hattınızı el ile çalıştırabilirsiniz:
- .NET SDK
- Azure PowerShell modülü
- REST API
- Python SDK

### <a name="rest-api"></a>REST API
Aşağıdaki örnek komutta REST API kullanarak işlem hattınızı nasıl el ile çalıştırabileceğiniz gösterilmiştir:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Eksiksiz bir örnek için bkz. [Hızlı başlangıç: REST API kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki örnek komutta Azure PowerShell kullanarak işlem hattınızı nasıl el ile çalıştırabileceğiniz gösterilmiştir:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Parametreleri istek yükünün gövde kısmında iletirsiniz. .NET SDK, Azure PowerShell ve Python SDK’da değerleri, çağrıya bağımsız değişken olarak iletilen bir sözlükte iletirsiniz:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

Yanıt yükü, işlem hattı çalıştırmasının benzersiz kimliği olur:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Eksiksiz bir örnek için bkz. [Hızlı başlangıç: Azure PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>.NET SDK
Aşağıdaki örnek çağrıda .NET SDK kullanarak işlem hattınızı nasıl el ile çalıştırabileceğiniz gösterilmiştir:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Eksiksiz bir örnek için bkz. [Hızlı başlangıç: .NET SDK kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> .NET SDK kullanarak Azure İşlevleri'nden, kendi web hizmetlerinizden Data Factory işlem hatları çağırma gibi işlemler yapabilirsiniz.

<h2 id="triggers">Yürütme tetikleme</h2>
Tetikleyiciler, işlem hattı çalıştırmasını yürütmenin bir diğer yoludur. Tetikleyiciler, bir işlem hattı çalıştırmasının başlatılması gereken zamanı belirleyen işlem birimini temsil eder. Data Factory şu anda üç tetikleyici türünü desteklemektedir:

- Zamanlama tetikleyicisi: Bir işlem hattını duvar saati zamanlamasıyla çağıran bir tetikleyici.

- Atlayan pencere tetikleyicisi: Durumunu koruyarak düzenli bir aralıkta çalışan bir tetikleyici.

- Olay tabanlı tetikleyici: Bir olaya yanıt veren tetikleyicidir.

İşlem hatları ve Tetikleyiciler çoktan çoğa ilişkiye sahiptir (atlayan pencere tetikleyicisi hariç). Birden çok tetikleyici tek bir işlem hattını başlatabilir veya tek bir tetikleyici birden çok işlem hattı başlatabilir. Aşağıdaki tetikleyici tanımında, **pipelines** özelliği belirli bir tetikleyici tarafından tetiklenen işlem hattı listesini ifade eder. Özellik tanımı, işlem hattı parametrelerinin değerlerini içerir.

### <a name="basic-trigger-definition"></a>Temel tetikleyici tanımı

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Zamanlama tetikleyicisi
Zamanlama tetikleyicisi, işlem hatlarını duvar saati zamanlamasıyla çalıştırır. Bu tetikleyici düzenli ve gelişmiş takvim seçeneklerini destekler. Örneğin, “haftalık” veya “Pazartesi saat 17:00 ve Perşembe saat 21:00” gibi aralıkları destekler. Veri kümesi deseni belirsiz olduğundan ve tetikleyici zaman serisi verileri ile zaman serisi dışı veriler arasında ayrım yapmadığından zamanlama tetikleyicisi esnektir.

Zamanlama tetikleyicileri hakkında daha fazla bilgi ve örnekler için bkz. [Zamanlama tetikleyicisi oluşturma](how-to-create-schedule-trigger.md).

## <a name="schedule-trigger-definition"></a>Zamanlama tetikleyicisi tanımı
Bir zamanlama tetikleyicisi oluştururken JSON tanımı kullanarak zamanlamayı ve yinelemeyi belirtirsiniz.

Zamanlama tetikleyicinizin bir işlem hattı çalıştırmasını başlatması için tetikleyici tanımındaki belirli işlem hattının işlem hattı başvurusunu ekleyin. İşlem hatları ve tetikleyiciler çoka çok ilişkisine sahiptir. Birden çok tetikleyici tek bir işlem hattını başlatabilir. Tek bir tetikleyici birden fazla işlem hattını başlatabilir.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> **parameters** özelliği, **pipelines** öğesinin zorunlu bir özelliğidir. İşlem hattınız herhangi bir parametre almasa bile, **parameters** özelliği için boş bir JSON tanımı eklemeniz gerekir.

### <a name="schema-overview"></a>Şemaya genel bakış
Aşağıdaki tabloda bir tetikleyicinin yinelenmesi ve zamanlanmasıyla ilgili ana şema öğelerinin genel bir özeti verilmiştir:

| JSON özelliği | Açıklama |
|:--- |:--- |
| **startTime** | Bir tarih-saat değeri. Temel zamanlamalar için **startTime** özelliğinin değeri ilk oluşum için geçerli olur. Karmaşık zamanlamalar için tetikleyici belirtilen **startTime** değerinden önce başlamaz. |
| **endTime** | Tetikleyicinin bitiş tarihi ve saati. Tetikleyici belirtilen bitiş tarihi ve saatinden sonra yürütülmez. Bu özelliğin değeri geçmişte olamaz. <!-- This property is optional. --> |
| **timeZone** | Saat dilimi. Şu anda yalnızca UTC saat dilimi desteklenmektedir. |
| **recurrence** | Tetikleyici için yinelenme kurallarını belirten bir yinelenme nesnesi. recurrence nesnesi şu öğeleri destekler: **frequency**, **interval**, **endTime**, **count** ve **schedule**. Bir yinelenme nesnesi tanımlanırken **frequency** öğesi gereklidir. Yinelenme nesnesinin diğer öğeleri isteğe bağlıdır. |
| **frequency** | Tetikleyicinin yineleneceği sıklık birimi. "Minute", "hour", "day", "week" ve "month" değerleri desteklenir. |
| **interval** | **frequency** değerinin aralığını gösteren pozitif bir tamsayı. **frequency** değeri tetikleyicinin çalışma sıklığını belirler. Örneğin **interval** değeri 3, **frequency** değeri de "week" ise tetikleyici üç haftada bir yinelenir. |
| **schedule** | Tetikleyicinin yinelenme zamanlaması. **frequency** değeri belirtilen bir tetikleyici, yinelenmesini bir yinelenme zamanlamasına göre değiştirir. **schedule** özelliği, yinelenme için dakika, saat, haftanın günü, ayın günü ve hafta numarası tabanlı değişiklikleri içerir.

### <a name="schedule-trigger-example"></a>Zamanlama tetikleyicisi örneği

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Şema varsayılanları, sınırlar ve örnekler

| JSON özelliği | Tür | Gerekli | Varsayılan değer | Geçerli değerler | Örnek |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | Evet | None | ISO 8601 tarih-saatleri | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | object | Evet | None | Yinelenme nesnesi | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | number | Hayır | 1 | 1-1000 arası | `"interval":10` |
| **endTime** | string | Evet | None | Gelecekteki bir zamanı temsil eden tarih-saat değeri | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | object | Hayır | None | Zamanlama nesnesi | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime özelliği
Aşağıdaki tabloda **startTime** özelliğinin bir tetikleyici çalıştırmasını nasıl denetlediği gösterilmektedir:

| startTime değeri | Zamanlama olmadan yinelenme | Zamanlama ile yinelenme |
|:--- |:--- |:--- |
| **Geçmişe ait bir başlangıç zamanı** | Başlangıç zamanından sonraki ilk gelecek yürütme zamanını hesaplar ve bu zamanda çalıştırır.<br /><br />Son yürütme zamanına göre hesaplanan sonraki yürütmeleri çalıştırır.<br /><br />Bu tablonun altındaki örneğe bakın. | Tetikleyici belirtilen başlangıç zamanından _önce_ başlamaz. İlk yinelenme, başlangıç zamanından hesaplanan zamanlamaya göre gerçekleştirilir.<br /><br />Sonraki yürütmeleri yinelenme zamanlamasına göre çalıştırır. |
| **Geleceğe ait veya şimdiki zamana denk gelen başlangıç zamanı** | Belirtilen başlangıç zamanında bir kez çalışır.<br /><br />Son yürütme zamanına göre hesaplanan sonraki yürütmeleri çalıştırır. | Tetikleyici belirtilen başlangıç zamanından _önce_ başlamaz. İlk yinelenme, başlangıç zamanından hesaplanan zamanlamaya göre gerçekleştirilir.<br /><br />Sonraki yürütmeleri yinelenme zamanlamasına göre çalıştırır. |

Başlangıç zamanı geçmişte olduğunda ve bir yinelenme olmasına rağmen zamanlama olmadığında neler olacağını gösteren bir örneğe bakalım. Geçerli zamanın 2017-04-08 13:00, başlangıç zamanının 2017-04-07 14:00 ve yinelemenin iki günde bir olduğunu varsayalım. ( **Yinelenme** değeri, **Sıklık** özelliği "Day" ve **Interval** özelliği 2 olarak ayarlanarak tanımlanır.) **StartTime** değerinin geçmişte olduğuna ve geçerli zamandan önce gerçekleşdiğine dikkat edin.

Bu koşullar altında ilk yürütme 14:00 2017-04-09 ' dir. Zamanlayıcı altyapısı çalıştırma yinelenmelerini başlangıç zamanından itibaren hesaplar. Geçmişteki örnekler dikkate alınmaz. Altyapı gelecekte gerçekleşen bir sonraki örneği kullanır. Bu senaryoda başlangıç zamanı 07-04-2017, saat 14:00 olur. Sonraki örnek bu zamandan iki gün sonrası olan 09-04-2017 saat 14:00'a denk gelir.

**startTime** ister 05-04-2017 14:00 isterse 01-04-2017 14:00 olsun, ilk yürütme zamanı değişmez. İlk yürütme sonrasındaki yürütmeler zamanlama kullanılarak hesaplanır. Dolayısıyla, izleyen yürütmeler 11-04-2017 14:00, sonra 13-04-2017 14:00, sonra 15-04-2017 14:00, vb. olur.

Son olarak, bir tetikleyicinin zamanlamasında saatler veya dakikalar ayarlanmazsa varsayılan olarak ilk yürütmenin saat veya dakikaları kullanılır.

### <a name="schedule-property"></a>schedule özelliği
Tetikleyici yürütmelerinin sayısını **sınırlandırmak** için *schedule* özelliğini kullanabilirsiniz. Örneğin, aylık sıklığa sahip bir tetikleyici 31. günde çalışacak şekilde zamanlanırsa, tetikleyici yalnızca otuz birinci günü olan aylarda çalışır.

Ayrıca, tetikleyici yürütmelerinin sayısını **artırmak** için de *schedule* kullanabilirsiniz. Örneğin, aylık sıklığa sahip olan ve ayın 1. ve 2. günlerinde çalışacak şekilde zamanlanan bir tetikleyici, ayda bir kez çalışmak yerine ayın birinci ve ikinci günlerinde çalışır.

Birden fazla **schedule** öğesi belirtilmişse değerlendirme sırası en büyük schedule ayarından en küçüğe doğru (hafta numarası, ayın günü, haftanın günü, saat, dakika) olur.

Aşağıdaki tabloda **schedule** öğeleri ayrıntılı bir şekilde açıklanmıştır:

| JSON öğesi | Açıklama | Geçerli değerler |
|:--- |:--- |:--- |
| **minutes** | Tetikleyicinin çalıştığı dakika değeri. |- Tamsayı<br />- Tamsayı dizisi|
| **hours** | Tetikleyicinin çalıştığı saat değeri. |- Tamsayı<br />- Tamsayı dizisi|
| **weekDays** | Tetikleyicinin çalıştığı hafta günleri. Bu değer yalnızca haftalık bir sıklıkta belirtilebilir.|<br />- Pazartesi<br />- Salı<br />- Çarşamba<br />- Perşembe<br />- Cuma<br />- Cumartesi<br />- Pazar<br />- Gün değerleri dizisi (en fazla dizi boyutu 7’dir)<br /><br />Gün değerleri büyük/küçük harfe duyarlı değildir|
| **monthlyOccurrences** | Tetikleyicinin çalıştığı ay günleri. Bu değer yalnızca aylık bir sıklık ile belirtilebilir. |- **Monthlyoccurrence** nesnelerinin dizisi: `{ "day": day, "occurrence": occurrence }`<br />- **day** özniteliği, tetikleyicinin çalıştığı gündür. Örneğin, **day** değeri **olan bir**monthlyOccurrences`{Sunday}` özelliği, ayın her Pazar günü anlamına gelir. **day** özniteliği gereklidir.<br />- **occurrence** özniteliği, ay içinde belirtilen **day** değerinin gerçekleşmesidir. Örneğin, **day** ve **occurrence** değerleri **olan bir**monthlyOccurrences`{Sunday, -1}` özelliği, ayın son Pazar günü anlamına gelir. **occurrence** özniteliği isteğe bağlıdır.|
| **monthDays** | Tetikleyicinin çalıştığı ay günü. Bu değer yalnızca aylık bir sıklık ile belirtilebilir. |- <= -1 ve >= -31 koşullarına uyan herhangi bir değer<br />- >= 1 ve <= 31 koşullarına uyan herhangi bir değer<br />- Değer dizisi|

## <a name="tumbling-window-trigger"></a>Atlayan pencere tetikleyicisi
Atlayan pencere tetikleyicileri, durumu korurken belirtilen bir başlangıç zamanından itibaren periyodik bir zaman aralığında başlatılan bir tetikleyici türüdür. Atlayan pencereler sabit boyutlu, çakışmayan ve bitişik zaman aralıkları dizisidir.

Atlayan pencere tetikleyicileri hakkında daha fazla bilgi ve örnekler için bkz. [Atlayan pencere tetikleyicisi oluşturma](how-to-create-tumbling-window-trigger.md).

## <a name="event-based-trigger"></a>Olay tabanlı tetikleyici

Olay tabanlı tetikleyici, bir dosyanın yanıtı veya Azure Blob depolama alanında bir dosyanın silinmesi gibi bir olaya yanıt olarak işlem hatlarını çalıştırır.

Olay tabanlı tetikleyiciler hakkında daha fazla bilgi için bkz. [Bir olaya yanıt olarak işlem hattı çalıştıran bir tetikleyici oluşturma](how-to-create-event-trigger.md).

## <a name="examples-of-trigger-recurrence-schedules"></a>Tetikleyici yineleme zamanlaması örnekleri
Bu bölümde yineleme zamanlaması örnekleri sağlanır. **schedule** nesnesine ve onun öğelerine odaklanılmıştır.

Örneklerde **interval** değerinin 1 olduğu ve zamanlama tanımına göre **frequency** değerinin doğru olduğu varsayılmıştır. Örneğin, hem "day" şeklinde bir **frequency** değeriniz hem de **schedule** nesnesinde bir **monthDays** değişikliğiniz olamaz. Bu tür kısıtlamalar önceki bölümde verilen tabloda açıklanmıştır.

| Örnek | Açıklama |
|:--- |:--- |
| `{"hours":[5]}` | Her gün 05.00'te çalıştır. |
| `{"minutes":[15], "hours":[5]}` | Her gün 05.15'te çalıştır. |
| `{"minutes":[15], "hours":[5,17]}` | Her gün 05.15 ve 17.15’te çalıştır. |
| `{"minutes":[15,45], "hours":[5,17]}` | Her gün 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |
| `{"minutes":[0,15,30,45]}` | 15 dakikada bir çalıştır. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Saatte bir çalıştır.<br /><br />Bu tetikleyici saatte bir çalışır. **startTime** değeri belirtilirse dakikalar bu değer tarafından denetlenir. Değer belirtilmezse, dakikalar oluşturma zamanı tarafından denetlenir. Örneğin başlangıç zamanı veya oluşturma zamanı (hangisi geçerliyse) 12:25 ise tetikleyici 00.25, 01.25, 02.25, ... ve 23.25 saatlerinde çalışır.<br /><br />Bu zamanlama, **frequency** değeri "hour", **interval** değeri 1 olan ve **schedule** olmayan bir tetikleyiciye sahip olmakla eşdeğerdir. Bu zamanlama farklı **frequency** ve **interval** değerleriyle kullanılarak başka tetikleyiciler oluşturulabilir. Örneğin, **frequency** değeri "day" olduğunda her gün çalışan zamanlama, **frequency** değeri "month" olduğunda yalnızca ayda bir kez çalışır. |
| `{"minutes":[0]}` | Her saat başı çalıştır.<br /><br />Bu tetikleyici 12.00, 01.00, 02.00 gibi bir saatte başlayarak saat başı çalışır.<br /><br />Bu zamanlama, **frequency** değeri "hour" olup **startTime** değeri sıfır dakika olan ve **schedule** değeri olmayıp **frequency** değeri "day" olan bir tetikleyiciye eşdeğerdir. **frequency** değeri "week" veya "month" olursa zamanlama yalnızca haftada bir gün veya ayda bir gün çalışır. |
| `{"minutes":[15]}` | Her saat başını 15 dakika geçe çalıştır.<br /><br />Bu tetikleyici, 00.15, 01.15, 02.15, vb. bir saatte başlayıp 23.15’te bitecek şekilde her saat başını 15 dakika geçe çalışır. |
| `{"hours":[17], "weekDays":["saturday"]}` | Her hafta Cumartesi günleri saat 17.00'de çalıştır. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 17.00'de çalıştır. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 17.15 ve 17.45'te çalıştır. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Haftanın her günü 15 dakikada bir çalıştır. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Haftanın her günü 09.00 ile 16.45 arasında 15 dakikada bir çalıştır. |
| `{"weekDays":["tuesday", "thursday"]}` | Salı ve Perşembe günleri belirtilen başlangıç saatinde çalıştır. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Her ayın yirmi sekizinci gününde saat 06.00'da çalıştır (**frequency** değerinin "month" olduğu kabul edilir). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Ayın son günü saat 06.00'da çalıştır.<br /><br />Bir tetikleyiciyi ayın son gününde çalıştırmak için 28, 29, 30 veya 31 yerine -1 değerini kullanın. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Her ayın ilk ve son günü saat 06.00'da çalıştır. |
| `{monthDays":[1,14]}` | Her ayın ilk ve on dördüncü gününde belirtilen başlangıç zamanında çalıştır. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Her ayın ilk Cuma günü saat 05.00'te çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Her ayın ilk Cuma gününde belirtilen başlangıç zamanında çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Her ay, ayın sondan üçüncü Cuma gününde belirtilen zamanda çalıştır. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Her ayın ilk ve son Cuma günü saat 05.15'te çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Her ayın ilk ve son Cuma günü belirtilen başlangıç zamanında çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Her ayın beşinci Cuma günü belirtilen başlangıç zamanında çalıştır.<br /><br />Ayın beşinci Cuma günü yoksa, işlem hattı çalıştırılmaz. Tetikleyiciyi ayın son Cuma gününde çalıştırmak istiyorsanız **occurrence** değeri için 5 yerine -1 yazın. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Ayın son Cuma günü 15 dakikada bir çalıştır. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Her ayın üçüncü Çarşamba günü 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |

## <a name="trigger-type-comparison"></a>Tetikleyici türü karşılaştırması
Hem atlayan pencere tetikleyicisi hem de zamanlama tetikleyicisi zaman sinyalleriyle çalışıyor. Bunların birbirinden farkı nedir?

Aşağıdaki tabloda atlayan pencere tetikleyicisi ile zamanlama tetikleyicisinin karşılaştırması sağlanmıştır:

|  | Atlayan pencere tetikleyicisi | Zamanlama tetikleyicisi |
|:--- |:--- |:--- |
| **Doldurma senaryoları** | Destekleniyor. Geçmişteki aralıklar için işlem hattı çalıştırmaları zamanlanabilir. | Desteklenmiyor. İşlem hattı çalıştırmaları yalnızca geçerli zamandan sonraki ve gelecekteki zaman dönemlerinde yürütülebilir. |
| **Güvenilirlik** | %100 güvenilirlik. Belirtilen bir başlangıç tarihinden itibaren boşluk olmayacak şekilde tüm aralıklar için işlem hattı çalıştırması zamanlanabilir. | Daha az güvenilir. |
| **Yeniden deneme özelliği** | Destekleniyor. Başarısız işlem hattı çalıştırmaları varsayılan olarak 0 yeniden deneme ilkesine veya kullanıcı tarafından tetikleyici tanımında belirtilen bir ilkeye sahiptir. İşlem hattı çalıştırmaları eş zamanlılık/sunucu/azaltma sınırları (yani 400: Kullanıcı Hatası, 429: Çok fazla istek var ve 500: İç Sunucu hatası durum kodları) nedeniyle başarısız olursa otomatik olarak yeniden denenir. | Desteklenmiyor. |
| **Eşzamanlılık** | Destekleniyor. Kullanıcılar tetikleyicinin eş zamanlılık sınırlarını açıkça ayarlayabilir. 1 ile 50 arasında eş zamanlı tetikleyici işlem hattı çalıştırmasına izin verir. | Desteklenmiyor. |
| **Sistem değişkenleri** | **WindowStart** ve **WindowEnd** sistem değişkenlerinin kullanımını destekler. Kullanıcılar, tetikleyici tanımında tetikleyici sistem değişkenleri olarak `triggerOutputs().windowStartTime` ve `triggerOutputs().windowEndTime` değişkenine erişebilir. Değerler sırasıyla aralık başlangıç zamanı ve aralık bitiş zamanı olarak kullanılır. Örneğin, saat başı çalışan bir atlayan pencere tetikleyicisi için 01.00 ile 02.00 arası aralığın tanımı `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` ve `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z` şeklinde olur. | Desteklenmiyor. |
| **İşlem hattı-tetikleyici ilişkisi** | Bire bir ilişkileri destekler. Yalnızca bir işlem hattı tetiklenebilir. | Çoka çok ilişkileri destekler. Birden çok tetikleyici tek bir işlem hattını başlatabilir. Tek bir tetikleyici birden fazla işlem hattını başlatabilir. |

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki öğreticilere bakın:

- [Hızlı başlangıç: .NET SDK kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md)
- [Zamanlama tetikleyicisi oluşturma](how-to-create-schedule-trigger.md)
- [Atlayan pencere tetikleyicisi oluşturma](how-to-create-tumbling-window-trigger.md)
