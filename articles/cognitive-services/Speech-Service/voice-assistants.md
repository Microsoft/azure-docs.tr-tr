---
title: Ses yardımcıları-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma yazılım geliştirme seti 'ni (SDK) kullanan sesli Yardımcılar için özelliklere, özelliklere ve sınırlamalara genel bakış.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: bd808c0c71e02483b4c4b06e612720c1802869a0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577969"
---
# <a name="about-voice-assistants"></a>Sesli yardımcılar hakkında

Azure konuşma Hizmetleri 'ni kullanan sesli yardımcılar, geliştiricilerin uygulamaları ve deneyimleri için doğal ve insan benzeri konuşma arabirimleri oluşturmalarına olanak sağlar.

Ses Yardımcısı hizmeti, (1) bot çerçevesinin doğrudan hat konuşma kanalını ya da (2) görev tamamlama için tümleşik özel komutlar (Önizleme) hizmetini kullanan bir cihaz ile yardımcı uygulama arasında hızlı ve güvenilir bir etkileşim sağlar.

Uygulamalar, konuşma yazılım geliştirme seti (SDK) ile ses Yardımcısı hizmetine bağlanır.

   ![Ses Yardımcısı düzenleme hizmeti akışının kavramsal diyagramı](media/voice-assistants/overview.png "Sesli yardım akışı")

## <a name="choosing-an-assistant-solution"></a>Yardımcı çözümü seçme

Bir ses Yardımcısı oluşturmaya yönelik ilk adım, ne yapacağına karar vermelidir. Azure konuşma Hizmetleri, yardımcı etkileşimlerinizi üretmek için birden çok ve tamamlayıcı çözüm sunar. Bot çerçevesinin [doğrudan hat konuşma](direct-line-speech.md) kanalının sağladığı esneklik ve çok yönlülük, basit senaryolar Için [özel komutların (Önizleme)](custom-commands.md) basitliğini veya basitliğini isteyip istemediğinizi, doğru araçların seçilmesi, çalışmaya başlamanızı sağlar.

| Şunu yapmak istiyorsanız... | Göz önüne alın... | Örneğin... |
|-------------------|------------------|----------------|
|Güçlü beceriler tümleştirmesi ve tam dağıtım denetimi ile açık uçlu konuşma | Bot çerçevesinin [doğrudan hat konuşma](direct-line-speech.md) kanalı | <ul><li>"Seattle 'a gitmem gerekiyor"</li><li>"Ne tür bir pizza düzenleyebilir?"</li></ul>
|Basitleştirilmiş yazma ve barındırma ile komut ve denetim veya görev odaklı konuşma | [Özel komutlar (Önizleme)](custom-commands.md) | <ul><li>"Ek yük ışığını aç"</li><li>"5 derece çarpıtma yapın"</ul>

Yardımcınızın hangi özellikleri işlemesini istediğinizi bilmiyorsanız en iyi varsayılan seçenek olarak [doğrudan ses konuşmanızı](direct-line-speech.md) öneririz. Ortak desenleri oluşturmak ve mevcut bilgi kaynaklarınızı kullanmak için, [sanal yardımcı çözümü ve kurumsal şablon](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) ve [soru-cevap oluşturma hizmeti](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) gibi zengin araç ve yazma yardımlarıyla tümleştirme sunar.

[Özel komutlar (Önizleme)](custom-commands.md) , doğal dil komut ve denetim senaryoları için özel olarak tasarlanmış ve kolay bir yazma ve barındırma deneyimi sağlar.
   ![Yardımcı çözümlerin karşılaştırması](media/voice-assistants/assistant-solution-comparison.png "Compyardımcı çözümlerin Arison ")

## <a name="core-features"></a>Temel Özellikler

Yardımcı etkileşimlerinizi oluşturmak için [doğrudan hat konuşmayı](direct-line-speech.md) veya [özel komutları (Önizleme)](custom-commands.md) seçmenize bakılmaksızın, yardımcınızı marka, ürün ve kişiliğinizi özelleştirmek için zengin bir özelleştirme özellikleri kümesi kullanabilirsiniz.

| Kategori | Özellikler |
|----------|----------|
|[Özel anahtar sözcük](speech-devices-sdk-create-kws.md) | Kullanıcılar, "Hey contoso" gibi özel bir anahtar sözcükle konuşmaları kullanarak konuşmalar başlatabilir. Uygulama bunu konuşma SDK 'sında özel bir anahtar sözcük altyapısı ile yapar ve [burada oluşturabileceğiniz](speech-devices-sdk-create-kws.md)özel bir anahtar sözcükle yapılandırılabilir. Sesli yardımcılar, anahtar sözcük etkinleştirmenin doğruluğunu artırmak için hizmet tarafı anahtar sözcük doğrulamasını kullanabilir (tek başına cihaz).
|[Konuşmayı metne dönüştürme](speech-to-text.md) | Sesli yardımcılar, Azure konuşma hizmetlerinden [konuşmaya metin](speech-to-text.md) kullanarak gerçek zamanlı sesi tanınan metne dönüştürür. Bu metin, hem yardımcınızın hem de istemci uygulamanızın bulunduğu bir şekilde kullanılabilir.
|[Metinden konuşmaya](text-to-speech.md) | Yardımcınızdan metinsel yanıtlar, Azure konuşma hizmetlerinden gelen [metinden konuşmaya](text-to-speech.md) kullanılarak dağıtılır. Bu birleştirme daha sonra istemci uygulamanız için bir ses akışı olarak sunulur. Microsoft, markanızla ilgili bir ses sağlayan kendi özel, yüksek kaliteli sinir TTS sesinizi oluşturma olanağı sunar. Daha fazla bilgi edinmek için [bizimle iletişime geçin](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Sesli yardımcılar ile çalışmaya başlama

Kodu, 10 dakikadan kısa bir süre içinde çalıştırmak için tasarlanan hızlı başlangıç sunuyoruz. Bu tablo, dile göre düzenlenen sesli yardım hızlı başlangıçlarını bir listesini içerir.

| Hızlı Başlangıç | Platform | API başvurusu |
|------------|----------|---------------|
| C#, UWP | Windows | [Ata](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Ata](https://aka.ms/csspeech/javaref) |
| Java | Android | [Ata](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Örnek kod

Bir ses Yardımcısı oluşturmaya yönelik örnek kod GitHub ' da kullanılabilir. Bu örnekler, çeşitli popüler programlama dillerinde Yardımcınıza bağlanmak için istemci uygulamasını kapsar.

* [Ses Yardımcısı örnekleri (SDK)](https://aka.ms/csspeech/samples)
* [Öğretici: konuşma SDK 'Sı ile yardımcınızı etkinleştirme,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Öğretici

[Konuşma SDK 'sını ve doğrudan hat konuşma kanalını kullanarak yardımcınızı sesli olarak nasıl etkinleştireceğinizi](tutorial-voice-enable-your-bot-speech-sdk.md)gösteren bir öğretici.

## <a name="customization"></a>Özelleştirme

Azure konuşma Hizmetleri kullanılarak oluşturulan sesli yardımcılar, [konuşmadan metne](speech-to-text.md), [metinden konuşmaya](text-to-speech.md)ve [özel anahtar sözcük seçimi](speech-devices-sdk-create-kws.md)için kullanılabilen özelleştirme seçeneklerinin tam aralığını kullanabilir.

> [!NOTE]
> Özelleştirme seçenekleri dile/yerel ayara göre değişir (bkz. [desteklenen diller](supported-languages.md)).

## <a name="reference-docs"></a>Başvuru belgeleri

* [Konuşma SDK'sı](speech-sdk-reference.md)
* [Azure Bot Hizmeti](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
* [Konuşma SDK 'sını alın](speech-sdk.md)
* [Özel komutlar hakkında daha fazla bilgi edinin (Önizleme)](custom-commands.md)
* [Doğrudan hat konuşma hakkında daha fazla bilgi edinin](direct-line-speech.md)