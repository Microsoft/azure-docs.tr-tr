---
title: Özel görünüminizden videolar alın-Bing Özel Arama
titleSuffix: Azure Cognitive Services
description: Web 'in özel görünümünden videoları almak için Bing Özel Arama kullanma hakkında üst düzey genel bakış.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4e2bb14519f8ed4e3e4a0e9ac8800957b30229a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338427"
---
# <a name="get-videos-from-your-custom-view"></a>Özel görünüminizden videoları alın

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing özel videoları arama, videolar ile özel arama deneyiminizi zenginleştirmenizi sağlar. Özel arama, web sonuçlarına benzer şekilde örneğinizin web sitesi listesinde video aramayı destekler. Videoları, Bing 'in özel video arama API 'sini kullanarak veya barındırılan Kullanıcı Arabirimi özelliği aracılığıyla alabilirsiniz. Barındırılan Kullanıcı arabirimi özelliğinin kullanılması basittir ve arama deneyiminizi kısa bir sırada çalışır duruma getirmek için önerilir. Barındırılan Kullanıcı arabiriminizi videoları içerecek şekilde yapılandırma hakkında daha fazla bilgi için bkz. [barındırılan Kullanıcı arabirimi deneyiminizi yapılandırma](hosted-ui.md).

Arama sonuçlarının görüntülenmesi üzerinde daha fazla denetim istiyorsanız, Bing 'in özel videolar arama API 'sini kullanabilirsiniz. API 'YI çağırmak Bing Video Arama API'si çağırmaya benzer olduğundan, API 'YI çağıran örnekler için [Bing video arama](../bing-video-search/overview.md) kullanıma alın. Ancak bunu yapmadan önce [özel videolar arama API 'si başvuru](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) içeriği hakkında bilgi edinin. Ana farklılıklar, desteklenen sorgu parametreleridir (customConfig sorgu parametresini dahil etmeniz gerekir) ve isteklerin gönderileceği uç nokta.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->