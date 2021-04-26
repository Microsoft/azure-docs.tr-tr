---
title: JSON düzleştirme ve kaçış kuralları-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 ' de JSON düzleştirme, kaçış ve dizi işleme hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: b0ce3d2bdcb79fec4c032f44171ef6c91de47b9e
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505442"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON Düzleştirme, Kaçış ve Dizi İşleme

Azure Time Series Insights Gen2 ortamınız, belirli bir adlandırma kuralları kümesinden sonra, normal ve soğuk mağazalarınızın sütunlarını dinamik olarak oluşturur. Bir olay tamamlandığında, JSON yüküne ve özellik adlarına bir dizi kural uygulanır. Bunlar, belirli özel karakterleri kaçış ve iç içe geçmiş JSON nesnelerini düzleştirme içerir. JSON 'nizin şeklinin, olaylarınızın nasıl depolandığını ve sorgulandığını nasıl etkileyeceğini anlayabilmeniz için bu kuralları bilmeniz önemlidir. Kuralların tam listesi için aşağıdaki tabloya bakın. & B örnekleri, bir dizide birden çok zaman serisini verimli bir şekilde toplu olarak nasıl sağlayabileceğinizi gösterir.

> [!IMPORTANT]
>
> * [Zaman SERISI kimliği özelliğini](./how-to-select-tsid.md) ve/veya olay kaynağı [zaman damgası özellikleri 'ni (ies)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)seçmeden önce aşağıdaki kuralları gözden geçirin. TS KIMLIĞINIZ veya zaman Damgalarınız iç içe geçmiş bir nesne içindeyse veya aşağıdaki özel karakterlerden birini içeriyorsa, sağladığınız Özellik adının, giriş kuralları uygulandıktan *sonra* sütun adıyla eşleştiğinden emin olmanız önemlidir. [Aşağıdaki örneğe](concepts-json-flattening-escaping-rules.md#example-b) bakın.

| Kural | Örnek JSON | [Zaman serisi Ifadesi söz dizimi](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet içindeki özellik sütunu adı
|---|---|---|---|
| Azure Time Series Insights Gen2 veri türü, sütun adınızın sonuna "_" olarak eklenir \<dataType\> | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| Olay kaynağı [zaman damgası özelliği](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) , depolama alanında "timestamp" olarak ve UTC 'de depolanan değer olarak Azure Time Series Insights Gen2 kaydedilir. Olay kaynak (ler) zaman damgası özelliğini çözümünüzün ihtiyaçlarını karşılayacak şekilde özelleştirebilirsiniz, ancak sıcak ve soğuk depolamada sütun adı "timestamp" olur. Olay kaynak zaman damgası olmayan diğer tarih saat JSON özellikleri, yukarıdaki kuralda belirtildiği gibi sütun adında "_datetime" ile kaydedilir.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Özel karakterleri içeren JSON özelliği adları. [\ ve ', [' ve '] kullanılarak atlanmalıdır  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| [' Ve '] içinde, tek tırnak ve ters eğik çizgiler için ek kaçış var. Tek bir teklif \ ' olarak yazılır ve şu şekilde bir ters eğik çizgi yazılacak \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| İç içe geçmiş JSON nesneleri, ayırıcı olarak bir noktayla düzleştirilir. 10 düzeyden fazla iç içe geçme destekleniyor. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long``$event['series']['value'].Long`veya`$event.series['value'].Long` |  `series.value_long` |
| Temel türlerin dizileri dinamik tür olarak depolanır |  ```"values": [154, 149, 147]``` | Dinamik türler yalnızca [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 'si aracılığıyla alınabilir | `values_dynamic` |
| Nesneler içeren diziler, nesne içeriğine bağlı olarak iki davranışa sahiptir: TS ID 'ler veya TimeStamp Özelliği (lar) bir dizideki nesneler içindeyse, dizi ilk JSON yükünün birden çok olay üretmesiyle ilgili olarak alınır. Bu, birden çok olayı tek bir JSON yapısında toplu hale getirmenizi sağlar. Dizi eşleri olan üst düzey özellikler her bir untoplaal nesnesiyle kaydedilir. TS KIMLIĞINIZ ve zaman Damgalarınız dizi içinde *değilse* , dinamik tür olarak tümü kaydedilir. | Aşağıda [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)ve [C](concepts-json-flattening-escaping-rules.md#example-c) örneklerine bakın
| Karışık öğeleri içeren diziler düzleştirilmez. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Dinamik türler yalnızca [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 'si aracılığıyla alınabilir | `values_dynamic` |
| 512 karakter JSON Özellik adı sınırlıdır. Ad 512 karakteri aşarsa, 512 olarak kesilir ve ' _< ' diyez kodu ' > ' eklenir. Bunun aynı zamanda, iç içe geçmiş bir nesne yolu belirten nesne düzleştirilerek birleştirilmiş özellik adları için de geçerli olduğunu **unutmayın** . |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Diziler için çift davranışı anlama

Nesnelerin dizileri, verilerinizin nasıl modellendirildiğine bağlı olarak, tümüyle depolanacak veya birden çok olaya bölünecektir. Bu, toplu iş olayları için bir dizi kullanmanıza ve kök nesne düzeyinde tanımlanan yinelenen telemetri özelliklerini kullanmaktan kaçınmanızı sağlar. Daha az Event Hubs veya IoT Hub iletisi gönderilirken toplu işleme avantajlı olabilir.

Ancak, bazı durumlarda, nesne içeren diziler yalnızca diğer değerlerin bağlamında anlamlıdır. Birden çok olay oluşturmak, verileri anlamlı bir şekilde işleyebilir. Bir nesne dizisinin, dinamik bir tür olarak olduğundan emin olmak için aşağıdaki veri modelleme kılavuzunu izleyin ve [örnek C](concepts-json-flattening-escaping-rules.md#example-c) 'ye göz atın

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Nesne dizimin birden çok olay üretmesi durumunda nasıl anlaşılır

Bir veya daha fazla zaman serisi KIMLIĞI özellikleri bir dizideki nesneler içinde iç içe ise *veya* olay kaynağı zaman damgası özelliği iç içe ise, alma altyapısı, birden çok olay oluşturmak için onu böler. TS KIMLIĞINIZ ve/veya zaman Damgalarınız için verdiğiniz Özellik adları yukarıdaki düzleştirme kurallarını izlemelidir ve bu nedenle JSON 'nizin şeklini gösterir. Aşağıdaki örneklere bakın ve [zaman SERISI kimliği özelliği seçme](./how-to-select-tsid.md) kılavuzundaki kılavuza göz atın.

### <a name="example-a"></a>Örnek A

Nesne kökündeki zaman serisi KIMLIĞI ve iç içe zaman damgası \
**Ortam zaman SERISI kimliği:**`"id"`\
**Olay kaynağı zaman damgası:**`"values.time"`\
**JSON yükü:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Parquet dosyasının sonucu:**\
Yukarıdaki yapılandırma ve yük, üç sütun ve dört olay üretecektir

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Örnek B

İç içe yerleştirilmiş bir özelliği olan bileşik zaman serisi KIMLIĞI \
**Ortam zaman SERISI kimliği:** `"plantId"` ' `"telemetry.tagId"`\
**Olay kaynağı zaman damgası:**`"timestamp"`\
**JSON yükü:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Parquet dosyasının sonucu:**\
Yukarıdaki yapılandırma ve yük, dört sütun ve altı olay üretecektir

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30,9918 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Örnek C

Zaman serisi KIMLIĞI ve zaman damgası nesne kökünde \
**Ortam zaman SERISI kimliği:**`"id"`\
**Olay kaynağı zaman damgası:**`"timestamp"`\
**JSON yükü:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Parquet dosyasının sonucu:**\
Yukarıdaki yapılandırma ve yük, üç sütun ve bir olay oluşturacak

| timestamp  | id_string | datapoints_dynamic
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Sonraki adımlar

* Ortamınızın [verimlilik sınırlamalarını](./concepts-streaming-ingress-throughput-limits.md) anlayın
