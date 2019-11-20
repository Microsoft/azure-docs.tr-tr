---
title: Doğrudan hat konuşma konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma yazılım geliştirme seti (SDK) ile doğrudan hat konuşmayı kullanan ses yardımcılarının özelliklerine, özelliklerine ve kısıtlamalarına genel bakış.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 72029d1d56c9d561e629cc750244fd7de8ec113a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507181"
---
# <a name="about-direct-line-speech"></a>Doğrudan konuşma hattı hakkında

[Ses yardımcıları](voice-assistants.md) kullanıcıları dinler ve genellikle geri konuşarak bir işlem gerçekleştirebilir. Bu kişiler, kullanıcının konuşma işlemini yapmak için [konuşmayı metne](speech-to-text.md) dönüştürme özelliğini kullanır ve ardından metnin doğal dilde anlaşılmasına ilişkin işlem yapın. Bu eylem sıklıkla, [metinden konuşmaya](text-to-speech.md)ile oluşturulan yardımcdan konuşulan çıktıyı içerir. Cihazlar, konuşma SDK 'sının `DialogServiceConnector` nesnesiyle birlikte yardımcılar 'a bağlanır.

**Doğrudan hat konuşmayı** , bot çerçevesi ve doğrudan hat konuşma kanalı tarafından desteklenen ve botları olan sesli, sesli etkileşim için iyileştirilmiş, esnek ve genişletilebilir bir ses Yardımcısı oluşturmaya yönelik sağlam, uçtan uca bir çözümdür.

Doğrudan hat konuşma, sesli Yardımcılar için en yüksek özelleştirme ve gelişmiş algoritmaların mümkündür düzeylerini sunmaktadır. Görev tamamlama veya komut ve denetim kullanımı ile bunların açık sonlandırmış, doğal veya sonlandırılmasıyla ilgili konuşma senaryolarına çok uygundur. Bu yüksek düzeyde esneklik daha büyük bir karmaşıklıkla gelir ve doğal dil girişi kullanılarak iyi tanımlanmış görevler kapsamındaki senaryolar, kolaylaştırılmış bir çözüm deneyimi için [özel komutları (Önizleme)](custom-commands.md) düşünmek isteyebilir.

## <a name="getting-started-with-direct-line-speech"></a>Doğrudan hat konuşmaya başlarken

Doğrudan hat konuşmayı kullanarak bir ses Yardımcısı oluşturmaya yönelik ilk adım, [bir konuşma aboneliği anahtarı almak](get-started.md), bu abonelikle ilişkilendirilmiş yeni bir bot oluşturmak ve bot 'U doğrudan hat konuşma kanalına bağlamak içindir.

   ![Doğrudan hat konuşma düzenleme hizmeti akışının kavramsal diyagramı](media/voice-assistants/overview-directlinespeech.png "Konuşma kanalı akışı")

Doğrudan hat konuşmayı kullanarak basit bir ses Yardımcısı oluşturmaya yönelik tam bir adım adım kılavuz için lütfen konuşma [öğreticisine ve doğrudan hat konuşma kanalına sahip konuşmayı etkinleştirme öğreticisine](tutorial-voice-enable-your-bot-speech-sdk.md)bakın.

Ayrıca, 10 dakikadan kısa bir süre içinde kod çalıştırmak için tasarlanan hızlı başlangıçları sunuyoruz. Bu tablo, dile göre düzenlenen sesli yardım hızlı başlangıçlarını bir listesini içerir.

| Hızlı Başlangıç | Platform | API başvurusu |
|------------|----------|---------------|
| C#, UWP | Windows | [Ata](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Ata](https://aka.ms/csspeech/javaref) |
| Java | Android | [Ata](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Örnek kod

Bir ses Yardımcısı oluşturmaya yönelik örnek kod GitHub ' da kullanılabilir. Bu örnekler, çeşitli popüler programlama dillerinde Yardımcınıza bağlanmak için istemci uygulamasını kapsar.

* [Ses Yardımcısı örnekleri (SDK)](https://aka.ms/csspeech/samples)
* [Öğretici: konuşma SDK 'Sı ile yardımcınızı etkinleştirme,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Özelleştirme

Azure konuşma Hizmetleri kullanılarak oluşturulan sesli yardımcılar, [konuşmadan metne](speech-to-text.md), [metinden konuşmaya](text-to-speech.md)ve [özel anahtar sözcük seçimi](speech-devices-sdk-create-kws.md)için kullanılabilen özelleştirme seçeneklerinin tam aralığını kullanabilir.

> [!NOTE]
> Özelleştirme seçenekleri dile/yerel ayara göre değişir (bkz. [desteklenen diller](supported-languages.md)).

Ses yardımcıları için doğrudan hat konuşmayı ve ilgili işlevselliği, [sanal yardımcı çözümü ve kurumsal şablon](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)için ideal bir ektir. Doğrudan hat konuşması uyumlu bir bot ile çalışabilse de, bu kaynaklar yüksek kaliteli konuşma deneyimleri için yeniden kullanılabilir bir temel ve hızla çalışmaya başlamak için ortak destekleyici beceriler ve modeller sağlar.

## <a name="reference-docs"></a>Başvuru belgeleri

* [Konuşma SDK'sı](speech-sdk-reference.md)
* [Azure Bot Hizmeti](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
* [Konuşma SDK 'sını alın](speech-sdk.md)
* [Temel bot oluşturma ve dağıtma](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Sanal yardımcı çözümü ve kurumsal şablonu al](https://github.com/Microsoft/AI)
