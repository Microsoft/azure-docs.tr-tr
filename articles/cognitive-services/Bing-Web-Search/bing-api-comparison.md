---
title: Bing Arama API'leri nedir?
titleSuffix: Azure Cognitive Services
description: Bing Arama API'leri hakkında bilgi edinmek ve uygulama ve hizmetlerinize bilişsel Internet aramalarını nasıl etkinleştirebileceğinizi öğrenmek için bu makaleyi kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349546"
---
# <a name="what-are-the-bing-search-apis"></a>Bing Arama API'leri nedir?

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Arama API'leri, web sayfalarını, resimleri, haberleri, konumları ve daha fazlasını reklamlar olmadan bulan web bağlantılı uygulamalar ve hizmetler oluşturmanıza olanak sağlar. Bing Arama REST API 'Lerini veya SDK 'Larını kullanarak arama istekleri göndererek, Web aramalarında ilgili bilgileri ve içerikleri alabilirsiniz. Farklı Bing arama API 'Leri ve bilişsel aramaları uygulama ve hizmetlerinizle nasıl tümleştirebileceğinizi öğrenmek için bu makaleyi kullanın. Fiyatlandırma ve oran limitleri API 'Ler arasında farklılık gösterebilir.

## <a name="the-bing-web-search-api"></a>Bing Web Araması API'si

[Bing Web araması API'si](../Bing-Web-Search/overview.md) , Web sayfaları, görüntüler, video, Haberler ve daha fazlasını döndürür. Belirli içerik türlerini dahil etmek veya hariç tutmak için bu API 'ye gönderilen arama sorgularını filtreleyebilirsiniz.

Tüm ilgili Web içeriği türlerini aramak zorunda olabilecek uygulamalarda Bing Web Araması API'si kullanmayı düşünün. Uygulamanız belirli bir çevrimiçi içerik türünü aramalarsa aşağıdaki arama API 'Lerinden birini göz önünde bulundurun:

## <a name="content-specific-bing-search-apis"></a>İçeriğe özgü Bing arama API 'Leri

Aşağıdaki Bing arama API 'Leri, Web 'den görüntüler, Haberler, yerel işletmeler ve videolar gibi belirli içerikleri döndürür.

| Bing API 'SI | Description |
| -- | -- |
| [Varlık Arama](../Bing-Entities-Search/overview.md) | Bing Varlık Arama API'si kişi, yer veya şeyler olabilen varlıklar içeren arama sonuçları döndürür. Sorguya bağlı olarak, API arama sorgusunu karşılayan bir veya daha fazla varlık döndürür. Arama sorgusu, önemli bireyler, yerel işletmeler, yer işaretleri, hedefler ve daha fazlasını içerebilir. |
| [Resim Arama](../Bing-Image-Search/overview.md) | Bing Resim Arama API'si, [Bing.com/images](https://www.Bing.com/images)ile benzer yüksek kaliteli statik ve animasyonlu görüntüleri aramanıza ve bulmanıza olanak tanır. Boyut, renk, lisans ve yenilik dahil olmak üzere, görüntüleri özniteliğe göre dahil etmek veya hariç tutmak için aramaları iyileştirebilirsiniz. Ayrıca, popüler görüntüleri arayabilir, bunları bunlarla ilgili Öngörüler elde etmek için görüntüleri karşıya yükleyebilir ve küçük resim önizlemeleri görüntüleyebilirsiniz. |
| [Haber Arama](../Bing-News-Search/search-the-web.md) | Bing Haber Arama API'si, [Bing.com/News](https://www.Bing.com/news)benzer haber hikayelerini bulmanıza olanak tanır. API, birden fazla kaynaktan veya belirli etki alanlarından haber makaleleri döndürür. Popüler makaleleri, popüler hikayeleri ve başlıkları almak için kategoriler arasında arama yapabilirsiniz. |
| [Video Arama](../Bing-Video-Search/overview.md) | Bing Video Arama API'si Web üzerinde videoları bulmanıza olanak sağlar. Popüler videolar, ilgili içerik ve küçük resim önizlemeleri alın. |
| [Görsel Arama](../Bing-visual-search/overview.md) | Görsel açıdan benzer ürünler, görüntüler ve ilgili aramalar gibi, BT hakkında öngörülü bilgiler almak için bir görüntüyü karşıya yükleyin veya URL kullanın. |
 [Yerel Iş arama](../bing-local-business-search/overview.md) | Bing yerel Iş Arama API 'SI, uygulamalarınızın arama sorguları temelinde yerel işletmeler hakkında kişi ve konum bilgilerini bulmasını sağlar. |

## <a name="the-bing-custom-search-api"></a>Bing Özel Arama API'si

[Bing özel arama](../Bing-Custom-Search/overview.md) API ile özel bir arama örneği oluşturmak, yalnızca ilgilendiğiniz içerik ve konularda odaklanan bir arama deneyimi oluşturmanıza olanak sağlar. Örneğin, etki alanlarını, Web sitelerini ve Bing tarafından arama yapılacak belirli Web sayfalarını belirttikten sonra, Bing Özel Arama sonuçları ilgili içeriğe göre uyarlayacaktır. Arama deneyiminizi daha fazla özelleştirmek için Bing özel otomatik öneri, resim ve Video Arama API 'Lerini ekleyebilirsiniz.

## <a name="additional-bing-search-apis"></a>Ek Bing Arama API'leri

Aşağıdaki Bing Arama API'leri, diğer Bing arama API 'Leriyle birleştirerek arama deneyiminizi iyileştirmenize olanak tanır.

| API | Açıklama |
| -- | -- |
| [Bing Otomatik Öneri](../Bing-Autosuggest/get-suggested-search-terms.md) | Önerilen aramaları gerçek zamanlı döndürerek Bing Otomatik Öneri API'si ile uygulamanızın arama deneyimini geliştirebilirsiniz.  |
| [Bing Istatistikleri](bing-web-stats.md) | Bing Istatistikleri, uygulamanızın kullandığı Bing Arama API'leri için analiz sağlar. Kullanılabilir analizler çağrı hacmi, en çok sorgu dizeleri ve coğrafi dağıtım içerir. |

## <a name="next-steps"></a>Sonraki adımlar

* Bing Arama API [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* Bing Arama API'leri ile edinilen içeriğin ve bilgilerin kabul edilebilir kullanımları [Bing Kullanımı ve Görüntü Gereksinimleri](./use-display-requirements.md) konusunda belirtilmektedir.