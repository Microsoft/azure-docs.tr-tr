---
title: Özel sinir Voice genel bakış-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Özel sinir Voice, bir örnek olarak kendi ses verilerinizi sağlayarak uygulamalarınız için bir çeşit özelleştirilmiş yapay ses oluşturmanıza olanak sağlayan bir metinden konuşmaya özelliğidir.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713108"
---
# <a name="what-is-custom-neural-voice"></a>Özel sinir sesi nedir?

Özel sinir Voice, bir örnek olarak kendi ses verilerinizi sağlayarak uygulamalarınız için bir çeşit özelleştirilmiş yapay ses oluşturmanıza olanak sağlayan bir [metin okuma](./text-to-speech.md) (TTS) özelliğidir. Metin okuma, seçilen bir ses gibi ses kullanan bir makine öğrenimi modeli kullanarak metni yapay konuşmaya dönüştürmeye göre yapılır. [REST API](./rest-text-to-speech.md), uygulamalarınızın [önceden oluşturulmuş seslerle](./language-support.md#neural-voices) veya özel sinir Voice özelliği aracılığıyla geliştirilen [özel ses](./how-to-custom-voice-prepare-data.md) modelleriniz ile konuşmasını sağlayabilirsiniz. Özel sinir Voice, insan sesiyle karşılaştırıldığında genellikle ayırt edilemeyen doğal bir ses sesi oluşturan sinir TTS teknolojisini temel alır.
Özel sinir Voice 'ın gerçekçi ve doğal seslendirme sesi, markaların, makineleri bir şekilde sunacağı ve kullanıcıların uygulamalarla bir doğal şekilde etkileşim kurmasına izin verebilir.

> [!NOTE]
> Özel sinir Voice özelliği için kayıt gerekir ve erişim Microsoft 'un uygunluk ve kullanım ölçütlerine göre sınırlandırılır. Bu özelliği kullanmak isteyen müşterilerin kullanım örneklerini [Intake formu](https://aka.ms/customneural)aracılığıyla kaydetmesi gerekir.

## <a name="the-basics-of-custom-neural-voice"></a>Özel sinir seslendirme temelleri

Özel sinir Voice için kullanılan temeldeki sinir TTS teknolojisi üç ana bileşenden oluşur: metin Çözümleyicisi, sinir akustik model ve sinir vocoder. Metinden doğal yapay bir konuşma oluşturmak için metin, ilk olarak metin Çözümleyicisi 'ne giriş, bu da Fonem sırası biçiminde çıktı sağlar. Fonem, bir kelimeyi belirli bir dilde diğerlerinden ayıran temel bir ses birimidir. Bir dizi alfabesine, metinde sunulan sözcüklerin söylenmelerini tanımlar. 

Ardından, Phonem sırası, Timbre, konuşma stili, hız, ıntonations ve stres desenleri gibi konuşma sinyallerini tanımlayan akustik özellikleri tahmin etmek için sinir akustik modeline gider. Son olarak, sinir vocoder, bir yapay konuşma oluşturulması için akustik özellikleri duyulabilir dalgaya dönüştürür.

![Özel sinir sesi için giriş resmi.](./media/custom-voice/cnv-intro.png)

Sinir TTS ses modelleri, insan seslerine ait kayıt örneklerine göre derin sinir ağları kullanılarak eğitilir. Bu [blogda](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911), sinir TTS 'nin, son teknoloji sinir konuşma seniş modelleriyle nasıl çalıştığını anladık. Bu blog Ayrıca, bir Evrensel temel modelin, hedef konuşmacının 2 saatten (ya da 2.000 ' den az) daha az konuşma verisi ile nasıl uyarlandığını ve bu hedef hoparlörün sesinden konuşmasını öğreneceğinizi açıklar. Sinir vocoder 'ın eğitilme hakkında bilgi edinmek için [blog gönderisine](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)bakın.

Özel sinir Voice 'ın özelleştirme özelliği sayesinde, sinir TTS altyapısını Kullanıcı senaryolarınıza daha iyi uyacak şekilde uyarlayabilir. Özel bir sinir sesi oluşturmak için [konuşma Studio 'yu](https://speech.microsoft.com/customvoice) kullanarak kayıtlı sesi ve karşılık gelen betikleri karşıya yükleyin, modeli eğitme ve sesi özel bir uç noktaya dağıtın. Kullanım örneğine bağlı olarak, özel sinir sesi, metni gerçek zamanlı olarak konuşmaya dönüştürmek (örn. bir akıllı sanal Yardımcıda kullanılır) ya da çevrimdışı ses içeriği (örneğin, ses rehberinde veya e-öğrenme uygulamalarındaki yönergeler) Kullanıcı tarafından girilen metin girişi ile çevrimdışı hale getirerek kullanılabilir. Bu, [REST API](./rest-text-to-speech.md), [konuşma SDK 'sı](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)veya bir [Web portalı](https://speech.microsoft.com/audiocontentcreation)aracılığıyla kullanılabilir hale getirilir.

## <a name="terms-and-definitions"></a>Terimler ve tanımlar

| **Süre**      | **Tanım**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ses modeli   | Hedef konuşmacının benzersiz Vocal özelliklerini taklit eden bir metin okuma modeli. Ses *modeli* , *ses yazı tipi* veya *yapay ses* olarak da bilinir. Ses modeli, okunabilir olmayan ve ses kayıtları içermeyen ikili biçimdeki bir parametre kümesidir. İnsan sesinin sesini türetmek veya oluşturmak için ters mühendislik uygulanabilir. |
| Ses tatatmi  | Sesler kaydedildiği ve ses tatatçine benzer seslere yönelik ses modelleri oluşturmak için kullanılan kişiler veya hedef hoparlörler.                                                                                                                                                                                                                                                   |
| Standart TTS  | Konuşma dilini, klasik programlama veya istatistiksel yöntemler kullanılarak yeniden karıştırılmasına ve eşleştiribilecekleri şekilde fonetik kod parçacıklarına kesen standart veya "geleneksel" bir yöntem.                                                                                                                                                                                                    |
| Sinir TTS    | Sinir TTS, farklı sinir ağlarını kullanarak,, işlemsel programlama veya istatistiksel yöntemlerin kullanılması yerine, doğal insan konuşmayla ilgili olarak "öğrenilmiş" olan ağları kullanarak konuşmayı sentezler. Sinir TTS, hedef ses taçesinin kayıtlarına ek olarak, birçok farklı hoparlörden ses kayıtları ile oluşturulmuş bir kaynak kitaplığı/temel modeli kullanır.          |
| Eğitim verileri | Sesli taçın ses kayıtlarını ve ilişkili metin dökümünü içeren özel bir sinir Voice eğitim veri kümesi.                                                                                                                                                                                                                                                               |
| Bilgisini       | Bu sesin ne olmasını istediğinizi bir kişi açıklar. İyi bir kişi tasarımı, tüm ses oluşturmayı, önceden oluşturulmuş kullanılabilir bir ses modeli seçip seçmeksizin yeni bir ses tatatçi atama ve kaydetme yoluyla sıfırdan başlayıp sıfırdan başlayıp bu uygulamayı bilgilendirir.                                                                                                |
| Komut Dosyası        | Bir komut dosyası, ses Taçınız tarafından söylenen duyuları içeren bir metin dosyasıdır. ("*Utterslar*" terimi hem tam cümleleri hem de daha kısa tümceleri kapsar.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Yapay zekanın sorumluluk sahibi bir biçimde kullanılması

Özel sinir Voice 'ın nasıl kullanılacağını öğrenmek için bkz. [asetat notunun](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Microsoft 'un saydamlık notları, AI teknolojimizin nasıl çalıştığını anlamanıza yardımcı olmak üzere tasarlanmıştır. sistem sahipleri, bu durum sistem performansını ve davranışını etkilemesinin yanı sıra teknoloji, kişiler ve ortam dahil olmak üzere tüm sistem hakkında düşünmesinin önemini de kolaylaştırabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel Ses’i kullanmaya başlama](how-to-custom-voice.md)
* [Özel bir ses uç noktası oluşturma ve kullanma](how-to-custom-voice-create-voice.md)