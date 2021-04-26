---
title: Web 'den görüntü al-Bing Resim Arama API'si
titleSuffix: Azure Cognitive Services
description: Web 'den ilgili görüntüleri aramak ve almak için Bing Resim Arama API'si kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342065"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Bing Resim Arama API'si Web 'den resimleri alın

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Resim Arama REST API kullandığınızda, aşağıdaki GET isteğini göndererek arama teriminizle ilgili Web 'den görüntü alabilirsiniz:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

URL kodlamalı arama teriminizle ilgili [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) sorgu parametresini kullanın. Örneğin, *SAI dingerleri* girerseniz, `q` veya olarak ayarlayın `sailing+dinghies` `sailing%20dinghies` .

> [!IMPORTANT]
> * Tüm isteklerin bir istemciden değil, bir sunucudan yapılması gerekir.
> * Bing arama API 'Lerinden birini ilk kez arıyorsanız, istemci KIMLIĞI üst bilgisini eklemeyin. Yalnızca daha önce Kullanıcı ve cihaz birleşimi için istemci KIMLIĞI döndüren bir Bing API 'SI çağrılırsa, istemci KIMLIĞINI ekleyin.

## <a name="get-images-from-a-specific-web-domain"></a>Belirli bir Web etki alanındaki görüntüleri al

Belirli bir etki alanındaki görüntüleri almak için [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) dize işlecini kullanın.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> İşleci kullanarak sorgulara verilen yanıtlar `site:` , [SafeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) ayarından bağımsız olarak yetişkinlere yönelik içerik içerebilir. Yalnızca `site:` etki alanındaki içeriğin farkında değilseniz kullanın.

## <a name="filter-images"></a>Görüntüleri filtrele

 Varsayılan olarak, Resim Arama API 'SI sorguyla ilgili olan tüm görüntüleri döndürür. Bing 'in döndürdüğü görüntüleri filtrelemek istiyorsanız (örneğin, yalnızca saydam bir arka plana veya belirli boyuta sahip görüntüleri döndürmek için) aşağıdaki sorgu parametrelerini kullanın:

* [en boy](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— görüntüleri en boy oranına göre filtreleyin (örneğin, standart veya geniş ekran görüntüleri).
* [Color](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— görüntüleri baskın renge veya siyaha ve beyaza göre filtreleyin.
* [yenilik](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— resimleri yaş (örneğin, son hafta içinde Bing tarafından bulunan görüntüler) olarak filtreleyin.
* [Yükseklik](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [Genişlik](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— görüntüleri genişliğe ve yüksekliğe göre filtreleyin.
* [ImageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— görüntüleri içeriğe göre filtreleyin (örneğin, yalnızca bir kişinin yüzünü gösteren görüntüler).
* [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— görüntüleri türe göre filtreleyin (örneğin, küçük resim, animasyonlu GIF 'ler veya saydam arka planlar).
* [Lisans](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— görüntüleri siteyle ilişkili lisans türüne göre filtreleyin.
* [Boyut](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— görüntüleri, 200 x 200 piksel kadar küçük görüntüler gibi boyuta göre filtreleyin.

Belirli bir etki alanındaki görüntüleri almak için [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) dize işlecini kullanın.

Aşağıdaki örnek, Bing 'in geçen hafta içinde bulduğu ContosoSailing.com 'tan küçük görüntülerin nasıl alınacağını gösterir.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing Resim Arama yanıt biçimi

Bing 'den yanıt iletisi, bilişsel hizmetler 'in sorguyla ilgili olduğunu belirlenen görüntülerin listesini içeren bir [görüntü](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) yanıtı içerir. Listedeki her [görüntü](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) nesnesi, görüntü hakkında şu bilgileri IÇERIR: URL, boyutu, boyutları, kodlama biçimi, görüntünün küçük resminin URL 'si ve küçük resmin boyutları.

> [!NOTE]
> * Görüntülerin yanıtta belirtilen sırada görüntülenmesi gerekir.
> * URL biçimleri ve parametreleri bildirimde bulunulmadan değişikliğe tabi olduğundan, tüm URL 'Leri olduğu gibi kullanın. URL biçimi veya parametreler üzerinde belirtilenler dışında bağımlılık kullanmamalısınız.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Bing Resim Arama API'sini çağırdığınızda Bing, sonuç listesini döndürür. Bu liste sorguyla ilgili tüm sonuçların alt kümesidir. Yanıtın `totalEstimatedMatches` alanı, görüntülenebilecek tahmini görüntü sayısını içerir. Görüntülerin geri kalanı hakkında daha fazla bilgi için bkz. [sayfalama görüntüleri](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Sonraki adımlar

Daha önce Bing Resim Arama API'si denemediyseniz [hızlı](../quickstarts/csharp.md)başlangıcı deneyin. Daha karmaşık bir şeyler arıyorsanız, [tek sayfalı bir Web uygulaması](../tutorial-bing-image-search-single-page-app.md)Oluşturma öğreticisini deneyin.