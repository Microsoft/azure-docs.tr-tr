---
title: Bing Haber Arama uç noktaları
titleSuffix: Azure Cognitive Services
description: Bu makale, haber arama API uç noktalarının bir özetini sağlar; Haberler, en son haberler ve popüler haberler.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111490"
---
# <a name="bing-news-search-api-endpoints"></a>Bing Haber Arama API'si uç noktaları

**Haber Arama API 'si** , haber makalelerini, Web sayfalarını, görüntüleri, Videoları ve [varlıkları](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)döndürür. Varlıklar bir kişi, yer veya konu hakkında özet bilgiler içerir.

## <a name="endpoints"></a>Uç Noktalar

Bing Haber Arama API'si kullanarak haber arama sonuçları almak için aşağıdaki uç noktalardan birine bir `GET` isteği gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.

### <a name="news-items-by-search-query"></a>Arama sorgusuna göre haber öğeleri

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Arama sorgusunu temel alan haber öğelerini döndürür. Arama sorgusu boşsa, API, farklı kategorilerdeki en iyi haber makalelerini döndürür. Arama teriminizi kodlayıp`q=""` parametresine ekleyerek URL ile bir sorgu gönderin. Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Kategoriye göre popüler Haberler öğeleri

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Kategoriye göre en popüler haber öğelerini döndürür. `category=business`, `category=sports`veya `category=entertainment`kullanarak en yüksek iş, spor veya eğlence makalelerini özellikle isteyebilirsiniz.  `category` parametresi yalnızca `/news` URL 'SI ile kullanılabilir. Kategorileri belirtmek için bazı biçimsel gereksinimler vardır; [sorgu parametresi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) belgelerindeki `category` bakın. Arama teriminizi kodlayıp`q=""` parametresine ekleyerek URL ile bir sorgu gönderin. Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Popüler Haberler konuları 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Sosyal ağlarda Şu anda popüler olan haber konularını döndürür. `/trendingtopics` seçeneği dahil edildiğinde, Bing arama `freshness` ve `?q=""`gibi diğer birkaç parametreyi yoksayar. Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Sonraki adımlar

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkındaki ayrıntılar için bkz. [Bing Haber Arama API 'si v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) başvurusu.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Haber Arama API 'sini kullanan temel isteklerin örnekleri için bkz. [Bing haber arama Hızlı Başlat](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
