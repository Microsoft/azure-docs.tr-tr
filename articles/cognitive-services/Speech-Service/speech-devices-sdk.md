---
title: Konuşma cihazları SDK 'Sı hakkında-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma cihaz SDK'sı ile çalışmaya başlayın. Konuşma hizmeti, çok çeşitli cihazları ve ses kaynakları ile çalışır. Konuşma cihaz SDK'sı, amaca yönelik olarak tasarlanan, mikrofon dizi geliştirme takımları ile eşleştirilmiş önceden ayarlanmış bir kitaplıktır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 31d1a60cc6c1316dcfccb4f9ba2571708fddd991
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072389"
---
# <a name="about-the-speech-devices-sdk"></a>Konuşma cihazları SDK 'Sı hakkında

[Konuşma Hizmetleri](overview.md) , çok çeşitli cihazlarla ve ses kaynaklarıyla çalışır. Şimdi, konuşma uygulamalarınızın eşleşen donanım ve yazılım ile bir sonraki düzeye alabilir. Konuşma cihaz SDK'sı, amaca yönelik olarak tasarlanan, mikrofon dizi geliştirme takımları ile eşleştirilmiş pretuned bir kitaplıktır.

Konuşma cihaz SDK'sı yardımcı olabilir:

- Hızlı bir şekilde test: yeni ses senaryoları.
- Bulut tabanlı konuşma hizmetlerini cihazınızla daha kolay bir şekilde tümleştirin.
- Müşterileriniz için bir olağanüstü bir kullanıcı deneyimi oluşturun.

Konuşma cihaz SDK'sını kullanan [Speech SDK'sı](speech-sdk.md). Gelişmiş ses işleme algoritmamıza göre işlenen sesleri, cihazın mikrofon dizisinden [konuşma hizmetlerine](overview.md)göndermek için konuşma SDK 'sını kullanır. Çok kanallı ses daha doğru alanı uzak sağlamak için kullandığı [konuşma tanıma](speech-to-text.md) gürültü gizleme, echo iptal beamforming ve dereverberation aracılığıyla.

Ayrıca, bir kullanıcı etkileşimini Başlatan ipucu markanızla benzersiz olduğundan, kendi [özelleştirilmiş anahtar kelime](speech-devices-sdk-create-kws.md) sahip çevresel cihazlar oluşturmak Için konuşma cihazları SDK 'sını de kullanabilirsiniz.

Konuşma cihazları SDK 'Sı, [sesli yardımcılar](https://aka.ms/bots/speech/va), sürücü temelli sıralama sistemleri, [konuşma dökümü](conversation-transcription-service.md)ve akıllı hoparlörler gibi çeşitli ses özellikli senaryolara olanak sağlar. Geri bunları varsayılan için metin, kullanıcılara yanıt konuşun veya [özel sesli](how-to-customize-voice-font.md), arama sonuçları sağlamak [çevir](speech-translation.md) diğer diller ve daha fazlası. Hangi derleme görmeye umuyoruz!

## <a name="get-the-speech-devices-sdk"></a>Konuşma Cihazları SDK’sını edinme

### <a name="android"></a>Android

Android cihazları için [Android konuşma cihazları SDK 'sının](https://aka.ms/sdsdk-download-android)en son sürümünü indirin.

### <a name="windows"></a>Windows

Windows için örnek uygulama, platformlar arası bir Java uygulaması olarak sağlanır. [JRE konuşma cihazları SDK 'sının](https://aka.ms/sdsdk-download-JRE)en son sürümünü indirin.
Uygulama, konuşma SDK paketiyle oluşturulmuştur ve 64 bit Windows üzerinde Java IDE tutulma (v4). 64 bit Java 8 çalışma zamanı ortamında (JRE) çalışır.

### <a name="linux"></a>Linux

Linux için örnek uygulama, platformlar arası bir Java uygulaması olarak sağlanır. [JRE konuşma cihazları SDK 'sının](https://aka.ms/sdsdk-download-JRE)en son sürümünü indirin.
Uygulama, konuşma SDK paketiyle oluşturulmuştur ve 64 bitlik Linux (Ubuntu 16,04, Ubuntu 18,04, detem 9) üzerinde Java IDE tutulma (v4). 64 bit Java 8 çalışma zamanı ortamında (JRE) çalışır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma cihazınızı seçin](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Ücretsiz bir konuşma hizmeti abonelik anahtarı alın](get-started.md)
