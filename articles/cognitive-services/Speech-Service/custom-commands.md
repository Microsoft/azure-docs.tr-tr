---
title: Özel komutlar-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Özel komutların özelliklerine, özelliklerine ve kısıtlamalarına genel bir bakış, ses uygulamaları oluşturmaya yönelik bir çözümdür.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: e4ae8a09a554afe232091b68888af17dfa096771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102426478"
---
# <a name="what-is-custom-commands"></a>Özel Komutlar nedir?

[Sesli yardımcılar](voice-assistants.md) gibi uygulamalar, kullanıcıları dinler ve yanıt olarak bir işlem yapması ve genellikle geri konuşuyor. Bu kişiler, kullanıcının konuşma işlemini yapmak için [konuşmayı metne](speech-to-text.md) dönüştürme özelliğini kullanır ve ardından metnin doğal dilde anlaşılmasına ilişkin işlem yapın. Bu eylem sıklıkla, [metinden konuşmaya](text-to-speech.md)ile oluşturulan yardımcdan konuşulan çıktıyı içerir. Cihazlar, konuşma SDK 'sının nesnesiyle birlikte yardımcılar 'a bağlanır `DialogServiceConnector` .

**Özel komutlar** , sesli ilk etkileşim deneyimleri için iyileştirilmiş zengin sesli uygulama uygulamaları oluşturmayı kolaylaştırır. Birleştirilmiş bir yazma deneyimi, otomatik barındırma modeli ve görece daha düşük karmaşıklık sağlar. böylece, sesli komutlama senaryolarınız için en iyi çözümü oluşturmaya odaklanmanıza yardımcı olur.

Özel komutlar, görev tamamlama veya komut ve denetim senaryoları için özellikle iyi eşleşen Nesnelerin İnterneti (IoT) cihazları, çevresel ve gözetimsiz cihazlar için idealdir. Bu örnek, perakende ve otomobil sektörler için çözümler içerir. Bu, konuklarınız için en iyi şirket içi sesli denetimli deneyimleri oluşturmanıza, deponuzdaki envanterin yönetiminde ve hareket sırasında oto içi işlevselliği denetlemenize olanak tanır.

> [!TIP]
> Konumundaki giriş sayfamızda örnek tanıtımlarımızı görüntüleyin [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Karmaşık konuşma uygulamaları oluşturmaya ilgileniyorsanız, [sanal yardımcı çözümünü](/azure/bot-service/bot-builder-enterprise-template-overview)kullanarak bot çerçevesini denemeniz önerilir. Doğrudan hat konuşmayı kullanarak, herhangi bir bot Framework bot 'a ses ekleyebilirsiniz.

Özel komutlara yönelik iyi adaylar, iyi tanımlanmış değişken kümelerine sahip sabit bir sözlük 'e sahiptir. Örneğin, bir termostat denetimi gibi ana otomasyon görevleri idealdir.

   ![Görev tamamlama senaryolarına örnekler](media/voice-assistants/task-completion-examples.png "görev tamamlama örnekleri")

## <a name="getting-started-with-custom-commands"></a>Özel komutlarla çalışmaya başlama

Özel komutlarla amamız, tüm farklı teknolojileri öğrenmesini ve sesli komut uygulamanızı oluşturmaya odaklanmanızı sağlamak için bilişsel yükün azaltılmaktır. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure konuşma kaynağı oluşturmak </a>Için özel komutlar kullanmanın ilk adımı. Özel komutlar uygulamanızı konuşma Studio 'da yazabilir ve yayınladıktan sonra bir cihaz üzerindeki uygulamanın konuşma SDK 'sını kullanarak onunla iletişim kurabilme.

#### <a name="authoring-flow-for-custom-commands"></a>Özel komutlar için yazma akışı
   ![Özel komutlar için yazma akışı](media/voice-assistants/custom-commands-flow.png "Özel komutlar yazma akışı")

İlk özel komutlar uygulamasının kodu 10 dakikadan kısa bir süre içinde çalıştırmak için hızlı başlangıç Öğreticimizi izleyin.

* [Özel Komutlar kullanarak ses yardımcısı oluşturma](quickstart-custom-commands-application.md)

Hızlı başlangıç ile işiniz bittiğinde, özel bir komutlar uygulaması tasarlama, geliştirme, hata ayıklama, dağıtma ve tümleştirmeyle ilgili ayrıntılı adımlar için nasıl yapılır kılavuzlarımızı araştırıyoruz.

## <a name="building-voice-assistants-with-custom-commands"></a>Özel komutlarla sesli yardımcılar oluşturma
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir konuşma hizmeti abonelik anahtarı alın](overview.md#try-the-speech-service-for-free)
* [Örnekler için GitHub 'da sesli yardımcılar deponuzu görüntüleyin](https://aka.ms/speech/cc-samples)
* [Özel komutları denemek için konuşma Studio 'ya gidin](https://speech.microsoft.com/customcommands)
* [Konuşma SDK 'sını alın](speech-sdk.md)