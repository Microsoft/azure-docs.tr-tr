---
title: Bing Video Arama API'si kullanarak Web 'de popüler videolar için arama yapın
titleSuffix: Azure Cognitive Services
description: Web 'i popüler videolar için aramak üzere Bing Video Arama API'si nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500606"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Bing Video Arama API'si popüler videolar alın 

Bing Video Arama API'si, günümüzün popüler videolarını Web üzerinden ve farklı kategorilerde bulmanıza olanak sağlar. 

## <a name="get-request"></a>İsteği al

Günümüzün popüler videolarını Bing Video Arama API'si almak için aşağıdaki GET isteğini gönderin:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Pazar desteği

Aşağıdaki pazarlar, popüler videoları destekler.  
 
-   En-AU (Ingilizce, Avustralya)  
-   en-CA (Ingilizce, Kanada)  
-   en-GB (Ingilizce, Büyük Britanya)  
-   En-KIMLIĞI (Ingilizce, Endonezya)  
-   en-IE (Ingilizce, Irlanda)  
-   En-ın (Ingilizce, Hindistan)  
-   En-NZ (Ingilizce, Yeni Zelanda)  
-   En-PH (Ingilizce, Filipinler)  
-   En-SG (Ingilizce, Singapur)  
-   en-US (Ingilizce, Birleşik Devletler)  
-   En-WW (Ingilizce, dünya çapında toplam kod)  
-   En-ZA (Ingilizce, Güney Afrika)  
-   zh-CN (Çince, Çin)

## <a name="example-json-response"></a>Örnek JSON yanıtı  

Aşağıdaki örnek, kategori ve alt kategori ile listelenmiş popüler videolar içeren bir API yanıtı gösterir. Yanıt Ayrıca en popüler popüler videolar olan başlık videoları içerir ve bir veya daha fazla kategoriden gelebilir.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Video öngörüleri edinin](video-insights.md)