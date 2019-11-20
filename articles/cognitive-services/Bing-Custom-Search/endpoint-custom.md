---
title: Bing Özel Arama uç noktası
titleSuffix: Azure Cognitive Services
description: İlgilendiğiniz konularda özel arama deneyimleri oluşturun. Kullanıcılar, ilgilendikleri içeriğe göre ilgili arama sonuçlarını görür.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072779"
---
# <a name="custom-search"></a>Özel Arama
Bing Özel Arama API’si, önemsediğiniz konulara özel olarak uyarlanmış arama deneyimleri oluşturmanızı sağlar. Kullanıcılarınız, arama sonucu sayfalarındaki alakasız içeriği ayıklamak zorunda kalmadan içeriğe göre oluşturulan arama sonuçlarını görebilir.

## <a name="custom-search-endpoint"></a>Özel arama uç noktası
Bing Özel Arama API'si kullanarak sonuçları almak için aşağıdaki uç noktaya `GET` bir istek gönderin. Daha fazla belirtim tanımlamak için üstbilgiler ve URL parametreleri kullanın.

Uç noktası: `?q=""`tarafından tanımlanan Kullanıcı girişi ile ilgili olan JSON sonuçları olarak arama önerilerini döndürür.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Özel arama kaynaklarının nasıl ayarlanacağını anlatan örnekler için [öğreticiye](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)bakın. Üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar vb. hakkında ayrıntılar için [Bing özel arama API'si v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) başvurusuna bakın.

## <a name="custom-search-response-json"></a>Özel arama yanıtı JSON
Özel bir arama isteği, sonuçları JSON nesneleri olarak döndürür, bkz. [Yanıt nesneleri](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Özel otomatik öneri
Özel otomatik öneri API 'SI, Bing 'e kısmi arama sorgusu dönemi göndermenizi ve yapılandırabileceğiniz önerilen sorguların bir listesini geri almanızı sağlar. Özel otomatik önerilerle, API tarafından döndürülen önerileri ekler ve isteğe bağlı olarak Bing tarafından oluşturulan önerilerin eklenip eklenmeyeceğini belirtin.

## <a name="custom-autosuggest-endpoint"></a>Özel otomatik öneri uç noktası
Özel sorgu önerileri istemek için şunu öğesine bir GET isteği gönderin:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Özel öneriler tanımlama hakkında daha fazla bilgi için bkz. [özel arama önerileri tanımlama](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Özel Resim Arama
Özel Resim Arama API 'SI, Bing 'e bir arama sorgusu göndermenizi ve özel arama örneğinden ilgili görüntülerin bir listesini geri almanızı sağlar.

## <a name="custom-image-search-endpoint"></a>Özel Resim Arama uç noktası
Özel arama örneğinizin görüntülerini istemek için aşağıdaki URL 'ye bir GET isteği gönderin:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Özel arama örneği yapılandırma hakkında daha fazla bilgi için bkz. [özel arama deneyiminizi yapılandırma](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Sonraki adımlar
**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.  Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve/veya konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Özel Arama API'si kullanan temel isteklerin örnekleri için bkz. [özel arama hızlı başlatır](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
