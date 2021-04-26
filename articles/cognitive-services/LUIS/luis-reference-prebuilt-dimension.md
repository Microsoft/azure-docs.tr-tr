---
title: Boyut önceden oluşturulmuş varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) ' de önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 0b5f081dc8c0bc203ccafde4a513371d11e36879
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91538356"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için boyut önceden oluşturulmuş varlık
Önceden oluşturulmuş boyut varlığı, LUSıS uygulama kültürüne bakılmaksızın çeşitli boyut türlerini algılar. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını boyutlar içeren örnek bir değer eklemeniz gerekmez. Boyut varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir.

## <a name="types-of-dimension"></a>Boyut türleri

Boyut, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub deposundan yönetilir.

## <a name="resolution-for-dimension-entity"></a>Boyut varlığı için çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `false` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `true` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

Aşağıdaki örnek, **yerleşik. Dimension** varlığının çözünürlüğünü gösterir.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[E-posta](luis-reference-prebuilt-email.md), [sayı](luis-reference-prebuilt-number.md)ve [sıralı](luis-reference-prebuilt-ordinal.md) varlıklar hakkında bilgi edinin.
