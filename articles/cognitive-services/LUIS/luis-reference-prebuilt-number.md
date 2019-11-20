---
title: Önceden oluşturulmuş varlık sayısı-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale Language Understanding (LUSıS) içinde önceden oluşturulmuş varlık bilgilerini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6a59cf83b3912e31b8aae67319902ce516519af8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491298"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Bir LUSıS uygulaması için önceden oluşturulmuş varlık sayısı
Bilgi parçalarını anlatmak, ifade etmek ve tanımlamak için sayısal değerlerin kullanıldığı birçok yol vardır. Bu makalede, olası örneklerden yalnızca bazıları ele alınmaktadır. LUO, Kullanıcı araslarının çeşitlemelerini Yorumlar ve tutarlı sayısal değerler döndürür. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını sayı içeren örnek bir değer eklemeniz gerekmez. 

## <a name="types-of-number"></a>Sayı türleri
Numara [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub deposundan yönetiliyor

## <a name="examples-of-number-resolution"></a>Sayı çözümleme örnekleri

| İfade        | Varlık   | Çözünürlük |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


Luo, döndürdüğü JSON yanıtının `resolution` alanındaki **`builtin.number`** varlığın tanınan değerini içerir.

## <a name="resolution-for-prebuilt-number"></a>Önceden oluşturulmuş numara için çözüm

Sorgu için aşağıdaki varlık nesneleri döndürülür:

`order two dozen eggs`

#### <a name="v3-responsetabv3"></a>[V3 yanıtı](#tab/V3)

Aşağıdaki JSON, `verbose` parametresi `false`olarak ayarlanmıştır:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 ayrıntılı yanıt](#tab/V3-verbose)

Aşağıdaki JSON, `verbose` parametresi `true`olarak ayarlanmıştır:

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

Aşağıdaki örnek, "iki düzine" için 24 değerinin çözümlenme durumunu içeren LUSıS 'den bir JSON yanıtı gösterir.

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Para birimi](luis-reference-prebuilt-currency.md), [sıra sayısı](luis-reference-prebuilt-ordinal.md)ve [yüzde](luis-reference-prebuilt-percentage.md)bilgileri hakkında bilgi edinin. 
