---
title: Bing Varlık Arama API'si nedir?
titleSuffix: Azure Cognitive Services
description: Arama sorgularından varlıkları ve yerleri ayıklamak ve aramak için Bing Varlık Arama API'si kullanın.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 8f43401296a154ee40e7c214ad63da878129244a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424020"
---
# <a name="what-is-bing-entity-search-api"></a>Bing Varlık Arama API'si nedir?

Bing Varlık Arama API'si, Bing'e bir arama sorgusu gönderip varlıkları ve yerleri içeren sonuçlar alır. Yer sonuçları restoranlar, oteller veya diğer yerel işletmeleri kapsar. Sorguda yerel işletmenin adı belirtildiğinde veya bir işletme türü istendiğinde (yakınımdaki restoranlar gibi) Bing, yerleri döndürür. Sorgu iyi bilinen kişileri, yerleri (toist, eyalet, ülke/bölge vb.) veya şeyleri belirtiyorsa Bing varlık döndürür.

|Özellik  |Açıklama  |
|---------|---------|
|[Gerçek zamanlı arama önerileri](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Kullanıcılarınızın yazmasıyla açılan bir liste olarak görüntülenebilen arama önerileri sağlar.       | 
| [Varlık Kesinleştirme](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Birden çok olası anlam içeren sorgular için birden çok varlık alın. |
| [Yerleri bul](concepts/search-for-entities.md#find-places) | Yerel işletmeler ve varlıklar hakkında bilgi arayın ve geri dönüş yapın  |

## <a name="workflow"></a>İş akışı

Bing Varlık Arama API'si, yeniden bir Web hizmetidir ve HTTP istekleri yapıp JSON 'u ayrıştırabilen herhangi bir programlama dilinden çağrı yapmayı kolaylaştırır. REST API veya SDK kullanarak hizmeti kullanabilirsiniz.

1. Bing Arama API'lerine erişimi olan bir [Bilişsel Hizmetler API'si hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) oluşturun. Azure aboneliğiniz yoksa ücretsiz olarak [hesap oluşturabilirsiniz](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. API 'ye, geçerli bir arama sorgusuyla bir istek gönderin.
3. Döndürülen JSON iletisini ayrıştırarak API yanıtını işleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bing Varlık Arama API'si için [etkileşimli tanıtımı](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) deneyin. 
* İlk isteğinizi hızlı bir şekilde kullanmaya başlamak için hızlı [Başlangıç](quickstarts/csharp.md)yapmayı deneyin.
* [Bing varlık arama API'si v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) Reference bölümü.
* Bing Arama API'leri ile edinilen içeriğin ve bilgilerin kabul edilebilir kullanımları [Bing Kullanımı ve Görüntü Gereksinimleri](./use-display-requirements.md) konusunda belirtilmektedir.
