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
ms.openlocfilehash: 0e9a71e1e826569930cf593a7e264020617bdc3a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883403"
---
# <a name="what-are-the-bing-search-apis"></a>Bing Arama API'leri nedir?

Bing Arama API'leri, Web sayfaları, resimler, Haberler, konumlar ve reklamları bulunmayan web bağlantılı uygulamalar ve hizmetler oluşturmanıza olanak sağlar. Bing Arama REST API 'Lerini veya SDK 'Larını kullanarak arama istekleri göndererek, Web aramalarında ilgili bilgileri ve içerikleri alabilirsiniz. Farklı Bing arama API 'Leri ve bilişsel aramaları uygulama ve hizmetlerinizle nasıl tümleştirebileceğinizi öğrenmek için bu makaleyi kullanın. Fiyatlandırma ve oran limitleri API 'Ler arasında farklılık gösterebilir.

## <a name="the-bing-web-search-api"></a>Bing Web Araması API'si

[Bing Web araması API'si](../Bing-Web-Search/index.yml) , Web sayfaları, görüntüler, video, Haberler ve daha fazlasını döndürür. Belirli içerik türlerini dahil etmek veya hariç tutmak için bu API 'ye gönderilen arama sorgularını filtreleyebilirsiniz.

Tüm ilgili Web içeriği türlerini aramak zorunda olabilecek uygulamalarda Bing Web Araması API'si kullanmayı düşünün. Uygulamanız belirli bir çevrimiçi içerik türünü aramalarsa aşağıdaki arama API 'Lerinden birini göz önünde bulundurun:

## <a name="content-specific-bing-search-apis"></a>İçeriğe özgü Bing arama API 'Leri

Aşağıdaki Bing arama API 'Leri, Web 'den görüntüler, Haberler, yerel işletmeler ve videolar gibi belirli içerikleri döndürür.

| Bing API 'SI | Açıklama |
| -- | -- |
| [Varlık Arama](../Bing-Entities-Search/index.yml) | Bing Varlık Arama API'si kişi, yer veya şeyler olabilen varlıklar içeren arama sonuçları döndürür. Sorguya bağlı olarak, API arama sorgusunu karşılayan bir veya daha fazla varlık döndürür. Arama sorgusu, önemli bireyler, yerel işletmeler, yer işaretleri, hedefler ve daha fazlasını içerebilir. |
| [Resim Arama](../Bing-Image-Search/index.yml) | Bing Resim Arama API'si, [Bing.com/images](https://www.Bing.com/images)ile benzer yüksek kaliteli statik ve animasyonlu görüntüleri aramanıza ve bulmanıza olanak tanır. Boyut, renk, lisans ve yenilik dahil olmak üzere, görüntüleri özniteliğe göre dahil etmek veya hariç tutmak için aramaları iyileştirebilirsiniz. Ayrıca, popüler görüntüleri arayabilir, bunları bunlarla ilgili Öngörüler elde etmek için görüntüleri karşıya yükleyebilir ve küçük resim önizlemeleri görüntüleyebilirsiniz. |
| [Haber Arama](../Bing-News-Search/index.yml) | Bing Haber Arama API'si, [Bing.com/News](https://www.Bing.com/news)benzer haber hikayelerini bulmanıza olanak tanır. API, birden fazla kaynaktan veya belirli etki alanlarından haber makaleleri döndürür. Popüler makaleleri, popüler hikayeleri ve başlıkları almak için kategoriler arasında arama yapabilirsiniz. |
| [Video Arama](../Bing-Video-Search/index.yml) | Bing Video Arama API'si Web üzerinde videoları bulmanıza olanak sağlar. Popüler videolar, ilgili içerik ve küçük resim önizlemeleri alın. |
| [Görsel Arama](../Bing-visual-search/index.yml) | Görsel açıdan benzer ürünler, görüntüler ve ilgili aramalar gibi, BT hakkında öngörülü bilgiler almak için bir görüntüyü karşıya yükleyin veya URL kullanın. |
 [Yerel Iş arama](../bing-local-business-search/index.yml) | Bing yerel Iş Arama API 'SI, uygulamalarınızın arama sorguları temelinde yerel işletmeler hakkında kişi ve konum bilgilerini bulmasını sağlar. |

## <a name="the-bing-custom-search-api"></a>Bing Özel Arama API'si

[Bing özel arama](../Bing-Custom-Search/index.yml) API ile özel bir arama örneği oluşturmak, yalnızca ilgilendiğiniz içerik ve konularda odaklanan bir arama deneyimi oluşturmanıza olanak sağlar. Örneğin, etki alanlarını, Web sitelerini ve Bing tarafından arama yapılacak belirli Web sayfalarını belirttikten sonra, Bing Özel Arama sonuçları ilgili içeriğe göre uyarlayacaktır. Arama deneyiminizi daha fazla özelleştirmek için Bing özel otomatik öneri, resim ve Video Arama API 'Lerini ekleyebilirsiniz.

## <a name="additional-bing-search-apis"></a>Ek Bing Arama API'leri

Aşağıdaki Bing Arama API'leri, diğer Bing arama API 'Leriyle birleştirerek arama deneyiminizi iyileştirmenize olanak tanır.

| API | Açıklama |
| -- | -- |
| [Bing Otomatik Öneri](../Bing-Autosuggest/index.yml) | Önerilen aramaları gerçek zamanlı döndürerek Bing Otomatik Öneri API'si ile uygulamanızın arama deneyimini geliştirebilirsiniz.  |
| [Bing Istatistikleri](bing-web-stats.md) | Bing Istatistikleri, uygulamanızın kullandığı Bing Arama API'leri için analiz sağlar. Kullanılabilir analizler çağrı hacmi, en çok sorgu dizeleri ve coğrafi dağıtım içerir. |

## <a name="next-steps"></a>Sonraki adımlar

* Bing Arama API [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* Bing Arama API'leri ile edinilen içeriğin ve bilgilerin kabul edilebilir kullanımları [Bing Kullanımı ve Görüntü Gereksinimleri](./use-display-requirements.md) konusunda belirtilmektedir.
