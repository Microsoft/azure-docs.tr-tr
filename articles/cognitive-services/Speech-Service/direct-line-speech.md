---
title: Doğrudan hat konuşma konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma yazılım geliştirme seti (SDK) ile doğrudan hat konuşmayı kullanan ses yardımcılarının özelliklerine, özelliklerine ve kısıtlamalarına genel bakış.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611802"
---
# <a name="what-is-direct-line-speech"></a>Direct Line Speech nedir?

**Doğrudan hat konuşma** , esnek ve genişletilebilir bir ses Yardımcısı oluşturmaya yönelik sağlam, uçtan uca bir çözümdür. Bu, bot çerçevesi ve doğrudan hat konuşma kanalı tarafından desteklenir. Bu, botlar ile sesli ve sesli etkileşim için iyileştirilmiştir.

[Ses yardımcıları](voice-assistants.md) kullanıcıları dinler ve genellikle geri konuşarak bir işlem gerçekleştirebilir. Bu kişiler, kullanıcının konuşma işlemini yapmak için [konuşmayı metne](speech-to-text.md) dönüştürme özelliğini kullanır ve ardından metnin doğal dilde anlaşılmasına ilişkin işlem yapın. Bu eylem sıklıkla, [metinden konuşmaya](text-to-speech.md)ile oluşturulan yardımcdan konuşulan çıktıyı içerir.

Doğrudan hat konuşma, sesli Yardımcılar için en yüksek özelleştirme ve gelişmiş algoritmaların mümkündür düzeylerini sunmaktadır. Bu, görev tamamlama veya komut ve denetim kullanımı ile birlikte açık uçlu, doğal veya sonlandırımlar olan konuşma senaryoları için tasarlanmıştır. Bu yüksek düzeyde esneklik daha büyük bir karmaşıklıkla gelir ve doğal dil girişi kullanılarak iyi tanımlanmış görevler kapsamındaki senaryolar, kolaylaştırılmış bir çözüm deneyimi için [özel komutları](custom-commands.md) düşünmek isteyebilir.

## <a name="getting-started-with-direct-line-speech"></a>Doğrudan hat konuşmaya başlarken

Doğrudan hat konuşmayı kullanarak bir ses Yardımcısı oluşturmaya yönelik ilk adım, [bir konuşma aboneliği anahtarı almak](overview.md#try-the-speech-service-for-free), bu abonelikle ilişkilendirilmiş yeni bir bot oluşturmak ve bot 'U doğrudan hat konuşma kanalına bağlamak içindir.

   ![Doğrudan hat konuşma düzenleme hizmeti akışının kavramsal diyagramı](media/voice-assistants/overview-directlinespeech.png "Konuşma kanalı akışı")

Doğrudan hat konuşmayı kullanarak basit bir ses Yardımcısı oluşturmaya yönelik tam bir adım adım kılavuz için, konuşma [için konuşma öğreticisine ve doğrudan hat konuşma kanalına sahip konuşmayı etkinleştirme öğreticisine](tutorial-voice-enable-your-bot-speech-sdk.md)bakın.

Ayrıca, kod çalıştırıp API 'Leri hızla öğrenmenizi sağlayacak hızlı başlangıçlara da sunuyoruz. Bu tablo, dile ve platforma göre düzenlenen sesli yardım hızlı başlangıçlarını bir listesini içerir.

| Hızlı Başlangıç | Platform | API başvurusu |
|------------|----------|---------------|
| C#, UWP | Windows | [Gözat](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Gözat](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Gözat](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Örnek kod

Bir ses Yardımcısı oluşturmaya yönelik örnek kod GitHub ' da kullanılabilir. Bu örnekler, çeşitli popüler programlama dillerinde Yardımcınıza bağlanmak için istemci uygulamasını kapsar.

* [Ses Yardımcısı örnekleri (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Öğretici: konuşma SDK 'Sı ile yardımcınızı etkinleştirin, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Özelleştirme

Konuşma hizmeti kullanılarak oluşturulan sesli yardımcılar, [konuşmadan metne](speech-to-text.md), [metinden konuşmaya](text-to-speech.md)ve [özel anahtar sözcük seçimi](./custom-keyword-basics.md)için kullanılabilen özelleştirme seçeneklerinin tam aralığını kullanabilir.

> [!NOTE]
> Özelleştirme seçenekleri dile/yerel ayara göre değişir (bkz. [desteklenen diller](./language-support.md)).

Ses yardımcıları için doğrudan hat konuşmayı ve ilgili işlevselliği, [sanal yardımcı çözümü ve kurumsal şablon](/azure/bot-service/bot-builder-enterprise-template-overview)için ideal bir ektir. Doğrudan hat konuşması uyumlu bir bot ile çalışabilse de, bu kaynaklar yüksek kaliteli konuşma deneyimleri için yeniden kullanılabilir bir temel ve hızla çalışmaya başlamak için ortak destekleyici beceriler ve modeller sağlar.

## <a name="reference-docs"></a>Başvuru belgeleri

* [Konuşma SDK'sı](./speech-sdk.md)
* [Azure Bot Hizmeti](/azure/bot-service/)

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma hizmeti abonelik anahtarı alın](overview.md#try-the-speech-service-for-free)
* [Konuşma SDK 'sını alın](speech-sdk.md)
* [Temel bot oluşturma ve dağıtma](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Sanal yardımcı çözümü ve kurumsal şablonu al](https://github.com/Microsoft/AI)
