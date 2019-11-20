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
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481731"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Bing Arama API'leri sonuçları arasında sayfa oluşturma

Bing Web, özel, görüntü, haber veya Video Arama API 'Lerine bir çağrı gönderdiğinizde Bing, sorguyla ilgili olabilecek Toplam Sonuç sayısının bir alt kümesini döndürür. Tahmini toplam kullanılabilir sonuç sayısını almak için, yanıt nesnesinin `totalEstimatedMatches` alanına erişin. 

Örneğin: 

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

Kullanılabilir sonuçlar aracılığıyla sayfa eklemek için, isteğinizi gönderirken `count` ve `offset` sorgu parametrelerini kullanın.  

> [!NOTE]
>
> * Bing video, resim ve haber API 'Leri ile sayfalama yalnızca genel video (`/video/search`), Haberler (`/news/search`) ve görüntü (`/image/search`) aramalarında geçerlidir. Popüler konular ve Kategoriler aracılığıyla sayfalama desteklenmez.  
> * `TotalEstimatedMatches` alanı, geçerli sorgu için toplam arama sonucu sayısının tahminidir. `count` ve `offset` parametrelerini ayarladığınızda bu tahmin değişebilir.

| Parametre | Açıklama                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Yanıtta döndürülecek sonuçların sayısını belirtir. `count`varsayılan değerinin ve talep ettiğiniz en fazla sonuç sayısının API 'ye göre değiştiğini unutmayın. Bu değerleri, [sonraki adımlar](#next-steps)altındaki başvuru belgelerinde bulabilirsiniz. |
| `offset`  | Atlanacak sonuç sayısını belirtir. `offset` sıfır tabanlıdır ve küçüktür (`totalEstimatedMatches` - `count`) olmalıdır.                                           |

Örnek olarak, sayfa başına 15 sonuç göstermek istiyorsanız, sonuçların ilk sayfasını almak için `count` 15 olarak ayarlanır ve 0 olarak `offset`. Sonraki her API çağrısı için `offset` 15 ' i arttırmalısınız. Aşağıdaki örnek 45 uzaklığında başlayan 15 Web sayfasını ister.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Varsayılan `count` değerini kullanırsanız, API çağrılarınız içinde yalnızca `offset` sorgu parametresini belirtmeniz gerekir.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Bing görüntüsünü ve video API 'Lerini kullanırken yinelenen arama sonuçlarının önüne geçmek için `nextOffset` değerini kullanabilirsiniz. `Images` veya `Videos` yanıt nesnelerinden değeri alın ve `offset` parametresiyle isteklerinizi kullanın.  

> [!NOTE]
> Bing Web Araması API'si, Web sayfalarını, resimleri, Videoları ve haberleri içerebilen arama sonuçlarını döndürür. Bing Web Araması API'si arama sonuçlarıyla çalışırken, görüntü veya haber gibi diğer yanıt türlerini değil, yalnızca [Web sayfalarının](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)sayfalamalarından olursunuz. `WebPage` nesnelerinde arama sonuçları, diğer yanıt türlerinde da görünen sonuçları içerebilir.
>
> Herhangi bir filtre değeri belirtmeden `responseFilter` sorgu parametresini kullanırsanız, `count` ve `offset` parametrelerini kullanmayın. 

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Web Araması API 'Leri nelerdir?](bing-api-comparison.md)
* [Bing Web Araması API'si v7 başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Özel Arama API'si v7 başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing Haber Arama API'si v7 başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Arama API'si v7 başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Resim Arama API'si v7 başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
