---
title: Yanıtları göstermek için derecelendirmeyi kullanma-Bing Varlık Arama
titleSuffix: Azure Cognitive Services
description: Bing Varlık Arama API'si döndürdüğü yanıtları göstermek için derecelendirmenin nasıl kullanılacağını öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423906"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Varlık arama sonuçlarını göstermek için derecelendirmeyi kullanma  

Her varlık arama yanıtı, Bing Varlık Arama API'si tarafından döndürülen arama sonuçlarının nasıl görüntüleneceğini belirten bir [Rankingresponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) yanıtı içerir. Derecelendirme yanıtı, ana hat ve kenar çubuğu içeriğine neden olur. Direk sonucu en önemli veya belirgin bir sonuçdır ve öncelikle görüntülenmelidir. Kalan sonuçları geleneksel bir ana çizgi ve kenar çubuğu biçiminde görüntülemedıysanız, ana hat içeriğini kenar çubuğu içeriğinden daha yüksek görünürlük sağlamanız gerekir. 
  
Her grup içinde, [öğeler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) dizisi içeriğin içinde görünmesi gereken sırayı tanımlar. Her öğe, yanıt içindeki sonucu belirlemek için iki yol sağlar.  
 

|Alan | Açıklama  |
|---------|---------|
|`answerType` ve `resultIndex` | `answerType`yanıtı tanımlar (varlık veya yer) ve `resultIndex` bu yanıt içinde bir sonucu tanımlar (örneğin, bir varlık). Dizin 0 ' da başlar.|
|`value`    | `value`Yanıt ya da yanıt içindeki bir sonuç KIMLIĞIYLE eşleşen bir KIMLIK içerir. Yanıt ya da sonuçlar KIMLIĞI içeriyor ancak her ikisini de içermemelidir. |
  
`answerType` Ve`resultIndex` kullanarak iki adımlı bir işlemdir. İlk olarak, `answerType` görüntülenecek sonuçları içeren yanıtı belirlemek için kullanın. Daha sonra `resultIndex` , görüntülenecek sonucu almak için bu yanıtın sonuçlarına dizin eklemek için kullanın. (Değer, SearchResponse nesnesindeki alanın adıdır.) [](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) `answerType` Tüm yanıtın sonuçlarını birlikte görüntülemesi gerekiyorsa, derecelendirme yanıtı öğesi `resultIndex` alanı içermez.

KIMLIĞI kullanmak, derecelendirme KIMLIĞINI bir yanıtın KIMLIĞIYLE veya sonuçlardan biriyle eşleştirmeye gerek duyar. Bir yanıt nesnesi bir `id` alan içeriyorsa, tüm yanıtın sonuçlarını birlikte görüntüleyin. Örneğin, `Entities` nesnesi `id` alanı içeriyorsa, tüm varlıklar makalelerini birlikte görüntüleyin. Nesne alanını içermiyorsa, her varlık bir `id` alan içerir ve derecelendirme yanıtı, yerleri sonuçları ile karıştırmaz. `id` `Entities`  
  
## <a name="ranking-response-example"></a>Derecelendirme yanıtı örneği

Aşağıda bir [Rankingresponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)örneği gösterilmektedir.
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Bu sıralama yanıtına göre, kenar çubuğu JII Hendrix ile ilgili iki varlık sonucunu görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı web uygulaması oluşturma](tutorial-bing-entities-search-single-page-app.md)
