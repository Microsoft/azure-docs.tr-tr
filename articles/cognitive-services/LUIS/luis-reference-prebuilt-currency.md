---
title: Para birimi önceden oluşturulmuş varlık-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale Language Understanding (LUSıS) içindeki para birimi önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534345"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için para birimi önceden oluşturulmuş varlık
Önceden oluşturulmuş para birimi varlığı, LUSıS uygulama kültürüne bakılmaksızın birçok paydaya ve ülkede/bölgede para birimini algılar. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını para birimi içeren örnek bir değer eklemeniz gerekmez. Para birimi varlığı [birçok kültürde](luis-reference-prebuilt-entities.md)desteklenir.

## <a name="types-of-currency"></a>Para birimi türleri
Para birimi [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub deposundan yönetilir

## <a name="resolution-for-currency-entity"></a>Para birimi varlığı için çözüm

#### <a name="v3-response"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `false` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `true` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 yanıtı](#tab/V2)

Aşağıdaki örnek, **yerleşik. Currency** varlığının çözünürlüğünü gösterir.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[DatetimeV2](luis-reference-prebuilt-datetimev2.md), [Dimension](luis-reference-prebuilt-dimension.md)ve [email](luis-reference-prebuilt-email.md) varlıkları hakkında bilgi edinin.
