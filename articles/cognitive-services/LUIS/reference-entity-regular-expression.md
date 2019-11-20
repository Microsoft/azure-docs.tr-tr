---
title: Normal ifade varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: Normal ifade, ham söylenişi metin için en iyisidir. Büyük/küçük harf durumunu yoksayar ve kültürel türevini yoksayar.  Normal ifade eşleştirme, belirteç düzeyi değil, karakter düzeyinde yazım denetimi değişiklikleri yapıldıktan sonra uygulanır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695210"
---
# <a name="regular-expression-entity"></a>Normal ifade varlığı 

Normal ifade varlığı, sağladığınız normal ifade düzenine göre bir varlığı ayıklar.

Normal ifade, ham söylenişi metin için en iyisidir. Büyük/küçük harf durumunu yoksayar ve kültürel türevini yoksayar.  Normal ifade eşleştirme, belirteç düzeyi değil, karakter düzeyinde yazım denetimi değişiklikleri yapıldıktan sonra uygulanır. Normal ifade çok karmaşık ise (birçok köşeli ayraç kullanma gibi), bu ifadeyi modele ekleyemezsiniz. [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) kitaplığının tümünü değil, bölümünü kullanır. 

**Varlık, şu durumlarda iyi bir uyum:**

* Veriler tutarlı olarak tutarlı bir çeşitlerle biçimlendirilir.
* Normal ifadede 2 ' den fazla iç içe geçme düzeyi gerekmez. 

![Normal ifade varlığı](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Kullanım konuları

Normal ifadeler, eşleştirmeye beklediğinizden daha fazla eşleşemez. Buna bir örnek `one` ve `two` gibi sayısal bir kelime eşleştirmedir. Örnek, diğer sayılarla birlikte `one` sayısıyla eşleşen aşağıdaki Regex örneğidir:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Bu Regex ifadesi Ayrıca, `phone` gibi bu sayılarla biten sözcüklerle eşleşir. Bu gibi sorunları gidermek için, Regex eşleşmelerin, hesap sözcük sınırlarına uyduğundan emin olun. Bu örnek için sözcük sınırları kullanmanın Regex ifadesi aşağıdaki Regex içinde kullanılır:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Örnek JSON

@No__t-0 kullanırken, normal ifade varlık tanımı olarak, aşağıdaki JSON yanıtı sorgu için döndürülen normal ifade varlıklarını içeren bir örnektir:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)


Sorgu dizesinde `verbose=false` ayarlanmışsa bu JSON olur:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Sorgu dizesinde `verbose=true` ayarlanmışsa bu JSON olur:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

## <a name="next-steps"></a>Sonraki adımlar

Bu [öğreticide](luis-quickstart-intents-regex-entity.md), **normal ifade** varlığını kullanarak, düzenli olarak biçimlendirilen verileri bir noktadan ayıklamak için bir uygulama oluşturun.