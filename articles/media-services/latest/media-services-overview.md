---
title: Azure Media Services v3 genel bakış
titleSuffix: Azure Media Services
description: Hızlı başlangıçlar, öğreticiler ve kod örnekleri bağlantılarıyla Azure Media Services v3 'e yönelik üst düzey bir genel bakış.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, akış, yayın, canlı, çevrimdışı
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 61ffd4857ca9a330a4cb0eeace89791fc0973f70
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575085"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 genel bakış

Azure Media Services, yayın kalitesinde video akışı elde etmenizi, erişilebilirlik ve dağıtımı iyileştirmenizi, içerikleri analiz etmenizi ve daha fazlasını yapmanızı sağlayan çözümler derlemenize olanak tanıyan bulut tabanlı bir platformdur. Bir uygulama geliştiricisi, bir çağrı merkezi, devlet kurumu veya eğlence şirketi olsun, Media Services günümüzün en popüler mobil cihaz ve tarayıcılarındaki büyük kitlelere kadar üstün kalitede medya deneyimleri sunan uygulamalar oluşturmanıza yardımcı olur.

Media Services v3 SDK 'Ları, [Media Services v3 Openapı belirtimini (Swagger)](https://aka.ms/ams-v3-rest-sdk)temel alır.

> [!NOTE]
> Şu anda v3 kaynaklarını yönetmek için Azure portal kullanamazsınız. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

## <a name="what-can-i-do-with-media-services"></a>Media Services ile ne yapabilirim?

Media Services, bulutta çeşitli medya iş akışları oluşturmanıza olanak tanır. Media Services ile yapabilecekleriniz için bazı örnekler şunlardır:

* Videoları, çeşitli tarayıcılarda ve cihazlarda oynatılabilmesi için çeşitli biçimlerde sunma. Çeşitli istemcilere (mobil cihazlar, TV, PC vb.) hem isteğe bağlı hem de canlı akış teslimi için, video ve ses içeriğinin doğru şekilde kodlanıp paketlenmesi gerekir. Bu tür içeriklerin nasıl sunulacağını ve akışa alınacağını görmek için bkz. [Hızlı Başlangıç: Dosyaları kodlama ve akışa alma](stream-files-dotnet-quickstart.md).
* Futbol, bey, üniversite ve yüksek okul spor gibi büyük bir çevrimiçi kitleye canlı spor olayları akışını ve daha fazlasını yapın.
* Şehrler, şehir ve yasama gövdeleri gibi genel toplantıları ve olayları yayınlayın.
* Kaydedilen videoları veya ses içeriğini analiz edin. Örneğin, daha yüksek müşteri memnuniyeti elde etmek için kuruluşlar, konuşmayı metne dönüştürebilir ve arama dizinleri ve panolar derleyebilir. Daha sonra genel şikayetlerden, şikayet kaynaklarından ve diğer ilişkili verilerden istihbarat çıkarabilir.
* Bir müşterinin (örneğin, bir filmi stüdyosu), telif hakkıyla korunan bir çalışmaya erişimi ve kullanım yetkisini kısıtlaması gerektiğinde bir abonelik video hizmeti oluşturun ve DRM korumalı içeriği akışa alın.
* Uçak, tren ve otomobillerde kayıttan yürütülmesi için çevrimdışı içerik sunun. Bir müşterinin, ağ bağlantısının kesileceğini tahmin ettiğinde kayıttan yürütülmesi için içeriği telefonuna veya tabletine indirmesi gerekebilir.
* Konuşmayı metne dönüştürme, çok dilde çevirme ve benzeri Azure Media Services ve [Azure bilişsel hizmetler API'si](https://docs.microsoft.com/azure/#pivot=products&panel=ai) ile eğitim e-öğrenim video platformunu uygulayın.
* Daha geniş bir hedef kitle (örneğin, işitme engelli kişiler veya farklı bir dilde okumak isteyen kişiler) için videolara alt yazı ve açıklamalı alt yazılar eklemek için [Azure bilişsel hizmetler API'si](https://docs.microsoft.com/azure/#pivot=products&panel=ai) ile birlikte Azure Media Services kullanın.
* Anında yüksek yükleri daha iyi işleyebilmek için büyük ölçeklemeye ulaşmak üzere Azure CDN etkinleştirin (örneğin, bir ürün başlatma olayının başlangıcı).

## <a name="how-can-i-get-started-with-v3"></a>v3’ü kullanmaya nasıl başlayabilirim? 

Media Services v3 ile içerik kodlama ve paketleme, Videoları isteğe bağlı olarak yayımlama ve canlı yayınlama hakkında bilgi edinin. Öğreticiler, API başvuruları ve diğer belgeler, güvenli bir biçimde milyonlarca kullanıcıya ölçeklendirilebilen, isteğe bağlı ve canlı video veya ses akışları sağlama ile ilgili bilgiler içerir.

> [!TIP]
> Geliştirmeye başlamadan önce şunları gözden geçirin:<br/>[temel kavramları](concepts-overview.md) * (paketleme, kodlama ve koruma gibi önemli kavramlar)<br/>[Media Services v3 API 'Leriyle geliştirme](media-services-apis-overview.md) * (API 'lere erişme, adlandırma kuralları vb.)

### <a name="quickstarts"></a>Hızlı Başlangıçlar  

Hızlı başlangıçlarda yeni müşterilerin Media Services hızla deneyebilmesinin temel gün 1 yönergeleri gösterilir.

* [Video dosyaları akışı-.NET](stream-files-dotnet-quickstart.md)
* [Video dosyaları akışı-CLı](stream-files-cli-quickstart.md)
* [Stream video Files-Node. js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Öğreticiler

Öğreticiler, bazı en Media Services görevlerden bazılarının senaryo tabanlı yordamlarını gösterir.

* [Uzak dosya ve akış videosunu kodla – REST](stream-files-tutorial-with-rest.md)
* [Karşıya yüklenen dosya ve akış videosunu kodla-.NET](stream-files-tutorial-with-api.md)
* [Canlı akış-.NET](stream-live-tutorial-with-api.md)
* [Videonuzu çözümleyin-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamik şifreleme-.NET](protect-with-aes128.md)

### <a name="samples"></a>Örnekler

Azure Media Services kod örneklerine gitmek için [Bu örnek tarayıcıyı](https://docs.microsoft.com/samples/browse/?products=azure-media-services) kullanın.

### <a name="how-to-guides"></a>Nasıl yapılır kılavuzları

Nasıl yapılır kılavuzlarında bir görevi tamamlamayı gösteren kod örnekleri yer almaktadır. Bu bölümde birçok örnek bulacaksınız. Bunlardan bazıları şunlardır:

* [Hesap oluşturma-CLı](create-account-cli-how-to.md)
* [Erişim API 'Leri-CLı](access-api-cli-how-to.md)
* [İş girişi olarak HTTPS ile kodlama-.NET](job-input-from-http-how-to.md)  
* [Olayları izleme-Portal](monitor-events-portal-how-to.md)
* [Multi-DRM-.NET ile dinamik olarak şifreleyin](protect-with-drm.md) 
* [Özel bir dönüşümle kodlama-CLı](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

[Temel kavramlar hakkında bilgi edinin](concepts-overview.md)

