---
title: Konuşmayı metne dönüştürme-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşmadan metne özelliği, uygulamaların, araçların veya cihazlarınızın komut girişi olarak tüketebileceği, görüntüleyeceği ve eylemde bulunduğu bir şekilde ses akışlarının gerçek zamanlı olarak dökümünü sağlar. Bu hizmet, metinden konuşmaya (konuşma birleştirme) ve konuşma çevirisi özellikleriyle sorunsuz bir şekilde çalışır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075727"
---
# <a name="what-is-speech-to-text"></a>Konuşmayı metne dönüştürme nedir?

Azure konuşma hizmetlerinden konuşmaya metin olarak da bilinen konuşmadan metne dönüştürme, ses akışlarının gerçek zamanlı olarak kullanımını, uygulamalar, araçlar ya da cihazlarınızın komut girişi olarak tüketebileceği, görüntülemesi ve eylem yapması için kullanabileceği bir metne olanak sağlar. Bu hizmet, Microsoft 'un Cortana ve Office ürünleri için kullandığı aynı tanıma teknolojisi tarafından desteklenir ve çeviri ve metinden konuşmaya sorunsuz bir şekilde çalışır. Kullanılabilir konuşmadan metne yönelik dillerin tam listesi için bkz. [desteklenen diller](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Varsayılan olarak, konuşma-metin hizmeti, evrensel dil modelini kullanır. Bu model, Microsoft 'a ait veriler kullanılarak eğitildi ve buluta dağıtıldı. Konuşma ve dikte senaryoları için idealdir. Benzersiz bir ortamda tanıma ve döküm için konuşmaya metin kullanıyorsanız, çevresel gürültü veya sektöre özgü sözlük için özel akustik, dil ve telaffuz modeller oluşturup eğitebilirsiniz.

Konuşmayı bir mikrofondan kolayca yakalayabilir, bir akıştan okuyabilir veya ses dosyalarına konuşma SDK 'Sı ve REST API 'Leri ile erişebilirsiniz. Konuşma SDK'sı WAV/PCM 16 bit, 16 kHz/8 kHz, tek kanallı sesten konuşma tanıma gerçekleştirebilir. [Konuşmayı metne dönüştürme REST uç noktası](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) veya [toplu transkripsiyon hizmeti](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats) ile ek ses biçimleri için de destek sunulmaktadır.

## <a name="core-features"></a>Temel Özellikler

Konuşma SDK 'Sı ve REST API 'Leri aracılığıyla kullanılabilen özellikler şunlardır:

| Kullanım örneği | SDK | REST |
|--------- | --- | ---- |
| Kısa mesafeli (15 saniye <). Yalnızca son döküm sonucunu destekler. | Yes | Yes |
| Uzun ifade ve akış sesinin sürekli olarak dökümünü alma (> 15 saniye). , Geçici ve son döküm sonuçlarını destekler. | Yes | Hayır |
| [Lusıs](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)ile tanıma sonuçlarından hedefleri türetebilirsiniz. | Yes | Yok\* |
| Ses dosyalarının zaman uyumsuz olarak toplu olarak dökümünü alma. | Hayır  | Evet\*\* |
| Konuşma modellerini oluşturun ve yönetin. | Hayır | Evet\*\* |
| Özel model dağıtımları oluşturun ve yönetin. | Hayır  | Evet\*\* |
| Özel modellere karşı taban çizgisi modelinin doğruluğunu ölçmek için doğruluk testleri oluşturun. | Hayır  | Evet\*\* |
| Abonelikleri yönetin. | Hayır  | Evet\*\* |

\*_luya amaçları ve varlıkları ayrı bır Luo aboneliği kullanılarak türetilebilir. Bu abonelikle, SDK BASıS 'yi çağırabilir ve varlık ve amaç sonuçları sağlayabilir. REST API ile, LUSıS aboneliğiniz ile hedefleri ve varlıkları türetmek için LUSıS 'yi çağırabilirsiniz._

_Bu hizmetlere \*\*, Cris.ai uç noktası kullanılarak kullanılabilir. Bkz. [Swagger başvurusu](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Konuşmaya metne Başlarken

Her biri 10 dakikadan kısa bir süre içinde kod çalıştırmak için tasarlanan en popüler programlama dillerinde hızlı başlangıçları sunuyoruz. [Bu tablo](https://aka.ms/csspeech#5-minute-quickstarts) , bir konuşma SDK 'sı hızlı başlangıç listesini platform ve dile göre düzenlenmiş olarak içerir. Ayrıca, API başvurusu [burada](https://aka.ms/csspeech#reference)bulunabilir.

Konuşmayı metne REST hizmetini kullanmayı tercih ediyorsanız bkz. [REST API 'leri](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Öğreticiler ve örnek kod

Konuşma hizmetlerini kullanma şansınız olduktan sonra, konuşma SDK 'sını ve LUO kullanarak konuşmayı nasıl anlayacağınızı öğreten Öğreticimizi deneyin.

- [Öğretici: konuşma SDK 'Sı ve LUSıS ile konuşma amaçlarını tanımaC#](how-to-recognize-intents-from-speech-csharp.md)

Konuşma SDK 'Sı için örnek kod GitHub ' da kullanılabilir. Bu örnekler, bir dosya veya akıştan ses okuma, sürekli ve tek kararlı bir tanıma ve özel modellerle çalışma gibi yaygın senaryoları kapsar.

- [Konuşmaya metin örnekleri (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Toplu iş dökümü örnekleri (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Özelleştirme

Konuşma Hizmetleri tarafından kullanılan standart taban çizgisi modeline ek olarak, konuşma stili, sözlük ve arka plan gürültüsü gibi konuşma tanıma engellerini aşmak için, kullanılabilir verilerle modelleri gereksinimlerinize göre özelleştirebilirsiniz. [özel konuşma tanıma](how-to-custom-speech.md)

> [!NOTE]
> Özelleştirme seçenekleri dile/yerel ayara göre değişir (bkz. [desteklenen diller](supported-languages.md)).

## <a name="migration-guides"></a>Geçiş kılavuzları

> [!WARNING]
> Bing Konuşma, 15 Ekim 2019 ' de kullanımdan çıkarıldı.

Uygulamalarınız, araçlar veya ürünleriniz Bing Konuşma API 'Leri veya Özel Konuşma Tanıma kullanıyorsa, konuşma hizmetlerine geçiş yapmanıza yardımcı olacak kılavuzlar oluşturduk.

- [Bing Konuşma konuşma hizmetlerine geçirme](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Özel Konuşma Tanıma konuşma hizmetlerine geçirme](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Başvuru belgeleri

- [Konuşma SDK'sı](https://aka.ms/csspeech)
- [Konuşma cihazları SDK 'Sı](speech-devices-sdk.md)
- [REST API: konuşmayı metne dönüştürme](rest-speech-to-text.md)
- [REST API: metinden konuşmaya](rest-text-to-speech.md)
- [REST API: toplu Iş dökümü ve özelleştirme](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Sonraki adımlar

- [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
- [Konuşma SDK 'sını alın](speech-sdk.md)
