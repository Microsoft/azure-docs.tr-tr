---
title: include dosyası
description: include dosyası
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503741"
---
Örnek, belirli bir biçimi izler. 

`text` alanı, örnek konuşmanın metnini içerir. `intentName` alanı, LUIS uygulaması içindeki mevcut bir amacın adına karşılık gelmelidir. `entityLabels` alanı gereklidir. Herhangi bir varlığı etiketlemek istemiyorsanız, boş bir dizi girin.

entityLabels dizisi boş değilse `startCharIndex` ve `endCharIndex` değerlerinin `entityName` alanında başvurulan varlığı işaretlemesi gerekir. Dizin sıfır tabanlıdır, yani üstteki örnekte 6 sayısı Seattle sözcüğünün baş harfi S'den önceki boşluğa değil S harfine karşılık gelmektedir. Etiketi metindeki bir boşlukla başlatır veya bitirirseniz, konuşmaları eklemek için yapılan API çağrısı başarısız olur.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
