---
title: Özel görünümlerinizden görüntüleri alın-Bing Özel Arama
titleSuffix: Azure Cognitive Services
description: Web 'in özel görünümünden görüntü almak için Bing Özel Arama kullanma hakkında üst düzey genel bakış.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5025a68030f5dc3aec07d33af3f98370c8d64b87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338478"
---
# <a name="get-images-from-your-custom-view"></a>Özel görünümlerinizden görüntüleri alın

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing özel görüntüler arama, özel arama deneyiminizi görüntülerle zenginleştirmenizi sağlar. Özel arama, web sonuçlarına benzer şekilde örneğinizin web sitesi listesinde görüntü aramayı destekler. Resimleri, Bing 'in özel görüntülerini arama API 'sini kullanarak veya barındırılan Kullanıcı Arabirimi özelliği aracılığıyla alabilirsiniz. Barındırılan Kullanıcı arabirimi özelliğinin kullanılması basittir ve arama deneyiminizi kısa bir sırada çalışır duruma getirmek için önerilir.  Barındırılan Kullanıcı arabirimini görüntüler içerecek şekilde yapılandırma hakkında daha fazla bilgi için bkz. [barındırılan Kullanıcı arabirimi deneyiminizi yapılandırma](hosted-ui.md).

Arama sonuçlarının görüntülenmesi üzerinde daha fazla denetime sahip olmak istiyorsanız, Bing 'in özel görüntüler arama API 'sini kullanabilirsiniz. API 'YI çağırmak Bing Resim Arama API'si çağırmaya benzer olduğundan, API 'YI çağıran örnekler için [Bing resim arama](../Bing-Image-Search/overview.md) kullanıma alın. Ancak bunu yapmadan önce [özel görüntüler arama API 'si başvuru](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) içeriği hakkında bilgi edinin. Ana farklılıklar, desteklenen sorgu parametreleridir (customConfig sorgu parametresini dahil etmeniz gerekir) ve isteklerin gönderileceği uç nokta.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->