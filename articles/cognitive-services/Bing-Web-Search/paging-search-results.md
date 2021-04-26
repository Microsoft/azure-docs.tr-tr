---
title: Arama sonuçları aracılığıyla sayfa oluşturma-Bing Arama API'leri
titleSuffix: Azure Cognitive Services
description: Bing Arama API'leri arama sonuçları aracılığıyla nasıl sayfa oluşturacağınızı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350628"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Bing Arama API'leri sonuçları arasında sayfa oluşturma

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Web, özel, görüntü, haber veya Video Arama API 'Lerine bir çağrı gönderdiğinizde Bing, sorguyla ilgili olabilecek Toplam Sonuç sayısının bir alt kümesini döndürür. Tahmini toplam kullanılabilir sonuç sayısını almak için, yanıt nesnesinin `totalEstimatedMatches` alanına erişin. 

Örnek: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Arama sonuçları aracılığıyla sayfalama

Kullanılabilir sonuçlar aracılığıyla sayfa eklemek için `count` `offset` isteğinizi gönderirken ve sorgu parametrelerini kullanın.  

> [!NOTE]
>
> * Bing video, resim ve haber API 'Leri ile sayfalama yalnızca genel video ( `/video/search` ), haber ( `/news/search` ) ve görüntü ( `/image/search` ) aramalarında geçerlidir. Popüler konular ve Kategoriler aracılığıyla sayfalama desteklenmez.  
> * Bu `TotalEstimatedMatches` alan, geçerli sorgu için toplam arama sonucu sayısının tahminidir. `count`Ve `offset` parametrelerini ayarladığınızda bu tahmin değişebilir.

| Parametre | Açıklama                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Yanıtta döndürülecek sonuçların sayısını belirtir. Varsayılan değerinin `count` ve talep ettiğiniz en fazla sonuç SAYıSıNıN API 'ye göre değiştiğini unutmayın. Bu değerleri, [sonraki adımlar](#next-steps)altındaki başvuru belgelerinde bulabilirsiniz. |
| `offset`  | Atlanacak sonuç sayısını belirtir. `offset`Sıfır tabanlıdır ve () değerinden küçük olmalıdır `totalEstimatedMatches`  -  `count` .                                           |

Örnek olarak, sayfa başına 15 sonuç göstermek istiyorsanız, `count` `offset` sonuçların ilk sayfasını almak için 15 ve 0 olarak ayarlanır. Sonraki her API çağrısı için 15 oranında arttırılırsınız `offset` . Aşağıdaki örnek 45 uzaklığında başlayan 15 Web sayfasını ister.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Varsayılan `count` değeri kullanırsanız, `offset` API aramalarınızın yalnızca sorgu parametresini belirtmeniz gerekir.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Bing görüntüsünü ve video API 'Lerini kullanırken `nextOffset` yinelenen arama sonuçlarından kaçınmak için değerini kullanabilirsiniz. `Images`Veya `Videos` Yanıt nesnelerinden değeri alın ve bu parametre ile isteklerinizi kullanın `offset` .  

> [!NOTE]
> Bing Web Araması API'si, Web sayfalarını, resimleri, Videoları ve haberleri içerebilen arama sonuçlarını döndürür. Bing Web Araması API'si arama sonuçlarıyla çalışırken, görüntü veya haber gibi diğer yanıt türlerini değil, yalnızca [Web sayfalarının](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)sayfalamalarından olursunuz. Nesnelerde arama sonuçları `WebPage` , diğer yanıt türlerinde de görünen sonuçları içerebilir.
>
> `responseFilter`Sorgu parametresini herhangi bir filtre değeri belirtmeden kullanırsanız, `count` ve `offset` parametrelerini kullanmayın. 

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Web Araması API 'Leri nelerdir?](bing-api-comparison.md)
* [Bing Web Araması API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Özel Arama API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing Haber Arama API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Arama API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Resim Arama API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)