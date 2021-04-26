---
title: Yaklaşım Analizi-LUSıS
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554034"
---
# <a name="sentiment-analysis"></a>Yaklaşım analizi
Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir. [Metin analizi](../text-analytics/index.yml) belgelerindeki yaklaşım analizi hakkında daha fazla bilgi edinin.

LUSıS Metin Analizi v2 kullanır. 

Yaklaşım Analizi, uygulamanız yayımlandığında yapılandırılır. Daha fazla bilgi için bkz. [uygulama yayımlama](./luis-how-to-publish-app.md) .

## <a name="resolution-for-sentiment"></a>Yaklaşım için çözüm

Yaklaşım verileri, 1 ile 0 arasında pozitif (1 ' e yakın) veya negatif (0 ' a yakın) verilerin yaklaşımını belirten bir puandır.

#### <a name="english-language"></a>[İngilizce dili](#tab/english)

Kültür olduğunda `en-us` Yanıt şu şekilde olur:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Diğer diller](#tab/other-languages)

Diğer tüm kültürler için yanıt şu şekilde yapılır:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.
