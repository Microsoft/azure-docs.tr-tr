---
title: Yaklaşım Analizi-LUSıS
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508028"
---
# <a name="sentiment-analysis"></a>Yaklaşım analizi
Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir. [Metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) belgelerindeki yaklaşım analizi hakkında daha fazla bilgi edinin.


## <a name="resolution-for-sentiment"></a>Yaklaşım için çözüm

Yaklaşım verileri, 1 ile 0 arasında pozitif (1 ' e yakın) veya negatif (0 ' a yakın) verilerin yaklaşımını belirten bir puandır.

#### <a name="english-languagetabenglish"></a>[İngilizce dili](#tab/english)

Kültür `en-us`olduğunda yanıt şu şekilde olur:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Diğer diller](#tab/other-languages)

Diğer tüm kültürler için yanıt şu şekilde yapılır:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

