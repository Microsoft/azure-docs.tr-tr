---
title: Önceden oluşturulmuş varlık yüzdesi-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içindeki önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541705"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için önceden oluşturulmuş varlık yüzdesi
Yüzde numaraları kesirler, `3 1/2` veya yüzde olarak görünebilir `2%` . Bu varlık zaten eğitiltiğinden, uygulama amaçlarını yüzde içeren örnek bir değer eklemeniz gerekmez. Yüzde varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir.

## <a name="types-of-percentage"></a>Yüzde türleri
Yüzde, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub deposundan yönetilir

## <a name="resolution-for-prebuilt-percentage-entity"></a>Önceden oluşturulmuş yüzde varlığına yönelik çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `false` :

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `true` :

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
#### <a name="v2-response"></a>[V2 yanıtı](#tab/V2)

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
