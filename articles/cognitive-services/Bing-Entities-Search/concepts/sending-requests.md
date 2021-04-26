---
title: Bing Varlık Arama API'si arama istekleri gönderiliyor
titleSuffix: Azure cognitive Services
description: Bing Varlık Arama API'si, Bing'e bir arama sorgusu gönderip varlıkları ve yerleri içeren sonuçlar alır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353281"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Bing Varlık Arama API'si arama istekleri gönderiliyor

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Varlık Arama API'si, Bing'e bir arama sorgusu gönderip varlıkları ve yerleri içeren sonuçlar alır. Yer sonuçları restoranlar, oteller veya diğer yerel işletmeleri kapsar. Yerler için sorguda yerel işletmenin adı belirtilebilir veya liste isteği (yakınımdaki restoranlar gibi) gönderilebilir. Varlık sonuçları kişileri, yerleri veya nesneleri kapsar. Bu bağlamdaki yer, bir yerde, eyalet, ülke/bölge vb. için de kullanılır.

## <a name="the-endpoint"></a>Uç nokta

Varlık ve yer arama sonuçlarını almak için aşağıdaki uç noktaya bir GET isteği gönderin:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

İsteklerin HTTPS protokolünü kullanması gerekir.

Tüm isteklerin bir sunucudan gönderilmesini öneririz. Anahtarı bir istemci uygulamanın parçası olarak dağıtmak, kötü amaçlı bir üçüncü tarafa anahtara erişmek için daha fazla fırsat sunar. Ayrıca bir sunucudan çağrı yapmak API'nin gelecek sürümleri için tek bir yükseltme noktası sağlar.

## <a name="specifying-query-parameters-and-headers"></a>Sorgu parametrelerini ve üst bilgileri belirtme

İstek kullanıcının arama terimini içeren [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) sorgu parametresini belirtmelidir. İstek, sonuçların gelmesini istediğiniz pazarı tanımlayan [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) sorgu parametresini de belirtmelidir. İsteğe bağlı sorgu parametrelerinin bir listesi için bkz. [Sorgu Parametreleri](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters). Tüm sorgu parametrelerini URL'de kodlayın.  
  
İstek [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey) üstbilgisini belirtmelidir. İsteğe bağlı olmakla birlikte şu üstbilgileri de belirtmeniz önerilir:  
  
-   [Kullanıcı Aracısı](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-Clienentip](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-arama konumu](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

İstemci IP'si ve konum üstbilgileri konuma duyarlı içerik döndürmek için önemlidir.  

Tüm istek ve yanıt üstbilgilerinin bir listesi için bkz. [Üstbilgiler](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>İstek

Aşağıda önerilen tüm sorgu parametrelerini ve üstbilgilerini içeren bir varlık isteği gösterilmektedir. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Bing API'lerinden birini ilk kez çağırıyorsanız istemci kimliği üst bilgisini eklemeyin. İstemci kimliğini yalnızca önceden bir Bing API'sini çağırdıysanız ve Bing, kullanıcı ve cihaz birleşimi için bir istemci kimliği döndürdüyse dahil edin.

## <a name="the-response"></a>Yanıt

Aşağıda, bir önceki isteğin yanıtı gösterilmektedir. Örnek ayrıca Bing’e özgü yanıt üst bilgilerini de göstermektedir. Yanıt nesnesi hakkında bilgi için bkz. [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Sonraki adımlar

* [Bing varlık API 'SI ile varlıkları arama](search-for-entities.md)
* [Bing API kullanımı ve görüntüleme gereksinimleri](../../bing-web-search/use-display-requirements.md)