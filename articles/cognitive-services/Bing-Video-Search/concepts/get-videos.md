---
title: Bing Video Arama API'si kullanarak video arayın
titleSuffix: Azure Cognitive Services
description: Bing Video Arama Apifınds ve Web 'den ilgili videoları geri döndürmektedir, Web 'de akıllı ve odaklanmış video alımı için çeşitli özellikler sağlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 5add9597924aa77ede875d0056e83eceb4f99598
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111427"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Bing Video Arama API'si videoları arayın

Bing Video Arama API'si, Bing 'in bilişsel haber arama yeteneklerini uygulamalarınıza tümleştirmeyi kolaylaştırır. API birincil olarak Web 'den ilgili videoları bulur ve geri döndürürse, Web 'de akıllı ve odaklanmış video alımı için çeşitli özellikler sağlar.

## <a name="getting-videos"></a>Videoları alma

Web'den kullanıcının arama terimiyle ilgili videoları almak için aşağıdaki GET isteğini gönderin:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Tüm isteklerin bir sunucudan yapılması gerekir.

Bing API'lerinden birini ilk kez çağırıyorsanız istemci kimliği üst bilgisini eklemeyin. İstemci kimliğini yalnızca önceden bir Bing API'sini çağırdıysanız ve Bing, kullanıcı ve cihaz birleşimi için bir istemci kimliği döndürdüyse dahil edin.

Belirli bir etki alanındaki videoları almak için [site:](https://msdn.microsoft.com/library/ff795613.aspx) dize işlecini kullanın.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Yanıt, Bing'in sorguyla ilişkili olduğunu düşündüğü videoları bir listesini içeren [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) yanıtı içerir. Listedeki her [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) nesneyi videonun URL’sini, süresini, boyutlarını, kodlama biçimini ve diğer özniteliklerini içerir. Video nesnesi aynı zamanda video küçük resminin URL'sini ve küçük resmin boyutlarını içerir.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Video küçük resimleri

Bing Video Arama API'si tarafından döndürülen video küçük resimlerinin tümünü veya bir alt kümesini görüntüleyebilirsiniz. Alt küme görüntülerseniz kullanıcıya kalan videoları görüntüleme seçeneği sunun. Bing API [kullanımı ve görüntüleme gereksinimlerinin](../UseAndDisplayRequirements.md)bir parçası olarak, Videoları yanıtta belirtilen sırada görüntülerinizin olması gerekir. Küçük resmi yeniden boyutlandırma hakkında bilgi için bkz. [Küçük Resimleri Yeniden Boyutlandırma ve Kırpma](../../bing-web-search/resize-and-crop-thumbnails.md). 

Kullanıcı küçük resmin üzerine geldiğinde videonun küçük resim bir sürümünü oynatmak için [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) kullanabilirsiniz. Hareket küçük resmini görüntülediğinizde öznitelik belirlediğinizden emin olun.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Küçük resme tıklandığında, videoyu görüntülemek için üç seçenek vardır:

- Videoyu konak web sitesinde (örneğin YouTube) görüntülemek için [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) kullanın
- Videoyu Bing video tarayıcısında görüntülemek için [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) kullanın
- Videoyu kendi deneyiminize eklemek için [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) kullanın 

Videoyu oynatırken yayımcı ve oluşturucu özniteliklerini kullandığınızdan emin olun.

Video hakkında öngörüler almak için [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) kullanma hakkında daha fazla bilgi için bkz. [Video Öngörüleri](../video-insights.md).

## <a name="filtering-videos"></a>Videoları filtreleme

Video Arama API'si varsayılan olarak sorguyla ilgili tüm videoları döndürür. Yalnızca ücretsiz videolar veya uzunluğu beş dakikayı aşmayan videolar istiyorsanız, aşağıdaki filtre sorgu parametrelerini kullanırsınız:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash;Videoları fiyatlandırmaya göre filtreleyin (örneğin, ücretsiz videolar veya ücret ödemeniz gereken videolar)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash;Videoları çözünürlüğe göre filtrele (örneğin, 720p veya daha yüksek çözünürlüğe sahip videolar)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash;Videoları video uzunluğuna göre filtreleyin (örneğin, beş dakikadan kısa videolar)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash;Videoları yaşa göre filtreleyin (Bing'in geçen hafta keşfettiği videolar gibi)

Belirli bir etki alanındaki videoları almak için sorgu dizesine [site:](https://msdn.microsoft.com/library/ff795613.aspx) dize işlecini ekleyin.

> [!NOTE]
> Sorguya bağlı olarak `site:` sorgu işlecini kullanmanız halinde [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) ayarından bağımsız olarak yanıtta yetişkinlere yönelik içerik bulunabilir. `site:` işlecini yalnızca sitenin içeriği hakkında bilgi sahibiyseniz ve senaryonuz, yetişkinlere yönelik içeriğin mevcut olma ihtimalini destekliyorsa kullanın.

Aşağıdaki örnekte ContosoSailing.com adresinden 720p veya daha iyi çözünürlüğe sahip ve Bing’in geçen ay içinde bulduğu ücretsiz videoların nasıl alındığı gösterilmektedir.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Sorguyu genişletme

Bing özgün aramayı daraltmak için sorguyu genişletebiliyorsa [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) nesnesi `queryExpansions` alanını içerir. Örneğin, sorgu *Boşluk Temizleme* ise, genişletilmiş sorgular şunlar olabilir: Boşluk Temizleme **Araçları**, **Sıfırdan** Boşluk Temizleme, Boşluk Temizleme **Makinesi** ve **Kolay** Boşluk Temizleme.

Aşağıdaki örnekte *Boşluk Temizleme* için genişletilmiş sorgular gösterilmektedir.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` alanı [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) nesnelerinin listesini içerir. `text` alanı genişletilmiş sorguyu, `displayText` alanı ise genişletme terimini içerir. Metin ve küçük resim alanlarını kullanarak genişletilmiş sorgu dizelerini kullanıcıya gösterebilir ve kullanıcının bu genişletilmiş sorgu dizelerinden seçim yapmasını sağlayabilirsiniz. `webSearchUrl` URL veya `searchLink` URL kullanarak küçük resmi ve metni tıklanabilir hale getirebilirsiniz. `webSearchUrl` kullanarak kullanıcıyı Bing arama sonuçlarına gönderebilir veya `searchLink` ile kendi sonuç sayfanızı sunabilirsiniz.

## <a name="pivoting-the-query"></a>Sorguyu özetleme

Bing özgün arama sorgusunu parçalara ayırabiliyorsa [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) nesnesi `pivotSuggestions` alanını içerir. Örneğin, özgün sorgu *Boşluk Temizleme* ise Bing bunu *Boşluk* ve *Temizleme* olmak üzere ikiye bölebilir.

Aşağıdaki örnekte *Boşluk Temizleme* için öneri özetleri gösterilmektedir.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Her bir özet terim için gelen yanıtta önerilen sorguları içeren [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) nesnelerinin listesi yer alır. `text` alanı önerilen sorguyu, `displayText` alanı ise özgün sorguda özetin yerine kullanılan terimi içerir. Örneğin, Pencere Temizleme.

`text` ve `thumbnail` alanlarını kullanarak genişletilmiş sorgu dizelerini kullanıcıya gösterebilir ve kullanıcının bu genişletilmiş sorgu dizelerinden seçim yapmasını sağlayabilirsiniz. `webSearchUrl` URL veya `searchLink` URL kullanarak küçük resmi ve metni tıklanabilir hale getirebilirsiniz. `webSearchUrl` kullanarak kullanıcıyı Bing arama sonuçlarına gönderebilir veya `searchLink` ile kendi sonuç sayfanızı sunabilirsiniz.

## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
