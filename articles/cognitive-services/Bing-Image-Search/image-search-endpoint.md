---
title: Bing Resim Arama API'si uç noktaları
titleSuffix: Azure Cognitive Services
description: Resim Arama API 'SI üç uç nokta içerir. Endpoint 1, Web 'den görüntüleri döndürür. Uç nokta 2 ımageınsights döndürüyor. Uç nokta 3, popüler resimleri döndürür.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072639"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing Resim Arama API'si uç noktaları

**Resim arama API 'si** üç uç nokta içerir.  Endpoint 1, bir sorguya göre Web 'den görüntüleri döndürür. Uç nokta 2 [ımageınsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)döndürüyor.  Uç nokta 3, popüler resimleri döndürür.

## <a name="endpoints"></a>Uç Noktalar

Bing API kullanarak görüntü sonuçlarını almak için aşağıdaki uç noktalardan birine bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

**Uç nokta 1:** Kullanıcının arama sorgusuyla ilgili `?q=""`tarafından tanımlanan görüntüleri döndürür.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Uç nokta 2:** `GET` ya da `POST`kullanarak bir görüntüyle ilgili öngörüleri döndürür.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET isteği, görüntüyü içeren Web sayfaları gibi bir görüntüyle ilgili öngörüleri döndürür. `GET` isteğiyle [ınsi, Stoken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametresini ekleyin.

Ya da bir `POST` isteğinin gövdesine bir ikili görüntü ekleyebilir ve [modüller](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametresini `RecognizedEntities`olarak ayarlayabilirsiniz. Bu, görüntüdeki kişiler hakkında bilgi döndüren sonraki bir `GET` isteğinde parametre olarak kullanmak üzere bir [ınsibir sToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) döndürür.  `modules`, `insightsToken`kullanarak başka bir çağrı yapmadan `POST` sonuçlarında `RecognizedEntities` hariç tüm öngörüleri almak için `All` olarak ayarlayın.


**Uç nokta 3:** Başkaları tarafından yapılan arama isteklerine göre popüler olan görüntüleri döndürür. Görüntüler, örneğin, önemli kişilere veya olaylara bağlı olarak farklı kategorilere ayrılmıştır.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Popüler resimleri destekleyen pazarların bir listesi için bkz. [popüler görüntüler](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkında ayrıntılar için [Bing resim arama API'si v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) başvurusuna bakın.
## <a name="response-json"></a>JSON yanıtı
Bir görüntü arama isteğine yönelik yanıt, sonuçları JSON nesneleri olarak içerir. Sonuçları ayrıştırma örnekleri için bkz. [öğretici](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) ve [kaynak kodu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Sonraki adımlar
**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.  Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve/veya konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Görüntü arama API 'sini kullanan temel isteklerin örnekleri için bkz. [resim arama Hızlı Başlat](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
