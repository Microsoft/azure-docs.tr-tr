---
title: Makine tarafından öğrenilen varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017224"
---
# <a name="machine-learned-entity"></a>Makine tarafından öğrenilen varlık 



**Varlık, metin verileri olduğunda iyi bir uyum:**


![Liste varlığı](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Örnek JSON

Adlı bir listesi uygulamanın olduğu varsayalım `Cities`, şehir adları havaalanı (tac Sea), havaalanı kodu (SEA), posta kodu (98101) ve telefon alan kodunu (206) bulunduğu şehir de dahil olmak üzere çeşitleri için izin verme.

|Liste öğesi|Öğe eş anlamlı sözcükler|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Önceki utterance, sözcük içinde `paris` paris öğesine bir parçası olarak eşleştirilir `Cities` varlık listesi. Liste varlığı hem normalleştirilmiş öğenin adı, hem de öğe eş anlamlılar eşleşir.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)


Bu, sorgu dizesinde `verbose=false` ayarlandıysa JSON olur:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Bu, sorgu dizesinde `verbose=true` ayarlandıysa JSON olur:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Varlık listeleme|`Cities`|`paris`|


## <a name="next-steps"></a>Sonraki adımlar

[Liste](reference-entity-list.md) varlığı ve [normal ifade](reference-entity-regular-expression.md) varlığı hakkında bilgi edinin.