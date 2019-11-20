---
title: Web araması uç noktası
titleSuffix: Azure Cognitive Services
description: Web Araması sonuçlarını almak için aşağıdaki uç noktaya bir `GET` isteği gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111344"
---
# <a name="web-search-endpoint"></a>Web Araması uç noktası

**Web araması API 'Si** Web sayfaları, Haberler, görüntüler, videolar ve [varlıklar](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)döndürür. Varlıklarda bir kişi, yer veya konu hakkında özet bilgiler vardır.

## <a name="endpoint"></a>Uç Nokta

Bing API kullanarak Web Araması sonuçlarını almak için aşağıdaki uç noktaya bir `GET` isteği gönderin. Üst bilgiler ve URL parametreleri daha fazla belirtim tanımlar.

**Uç noktası**: kullanıcının `?q=""`tarafından tanımlanan arama sorgusuyla ilgili Web sonuçlarını döndürür.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Uç nokta: üstbilgiler, parametreler, Pazar kodları, yanıt nesneleri, hatalar ve daha fazlası hakkında ayrıntılar Için [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) başvurusuna bakın.

## <a name="response-json"></a>JSON yanıtı

Web araması isteğine olan yanıt, tüm sonuçları JSON nesneleri olarak içerir. Sonucun ayrıştırılması, her türün öğelerini işleyen yordamları gerektirir. Örnekler için [öğreticiye](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) ve [kaynak koduna](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) bakın.

## <a name="next-steps"></a>Sonraki adımlar

**Bing** API 'leri türlerine göre sonuçlar döndüren arama eylemlerini destekler. Tüm arama uç noktaları sonuçları JSON yanıt nesneleri olarak döndürür.  Tüm uç noktalar, Boylam, enlem ve arama yarıçapı tarafından belirli bir dili ve konumu döndüren sorguları destekler.

Her uç nokta tarafından desteklenen parametreler hakkında tüm bilgiler için, her tür için başvuru sayfalarına bakın.
Web araması API 'sini kullanan temel isteklerin örnekleri için bkz. [arama Web Hızlı başlar](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
