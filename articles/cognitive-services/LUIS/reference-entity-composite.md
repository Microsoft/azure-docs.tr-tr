---
title: Bileşik varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: Bileşik bir varlık, önceden oluşturulmuş varlıklar, basit, normal ifade ve liste varlıkları gibi diğer varlıklardan oluşur. Ayrı varlıklar bir bütün varlığı oluşturur.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3d936555a4d9a9c80718a24ba892c762f5a8354a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97608401"
---
# <a name="composite-entity"></a>Bileşik varlık

Bileşik bir varlık, önceden oluşturulmuş varlıklar, basit, normal ifade ve liste varlıkları gibi diğer varlıklardan oluşur. Ayrı varlıklar bir bütün varlığı oluşturur.

> [!CAUTION]
> Bu varlık **kullanım dışıdır**. Lütfen [makine öğrenimi varlığına](reference-entity-machine-learned-entity.md)geçiş yapın.

**Bu varlık, veriler şu durumlarda iyi bir uyum:**

* Birbirleriyle ilişkilidir.
* Konuşma bağlamında birbiriyle ilişkilidir.
* Çeşitli varlık türlerini kullanın.
* İstemci uygulama tarafından bir bilgi birimi olarak gruplanıp işlenmelidir.
* Makine öğrenimi gerektiren çeşitli Kullanıcı yelpazimine sahiptir.

![Bileşik varlık](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Örnek JSON

`number`Aşağıdaki utterance ile önceden oluşturulmuş ve bileşik bir varlık göz önünde bulundurun `Location::ToLocation` :

`book 2 tickets to cairo`

Bu `2` sayı, ve `cairo` , tolocation 'ın, varlıkların herhangi bir parçası olmayan aralarında sözcükler içerdiğine dikkat edin. [Luo](luis-reference-regions.md) Web sitesinde etiketli bir şekilde kullanılan yeşil alt çizgi, bileşik bir varlığı gösterir.

![Bileşik varlık](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

Bileşik varlıklar bir `compositeEntities` dizide döndürülür ve Birleşik içindeki tüm varlıklar dizide de döndürülür `entities` :

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

Bu, `verbose=false` sorgu dizesinde AYARLANDıYSA JSON 'dir:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Bu, `verbose=true` sorgu dizesinde AYARLANDıYSA JSON 'dir:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Önceden oluşturulmuş varlık numarası|"yerleşik. numara"|iki|
|Önceden oluşturulmuş varlık-GeographyV2|"Location:: ToLocation"|Cairo|

## <a name="next-steps"></a>Sonraki adımlar

Varlıklar hakkında daha fazla bilgi edinin:

* [Kavramlar](luis-concept-entity-types.md)
* [Oluşturma](luis-how-to-add-entities.md)
