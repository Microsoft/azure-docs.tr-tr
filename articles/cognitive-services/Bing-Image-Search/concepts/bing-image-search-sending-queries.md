---
title: Görüntü arama sorguları özelleştirme ve önerme-Bing Resim Arama API'si
titleSuffix: Azure Cognitive Services
description: Bing Resim Arama API'si göndereceğiniz arama sorgularını özelleştirme hakkında bilgi edinin.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 2566b2cf950df915f8ea843c34ea1fb6f8e7ea21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342014"
---
# <a name="customize-and-suggest-image-search-queries"></a>Görüntü arama sorguları özelleştirme ve önerme

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Sorguları özelleştirmeyi ve Bing Resim Arama API'si göndermek için arama terimleri önereceğinizi öğrenmek için bu makaleyi kullanın.

## <a name="suggest-search-terms"></a>Arama terimleri öner

Uygulamanızın arama koşullarının girildiği bir arama kutusu varsa, deneyimi geliştirmek için [Bing otomatik öneri API'si](../../bing-autosuggest/get-suggested-search-terms.md) kullanabilirsiniz. API, önerilen arama terimlerini gerçek zamanlı olarak gösterebilir. API, kısmi arama terimleri ve bilişsel hizmetler temelinde önerilen Sorgu dizelerini döndürür.

## <a name="pivot-the-query"></a>Sorguyu özetleyin

Bing özgün arama sorgusunu segmentlebiliyorsanız döndürülen [görüntüler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) nesnesi içerir `pivotSuggestions` . Özet öneriler, Kullanıcı için isteğe bağlı arama terimi olarak görüntülenebilir. Örneğin, özgün sorgu *Microsoft Surface* ise, Bing sorguyu *Microsoft* ve *Surface* 'a segmentedebilir ve her biri için önerilen özetleri sağlayabilir. Bu öneriler Kullanıcı için isteğe bağlı sorgu koşulları olarak görüntülenebilir.

Aşağıdaki örnek, *Microsoft yüzeyi* için pivot önerilerini göstermektedir:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

`pivotSuggestions` alanında özgün sorgunun ayrıldığı parçaların (özetler) listesi bulunur. Her bir özet terim için gelen yanıtta önerilen sorguları içeren [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) nesnelerinin listesi yer alır. `text`Alan önerilen sorguyu içerir. `displayText`Alan, özgün sorgudaki özetin yerini alan terimi içerir. Surface 'ın Yayın tarihi bir örnektir.

Özet sorgu dizesi kullanıcının aradıkı ise, `text` `thumbnail` Özet sorgu dizelerini göstermek için ve alanlarını kullanın. `webSearchUrl`URL 'yi veya URL 'yi kullanarak küçük resmi ve metni tıklatılabilir yapın `searchLink` . `webSearchUrl`Kullanıcıyı Bing arama sonuçlarına göndermek için kullanın. Kendi sonuç sayfanızı sağlarsanız, kullanın `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Sorguyu Genişlet

Bing özgün aramayı daraltmak için sorguyu genişletebiliyorsa [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) nesnesi `queryExpansions` alanını içerir. Örneğin, sorgu *Microsoft yüzeyiydi*, genişletilmiş sorgular şu şekilde olabilir:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **hub**.

Aşağıdaki örnekte *Microsoft Surface* için genişletilmiş sorgular gösterilmektedir.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` alanı [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) nesnelerinin listesini içerir. `text`Alan, genişletilmiş sorguyu içerir. `displayText`Alan, genişletme terimini içerir. Genişletilmiş sorgu dizesi kullanıcının arama yaptığı şeydir, `text` `thumbnail` genişletilmiş sorgu dizelerini göstermek için ve alanlarını kullanın. `webSearchUrl`URL 'yi veya URL 'yi kullanarak küçük resmi ve metni tıklatılabilir yapın `searchLink` . `webSearchUrl`Kullanıcıyı Bing arama sonuçlarına göndermek için kullanın. kendi sonuç sayfanızı sağlarsanız, kullanın `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>İstekleri azaltma

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daha önce Bing Resim Arama API'si denemediyseniz [hızlı](../quickstarts/csharp.md)başlangıcı deneyin. Daha karmaşık bir şeyler arıyorsanız, [tek sayfalı bir Web uygulaması](../tutorial-bing-image-search-single-page-app.md)Oluşturma öğreticisini deneyin.