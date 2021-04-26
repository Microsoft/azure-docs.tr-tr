---
title: Bing Varlık Arama API'si uç noktası
titleSuffix: Azure Cognitive Services
description: Bing Varlık Arama API'si bir sorguya göre Web 'den varlıkları döndüren bir uç nokta vardır. Bu arama sonuçları JSON içinde döndürülür.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338266"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Varlık Arama API'si uç noktası

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).


Bing Varlık Arama API'si bir sorguya göre Web 'den varlıkları döndüren bir uç nokta vardır. Bu arama sonuçları JSON içinde döndürülür.

## <a name="get-entity-results-from-the-endpoint"></a>Uç noktadan varlık sonuçları al

**BING API**'yi kullanarak varlık sonuçları almak için `GET` aşağıdaki uç noktaya bir istek gönderin. Arama isteğinizi özelleştirmek için [üst bilgileri](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) ve [sorgu parametrelerini](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) kullanın. Arama istekleri parametresi kullanılarak gönderilebilir `?q=` .

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Varlık Arama API'si nedir?](overview.md)

## <a name="see-also"></a>Ayrıca bkz. 

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar ve daha fazlası hakkında daha fazla bilgi için [Bing varlık arama API'si v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) Reference makalesine bakın.