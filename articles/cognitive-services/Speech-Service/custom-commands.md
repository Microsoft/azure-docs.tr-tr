---
title: Özel komutlar (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Sesli yardımcılar oluşturmaya yönelik bir çözüm olan özel komutlar (Önizleme) için özelliklere, özelliklere ve sınırlamalara genel bakış.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 62210bf480d09ce2a256a44b7554ac53aa06eb0c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579696"
---
# <a name="custom-commands-preview"></a>Özel komutlar (Önizleme)

[Ses yardımcıları](voice-assistants.md) kullanıcıları dinler ve genellikle geri konuşarak bir işlem gerçekleştirebilir. Bu kişiler, kullanıcının konuşma işlemini yapmak için [konuşmayı metne](speech-to-text.md) dönüştürme özelliğini kullanır ve ardından metnin doğal dilde anlaşılmasına ilişkin işlem yapın. Bu eylem sıklıkla, [metinden konuşmaya](text-to-speech.md)ile oluşturulan yardımcdan konuşulan çıktıyı içerir. Cihazlar, konuşma SDK 'sının `DialogServiceConnector` nesnesiyle birlikte yardımcılar 'a bağlanır.

**Özel komutlar (Önizleme)** , Ses Yardımcısı oluşturmak için kolaylaştırılmış bir çözümdür. Bu, birleştirilmiş bir yazma deneyimi, otomatik barındırma modeli ve görece daha düşük karmaşıklığa karşı [doğrudan hat konuşma](direct-line-speech.md)gibi diğer yardımcı oluşturma seçeneklerine benzer. Ancak bu basitleştirme, esneklik açısından bir düşüş ile birlikte gelir. Bu nedenle, özel komutlar (Önizleme) görev tamamlama veya komut ve denetim senaryoları için idealdir. Nesnelerin İnterneti (IoT) ve gözetimsiz cihazlar için oldukça iyi eşleştirilir.

Karmaşık konuşma etkileşimi ve [sanal yardımcı çözümü ve kurumsal şablon](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) gibi diğer çözümlerle tümleştirme Için doğrudan hat konuşmayı kullanmanız önerilir.

Özel komutlar (Önizleme) için iyi adaylarda, iyi tanımlanmış değişken kümelerine sahip sabit bir sözlük vardır. Örneğin, bir termostat denetimi gibi ana otomasyon görevleri idealdir.

   ![Görev tamamlama senaryolarına örnekler](media/voice-assistants/task-completion-examples.png "görev tamamlama örnekleri")

## <a name="getting-started-with-custom-commands-preview"></a>Özel komutlarla çalışmaya başlama (Önizleme)

Bir ses Yardımcısı oluşturmak için özel komutları (Önizleme) kullanmanın ilk adımı, [bir konuşma aboneliği anahtarı almak](get-started.md) ve [konuşma Studio](https://speech.microsoft.com)'Daki özel komutlar (Önizleme) oluşturucuya erişim kullanmaktır. Buradan, yeni bir özel komut (Önizleme) uygulaması yazabilir ve yayımlayabilirsiniz. bu sayede, bir cihaz üzerindeki uygulama konuşma SDK 'sını kullanarak onunla iletişim kurabilir.

   ![Özel komutlar için yazma akışı (Önizleme)](media/voice-assistants/custom-commands-flow.png "Özel komutlar (Önizleme) yazma akışı")

Kodu, 10 dakikadan kısa bir süre içinde çalıştırmak için tasarlanan hızlı başlangıç sunuyoruz.

* [Özel komutlar (Önizleme) uygulaması oluşturma](quickstart-custom-speech-commands-create-new.md)
* [Parametrelerle özel komutlar (Önizleme) uygulaması oluşturma](quickstart-custom-speech-commands-create-parameters.md)
* [Konuşma SDK 'Sı ile özel komutlar (Önizleme) uygulamasına bağlanma,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Örnek kod

Özel komutlarla (Önizleme) bir ses Yardımcısı oluşturmaya yönelik örnek kod GitHub ' da kullanılabilir.

* [Ses Yardımcısı örnekleri (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Özelleştirme

Azure konuşma Hizmetleri kullanılarak oluşturulan sesli yardımcılar, [konuşmadan metne](speech-to-text.md), [metinden konuşmaya](text-to-speech.md)ve [özel anahtar sözcük seçimi](speech-devices-sdk-create-kws.md)için kullanılabilen özelleştirme seçeneklerinin tam aralığını kullanabilir.

> [!NOTE]
> Özelleştirme seçenekleri dile/yerel ayara göre değişir (bkz. [desteklenen diller](supported-languages.md)).

## <a name="reference-docs"></a>Başvuru belgeleri

* [Konuşma SDK'sı](speech-sdk-reference.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
* [Konuşma SDK 'sını alın](speech-sdk.md)
