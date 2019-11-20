---
title: Telefon numarası önceden oluşturulmuş varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) içindeki telefon numarası önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 1cc7469bf6b29ed864fac3955dc8770aa879f84d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499538"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için telefon numarası önceden oluşturulmuş varlık
`phonenumber` varlığı, ülke kodu dahil olmak üzere çeşitli telefon numaralarını ayıklar. Bu varlık zaten eğitiltiğinden, uygulamaya örnek eklememe eklemeniz gerekmez. `phonenumber` varlığı yalnızca `en-us` kültür ' de desteklenir. 

## <a name="types-of-a-phone-number"></a>Telefon numarası türleri
`Phonenumber`, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub deposundan yönetiliyor

## <a name="resolution-for-this-prebuilt-entity"></a>Bu önceden oluşturulmuş varlık için çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

Aşağıdaki örnek, **yerleşik. PhoneNumber** varlığının çözünürlüğünü gösterir.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Yüzde](luis-reference-prebuilt-percentage.md), [sayı](luis-reference-prebuilt-number.md)ve [sıcaklık](luis-reference-prebuilt-temperature.md) varlıkları hakkında bilgi edinin. 
