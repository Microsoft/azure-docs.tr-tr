---
title: Web araması uç noktası
titleSuffix: Azure Cognitive Services
description: Web Araması sonuçlarını almak için `GET` aşağıdaki uç noktaya bir istek gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94381261"
---
# <a name="web-search-endpoint"></a>Web Araması uç noktası

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Web araması API 'Si** Web sayfaları, Haberler, görüntüler, videolar ve [varlıklar](../bing-entities-search/overview.md)döndürür. Varlıklarda bir kişi, yer veya konu hakkında özet bilgiler vardır.

## <a name="endpoint"></a>Uç Nokta

Bing API kullanarak Web Araması sonuçlarını almak için `GET` aşağıdaki uç noktaya bir istek gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.

**Uç nokta**: kullanıcının tarafından tanımlanan arama sorgusuyla ilgili Web sonuçlarını döndürür `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Uç nokta: üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar ve daha fazlası hakkında ayrıntılar Için [Bing Web API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) başvurusuna bakın.

## <a name="response-json"></a>JSON yanıtı

Web araması isteğine olan yanıt, tüm sonuçları JSON nesneleri olarak içerir. Sonucun ayrıştırılması, her türün öğelerini işleyen yordamları gerektirir. Örnekler için [öğreticiye](./tutorial-bing-web-search-single-page-app.md) ve [kaynak koduna](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) bakın.

## <a name="next-steps"></a>Sonraki adımlar

**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.  Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Web araması API 'sini kullanan temel isteklerin örnekleri için bkz. [arama Web Hızlı başlar](./overview.md).