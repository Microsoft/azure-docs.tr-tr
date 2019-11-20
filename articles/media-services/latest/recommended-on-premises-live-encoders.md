---
title: Media Services-Azure tarafından önerilen canlı akış kodlayıcıları | Microsoft Docs
description: Media Services tarafından önerilen canlı akış şirket içi kodlayıcılar hakkında bilgi edinin
services: media-services
keywords: kodlama; kodlayıcılar; medya
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 10/10/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: aa5eae3e40b8578f826b1b275995bbb3d346e586
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300936"
---
# <a name="recommended-live-streaming-encoders"></a>Önerilen canlı akış kodlayıcıları

Azure Media Services, canlı bir [olay](https://docs.microsoft.com/rest/api/media/liveevents) (kanal) canlı akış içeriğini işlemek için bir işlem hattını temsil eder. Canlı olay, canlı giriş akışlarını iki şekilde alır.

* Şirket içi bir Live Encoder, Media Services ile canlı kodlama gerçekleştirmek için etkinleştirilmemiş canlı olayına çoklu bit hızlı bir RTMP veya Kesintisiz Akış (parçalanmış MP4) akışı gönderir. Alınan akışlar, daha fazla işlem yapmadan canlı olayları geçer. Bu yönteme **doğrudan geçiş**adı verilir. Canlı kodlayıcı, bir geçişli kanala tek bit hızlı bir akış gönderebilir. İstemciye Uyarlamalı bit hızı akışa izin vermediğinden bu yapılandırmayı önermiyoruz.

  > [!NOTE]
  > Doğrudan geçiş yöntemi kullanmak, canlı akış yapmanın en ekonomik yoludur.
 
* Şirket içi bir Live Encoder, aşağıdaki biçimlerden birinde Media Services ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilen canlı olaya tek bit hızlı bir akış gönderir: RTMP veya Kesintisiz Akış (parçalanmış MP4). Canlı etkinlik daha sonra, gelen tek bit hızlı akışın çoklu bit hızında (Uyarlamalı) bir video akışına canlı kodlamasını gerçekleştirir.

Media Services ile canlı kodlama hakkında ayrıntılı bilgi için bkz. [Media Services v3 Ile canlı akış](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>RTMP çıkış yapan canlı kodlayıcılar

Media Services, aşağıdaki RTMP çıkışı sağlayan gerçek zamanlı kodlayıcılardan birinin kullanılmasını önerir. Desteklenen URL şemaları `rtmp://` veya `rtmps://` ' dir.

> [!NOTE]
> RTMP üzerinden akış yaparken güvenlik duvarı ve/veya ara sunucu ayarlarını kontrol ederek 1935 ve 1936 numaralı giden TCP bağlantı noktalarının açık olduğundan emin olun.

- Adobe Flash Media Live Encoder 3.2
- [Cambrıa canlı 4,3](https://www.capellasystems.net/products/cambria-live/)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Parçalanan MP4 veren canlı kodlayıcılar

Media Services, çıkış olarak çoklu bit hızına Kesintisiz Akış (parçalanmış MP4) sahip aşağıdaki canlı kodlayıcılardan birini kullanmanızı önerir. Desteklenen URL şemaları `http://` veya `https://` ' dir.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenıo MCP3
- Media Excel Hero Live ve Hero 4K (UHD/HEVC)

> [!TIP]
>  Canlı olayları birden çok dilde (örneğin, bir Ingilizce ses izi ve bir Ispanyolca ses izi) akışdıysanız, canlı akışı bir geçişli canlı olaya göndermek üzere yapılandırılmış medya Excel Live Encoder ile bunu yapabilirsiniz.

## <a name="configuring-on-premises-live-encoder-settings"></a>Şirket içi Live Encoder ayarlarını yapılandırma

Canlı olay türleriniz için geçerli olan ayarlar hakkında daha fazla bilgi için bkz. [canlı olay türleri karşılaştırması](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Kayıttan yürütme gereksinimleri

İçeriği kayıttan yürütmek için hem ses hem de video akışının mevcut olması gerekir. Yalnızca video akışının kayıttan yürütülmesi desteklenmez.

### <a name="configuration-tips"></a>Yapılandırma ipuçları

- Mümkün olduğunda, bir hardkablolu internet bağlantısı kullanın.
- Bant genişliği gereksinimlerini belirlerken, akış bit hızları ' nı çift belirleyin. Zorunlu olmasa da bu basit kural, ağ tıkanıklığı etkisini azaltmaya yardımcı olur.
- Yazılım tabanlı kodlayıcılar kullanırken gereksiz programları kapatın.
- Kodlayıcı yapılandırmasını başlattıktan sonra dönüştürme işlemi, olayda olumsuz etkilere sahiptir. Yapılandırma değişiklikleri olayın kararsız hale gelmesine neden olabilir. 
- Olaylarınızı ayarlamak için kendinize bir zaman kazandırdığınızdan emin olun. Yüksek ölçekli olaylar için, kurulum 'un etkinlikten bir saat öncesine başlamasını öneririz.

## <a name="becoming-an-on-premises-encoder-partner"></a>Şirket içi kodlayıcı iş ortağı olma

Azure Media Services şirket içi kodlayıcı iş ortağı olarak, Media Services kurumsal müşterilere kodlayıcınızı önererek ürününüzü yükseltir. Şirket içi kodlayıcı iş ortağı olmak için, Media Services ile şirket içi kodlayıcılarınızın uyumluluğunu doğrulamanız gerekir. Bunu yapmak için aşağıdaki doğrulamaları doldurun.

### <a name="pass-through-live-event-verification"></a>Doğrudan geçiş canlı olay doğrulaması

1. Media Services hesabınızda, **akış uç noktasının** çalıştığından emin olun. 
2. **Geçiş** canlı olayını oluşturun ve başlatın. <br/> Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing.md).
3. Alma URL 'sini alın ve şirket içi kodlayıcıyı, Media Services çoklu bit hızlı canlı akış göndermek için URL 'YI kullanacak şekilde yapılandırın.
4. Kodlayıcının girişinin gerçekten alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
5. Yeni bir **varlık** nesnesi oluşturun.
6. Canlı bir **çıktı** oluşturun ve oluşturduğunuz varlık adını kullanın.
7. Yerleşik **akış ilkesi** türleriyle bir **akış Bulucu** oluşturun.
8. Kullanılacak URL 'Leri geri almak için **akış bulucunun** yollarını listeleyin.
9. Akış yapmak istediğiniz **akış uç noktası** için ana bilgisayar adını alın.
10. Tam URL 'yi almak için adım 8 ' deki URL 'YI adım 9 ' da ana bilgisayar adıyla birleştirin.
11. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın.
12. Canlı etkinliği durdurun. 
13. Kayıttan yürütmenin tüm kalite düzeylerinde görünür bir görünmüyor olmamasını sağlamak için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın. Ya da canlı oturum sırasında önizleme URL 'SI aracılığıyla izleyin ve doğrulayın.
14. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin.
15. Her bir örneği oluşturduktan sonra canlı olay durumunu sıfırlayın.
16. Kodlarınızın desteklediği tüm yapılandırmalarda 5 ile 15 arasındaki adımları yineleyin (reklam sinyali, açıklamalı alt yazılar veya farklı kodlama hızları olmadan).

### <a name="live-encoding-live-event-verification"></a>Canlı kodlama canlı olay doğrulaması

1. Media Services hesabınızda, **akış uç noktasının** çalıştığından emin olun. 
2. **Canlı kodlama** canlı olayını oluşturun ve başlatın. <br/> Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing.md).
3. İçeri ve Media Services için tek bit hızlı canlı bir akış göndermek üzere kodlayıcıyı yapılandırın ve kodlayıcınızı yapılandırın.
4. Kodlayıcının girişinin gerçekten alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
5. Yeni bir **varlık** nesnesi oluşturun.
6. Canlı bir **çıktı** oluşturun ve oluşturduğunuz varlık adını kullanın.
7. Yerleşik **akış ilkesi** türleriyle bir **akış Bulucu** oluşturun.
8. Kullanılacak URL 'Leri geri almak için **akış bulucunun** yollarını listeleyin.
9. Akış yapmak istediğiniz **akış uç noktası** için ana bilgisayar adını alın.
10. Tam URL 'yi almak için adım 8 ' deki URL 'YI adım 9 ' da ana bilgisayar adıyla birleştirin.
11. Gerçek zamanlı kodlarınızı yaklaşık 10 dakika boyunca çalıştırın.
12. Canlı etkinliği durdurun.
13. Kayıttan yürütmenin tüm kalite seviyeleri için görünür olmadığından emin olmak üzere arşivlenmiş varlığı izlemek için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın. Ya da canlı oturum sırasında önizleme URL 'SI aracılığıyla izleyin ve doğrulayın.
14. Varlık KIMLIĞINI, Canlı Arşiv için yayımlanan akış URL 'sini ve canlı kodlayıcıınızdan kullanılan ayarları ve sürümü kaydedin.
15. Her bir örneği oluşturduktan sonra canlı olay durumunu sıfırlayın.
16. Kodlarınızın desteklediği tüm yapılandırmalarda 5 ile 15 arasındaki adımları yineleyin (reklam sinyali, açıklamalı alt yazılar veya farklı kodlama hızları olmadan).

### <a name="longevity-verification"></a>Longeçekimi doğrulaması

Adım 11 hariç [geçiş canlı olay doğrulaması ile](#pass-through-live-event-verification) aynı adımları izleyin. <br/>10 dakika yerine, canlı kodlayıcıyı bir hafta veya daha uzun bir süre içinde çalıştırın. Kayıttan yürütmenin görünür olmadığından emin olmak için zaman zaman (veya arşivlenmiş bir varlık) ile ilgili canlı akışı izlemek için [Azure Media Player](https://aka.ms/azuremediaplayer) gibi bir oyuncu kullanın.

### <a name="email-your-recorded-settings"></a>Kayıtlı ayarlarınıza e-posta gönderin

Son olarak, kayıtlı ayarlarınızı ve Canlı Arşiv parametrelerinizi, tüm otomatik doğrulama denetimlerinin başarılı olduğunu belirten bir bildirim olarak amshelp@microsoft.com ' dan Azure Media Services. Ayrıca, herhangi bir izleme için iletişim bilgilerinizi ekleyin. Azure Media Services ekibine bu işlemle ilgili tüm soruları ile başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3 ile canlı akış](live-streaming-overview.md)
