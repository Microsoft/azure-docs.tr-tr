---
title: Model. herhangi bir varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: Desen. Any, varlığın nerede başladığını ve bittiğini işaretlemek için yalnızca bir desen şablonunda kullanılan değişken uzunluklu bir yer tutucudur.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91542215"
---
# <a name="patternany-entity"></a>Pattern.any varlığı

Desen. Any, varlığın nerede başladığını ve bittiğini işaretlemek için yalnızca bir desen şablonunda kullanılan değişken uzunluklu bir yer tutucudur.  

Model. tüm varlıkların, amaç Kullanıcı örnekleri değil, [model](luis-how-to-model-intent-pattern.md) şablonu örneklerinde işaretlenmesi gerekir.

**Varlık, şu durumlarda iyi bir uyum:**

* Varlığın sonunda, utterance 'in kalan metniyle karışıyor olabilir.

## <a name="usage"></a>Kullanım

Başlığa göre kitap arayan bir istemci uygulaması verilirken, model. any tüm başlığı ayıklar. Bu kitap araması için desen. any kullanarak bir şablon söylenişi `Was {BookTitle} written by an American this year[?]` .

Aşağıdaki tabloda, her satırda utterance 'in iki sürümü vardır. En üstteki söylenişi, luın ilk olarak söylenişi 'yi nasıl göreceğini görecektir. Kitap başlığının başladığı ve bittiği yerde net değildir. En alt, bir desen kullanır. varlığın başlangıcını ve bitişini işaretlemek için herhangi bir varlık.

|Kalın olan varlık ile utterance|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Bu yıl **için bir hat ve diğer klinik Tales 'e ait kadın geçen adam** mi?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Bu yıl bir Amerikan tarafından yazılan **Frog Pagr s 'de yarım uyku** mi oldu?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**Açık limon pastanın BT 'Nin belirli** bir özelliği: Bu yıl bir Amerikan tarafından yazılmış bir Nolik mi?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Cemdeki bir Woketi vardı!** Bu yıl bir Amerikan tarafından yazılmıştır mı?|
||



## <a name="example-json"></a>Örnek JSON

Şu sorguyu inceleyin:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Katıştırılmış form adı ile bir model olarak ayıklanacak. Any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

Bu, `verbose=false` sorgu dizesinde AYARLANDıYSA JSON 'dir:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Bu, `verbose=true` sorgu dizesinde AYARLANDıYSA JSON 'dir:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Sonraki adımlar

Bu [öğreticide](luis-tutorial-pattern.md), **bir desen. her türlü** varlığı kullanarak, utülerin iyi biçimlendirildiğinden ve verilerin sonunun, utterlik 'in kalan sözcükleriyle kolayca karıştırılabileceği bir veri çıkartır.
