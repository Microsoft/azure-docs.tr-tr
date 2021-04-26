---
title: Konuşma dökümü (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma dökümü, her konuşmadan dolayı tanımayı, konuşmacı KIMLIĞINI ve seçimi birleştiren toplantılar için bir çözümdür.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: trbye
ms.openlocfilehash: 903e8db14a2830236ae81a2a3b5416491d03e8c7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643242"
---
# <a name="what-is-conversation-transcription-preview"></a>Konuşma dökümü nedir (Önizleme)?

Konuşma dökümü, her konuşmacının gerçek zamanlı ve/veya zaman uyumsuz olarak dökümünü sağlamak için konuşma tanımayı, konuşmacı tanımayı ve tümce oluşturma işlevini her konuşmacı (daha da _bilinen) birleştiren_ bir [konuşmaya metin](speech-to-text.md) çözümüdür. Konuşma dökümü, kimin ne olduğunu ve ne zaman söylediklerini belirlemek için konuşmalarda hoparlörleri ayırt eder ve geliştiricilerin çok oyunculu bir savunma gerçekleştiren uygulamalarına konuşmadan metin eklemesini kolaylaştırır.

## <a name="key-features"></a>Önemli özellikler

- **Zaman damgaları** -her konuşmacı söylenişi bir zaman damgasına sahiptir, böylece bir tümceciğin ne zaman olduğunu kolayca bulabilirsiniz.
- **Okunabilir döküm dosyaları** -döküm, metnin söylediklerle yakından eşleştiğinden emin olmak için biçimlendirme ve noktalama işaretleri otomatik olarak eklenir.
- Kullanıcı **profilleri** -Kullanıcı profilleri, Kullanıcı ses örnekleri toplayıp imza oluşturmaya gönderilerek oluşturulur.
- **Konuşmacı kimliği** -konuşmacılar Kullanıcı profilleri kullanılarak tanımlanır ve her birine bir _Konuşmacı tanımlayıcısı_ atanır.
- **Çok konuşmacı** belirleme-her konuşmacı tanımlayıcısı ile ses akışını sendikme ne olduğunu söylediklerinizi belirleme.
- **Gerçek** zamanlı döküm: konuşmaların ne zaman ve ne zaman söylediğini belirten canlı yazılı betikler sağlar.
- **zaman uyumsuz** döküm: çok kanallı bir ses akışı kullanarak dökümü daha yüksek doğruluk sağlar.

> [!NOTE]
> Konuşma dökümü, odada bulunan hoparlör sayısına bir sınır koymasa da, oturum başına 2-10 hoparlörleri için en iyi duruma getirilmiştir.

## <a name="get-started"></a>başlarken

Başlamak [için gerçek](how-to-use-conversation-transcription.md) zamanlı konuşma dökümünü inceleyin.

## <a name="use-cases"></a>Uygulama alanları

Engelli ve işitme güçlüğü çeken katılımcılar gibi herkese açık bir şekilde toplantılar yapmak için gerçek zamanlı olarak döküm almak önemlidir. Gerçek zamanlı modda konuşma dökümü, sesi alıp kim olduğunu belirler ve tüm toplantı katılımcılarının bir gecikme süresi olmadan toplantıya katılmasını sağlar.

### <a name="improved-efficiency"></a>Geliştirilmiş verimlilik

Toplantı katılımcıları toplantıya odaklanabilir ve görüşme dekontunu göz önünde bırakabilir. Katılımcılar, toplantıya etkin bir şekilde geçebilir ve bir sonraki adımda hızlı bir şekilde hareket edebilir ve Not almak yerine el yazısı ve toplantı sırasında eksik olabilir.

## <a name="how-it-works"></a>Nasıl çalışır?

Bu, konuşma dökümünü nasıl çalıştığına ilişkin üst düzey bir genel bakıştır.

![Içeri aktarma konuşma dökümü diyagramı](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Beklenen girişler

- **Çok kanallı ses akışı** – belirtim ve tasarım ayrıntıları için bkz. [MICROSOFT Speech cihaz SDK mikrofonu](./speech-devices-sdk-microphone.md). Daha fazla bilgi edinmek veya bir geliştirme seti satın almak için bkz. [Microsoft Speech cihaz SDK 'Sını alma](./get-speech-devices-sdk.md).
- **Kullanıcı ses örnekleri** – konuşma dökümü, konuşmadan önce Kullanıcı profilleri gerektirir. Her kullanıcıdan ses kayıtları toplamanız gerekir, sonra da ses doğrulamak ve Kullanıcı profilleri oluşturmak için kayıtları [Imza oluşturma hizmetine](https://aka.ms/cts/signaturegenservice) göndermeniz gerekir.

> [!NOTE]
> Kullanıcı ses örnekleri isteğe bağlıdır. Bu giriş olmadan, döküm farklı konuşmacılar gösterir, ancak önceden kaydedilmiş özel konuşmacı adları olarak tanınması yerine "Speaker1", "Speaker2" vb. olarak gösterilir.


## <a name="real-time-vs-asynchronous"></a>Gerçek zamanlı vs. zaman uyumsuz

Konuşma dökümü üç döküm modu sunar:

### <a name="real-time"></a>Gerçek zamanlı

Ses verileri, konuşmacı tanımlayıcısı + döküm betiği döndürmek için canlı olarak işlenir. Döküm çözümünüz gereksiniminizde, konuşma katılımcılarına devam eden konuşmaları canlı döküm görünümü sağlamak istiyorsanız bu modu seçin. Örneğin, toplantıları daha erişilebilir hale getirmek için bir uygulama oluşturmak, işitme engellerin ve çok zor katılımcılarının gerçek zamanlı döküm için ideal bir kullanım durumdur.

### <a name="asynchronous"></a>Zaman uyumsuz

Ses verileri, konuşmacı tanımlayıcısını ve dökümünü döndürecek toplu işlem olarak işlenir. Döküm çözümünüz gereksiniminizi canlı döküm görünümü olmadan daha yüksek doğruluk sağlamak istiyorsanız bu modu seçin. Örneğin, Toplantı katılımcılarının kaçırılmış toplantılara kolayca yakalayabilmesini sağlamak için bir uygulama oluşturmak istiyorsanız, yüksek doğruluk dökümü elde etmek için zaman uyumsuz döküm modunu kullanın.

### <a name="real-time-plus-asynchronous"></a>Gerçek zamanlı ve zaman uyumsuz

Ses verileri, konuşmacı tanımlayıcısı + döküm döndüren canlı olarak işlenir ve ayrıca zaman uyumsuz işleme aracılığıyla yüksek doğruluk dökümü almak için bir istek oluşturulur. Uygulamanızın gerçek zamanlı bir dökümle ihtiyacı varsa, ancak konuşma veya toplantı gerçekleştirildikten sonra kullanılmak üzere daha yüksek doğruluk dökümü gerektiren bu modu seçin.

## <a name="language-support"></a>Dil desteği

Şu anda görüşme dökümü aşağıdaki bölgelerde bulunan [Tüm konuşmadan metne dilleri](language-support.md#speech-to-text) destekler:  `centralus` , `eastasia` , `eastus` , `westeurope` . Ek yerel ayar desteği gerekiyorsa [konuşma dökümü özelliği ekibi](mailto:CTSFeatureCrew@microsoft.com)' ne başvurun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşmaları gerçek zamanlı olarak dönüştürme](how-to-use-conversation-transcription.md)
