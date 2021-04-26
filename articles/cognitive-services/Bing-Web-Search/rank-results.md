---
title: Arama sonuçlarını göstermek için randama kullanma-Bing Web Araması API'si
titleSuffix: Azure Cognitive Services
description: Bing Web Araması API'si arama sonuçlarını göstermek için derecelendirmenin nasıl kullanılacağını öğrenin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380458"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Bing Web Araması API'si sonuçlarını göstermek için derecelendirme kullanma  

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Her arama yanıtı, arama sonuçlarının nasıl görüntüleneceğini belirten bir [Rankingresponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) yanıtı içerir. Sıralama yanıtı, geleneksel bir arama sonuçları sayfası için ana hat içeriğine ve kenar çubuğu içeriğine göre sonuçları gruplandırır. Sonuçları geleneksel bir ana çizgi ve kenar çubuğu biçiminde görüntülemedikçe, ana hat içeriğini kenar çubuğu içeriğinden daha yüksek görünürlük sağlamanız gerekir.  

Her grup içinde (ana hat veya kenar çubuğu), [öğeler](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) dizisi içeriğin gösterilmesi gereken sırayı tanımlar. Her öğe, yanıt içindeki sonucu belirlemek için aşağıdaki iki yöntemi sağlar.  

-   `answerType` ve `resultIndex` — `answerType` alan, yanıtı (örneğin, Web sayfası veya haberleri) tanımlar ve `resultIndex` Yanıt içinde bir sonuç tanımlar (örneğin, bir haber makalesi). Dizin sıfır tabanlıdır.  

-   `value` — Alan, yanıtın `value` kimliği veya Yanıt içindeki bir sonuç kimliğiyle eşleşen BIR kimlik içerir. Yanıt ya da sonuçlar KIMLIĞI içeriyor ancak her ikisini de içermemelidir.  

Yalnızca bir yanıtın KIMLIĞI veya sonuçlarından biri ile derecelendirme KIMLIĞINI eşleştirmek gerektiğinden, KIMLIĞI kullanmak daha basittir. Bir yanıt nesnesi bir alan içeriyorsa `id` , tüm yanıtın sonuçlarını birlikte görüntüleyin. Örneğin, `News` nesnesi `id` alanı içeriyorsa, tüm haber makalelerini birlikte görüntüleyin. `News`Nesne `id` alanını içermiyorsa, her haber makalesi bir `id` alan içerir ve derecelendirme yanıtı, haber makalelerini diğer yanıtların sonuçlarıyla karıştırır.  

Ve kullanımı `answerType` `resultIndex` biraz daha karmaşıktır. `answerType`Görüntülenecek sonuçları içeren yanıtı tanımlamak için kullanırsınız. Bundan sonra, `resultIndex` görüntülenecek sonucu elde etmek için yanıtın sonuçlarını kullanarak dizin oluşturabilirsiniz. ( `answerType` Değer, [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) nesnesindeki alanın adıdır.) Tüm yanıtın sonuçlarını birlikte görüntülemesi gerekiyorsa, derecelendirme yanıtı öğesi `resultIndex` alanı içermez.  

## <a name="ranking-response-example"></a>Derecelendirme yanıtı örneği

Aşağıda bir [Rankingresponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)örneği gösterilmektedir. Web yanıtı bir `id` alan içermediğinden, tüm Web sayfalarını derecelendirmeden (her Web sayfasında bir alan içerir) ayrı ayrı görüntülerdiniz `id` . Ve görüntüler, videolar ve ilgili arama cevapları alanı içereceğinden `id` , Bu yanıtların her birinin sonucunu derecelendirmeden başlayarak birlikte görüntüleyebilirsiniz.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Bu sıralama yanıtına göre, ana hat aşağıdaki arama sonuçlarını görüntüler:  

-   İlk Web sayfası sonucu
-   Tüm görüntüler  
-   İkinci ve üçüncü Web sayfası sonuçları  
-   Tüm videolar  
-   4, 5 ve 6 Web sayfası sonuçları  

Ve kenar çubuğu aşağıdaki arama sonuçlarını görüntüler:  

-   İlgili tüm aramalar  


## <a name="next-steps"></a>Sonraki adımlar

Derecelendirildi sonuçları yükseltme hakkında daha fazla bilgi için bkz. [derecelendirildi yanıtları yükseltme](./filter-answers.md#promoting-answers-that-are-not-ranked).

Yanıttaki derecelendirilen yanıtların sayısını sınırlama hakkında bilgi için bkz. [yanıttaki yanıt sayısını sınırlandırma](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Sonuçları görüntülemek için derecelendirme kullanan bir C# örneği için bkz. [C# derecelendirme öğreticisi](./csharp-ranking-tutorial.md).