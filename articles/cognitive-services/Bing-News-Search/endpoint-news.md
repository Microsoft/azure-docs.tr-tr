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
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94366409"
---
# <a name="bing-news-search-api-endpoints"></a>Bing Haber Arama API'si uç noktaları

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Haber Arama API 'si** , haber makalelerini, Web sayfalarını, görüntüleri, Videoları ve [varlıkları](../bing-entities-search/overview.md)döndürür. Varlıklar bir kişi, yer veya konu hakkında özet bilgiler içerir.

## <a name="endpoints"></a>Uç Noktalar

Bing Haber Arama API'si kullanarak haber arama sonuçları almak için `GET` aşağıdaki uç noktalardan birine bir istek gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.

### <a name="news-items-by-search-query"></a>Arama sorgusuna göre haber öğeleri

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Arama sorgusunu temel alan haber öğelerini döndürür. Arama sorgusu boşsa, API, farklı kategorilerdeki en iyi haber makalelerini döndürür. Arama teriminizi kodlayıp parametreye ekleyerek URL ile bir sorgu gönderin `q=""` . Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Kategoriye göre popüler Haberler öğeleri

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Kategoriye göre en popüler haber öğelerini döndürür. , Veya kullanarak popüler iş, spor veya eğlence makalelerini özellikle isteyebilirsiniz `category=business` `category=sports` `category=entertainment` .  `category`Parametresi yalnızca URL ile birlikte kullanılabilir `/news` . Kategorileri belirtmek için bazı biçimsel gereksinimler vardır; `category` [sorgu parametresi](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) belgelerindeki bölümüne bakın. Arama teriminizi kodlayıp parametreye ekleyerek URL ile bir sorgu gönderin `q=""` . Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Popüler Haberler konuları 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Sosyal ağlarda Şu anda popüler olan haber konularını döndürür. `/trendingtopics`Seçenek dahil edildiğinde Bing arama, ve gibi diğer birkaç parametreyi yoksayar `freshness` `?q=""` . Kullanılabilirlik için bkz. [Desteklenen ülkeler/bölgeler ve pazarlar](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Sonraki adımlar

Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkındaki ayrıntılar için bkz. [Bing Haber Arama API 'si v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) başvurusu.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Haber Arama API 'sini kullanan temel isteklerin örnekleri için bkz. [Bing haber arama Hızlı Başlat](/azure/cognitive-services/bing-news-search).