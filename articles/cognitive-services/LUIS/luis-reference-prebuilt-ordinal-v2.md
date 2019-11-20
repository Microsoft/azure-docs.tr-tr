---
title: Sıralı v2 önceden oluşturulmuş varlık-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içinde sıralı v2 önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 02bc6657126cb1cf241c2ca4668e62bd49608d4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491257"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için sıralı v2 önceden oluşturulmuş varlık
Ordinal v2 numarası, `next`, `last`ve `previous`gibi göreli başvurular sağlamak için [sıra](luis-reference-prebuilt-ordinal.md) sayısını genişletir. Bunlar, sıralı önceden oluşturulmuş varlık kullanılarak ayıklanmaz.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Önceden oluşturulmuş Ordinal v2 varlığı için çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`what is the second to last choice in the list`

#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)

Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

Aşağıdaki örnek, **yerleşik. ordinalV2** varlığının çözünürlüğünü gösterir.

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Yüzde](luis-reference-prebuilt-percentage.md), [telefon numarası](luis-reference-prebuilt-phonenumber.md)ve [sıcaklık](luis-reference-prebuilt-temperature.md) varlıkları hakkında bilgi edinin. 
