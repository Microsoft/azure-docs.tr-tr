---
title: Önceden oluşturulmuş varlık yüzdesi-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içindeki önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e9aa3fc7c0ff76f6b93a8acc67681d1ed7cf4ad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491224"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için önceden oluşturulmuş varlık yüzdesi
Yüzde numaraları kesirler, `3 1/2`veya yüzde olarak görünebilir `2%`. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını yüzde içeren örnek bir değer eklemeniz gerekmez. Yüzde varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir. 

## <a name="types-of-percentage"></a>Yüzde türleri
Yüzde, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub deposundan yönetilir

## <a name="resolution-for-prebuilt-percentage-entity"></a>Önceden oluşturulmuş yüzde varlığına yönelik çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`set a trigger when my stock goes up 2%`

#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

Aşağıdaki örnek, **yerleşik. Percentage** varlığının çözünürlüğünü gösterir.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Sıra](luis-reference-prebuilt-ordinal.md), [sayı](luis-reference-prebuilt-number.md)ve [sıcaklık](luis-reference-prebuilt-temperature.md) varlıkları hakkında bilgi edinin. 
