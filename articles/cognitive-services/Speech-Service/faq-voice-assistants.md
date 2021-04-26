---
title: Sesli yardımcılar hakkında sık sorulan sorular
titleSuffix: Azure Cognitive Services
description: Özel komutlar veya doğrudan hat konuşma kanalı kullanarak sesli yardımcılar hakkında en popüler sorulara yanıtlar alın.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617786"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Sesli yardımcılar hakkında sık sorulan sorular

Bu belgede sorularınıza yanıt bulamazsanız, [diğer destek seçeneklerine](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)göz atın.

## <a name="general"></a>Genel

**S: Ses Yardımcısı nedir?**

Y **:** Cortana gibi, bir ses Yardımcısı, kullanıcının konuşulan makollarını dinleyen bir çözümdür, söz konusu söz konusu çıkışların içeriğini analiz etmek için bir veya daha fazla eylem gerçekleştirir ve genellikle konuşulan bir bileşeni içeren kullanıcıya bir yanıt verir. Bu, bir sistemle etkileşimde bulunmak için bir "sesli oturum, sesli çıkış" deneyimidir. Sesli yardım yazarları, `DialogServiceConnector` [özel komutlar](custom-commands.md) veya bot çerçevesinin [doğrudan çizgi konuşma](direct-line-speech.md) kanalı kullanılarak oluşturulan bir yardımcı ile iletişim kurmak için konuşma SDK 'sında kullanarak bir cihaz üzerinde uygulama oluşturur. Bu yardımcılar, marka veya ürününüze uyarlanmış bir deneyim sağlamak için özel anahtar sözcükler, özel konuşma ve özel ses kullanabilir.

**S: özel komutları mı yoksa doğrudan konuşma hattını mi kullanmalıyım? Fark nedir?**

**A:** [özel komutlar](custom-commands.md) , görev tamamlama senaryolarına uygun olan bir yardımcıyı kolayca oluşturup barındırmak için daha düşük karmaşıklığa sahip bir araç kümesidir. [Doğrudan hat](direct-line-speech.md) konuşması, güçlü bir konuşma senaryolarını etkinleştirebilecek daha zengin, daha gelişmiş yetenekler sağlar. Daha fazla bilgi için [yardımcı çözümlerin karşılaştırmasına](voice-assistants.md#choosing-an-assistant-solution) bakın.

**S: Nasıl yaparım? kullanmaya başlamak istiyor musunuz?**

Y **:** Özel komutlar (Önizleme) uygulaması veya temel bot Framework bot oluşturmaya başlamak için en iyi yol.

- [Özel komutlar (Önizleme) uygulaması oluşturma](./quickstart-custom-commands-application.md)
- [Temel bir bot Framework bot oluşturma](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [Bir bot 'ı doğrudan hat konuşma kanalına bağlama](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Hata Ayıklama

**S: Kanal gizliliğim nerede?**

Y **:** Doğrudan hat konuşma önizleme sürümünü kullandıysanız veya ilgili belgeleri okuyorsanız, doğrudan hat konuşma kanalı kaydı sayfasında bir gizli anahtar bulmayı bekleyebilir. `DialogServiceConfig`Konuşma SDK 'sında v 1.7 Factory yöntemi `FromBotSecret` de bu değeri bekler.

Doğrudan hat konuşma 'nın en son sürümü bir cihazdan bot ile iletişim kurma sürecini basitleştirir. Kanal kaydı sayfasında, üstteki açılan kutuda doğrudan hat konuşma kanalı kaydınızı bir konuşma kaynağıyla ilişkilendirir. Bağlantı kurulduktan sonra, v 1.8 konuşma SDK 'Sı, `BotFrameworkConfig::FromSubscription` `DialogServiceConnector` aboneliğiniz ile ilişkilendirdiğiniz bot ile iletişim kurmak üzere bir fabrika yöntemi içerir.

Hala, istemci uygulamanızı v 1.7 'den v 1.8 'e geçiriyorsanız, `DialogServiceConfig::FromBotSecret` Kanal gizli parametresi için boş olmayan ve null olmayan bir değer ile çalışmaya devam edebilir, örneğin, daha önce kullandığınız gizli dizi. Daha yeni bir kanal kaydıyla ilişkili bir konuşma aboneliği kullanılırken yalnızca yok sayılır. Bu değer, hizmet tarafı ilişkisinin ilgili olması için cihazda denetlendiğinden, null ve boş olmayan bir değer _olmalıdır_ .

Daha ayrıntılı bir kılavuz için, lütfen kanal kaydını adım adım gösteren [öğretici bölümüne](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) bakın.

**S: bağlanırken bir 401 hatası alıyorum ve hiçbir şey işe yarar. Konuşma aboneliği anahtarımın geçerli olduğunu biliyorum. Ne var ne yok?**

Y **:** Aboneliğinizi Azure portal yönetirken, lütfen _bilişsel_ **Hizmetler** kaynağını (Microsoft. Biliveservicesallinone, "tüm bilişsel hizmetler") değil, **konuşma** kaynağını (Microsoft. CognitiveServicesSpeechServices, "konuşma") kullandığınızdan emin olun. Ayrıca, [sesli yardımcılar Için konuşma hizmeti bölgesi desteğini](regions.md#voice-assistants)inceleyin.

![doğrudan hat konuşmayı için doğru abonelik](media/voice-assistants/faq-supported-subscription.png "uyumlu bir konuşma aboneliği örneği")

**S: tanıma metnini My 'dan geri aldım `DialogServiceConnector` , ancak bir ' 1011 ' hatası ve botum 'dan hiçbir şey görmüyorum. Kaydol?**

Y **:** Bu hata, yardımcınızla ses Yardımcısı hizmeti arasında bir iletişim sorunu olduğunu gösterir.

- Özel komutlar için, özel komutlar uygulamanızın yayımlandığından emin olun
- Doğrudan hat konuşması için, [bot 'Unuzu doğrudan hat konuşma kanalına bağladığınıza](/azure/bot-service/bot-service-channel-connect-directlinespeech), bot 'Unuza [akış protokolü desteği](/azure/bot-service/directline-speech-bot) (ilgili Web yuva desteğiyle) eklemiş olduğunuzdan emin olun ve ardından, bot 'un kanaldan gelen isteklere yanıt verdiğini denetleyin.

**S: Bu kod hala çalışmıyor ve/veya kullanırken farklı bir hata alıyorum `DialogServiceConnector` . Ne yapmam gerekir?**

Y **:** Dosya tabanlı günlük kaydı önemli ölçüde daha fazla ayrıntı sağlar ve destek isteklerini hızlandırmaya yardımcı olabilir. Bu işlevi etkinleştirmek için bkz. [dosya günlüğünü kullanma](how-to-use-logging.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Sorun giderme](troubleshooting.md)
- [Sürüm notları](releasenotes.md)