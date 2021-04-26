---
title: Bing Otomatik Öneri API'si ile arama koşullarını önerme
titleSuffix: Azure Cognitive Services
description: Bu makalede, Bing Otomatik Öneri API'si kullanarak sorgu koşullarını önerme kavramı ve sorgu uzunluğunun ilgi açısından etkisi ele alınmaktadır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: be7686c4d8a676d2a1d85516d2e4aa6abe3f3bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353417"
---
# <a name="suggesting-query-terms"></a>Sorgu terimi önerme

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Genellikle, bir kullanıcının uygulamanızın arama kutusuna yeni bir karakter her yazdığında Bing Otomatik Öneri API'si çağırır. Sorgu dizesinin eksiksiz olması, API’nin döndürdüğü önerilen sorgu terimlerinin alakasını etkiler. Sorgu dizesi ne kadar eksiksizse, önerilen sorgu terimlerinin listesi de o kadar alakalıdır. Örneğin, API 'nin döndürebilecek önerilerin, `s` döndürdüğü sorgulardan daha az ilgili olması olasıdır `sailing dinghies` .

## <a name="example-request"></a>Örnek istek

Aşağıdaki örnekte, *sail* için önerilen sorgu dizelerini döndüren bir istek gösterilmektedir. [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) sorgu parametresini ayarladığınızda, kullanıcının kısmi sorgu terimini URL kodlamayı unutmayın. Örneğin, kullanıcı *sailing dinghies* terimini girdiyse, `q` öğesini `sailing+les` veya `sailing%20les` olarak ayarlayın.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Aşağıdaki yanıt, önerilen sorgu terimlerini içeren [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) nesnelerinin bir listesini içerir.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Önerilen sorgu koşullarını kullanma

Her öneri, `displayText`, `query` ve `url` alanını içerir. `displayText` alanı, arama kutunuzun açılır listesini doldurmak için kullandığınız önerilen sorguyu içerir. Yanıtın içerdiği tüm önerileri, verilen sırada görüntülemeniz gerekir.

Aşağıdaki örnek, Bing Otomatik Öneri API'si önerilen sorgu koşullarına sahip bir açılan arama kutusunu gösterir.

![Otomatik öneri açılır arama kutusu listesi](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Kullanıcı, açılır listeden bir önerilen sorgu seçerse, `query` alanındaki sorgu terimini kullanarak [Bing Web Araması API](../../bing-web-search/overview.md)’sini çağırır ve sonuçları kendiniz görüntülersiniz. Veya bunun yerine kullanıcıyı Bing arama sonuçları sayfasına göndermek için `url` alanındaki URL’yi kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Otomatik Öneri API’si nedir?](../get-suggested-search-terms.md)