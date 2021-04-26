---
title: Personbadı önceden oluşturulmuş varlık-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUSıS) ' de personName önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535432"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için sorumlu adı önceden oluşturulmuş varlık
Önceden oluşturulmuş Personbname varlığı kişi adlarını algılar. Bu varlık zaten eğitiltiğinden, uygulama hedefleri için personName 'i içeren örnek bir değer eklemeniz gerekmez. personName varlığı Ingilizce ve Çince [kültürlerde](luis-reference-prebuilt-entities.md)desteklenir.

## <a name="resolution-for-personname-entity"></a>PersonName varlığına yönelik çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 yanıtı](#tab/V3)


Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `false` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)
Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `true` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 yanıtı](#tab/V2)

Aşağıdaki örnek, **yerleşik. personName** varlığının çözümlenme durumunu göstermektedir.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[E-posta](luis-reference-prebuilt-email.md), [sayı](luis-reference-prebuilt-number.md)ve [sıralı](luis-reference-prebuilt-ordinal.md) varlıklar hakkında bilgi edinin.
