---
title: Konuşma dökümü nedir (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma dökümü, konuşma tanımayı, konuşmacı tanımayı ve her konuşmacının gerçek zamanlı ve/veya zaman uyumsuz bir şekilde dökümünü sağlamak üzere her konuşmacı (de bilinen olarak da bilinir) birleştiren bir konuşmaya metin çözümüdür. görül.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0c15b053028bd707159a632c98faaea8b9954a9b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075841"
---
# <a name="what-is-conversation-transcription-preview"></a>Konuşma dökümü nedir (Önizleme)?

Konuşma dökümü, konuşma tanımayı, konuşmacı tanımayı ve her konuşmacının yanı sıra zaman _uyumsuz olarak gerçek_zamanlı ve/veya zaman uyumsuz bir şekilde birleştiren bir [konuşmaya metin](speech-to-text.md) çözümüdür. herhangi bir konuşmanın dökümünü alma. Konuşma dökümü, kimin ne olduğunu ve ne zaman söylediklerini belirlemek için konuşmalarda hoparlörleri ayırt eder ve geliştiricilerin çok oyunculu bir savunma gerçekleştiren uygulamalarına konuşmadan metin eklemesini kolaylaştırır.

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

## <a name="use-cases"></a>Uygulama alanları

### <a name="inclusive-meetings"></a>Kapsamlı toplantılar

Engelli ve işitme güçlüğü çeken katılımcılar gibi herkese açık bir şekilde toplantılar yapmak için gerçek zamanlı olarak döküm almak önemlidir. Gerçek zamanlı modda konuşma dökümü, sesi alıp kim olduğunu belirler ve tüm toplantı katılımcılarının bir gecikme süresi olmadan toplantıya katılmasını sağlar.

### <a name="improved-efficiency"></a>Geliştirilmiş verimlilik

Toplantı katılımcıları toplantıya odaklanabilir ve görüşme dekontunu göz önünde bırakabilir. Katılımcılar, toplantıya etkin bir şekilde geçebilir ve bir sonraki adımda hızlı bir şekilde hareket edebilir ve Not almak yerine el yazısı ve toplantı sırasında eksik olabilir.

## <a name="how-it-works"></a>Nasıl çalışır?

Bu, konuşma dökümünü nasıl çalıştığına ilişkin üst düzey bir genel bakıştır.

![Içeri aktarma konuşma dökümü diyagramı](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Beklenen girişler

- **Çok kanallı ses akışı** – belirtim ve tasarım ayrıntıları için bkz. [MICROSOFT Speech cihaz SDK mikrofonu](https://aka.ms/cts/microphone). Daha fazla bilgi edinmek veya bir geliştirme seti satın almak için bkz. [Microsoft Speech cihaz SDK 'Sını alma](https://aka.ms/cts/getsdk).
- **Kullanıcı ses örnekleri** – konuşma dökümü, konuşmadan önce Kullanıcı profilleri gerektirir. Her kullanıcıdan ses kayıtları toplamanız gerekir, sonra da ses doğrulamak ve Kullanıcı profilleri oluşturmak için kayıtları [Imza oluşturma hizmetine](https://aka.ms/cts/signaturegenservice) göndermeniz gerekir.

## <a name="real-time-vs-asynchronous"></a>Gerçek zamanlı vs. zaman uyumsuz

Konuşma dökümü üç döküm modu sunar:

### <a name="real-time"></a>Gerçek zamanlı

Ses verileri, konuşmacı tanımlayıcısı + döküm betiği döndürmek için canlı olarak işlenir. Döküm çözümünüz gereksiniminizde, konuşma katılımcılarına devam eden konuşmaları canlı döküm görünümü sağlamak istiyorsanız bu modu seçin. Örneğin, toplantıları daha erişilebilir hale getirmek için bir uygulama oluşturmak, işitme engellerin ve çok zor katılımcılarının gerçek zamanlı döküm için ideal bir kullanım durumdur.

### <a name="asynchronous"></a>En

Ses verileri, konuşmacı tanımlayıcısını ve dökümünü döndürecek toplu işlem olarak işlenir. Döküm çözümünüz gereksiniminizi canlı döküm görünümü olmadan daha yüksek doğruluk sağlamak istiyorsanız bu modu seçin. Örneğin, Toplantı katılımcılarının kaçırılmış toplantılara kolayca yakalayabilmesini sağlamak için bir uygulama oluşturmak istiyorsanız, yüksek doğruluk dökümü elde etmek için zaman uyumsuz döküm modunu kullanın.

### <a name="real-time-plus-asynchronous"></a>Gerçek zamanlı ve zaman uyumsuz

Ses verileri, konuşmacı tanımlayıcısı + döküm döndüren canlı olarak işlenir ve ayrıca zaman uyumsuz işleme aracılığıyla yüksek doğruluk dökümü almak için bir istek oluşturulur. Uygulamanızın gerçek zamanlı bir dökümle ihtiyacı varsa, ancak konuşma veya toplantı gerçekleştirildikten sonra kullanılmak üzere daha yüksek doğruluk dökümü gerektiren bu modu seçin.

## <a name="language-support"></a>Dil desteği

Şu anda görüşme dökümü şu bölgelerde "en-US" ve "zh-CN" yi destekler: *merkezileştirme* ve *eastaya*. Ek yerel ayar desteği gerekiyorsa [konuşma dökümü özelliği ekibi](mailto:CTSFeatureCrew@microsoft.com)' ne başvurun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşmaları gerçek zamanlı olarak dönüştürme](how-to-use-conversation-transcription-service.md)
