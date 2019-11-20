---
title: Basit varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: Basit bir varlık, makine tarafından öğrenilen bağlamdaki tek bir kavramı açıklar. Sonuçları geliştirmek için basit bir varlık kullanırken bir tümcecik listesi ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671511"
---
# <a name="simple-entity"></a>Basit varlık 

Basit bir varlık, tek bir kavramı açıklayan ve makine tarafından öğrenilen bağlamdan öğrenilmiş genel bir varlıktır. Basit varlıklar genellikle şirket adları, ürün adları veya diğer ad kategorileri gibi adlar olduğundan, kullanılan adların sinyalini artırmak için basit bir varlık kullanırken bir [tümcecik listesi](luis-concept-feature.md) ekleyin. 

**Varlık, şu durumlarda iyi bir uyum:**

* Veriler sürekli olarak biçimlendirilmemiştir ancak aynı şeyi gösterir. 

![Basit varlık](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Örnek JSON

`Bob Jones wants 3 meatball pho`

Önceki deymede, `Bob Jones` basit bir `Customer` varlık olarak etiketlendi.

Uç noktadan döndürülen veriler varlık adını, utterance 'teki bulunan metni, bulunan metnin konumunu ve puanı içerir:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

Bu, sorgu dizesinde `verbose=false` ayarlandıysa JSON olur:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Veri nesnesi|Varlık adı|Değer|
|--|--|--|
|Basit varlık|`Customer`|`bob jones`|

## <a name="next-steps"></a>Sonraki adımlar

Bu [öğreticide](luis-quickstart-primary-and-secondary-data.md), **basit varlığı**kullanarak bir noktadan itibaren iş işi adının makineye öğrenilen verilerini ayıklayın. Ayıklama doğruluğunu artırmak için, basit varlığa özel terimlerin [tümcecik listesini](luis-concept-feature.md) ekleyin.