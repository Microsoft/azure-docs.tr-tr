---
title: Yaş önceden oluşturulmuş varlık-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içindeki yaş önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 2a49cf406dea045302f84e95318eca1557fc3e22
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465096"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için yaş önceden oluşturulmuş varlık
Önceden oluşturulan yaş varlığı, Yaş değerini hem sayısal hem de günler, hafta, ay ve yıl bakımından yakalar. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını yaş içeren örnek bir değer eklemeniz gerekmez. Yaş varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir. 

## <a name="types-of-age"></a>Yaş türleri
Yaş [metni](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub deposundan yaş yönetiliyor

## <a name="resolution-for-prebuilt-age-entity"></a>Önceden oluşturulmuş yaş varlığına yönelik çözüm



#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 yanıtı](#tab/V2)

Aşağıdaki örnek, **yerleşik. Age** varlığının çözünürlüğünü gösterir.

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Para birimi](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)ve [Boyut](luis-reference-prebuilt-dimension.md) varlıkları hakkında bilgi edinin. 
