---
title: Konuşma hizmeti ile konuşma çevirisi
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti, uygulamalarınıza, araçlara ve cihazlarınıza uçtan uca, gerçek zamanlı, çok dilli bir konuşma çevirisi eklemenizi sağlar. Aynı API, konuşma ve konuşmadan metne çevirme için de kullanılabilir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 17ad32cba18915bf7f83163cd876686d42323750
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468749"
---
# <a name="what-is-speech-translation"></a>Konuşma çevirisi nedir?

Azure konuşma hizmetlerinden konuşma çevirisi, ses akışlarının gerçek zamanlı, çok dilli konuşmadan konuşmaya ve konuşmadan metne çevirisini sağlar. Konuşma SDK 'Sı ile uygulamalarınızın, araçların ve cihazlarınızın, sunulan ses için kaynak dökümlerini ve çeviri çıkışlarını erişimi vardır. Okuma tespit edildiğinde ve değişim sonuçları sentezleştirilmiş konuşmaya dönüştürülebileceğinden, geçici döküm ve çeviri sonuçları döndürülür.

Microsoft 'un çeviri altyapısı iki farklı yaklaşımdan desteklenir: istatistiksel makine çevirisi (SMT) ve sinir makine çevirisi (NMT). SMT, birkaç sözcük bağlamında mümkün olan en iyi çevirileri tahmin etmek için gelişmiş istatistiksel analizler kullanır. NMT ile, sözcükleri çevirmek için Tümcelerin tam bağlamını kullanarak daha doğru, doğal bir şekilde daha doğru, doğal bir çeviri sağlamak için sinir Networks kullanılır.

Günümüzde Microsoft, en popüler dillere çeviri için NMT kullanır. [Konuşma tanıma çevirisi için kullanılabilen tüm diller](language-support.md#speech-translation) NMT tarafından desteklenmektedir. Konuşmaya metin çevirisi, dil çiftine göre SMT veya NMT kullanabilir. Hedef dil NMT tarafından desteklenirken, tam çeviri NMT destekli olur. Hedef dil NMT tarafından desteklenmediğinde, çeviri, iki dil arasında "pivot" olarak Ingilizce kullanan NMT ve SMT 'in bir karması olur.

## <a name="core-features"></a>Temel Özellikler

Konuşma SDK 'Sı ve REST API 'Leri aracılığıyla kullanılabilen özellikler şunlardır:

| Kullanım örneği | SDK | REST |
|----------|-----|------|
| Tanıma sonuçlarıyla konuşmaya metin çevirisi. | Evet | Hayır |
| Konuşmadan konuşmaya çevirisi. | Evet | Hayır |
| Ara tanıma ve çeviri sonuçları. | Evet | Hayır |

## <a name="get-started-with-speech-translation"></a>Konuşma çevirisi 'ni kullanmaya başlama

Kodu, 10 dakikadan kısa bir süre içinde çalıştırmak için tasarlanan hızlı başlangıç sunuyoruz. Bu tablo, dile göre düzenlenen konuşma çevirisi hızlı başlangıçlarını bir listesini içerir.

| Hızlı Başlangıç | Platform | API başvurusu |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Ata](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Ata](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Ata](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Ata](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Ata](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Örnek kod

Konuşma SDK 'Sı için örnek kod GitHub ' da kullanılabilir. Bu örnekler, bir dosya veya akıştan ses okuma, sürekli ve tek kararlı tanıma/çeviri gibi yaygın senaryoları kapsar ve özel modellerle çalışır.

* [Konuşmadan metne ve çeviri örnekleri (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Geçiş kılavuzları

Uygulamalarınız, araçlar veya ürünleriniz [Translator konuşma çevirisi API'si](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)kullanıyorsa, konuşma hizmetlerine geçiş yapmanıza yardımcı olacak kılavuzlar oluşturduk.

* [Translator Konuşma Çevirisi API'si konuşma hizmetlerine geçirme](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Başvuru belgeleri

* [Konuşma SDK'sı](speech-sdk-reference.md)
* [Konuşma cihazları SDK 'Sı](speech-devices-sdk.md)
* [REST API: konuşmayı metne dönüştürme](rest-speech-to-text.md)
* [REST API: metinden konuşmaya](rest-text-to-speech.md)
* [REST API: toplu Iş dökümü ve özelleştirme](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
* [Konuşma SDK 'sını alın](speech-sdk.md)
