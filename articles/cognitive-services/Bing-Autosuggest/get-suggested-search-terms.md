---
title: Bing Otomatik Öneri nedir?
titleSuffix: Azure Cognitive Services
description: Bing Otomatik Öneri API'si, arama kutusundaki kısmi sorgu dizesine göre önerilen sorguların bir listesini döndürür.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5b1e4cc8dfc89d6dcc5d29a368e089402b284352
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072865"
---
# <a name="what-is-bing-autosuggest"></a>Bing Otomatik Öneri nedir?

Uygulamanız Bing Arama API'leri herhangi birine sorgu gönderiyorsa, kullanıcılarınızın arama deneyimini geliştirmek için Bing Otomatik Öneri API'si kullanabilirsiniz. Bing Otomatik Öneri API'si, arama kutusundaki kısmi sorgu dizesine göre önerilen sorguların bir listesini döndürür. Arama kutusuna karakterler girildiğinde, önerileri bir açılan listede görüntüleyebilirsiniz.

## <a name="bing-autosuggest-api-features"></a>Bing Otomatik Öneri API'si özellikleri

| Özellik                                                                                                                                                                                 | Açıklama                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Gerçek zamanlı arama terimleri önerme](concepts/get-suggestions.md) | Otomatik öneri API 'sini kullanarak, girilen arama koşullarını göstermek için uygulama deneyiminizi geliştirebilirsiniz. |

## <a name="workflow"></a>İş akışı

Bing Otomatik Öneri API'si, HTTP istekleri yapan ve JSON 'u ayrıştırabilen herhangi bir programlama dilinden kolayca çağrılacak bir Web hizmetidir. 

1. Bing Arama API'lerine erişimi olan bir [Bilişsel Hizmetler API'si hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) oluşturun. Azure aboneliğiniz yoksa ücretsiz olarak [hesap oluşturabilirsiniz](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Her kullanıcı uygulamanızın arama kutusuna yeni bir karakter yazdığında, bu API 'ye bir istek gönderin.
3. Döndürülen JSON iletisini ayrıştırarak API yanıtını işleyin.

Genellikle, kullanıcı uygulamanızın arama kutusuna her yeni bir karakter yazdığında bu API 'YI çağırın. Daha fazla karakter girildiğinde, API daha ilgili önerilen arama sorgularını geri döndürür. Örneğin, API 'nin tek bir `s` döndürebileceğinden öneriler, `sail`ait olanlardan daha az ilgili olabilir.

Aşağıdaki örnek, Bing Otomatik Öneri API'si önerilen sorgu koşullarına sahip bir açılan arama kutusunu gösterir.

![Otomatik öneri açılır arama kutusu listesi](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Kullanıcı aşağı açılan listeden bir öneri seçtiğinde, Bing Arama API'leri birini kullanarak aramaya başlayabilir veya doğrudan Bing arama sonuçları sayfasına gidebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

İlk isteğinizi hızlı bir şekilde başlatmak için bkz. [İlk Sorgunuzu Yapma](quickstarts/csharp.md).

[Bing Otomatik Öneri API’si v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) başvurusunu inceleyin. Başvuruda, önerilen sorgu terimlerini istemek için kullanacağınız uç noktaların, üst bilgilerin ve sorgu parametrelerinin listesinin yanı sıra yanıt nesnelerinin tanımları yer alır.

[Bing Web Araması API](../bing-web-search/search-the-web.md)’sini kullanarak web’de arama yapmayı öğrenin.

Arama sonuçlarını kullanma kurallarına uygun hareket ettiğinizden emin olmak için [Bing Kullanım ve Görüntüleme Gereksinimleri](./useanddisplayrequirements.md)'ni okumayı unutmayın.
