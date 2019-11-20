---
title: Sıcaklık önceden oluşturulmuş varlık-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içindeki sıcaklık önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: f2ea08694419caaaf54e4fed45c7c1589be2473d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499514"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için sıcaklık önceden oluşturulmuş varlık
Sıcaklık çeşitli sıcaklık türlerini ayıklar. Bu varlık zaten eğitiltiğinden, uygulamanın sıcaklığını içeren örnek bir değer eklemeniz gerekmez. Sıcaklık varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir. 

## <a name="types-of-temperature"></a>Sıcaklık türleri
Sıcaklık, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub deposundan yönetiliyor

## <a name="resolution-for-prebuilt-temperature-entity"></a>Önceden oluşturulmuş sıcaklık varlığına yönelik çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`set the temperature to 30 degrees`


#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 yanıtı](#tab/V2)

Aşağıdaki örnek, **yerleşik. sıcaklık** varlığının çözünürlüğünü gösterir.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Yüzde](luis-reference-prebuilt-percentage.md), [sayı](luis-reference-prebuilt-number.md)ve [yaş](luis-reference-prebuilt-age.md) varlıkları hakkında bilgi edinin. 
