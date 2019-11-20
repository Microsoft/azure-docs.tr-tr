---
title: Azure Media Services v3 ile canlı akışa genel bakış | Microsoft Docs
description: Bu makalede, Azure Media Services v3 kullanarak canlı akışa genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/12/2019
ms.author: juliako
ms.openlocfilehash: 8d7db428d7f71383abf5425d7cc1ddbbab3b7a52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037861"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3 ile canlı akış

Azure Media Services, Azure bulutunda müşterilerinize canlı olaylar sunmanıza olanak sağlar. Canlı olaylarınızın Media Services akışını sağlamak için şunlar gerekir:  

- Canlı etkinliği yakalamak için kullanılan bir kamera.<br/>Kurulum fikirleri için [basit ve taşınabilir olay video dişli kurulumuna]( https://link.medium.com/KNTtiN6IeT)göz atın.

    Bir kameraya erişiminiz yoksa, [Telestream kablolu dönüştürme](https://www.telestream.net/wirecast/overview.htm) gibi araçlar bir video dosyasından canlı bir akış oluşturmak için kullanılabilir.
- Bir kameradan (veya dizüstü bilgisayar gibi başka bir cihazdan) sinyalleri Media Services gönderilen bir katkı akışına dönüştüren canlı bir video Kodlayıcısı. Katkı akışı, reklamları ile ilgili, SCTE-35 işaretçileri gibi sinyalleri içerebilir.<br/>Önerilen canlı akış kodlayıcıları listesi için bkz. [canlı akış kodlayıcılar](recommended-on-premises-live-encoders.md). Ayrıca, bu bloga göz atın: [OBS Ile canlı akış üretimi](https://link.medium.com/ttuwHpaJeT).
- Media Services ' deki bileşenler, canlı etkinliği müşterilerinize alma, önizleme, paketleme, kaydetme, şifreleme ve yayınlaymanıza, daha fazla dağıtım için bir CDN 'ye yönelik bir CDN 'ye yönelik olarak.

Bu makale, Media Services ile canlı akış ve diğer ilgili makalelerin bağlantılarıyla ilgili genel bakış ve kılavuzluk sağlar.
 
> [!NOTE]
> Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

## <a name="dynamic-packaging"></a>Dinamik paketleme

Media Services ile, canlı akışlarınızı, hizmet 'e gönderilen katkı akışından [MPEG Dash, HLS ve kesintisiz akış biçimlerinde](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) önizleme ve yayınlama olanağı sağlayan [dinamik paketlemeden](dynamic-packaging-overview.md)yararlanabilirsiniz. Görüntüleyicilerinizin her türlü HLS, ÇIZGI veya Kesintisiz Akış uyumlu oyuncularla canlı akışı kayıttan yürütebileceği. Bu protokollerden herhangi birine akışını sunmak için Web veya mobil uygulamalarınızda [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) kullanabilirsiniz.

## <a name="dynamic-encryption"></a>Dinamik şifreleme

Dinamik şifreleme, etkin veya isteğe bağlı içeriğinizi AES-128 veya üç ana dijital hak yönetimi (DRM) sisteminden dinamik olarak şifrelemenize olanak sağlar: Microsoft PlayReady, Google Widevine ve Apple FairPlay. Media Services de AES anahtarları ve DRM sunmaya yönelik bir hizmet sağlar (PlayReady, Widevine ve FairPlay) lisansları yetkili istemcilere. Daha fazla bilgi için bkz. [dinamik şifreleme](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dinamik bildirim

Dinamik filtreleme, oyunculara gönderilen iz, biçim, bitme ve sunum süresi pencerelerinin sayısını denetlemek için kullanılır. Daha fazla bilgi için bkz. [filtreler ve dinamik bildirimler](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Canlı olay türleri

[Canlı Etkinlikler](https://docs.microsoft.com/rest/api/media/liveevents) sırasında canlı video akışları alınır ve işlenir. Canlı bir olay iki türden biri olabilir: doğrudan geçiş ve canlı kodlama. Media Services v3 sürümünde canlı akış hakkında daha fazla bilgi için bkz. [canlı olaylar ve canlı çıktılar](live-events-outputs-concept.md).

### <a name="pass-through"></a>Geçiş

![doğrudan geçiş](./media/live-streaming/pass-through.svg)

Geçiş **canlı olayını**kullanırken, çoklu bit hızı video akışı oluşturmak için şirket içi Live Encoder ' ı kullanır ve canlı olaya katkı akışı olarak (RTMP veya PARÇALANMıŞ-MP4 giriş protokolünü kullanarak) gönderebilirsiniz. Canlı etkinlik daha sonra gelen video akışlarını dinamik paketlemeden (akış uç noktası) daha fazla kodlama olmadan taşır. Bu tür bir geçişli canlı etkinlik, uzun süreli canlı etkinlikler veya 24x365 doğrusal canlı akış için iyileştirilmiştir. 

### <a name="live-encoding"></a>Live encoding  

![gerçek zamanlı kodlama](./media/live-streaming/live-encoding.svg)

Media Services ile bulut kodlaması kullanırken, şirket içi Live Encoder ' ı canlı olaya (RTMP veya parçalanmış-MP4 giriş protokolünü kullanarak) katkı akışı (en fazla 32Mbps toplama) olarak tek bir bit hızlı video gönderecek şekilde yapılandırırsınız. Canlı olay aktarıcı, gelen tek [bit hızı akışını, her zaman](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) hızlı bir şekılde ve MPEG-Dash, Apple http canlı akışı (HLS) ve Microsoft kesintisiz akış gibi endüstri standardı protokoller aracılığıyla oynatma için kullanılabilir hale getirir. 

### <a name="live-transcription"></a>Canlı döküm

Canlı döküm, doğrudan geçiş ya da canlı kodlama olan canlı olaylarla kullanabileceğiniz bir özelliktir. Daha fazla bilgi için bkz. [canlı](live-transcription.md)döküm. Bu özellik etkinleştirildiğinde, hizmet bilişsel hizmetler 'in [konuşmadan metne](../../cognitive-services/speech-service/speech-to-text.md) özelliğini kullanarak gelen seslerdeki konuşulan kelimeleri metne dönüştürür. Bu metin daha sonra MPEG-DASH ve HLS protokollerinde video ve ses ile birlikte teslim için kullanılabilir hale getirilir.

> [!NOTE]
> Şu anda, canlı döküm Batı ABD 2 ' de önizleme özelliği olarak sunulmaktadır.

## <a name="live-streaming-workflow"></a>Canlı akış iş akışı

Media Services v3 sürümünde canlı akış iş akışını anlamak için öncelikle aşağıdaki kavramları gözden geçirmeniz ve anlamanız gerekir: 

- [Akış Uç Noktaları](streaming-endpoint-concept.md)
- [Canlı Etkinlikler ve Canlı Çıkışlar](live-events-outputs-concept.md)
- [Akış Bulucular](streaming-locators-concept.md)

### <a name="general-steps"></a>Genel adımlar

1. Media Services hesabınızda, **akış uç noktasının** (Origin) çalıştığından emin olun. 
2. Canlı bir [olay](live-events-outputs-concept.md)oluşturun. <br/>Olayı oluştururken, başlatmayı belirtebilirsiniz. Alternatif olarak, akışı başlatmaya hazırsanız olayını başlatabilirsiniz.<br/> Autostart değeri true olarak ayarlandığında, canlı olay oluşturulduktan sonra hemen başlatılır. Faturalandırma, canlı olay çalışmaya başladıktan hemen sonra başlar. Daha fazla faturalandırmayı durdurmak için canlı olay kaynağında durdurmayı açıkça çağırmanız gerekir. Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing.md).
3. Gelen URL 'leri alın ve şirket içi kodlayıcınızı, katkı akışını göndermek için URL 'YI kullanacak şekilde yapılandırın.<br/>Bkz. [Önerilen canlı kodlayıcılar](recommended-on-premises-live-encoders.md).
4. Kodlayıcının girişinin gerçekten alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
5. Yeni bir **varlık** nesnesi oluşturun. 

    Her canlı çıktı bir varlıkla ilişkilendirilir ve bu, videoyu ilişkili Azure Blob depolama kapsayıcısına kaydetmek için kullanır. 
6. Canlı bir **çıktı** oluşturun ve akışın kıymete arşivlenmesi için oluşturduğunuz varlık adını kullanın.

    Canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur. Canlı çıktıyı sildiğinizde, ilgili varlık ve varlığın içeriğini silmezsiniz.
7. [Yerleşik akış ilkesi türleriyle](streaming-policy-concept.md)bir **akış Bulucu** oluşturun.

    Canlı çıktıyı yayımlamak için ilişkili varlık için bir akış Bulucu oluşturmanız gerekir. 
8. Kullanılacak URL 'Leri geri almak için **akış bulucunun** yollarını listeleyin (bunlar belirleyici 'dir).
9. Akışı yapmak istediğiniz **akış uç noktası** (başlangıç) için ana bilgisayar adını alın.
10. Tam URL 'yi almak için, adım 8 ' deki URL 'YI adım 9 ' daki ana bilgisayar adı ile birleştirin.
11. **Canlı olaylarınızın** görüntülenmesini durdurmak isterseniz, olay akışını durdurup **akış bulucuyu**silmeniz gerekir.
12. Olayların akışla aktarılmasını tamamlayıp önceden sağlanan kaynakları temizlemek istediğinizde aşağıdaki yordamı izleyin.

    * Kodlayıcıdan akışı göndermeyi durdurun.
    * Canlı etkinliği durdurun. Canlı etkinlik durdurulduktan sonra ücret ödemeyecektir. Tekrar başlatmanız gerektiğinde, aynı alma URL’sine sahip olacağından kodlayıcıyı yeniden yapılandırmanız gerekmez.
    * Canlı olayınızın arşivini isteğe bağlı bir akış olarak sunmaya devam etmek istemiyorsanız Akış Uç Noktanızı durdurabilirsiniz. Canlı etkinlik durdurulmuş durumdaysa, hiçbir ücret ödemeyecektir.

Canlı çıktının Arşivlenmesi gereken varlık, canlı çıktı silindiğinde otomatik olarak isteğe bağlı bir varlık haline gelir. Canlı bir olay durdurulmadan önce tüm canlı çıktıları silmeniz gerekir. Durdurulduğunda canlı çıktıları otomatik olarak kaldırmak için, bir [Removeoutputsonstop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) isteğe bağlı bayrağını kullanabilirsiniz. 

> [!TIP]
> [Canlı akış öğreticisine](stream-live-tutorial-with-api.md)bakın. makalede yukarıda açıklanan adımları uygulayan kod incelandı.

## <a name="other-important-articles"></a>Diğer önemli makaleler

- [Önerilen canlı kodlayıcılar](recommended-on-premises-live-encoders.md)
- [Bulut DVR kullanma](live-event-cloud-dvr.md)
- [Canlı olay türleri özelliği karşılaştırması](live-event-types-comparison.md)
- [Durumlar ve faturalandırma](live-event-states-billing.md)
- [Gecikme süresi](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)
* [Media Services V2 'den v3 'e geçmek için geçiş kılavuzu](migrate-from-v2-to-v3.md)
