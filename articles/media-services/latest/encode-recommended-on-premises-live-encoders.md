---
title: Media Services tarafından önerilen canlı akış kodlayıcıları
description: Media Services tarafından önerilen canlı akış şirket içi kodlayıcılar hakkında bilgi edinin
services: media-services
keywords: kodlama; kodlayıcılar; medya
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: 8210efe9c17a6edcb18fe114b7f6165d8cbd8360
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282230"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Doğrulanan şirket içi canlı akış kodlayıcıları

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, canlı bir [olay](/rest/api/media/liveevents) (kanal) canlı akış içeriğini işlemek için bir işlem hattını temsil eder. Canlı olay, canlı giriş akışlarını iki şekilde alır.

* Şirket içi bir Live Encoder, Media Services ile canlı kodlama gerçekleştirmek için etkinleştirilmemiş canlı olayına çoklu bit hızlı bir RTMP veya Kesintisiz Akış (parçalanmış MP4) akışı gönderir. Alınan akışlar, daha fazla işlem yapmadan canlı olayları geçer. Bu yönteme **doğrudan geçiş** adı verilir. Canlı kodlayıcının, istemciye Uyarlamalı bit hızında akışa izin vermek için tek bit hızlı bir akış yerine çoklu bit hızına sahip akışlar göndermesini öneririz. 

    Geçişli canlı etkinlik için Multi-bitücretler akışları kullanıyorsanız, kayıttan yürütme tarafında beklenmeyen bir davranışın olmaması için video GOP boyutu ve farklı bit hızlarındaki video parçaları eşitlenmelidir.

  > [!TIP]
  > Doğrudan geçiş yöntemi kullanmak, canlı akış yapmanın en ekonomik yoludur.
 
* Şirket içi bir Live Encoder, aşağıdaki biçimlerden birinde Media Services ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilen canlı olaya tek bit hızlı bir akış gönderir: RTMP veya Kesintisiz Akış (parçalanmış MP4). Canlı etkinlik daha sonra, gelen tek bit hızlı akışın çoklu bit hızında (Uyarlamalı) bir video akışına canlı kodlamasını gerçekleştirir.

Bu makalede doğrulanan şirket içi canlı akış kodlayıcıları ele alınmaktadır. Doğrulama, satıcı kendi kendine doğrulama veya müşteri doğrulaması aracılığıyla yapılır. Microsoft Azure Media Services her bir kodlayıcı için tam veya kapsamlı bir test yapmaz ve güncelleştirmelerde sürekli olarak yeniden doğrulama yapmaz. Şirket içi Live Encoder ' ı nasıl doğrulayacağınız hakkında yönergeler için bkz. Şirket [içi kodlayıcınızı doğrulama](encode-on-premises-encoder-partner.md)

Media Services ile canlı kodlama hakkında ayrıntılı bilgi için bkz. [Media Services v3 Ile canlı akış](stream-live-streaming-concept.md).

## <a name="encoder-requirements"></a>Kodlayıcı gereksinimleri

Kodlayıcılar, HTTPS veya RTMPS protokollerini kullanırken TLS 1,2 ' i desteklemelidir.

## <a name="live-encoders-that-output-rtmp"></a>RTMP çıkış yapan canlı kodlayıcılar

Media Services, aşağıdaki RTMP çıkışı sağlayan gerçek zamanlı kodlayıcılardan birinin kullanılmasını önerir. Desteklenen URL şemaları `rtmp://` veya `rtmps://` .

RTMP üzerinden akış yaparken güvenlik duvarı ve/veya ara sunucu ayarlarını kontrol ederek 1935 ve 1936 numaralı giden TCP bağlantı noktalarının açık olduğundan emin olun.<br/><br/>
RTMPS üzerinden akış yaparken güvenlik duvarı ve/veya ara sunucu ayarlarını kontrol ederek 2935 ve 2936 numaralı giden TCP bağlantı noktalarının açık olduğundan emin olun.

> [!NOTE]
> RTMPS protokollerini kullanırken kodlayıcılar TLS 1,2 ' i desteklemelidir.

- Adobe Flash Media Live Encoder 3.2
- [Antix dijital](http://www.antixdigital.com/) StreamZ Live (daha önce Imagine Communication SelenioFlex Live)
- [BlackICE mini ve ATEM mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambrıa canlı 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elete canlı (sürüm 2.14.15 ve üzeri)
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 ve Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream kablolu dönüştürme (TLS 1,2 gereksinimi nedeniyle sürüm 13.0.2 veya üzeri)
- Telestream kablolu yayın öğeleri (yalnızca RTMP desteklenir. TLS 1.2 + eksikliği nedeniyle RTMPS desteği yok
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> Yukarıdaki kodlayıcılar listesi yalnızca bir öneri listesidir. Kodlayıcılar Microsoft tarafından önemli bir şekilde sınanmamıştır veya doğrulanmaz ve güncelleştirme ya da son değişiklikler, bir uyumluluk kesintiye uğraabilecek olan kodlayıcı satıcıları veya açık kaynak projeleri tarafından tanıtılamaz. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Parçalanmış MP4 (Kesintisiz Akış alma) izleyen canlı kodlayıcılar

Media Services, çıkış olarak çoklu bit hızına Kesintisiz Akış (parçalanmış MP4) sahip aşağıdaki canlı kodlayıcılardan birini kullanmanızı önerir. Desteklenen URL şemaları `http://` veya `https://` .

> [!NOTE]
> Kodlayıcılar, HTTPS protokollerini kullanırken TLS 1,2 ' i desteklemelidir.

- Ateme TITAN Live
- [Antix dijital](http://www.antixdigital.com/) StreamZ Live (daha önce Imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Dinamik kullanım (TLS 1,2 gereksinimi nedeniyle sürüm 2.14.15 ve üzeri)
- Envivio 4Caster C4 Gen III 
- [FFmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live ve Hero 4K (UHD/HEVC)

> [!TIP]
>  Canlı olayları birden çok dilde (örneğin, bir Ingilizce ses izi ve bir Ispanyolca ses izi) akışdıysanız, canlı akışı bir geçişli canlı olaya göndermek üzere yapılandırılmış medya Excel Live Encoder ile bunu yapabilirsiniz.

> [!WARNING]
> Yukarıdaki kodlayıcılar listesi yalnızca bir öneri listesidir. Kodlayıcılar, Microsoft tarafından sürekli olarak test edilmez veya doğrulanmaz ve destek ya da hatalar, her zaman uyumluluğu kesen kodlayıcı satıcıları veya açık kaynak projeleri tarafından tanıtılamaz. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Şirket içi Live Encoder ayarlarını yapılandırma

Canlı olay türleriniz için geçerli olan ayarlar hakkında daha fazla bilgi için bkz. [canlı olay türleri karşılaştırması](live-event-types-comparison-reference.md).

### <a name="playback-requirements"></a>Kayıttan yürütme gereksinimleri

İçeriği kayıttan yürütmek için hem ses hem de video akışının mevcut olması gerekir. Yalnızca video akışının kayıttan yürütülmesi desteklenmez.

### <a name="configuration-tips"></a>Yapılandırma ipuçları

- Mümkünse kablolu internet bağlantısı kullanın.
- Bant genişliği gereksinimlerini belirlerken, akış bit hızları ' nı çift belirleyin. Zorunlu olmasa da bu basit kural, ağ tıkanıklığı etkisini azaltmaya yardımcı olur.
- Yazılım tabanlı kodlayıcılar kullanırken gereksiz programları kapatın.
- Kodlayıcı yapılandırmasını başlattıktan sonra dönüştürme işlemi, olayda olumsuz etkilere sahiptir. Yapılandırma değişiklikleri olayın kararsız hale gelmesine neden olabilir. 
- Azure Media Services için sürekli uyumluluk için Kodlayıcı yazılımının yeni sürümlerini her zaman test edin ve doğrulayın. Microsoft bu listedeki kodlayıcıları yeniden doğrulamaz ve çoğu doğrulama yazılım satıcıları tarafından doğrudan bir "kendi kendine sertifika" olarak gerçekleştirilir.
- Olaylarınızı ayarlamak için kendinize bir zaman kazandırdığınızdan emin olun. Yüksek ölçekli olaylar için, kurulum 'un etkinlikten bir saat öncesine başlamasını öneririz.
- H., uyumlu video ve AAC-LC ses codec çıkışını kullanın.
- Yayın yaptığınız canlı olay türü için desteklenen çözümlerin ve çerçeve tarifelerinin hızını kontrol edin (örneğin, 60fps Şu anda reddedilir.)
- Video kalitelerine göre anahtar çerçeve veya GOP zamana bağlı hizalama olduğundan emin olun.
- Her video kalitesi için benzersiz bir akış adı olduğundan emin olun.
- En iyi Uyarlamalı bit hızı performansı için önerilen katı CBR kodlamasını kullanın.

> [!IMPORTANT]
> Parçaların buluta yüklenmesi için CPU ve GÇ işlemleri içeren makinenin (CPU/bellek/vb.) fiziksel durumunu izleyin. Kodlayıcıdaki herhangi bir ayarı değiştirirseniz, değişikliğin etkili olması için kanalları/Canlı olayı sıfırlayın.

## <a name="see-also"></a>Ayrıca bkz.

[Media Services v3 ile canlı akış](stream-live-streaming-concept.md)

## <a name="next-steps"></a>Sonraki adımlar

[Kodlayıcınızı doğrulama](encode-on-premises-encoder-partner.md)
