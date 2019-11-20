---
title: Bing Haber Arama API'si haberleri arayın
titleSuffix: Azure Cognitive Services
description: Genel Haberler, popüler konular ve haber başlıkları için arama sorguları gönderme hakkında bilgi edinin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.openlocfilehash: b70cf50bb33ca7962116586a347c508b15abcd0c
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423880"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Bing Haber Arama API'si haberleri arayın

Bing Resim Arama API'si, Bing 'in bilişsel haber arama yeteneklerini uygulamalarınıza tümleştirmeyi kolaylaştırır.

Bing Haber Arama API'si öncelikle ilgili haber makalelerini bulup döndürse de, Web 'de akıllı ve odaklanmış haber alımı için çeşitli özellikler sağlar.

## <a name="suggest-and-use-search-terms"></a>Arama terimleri öner ve kullan

Kullanıcıların arama terimlerini gireceği bir arama kutusu sağlıyorsanız deneyimi geliştirmek için [Bing Otomatik Öneri API'sini](../../bing-autosuggest/get-suggested-search-terms.md) kullanın. API, kullanıcı yazarken kısmi arama terimlerine dayalı önerilen sorgu dizelerini yönetin.

Kullanıcı arama terimini girdikten sonra [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) sorgu parametresini ayarlamadan önce terimi URL ile kodlayın. Örneğin kullanıcı *sailing dinghies* terimini girerse `q` öğesini `sailing+dinghies` veya `sailing%20dinghies` olarak ayarlayın.

## <a name="get-general-news"></a>Genel haberleri al

Web'den kullanıcının arama terimiyle ilgili genel haber makalelerini almak için aşağıdaki GET isteğini gönderin:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Bing API'lerinden birini ilk kez çağırıyorsanız istemci kimliği üst bilgisini eklemeyin. İstemci kimliğini yalnızca önceden bir Bing API'sini çağırdıysanız ve Bing, kullanıcı ve cihaz birleşimi için bir istemci kimliği döndürdüyse dahil edin.

Belirli bir etki alanındaki haberleri almak için [site:](https://msdn.microsoft.com/library/ff795613.aspx) sorgu işlecini kullanın.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Aşağıdaki JSON örneği, önceki sorguya olan yanıtı gösterir. Bing arama API 'Leri için [kullanım ve görüntüleme gereksinimlerinin](../useanddisplayrequirements.md) bir parçası olarak, her bir haber makalesini yanıtta belirtilen sırada görüntümalısınız. Makalede kümelenmiş makaleler varsa ilgili makalelerin var olduğunu ve bunları istek üzerine görüntülendiğini belirtmeniz gerekir.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

[news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) yanıtı, Bing’in sorguyla ilgili olduğunu düşündüğü haber makalelerini listeler. `totalEstimatedMatches` alanı, görüntülenebilecek tahmini makale sayısını içerir. Makaleler arasında gezinme hakkında bilgi için bkz. [Haberleri Sayfalama](../paging-news.md).

Listedeki her [haber makalesi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle), makalenin adı, açıklaması ve konağın web sitesindeki makalenin URL’sini içerir. Makale bir görüntü içeriyorsa, nesne görüntünün küçük resmini içerir. Kullanıcıyı konağın sitesindeki haber makalesine götüren bir köprü bağlantı oluşturmak için `name` ve `url` kullanın. Makale bir görüntü içeriyorsa, `url` kullanarak görüntüyü de tıklanabilir hale getirin. Makaleyi ilişkilendirmek için `provider` kullandığınızdan emin olun.

Bing haber makalesinin kategorisini belirleyebiliyorsa, makale `category` alanını içerir.

## <a name="get-todays-top-news"></a>Bugünün en iyi haberlerini alın

Günümüzün en popüler haber makalelerini almak için, aynı genel haber isteğini daha önce olduğu gibi gönderebilirsiniz ve `q` parametreyi yok edin.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

En iyi haberleri alma yanıtı, genel haberleri alma ile neredeyse aynıdır. Ancak, belirli sayıda sonuç olduğu için `news` yanıtı `totalEstimatedMatches` alanını içermez. En önemli haber makalelerinin sayısı, haber döngüsüne bağlı olarak farklılık gösterebilir. Makaleyi kullanmak `provider` için alanı kullandığınızdan emin olun.

## <a name="get-news-by-category"></a>Kategoriye göre haberleri al

Önemli spor veya eğlence haberleri gibi kategoriye göre haber makalelerini almak için, Bing’e aşağıdaki GET isteğini gönderin:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Alınacak makale kategorilerini belirtmek için [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) sorgu parametresini kullanın. Belirtebileceğiniz olası haber kategorilerinin listesi için bkz. [Pazara Göre Haber Kategorileri](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market).

Haberleri kategoriye göre alma isteğine verilen yanıt, genel haberleri alma ile neredeyse aynıdır. Ancak, makalelerin tümü belirtilen kategoriye aittir.

## <a name="get-headline-news"></a>Başlık haberleri al

Manşet haber makalelerini istemek ve tüm haber kategorilerinden makaleler almak için, Bing’e aşağıdaki GET isteğini gönderin:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

[category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) sorgu parametresini dahil etmeyin.

Manşet haberlerini alma isteğine verilen yanıt, günün önemli haberlerini alma ile neredeyse aynıdır. Makale bir manşet makalesi ise `headline` alanı **true** olarak ayarlanır.

Varsayılan olarak, yanıt 12 adede kadar manşet makalesi içerir. Döndürülecek manşet makalelerinin sayısını değiştirmek için [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount) sorgu parametresini belirtin. Yanıt ayrıca her haber kategorisinde manşet olmayan dört makale içerebilir.

Yanıt, kümeleri tek bir makale olarak sayar. Bir kümede birden fazla makale olabileceği için, yanıt 12’den fazla manşet makalesi ve kategoriye göre manşet olmayan dörtten fazla makale içerebilir.

## <a name="get-trending-news"></a>Popüler haberleri al

Sosyal ağlarda popüler olan haber başlıklarını almak için Bing’e aşağıdaki GET isteğini gönderin:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Popüler Konular yalnızca en-US ve zh-CN pazarlarında kullanılabilir.

Aşağıdaki JSON, önceki isteğin yanıtıdır. Her popüler haber makalesi ilgili bir görüntü, flaş haber bayrağı ve makalenin Bing arama sonuçlarının URL’sini içerir. Kullanıcıyı Bing arama sonuçları sayfasına götürmek için `webSearchUrl` alanındaki URL’yi kullanın. Veya sonuçları kendiniz görüntülemek isterseniz sorgu metnini kullanarak [Web Araması API'sini](../../bing-web-search/search-the-web.md) çağırın.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>İlgili haberleri alma

Bir haber makalesiyle ilgili başka makaleler varsa, haber makalesi [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles) alanını içerebilir. Kümelenmiş makaleleri içeren bir makale aşağıda gösterilmiştir.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Haber Arama sonuçları arasında sayfa oluşturma](../paging-news.md)
