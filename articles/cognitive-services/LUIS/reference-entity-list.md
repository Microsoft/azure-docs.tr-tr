---
title: Liste varlık türü-LUSıS
titleSuffix: Azure Cognitive Services
description: Liste varlıkları, sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. LUIS, liste varlıkları için ek değerler bulmaz. Geçerli listeye göre yeni kelimelerin önerilerini görmek için öner özelliğini kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 1307e6cfca0debe7623eb775c69527a74584033d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011983"
---
# <a name="list-entity"></a>Liste varlığı 

Liste varlıkları, sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. LUIS, liste varlıkları için ek değerler bulmaz. Kullanım **önerilir** yeni sözcükleri sunabileceği önerileri görmek için özellik geçerli listede bağlı. Birden fazla liste varlığı ile aynı değeri varsa, her varlık uç nokta sorguda döndürülür. 

Bir liste varlığı makine tarafından öğrenilen değildir. Tam metin bir eşleşmedir. LUIS, herhangi bir listede bir öğe için herhangi bir eşleşme yanıtta bir varlık olarak işaretler. 

**Varlık, metin verileri olduğunda iyi bir uyum:**

* Bilinen bir küme.
* Genellikle değişmez. Listeyi sık olarak değiştirmeniz veya listenin kendi kendine genişlemesine ihtiyaç duyuyorsanız, tümcecik listesi ile birlikte bulunan basit bir varlık daha iyi bir seçimdir. 
* Küme, bu varlık türü için maksimum LUIS [sınırlarını](luis-boundaries.md) aşmaz.
* Konuşmadaki metin bir eşanlamlı sözcük veya kurallı ad ile tam olarak eşleşiyor. LUSıS, tam metin eşleştirmelerinin ötesinde listeyi kullanmaz. Belirsiz eşleştirme, büyük/küçük harfe duyarlı, sözcük kökü oluşturma, plurals ve diğer çeşitlemeler bir liste varlığıyla çözümlenmez. Çeşitlemeleri yönetmek için, isteğe bağlı metin söz dizimine sahip bir [model](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) kullanmayı düşünün.

![Liste varlığı](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Liste varlığına içeri aktarılacak örnek. JSON

  Aşağıdaki. JSON biçimini kullanarak, mevcut bir liste varlığına değerleri içeri aktarabilirsiniz:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]  
  ```

## <a name="example-json-response"></a>Örnek JSON yanıtı

Adlı bir listesi uygulamanın olduğu varsayalım `Cities`, şehir adları havaalanı (tac Sea), havaalanı kodu (SEA), posta kodu (98101) ve telefon alan kodunu (206) bulunduğu şehir de dahil olmak üzere çeşitleri için izin verme.

|Liste öğesi|Öğe eş anlamlı sözcükler|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Önceki utterance, sözcük içinde `paris` paris öğesine bir parçası olarak eşleştirilir `Cities` varlık listesi. Liste varlığı hem normalleştirilmiş öğenin adı, hem de öğe eş anlamlılar eşleşir.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)


Bu, sorgu dizesinde `verbose=false` ayarlandıysa JSON olur:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Bu, sorgu dizesinde `verbose=true` ayarlandıysa JSON olur:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Veri nesnesi|Varlık adı|Değer|
|--|--|--|
|Varlık listeleme|`Cities`|`paris`|


## <a name="next-steps"></a>Sonraki adımlar

Bu [öğreticide](luis-quickstart-intent-and-list-entity.md), bilinen öğeler listesinden metnin tam eşleşmelerini çıkarmak için bir **liste varlığını** nasıl kullanacağınızı öğrenin. 
