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
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072884"
---
# <a name="suggesting-query-terms"></a>Sorgu terimi önerme

Genellikle, bir kullanıcının uygulamanızın arama kutusuna yeni bir karakter her yazdığında Bing Otomatik Öneri API'si çağırır. Sorgu dizesinin eksiksiz olması, API’nin döndürdüğü önerilen sorgu terimlerinin alakasını etkiler. Sorgu dizesi ne kadar eksiksizse, önerilen sorgu terimlerinin listesi de o kadar alakalıdır. Örneğin, `s` için API 'nin döndürebilecek önerilerin, `sailing dinghies`döndürdüğü sorgulardan daha az ilgili olması olasıdır.

## <a name="example-request"></a>Örnek istek

Aşağıdaki örnekte, *sail* için önerilen sorgu dizelerini döndüren bir istek gösterilmektedir. [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) sorgu parametresini ayarladığınızda, kullanıcının kısmi sorgu terimini URL kodlamayı unutmayın. Örneğin, kullanıcı *sailing dinghies* terimini girdiyse, `q` öğesini `sailing+les` veya `sailing%20les` olarak ayarlayın.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Aşağıdaki yanıt, önerilen sorgu terimlerini içeren [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) nesnelerinin bir listesini içerir.

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

Kullanıcı, açılır listeden bir önerilen sorgu seçerse, `query` alanındaki sorgu terimini kullanarak [Bing Web Araması API](../../bing-web-search/search-the-web.md)’sini çağırır ve sonuçları kendiniz görüntülersiniz. Veya bunun yerine kullanıcıyı Bing arama sonuçları sayfasına göndermek için `url` alanındaki URL’yi kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Otomatik Öneri API'si nedir?](../get-suggested-search-terms.md)