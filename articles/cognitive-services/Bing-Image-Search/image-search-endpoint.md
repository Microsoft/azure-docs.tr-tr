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
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94593527"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing Resim Arama API'si uç noktaları

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Resim arama API 'si** üç uç nokta içerir.  Endpoint 1, bir sorguya göre Web 'den görüntüleri döndürür. Uç nokta 2 [ımageınsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)döndürüyor.  Uç nokta 3, popüler resimleri döndürür.

## <a name="endpoints"></a>Uç Noktalar

Bing API kullanarak görüntü sonuçlarını almak için aşağıdaki uç noktalardan birine bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

**Uç nokta 1:** Kullanıcı tarafından tanımlanan arama sorgusuyla ilgili görüntüleri döndürür `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Uç nokta 2:** Ya da kullanarak bir görüntüyle ilgili öngörüleri `GET` döndürür `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET isteği, görüntüyü içeren Web sayfaları gibi bir görüntüyle ilgili öngörüleri döndürür. Bir istek ile [ınsi, Stoken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) parametresini ekleyin `GET` .

Ya da bir isteğin gövdesine bir ikili görüntü ekleyebilir `POST` ve [modüller](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) parametresini olarak ayarlayabilirsiniz `RecognizedEntities` . Bu işlem, görüntüdeki kişiler hakkında bilgi döndüren sonraki bir istekte parametre olarak kullanılacak bir [ınsibir sToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) döndürür `GET` .  `modules` `All` `RecognizedEntities` `POST` Kullanılarak başka bir çağrı yapmadan sonuçları hariç tüm öngörüleri almak için olarak ayarlayın `insightsToken` .


**Uç nokta 3:** Başkaları tarafından yapılan arama isteklerine göre popüler olan görüntüleri döndürür. Görüntüler, örneğin, önemli kişilere veya olaylara bağlı olarak farklı kategorilere ayrılmıştır.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Popüler resimleri destekleyen pazarların bir listesi için bkz. [popüler görüntüler](./trending-images.md).

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkında ayrıntılar için [Bing resim arama API'si v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) başvurusuna bakın.
## <a name="response-json"></a>JSON yanıtı
Bir görüntü arama isteğine yönelik yanıt, sonuçları JSON nesneleri olarak içerir. Sonuçları ayrıştırma örnekleri için bkz. [öğretici](./tutorial-bing-image-search-single-page-app.md) ve [kaynak kodu](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Sonraki adımlar
**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.  Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve/veya konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Görüntü arama API 'sini kullanan temel isteklerin örnekleri için bkz. [resim arama Hızlı Başlat](./overview.md).