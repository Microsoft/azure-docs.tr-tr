---
title: Azure Media Services terminoloji ve kavramlar-Azure | Microsoft Docs
description: Bu konu, Azure Media Services terminoloji ve kavramlar hakkında kısa bir genel bakış sunar ve daha ayrıntılı bilgi için bağlantılar sağlar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910311"
---
# <a name="media-services-concepts"></a>Media Services kavramlar

Bu konu, Azure Media Services terminoloji ve kavramlar hakkında kısa bir genel bakış sunar. Makale Ayrıca, Media Services v3 kavramlarını ve işlevselliğini ayrıntılı olarak içeren makalelere bağlantılar sağlar. 

Bu konularda açıklanan temel kavramlar, geliştirmeye başlamadan önce incelenmelidir.

> [!NOTE]
> Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

## <a name="terminology"></a>Terminoloji

Bu bölümde, bazı yaygın sektör koşullarının Media Services v3 API 'sine nasıl eşlendiğini gösterilmektedir.

### <a name="live-event"></a>Canlı Etkinlik

**Canlı bir olay** , video, ses ve gerçek zamanlı meta verilerin canlı akışını geri almak, kodlamak (isteğe bağlı olarak) ve paketlenmesi için bir işlem hattı temsil eder.

Media Services V2 API 'Lerinden geçiş yapmak için **canlı olay** , v2 'deki **Kanal** varlığının yerini alır. Daha fazla bilgi için bkz. [v2 'den v3 'e geçiş](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Akış uç noktası (Paketleme ve başlangıç)

**Akış uç noktası** , ortak akış medya protokollerinden (HLS veya Dash) birini kullanarak canlı ve isteğe bağlı içeriğinizi doğrudan bir istemci oynatıcı uygulamasına teslim edebilen dinamik (tam zamanında) paketleme ve kaynak hizmetini temsil eder. Ayrıca, **akış uç noktası** sektör lideri drms için dinamik (tam zamanında) şifreleme sağlar.

Medya akışı sektöründe bu hizmet genellikle bir **Paketleyici** veya **kaynak**olarak adlandırılır.  Sektörün bu özellik için diğer yaygın terimleri, JıOP (tam zamanında Paketleyici) veya JıITE (tam zamanında şifreleme) içerir. 
 
## <a name="cloud-upload-and-storage"></a>Bulutta karşıya yükleme ve depolama

Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız ve dijital dosyalarınızı **varlıklara**yüklemeniz gerekir.

- [Bulutta karşıya yükleme ve depolama](storage-account-concept.md)
- [Varlık kavramı](assets-concept.md)

## <a name="encoding"></a>Encoding

Yüksek kaliteli dijital medya dosyalarınızı varlıklara yükledikten sonra, bunları çok çeşitli tarayıcılarda ve cihazlarda yürütülebilecek biçimlere kodlayabilirsiniz. 

Media Services v3 ile kodlamak için **dönüşümler** ve **işler**oluşturmanız gerekir.

![Dönüşümler](./media/encoding/transforms-jobs.png)

- [Dönüşümler ve Işler](transforms-jobs-concept.md)
- [Media Services kodlama](encoding-concept.md)

## <a name="media-analytics"></a>Medya analizi

Video ve ses dosyalarınızı analiz etmek için **dönüşümler** ve **işler**de oluşturmanız gerekir.

- [Video ve ses dosyalarını çözümleme](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Paketleme, teslim, koruma

İçeriğiniz kodlandıktan sonra, **dinamik paketleme**özelliğinden yararlanabilirsiniz. Media Services, **akış uç noktası**/Origin, istemci oynatıcılara medya içeriği göndermek için kullanılan dinamik paketleme hizmetidir. Çıktı kıymetindeki videoların, kayıttan yürütmeye yönelik olarak kullanılabilmesini sağlamak için bir **akış Bulucu** oluşturmanız ve ardından akış URL 'leri oluşturmanız gerekir. 

**Akış bulucuyu**oluştururken, varlığın adının yanı sıra **akış ilkesi**belirtmeniz gerekir. Akış **ilkeleri** , akış **Konumlandırıcıları**için akış protokollerini ve şifreleme seçeneklerini (varsa) tanımlamanıza olanak sağlar.

Dinamik paketleme, içeriğinizi canlı veya isteğe bağlı olarak akışınızdan bağımsız olarak kullanılır. Aşağıdaki diyagramda, dinamik paketleme iş akışı ile isteğe bağlı akış gösterilmektedir.

![Dinamik paketleme](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Media Services ile, canlı ve isteğe bağlı içeriğinizi Gelişmiş Şifreleme Standardı (AES-128) veya/ve üç ana dijital hak yönetimi (DRM) sisteminden dinamik olarak şifreli olarak dağıtabilirsiniz: Microsoft PlayReady, Google Widevine ve Apple FairPlay. Media Services de AES anahtarları ve DRM sunmaya yönelik bir hizmet sağlar (PlayReady, Widevine ve FairPlay) lisansları yetkili istemcilere.

Akışınıza şifreleme seçenekleri belirtiyorsanız, **Içerik anahtarı ilkesini** oluşturun ve **akış bulucuınızla**ilişkilendirin. **Içerik anahtarı ilkesi** , içerik anahtarının son istemcilere nasıl teslim edildiğini yapılandırmanızı sağlar.

Aşağıdaki resimde Media Services content protection iş akışı gösterilmektedir: 

![İçerik koruma](./media/content-protection/content-protection.svg)

&#42;dinamik şifreleme, AES-128 "Clear Key", CBCS ve CENC 'yi destekler. 

Videonuzun yalnızca belirli bir işlemesini veya alt kliplerini akışa almak için **dinamik bildirimleri** Media Services kullanabilirsiniz. Aşağıdaki örnekte, bir Mezzanine örneğini yedi ISO MP4 'leri video yorumlamasına (180 p-1080p) kodlamak için bir kodlayıcı kullanılmıştır. Kodlanmış varlık, aşağıdaki akış protokollerinden herhangi birine dinamik olarak paketlenebilir: HLS, MPEG DASH ve sorunsuz.  Diyagramın en üstünde, filtre olmadan varlık için HLS bildirimi gösterilir (tüm yedi yorumlamaları içerir).  Sol alt tarafta, "Ott" adlı bir filtrenin uygulandığı HLS bildirimi gösterilmiştir. "Ott" filtresi, 1 MB/sn 'nin altındaki tüm bit hızlarının kaldırılması gerektiğini belirtir. Bu, doğru iki kalite düzeyinin yanıtta kaldırılmasına neden olur. Sağ alt köşede, "mobil" adlı bir filtrenin uygulandığı HLS bildirimi gösterilir. "Mobil" filtresi, çözümlemenin 720 ' den büyük olduğu ve iki 1080p çevirinin kaldırılmasına neden olan çevirileri kaldırmayı belirtir.

![İşleme filtrelemesi](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dinamik paketleme](dynamic-packaging-overview.md)
- [Akış Uç Noktaları](streaming-endpoint-concept.md)
- [Akış Bulucular](streaming-locators-concept.md)
- [Akış İlkeleri](streaming-policy-concept.md)
- [İçerik Anahtarı İlkeleri](content-key-policy-concept.md)
- [İçerik koruma](content-protection-overview.md)
- [Dinamik bildirimler](filters-dynamic-manifest-overview.md)
- [Filtreleri](filters-concept.md)

## <a name="live-streaming"></a>Canlı akış

Azure Media Services, Azure bulutunda müşterilerinize canlı olaylar sunmanıza olanak sağlar. **Canlı Etkinlikler** sırasında canlı video akışları alınır ve işlenir. **Canlı bir olay**oluşturduğunuzda, uzak bir kodlayıcıdan canlı bir sinyal göndermek için kullanabileceğiniz bir giriş uç noktası oluşturulur. Akışın **canlı olayına**akışını tamamladıktan sonra bir **varlık**, **canlı çıkış**ve **akış Bulucu**oluşturarak akış olayını başlatabilirsiniz. **Canlı çıktı** , akışı **varlığa** Arşivle ve **akış uç noktası**aracılığıyla görüntüleyicilerin kullanımına açık hale getirir. **Canlı bir olay** iki türden biri olabilir: **doğrudan geçiş** ve **canlı kodlama**.

Aşağıdaki görüntüde, geçişli tür iş akışı gösterilmektedir:

![doğrudan geçiş](./media/live-streaming/pass-through.svg)

- [Canlı akışa genel bakış](live-streaming-overview.md)
- [Canlı Etkinlikler ve Canlı Çıkışlar](live-events-outputs-concept.md)

## <a name="monitoring"></a>İzleme

### <a name="event-grid"></a>Event Grid

İşin ilerlemesini görmek için **Event Grid**kullanmanız gerekir. Media Services Ayrıca canlı olay türlerini de yayar. Event Grid ile uygulamalarınız neredeyse tüm Azure hizmetleri ve özel kaynaklardan gelen olayları takip edip bu olaylara yanıt verebilir. 

- [Event Grid olaylarını işleme](reacting-to-media-services-events.md)
- [Şemaları](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure İzleyici

Uygulamalarınızın Azure Izleyici ile nasıl çalıştığını anlamanıza yardımcı olan ölçümleri ve tanılama günlüklerini izleyin.

- [Ölçümler ve tanılama günlükleri](media-services-metrics-diagnostic-logs.md)
- [Tanılama günlükleri şemaları](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Yürütücü istemcileri

Çok çeşitli tarayıcılarda ve cihazlarda Media Services tarafından akan medya içeriğini oynatmak için Azure Media Player kullanabilirsiniz. Azure Media Player HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak için kullanır. 

- [Azure Media Player'a genel bakış](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Uzak dosya ve akış videosunu kodla – REST](stream-files-tutorial-with-rest.md)
* [Karşıya yüklenen dosya ve akış videosunu kodla-.NET](stream-files-tutorial-with-api.md)
* [Canlı akış-.NET](stream-live-tutorial-with-api.md)
* [Videonuzu çözümleyin-.NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamik şifreleme-.NET](protect-with-aes128.md)
* [Multi-DRM-.NET ile dinamik olarak şifreleyin](protect-with-drm.md) 
