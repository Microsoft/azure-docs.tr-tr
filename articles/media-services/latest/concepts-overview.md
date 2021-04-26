---
title: Media Services terminoloji ve kavramlar
description: Azure Media Services yönelik terminoloji ve kavramlar hakkında bilgi edinin.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: c015dfa668d815f86022a6f0c654df49416d9cd6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279587"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services terminoloji ve kavramlar

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu konu, Azure Media Services terminoloji ve kavramlar hakkında kısa bir genel bakış sunar. Makalede ayrıca Media Services v3 kavramları ve işlevselliği hakkında derinlemesine bir açıklama içeren makalelere bağlantılar da sağlanmaktadır.

Bu konularda açıklanan temel kavramlar, geliştirmeye başlamadan önce incelenmelidir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 terminolojisi

|Süre|Açıklama|
|---|---|
|Canlı etkinlik|**Canlı bir olay** , video, ses ve gerçek zamanlı meta verilerin canlı akışını geri almak, kodlamak (isteğe bağlı olarak) ve paketlenmesi için bir işlem hattı temsil eder.<br/><br/>Media Services V2 API 'Lerinden geçiş yapmak için **canlı olay** , v2 'deki **Kanal** varlığının yerini alır. Daha fazla bilgi için bkz. [v2 'den v3 'e geçiş](migrate-v-2-v-3-migration-introduction.md).|
|Akış uç noktası/paketleme/kaynak|**Akış uç noktası** , canlı ve isteğe bağlı içeriğinizi doğrudan bir istemci oynatıcı uygulamasına teslim edebilen dinamik (tam zamanında) paketleme ve Origin hizmetini temsil eder. Ortak akış medya protokollerinden birini (HLS veya DASH) kullanır. Ayrıca, **akış uç noktası** , sektör lideri dijital hak yönetimi sistemleri (drms) için dinamik (tam zamanında) şifreleme sağlar.<br/><br/>Medya akışı sektöründe bu hizmet genellikle bir **Paketleyici** veya **kaynak** olarak adlandırılır.  Sektörün bu özellik için diğer yaygın terimleri, JıOP (tam zamanında Paketleyici) veya JıITE (tam zamanında şifreleme) içerir.

## <a name="media-services-v3-concepts"></a>Media Services v3 kavramları

|Kavramlar|Description|Bağlantılar|
|---|---|---|
|Varlıklar ve karşıya içerik yükleme|Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız ve dijital dosyalarınızı **varlıklara** yüklemeniz gerekir.|[Bulutta karşıya yükleme ve depolama](storage-account-concept.md)<br/><br/>[Varlık kavramı](assets-concept.md)|
|İçerik kodlama|Yüksek kaliteli dijital medya dosyalarınızı varlıklara yükledikten sonra, bunları çok çeşitli tarayıcılarda ve cihazlarda yürütülebilecek biçimlere kodlayabilirsiniz. <br/><br/>Media Services v3 ile kodlamak için **dönüşümler** ve **işler** oluşturmanız gerekir.|[Dönüşümler ve Işler](transform-jobs-concept.md)<br/><br/>[Media Services kodlama](encode-concept.md)|
|İçerik çözümleme (Video Indexer)|Media Services v3, Media Services v3 önayarlarını kullanarak video ve Ses dosyalarınızın öngörülerini ayıklamanıza olanak tanır. Media Services v3 ön ayarlarını kullanarak içeriğinizi çözümlemek için **dönüşümler** ve **işler** oluşturmanız gerekir.<br/><br/>Daha ayrıntılı Öngörüler istiyorsanız [video Indexer](../video-indexer/index.yml) doğrudan kullanın.|[Video ve ses dosyalarını çözümleme](analyze-video-audio-files-concept.md)|
|Paketleme ve teslim|İçeriğiniz kodlandıktan sonra, **dinamik paketleme** özelliğinden yararlanabilirsiniz. Media Services, bir **akış uç noktası** , istemci oynatıcılara medya içeriği göndermek için kullanılan dinamik paketleme hizmetidir. Çıktı kıymetindeki videoların, kayıttan yürütmeye yönelik olarak kullanılabilmesini sağlamak için bir **akış Bulucu** oluşturmanız ve ardından akış URL 'leri oluşturmanız gerekir. <br/><br/>**Akış bulucuyu** oluştururken, varlığın adına ek olarak, **akış ilkesi** belirtmeniz gerekir. Akış **ilkeleri** , akış **Konumlandırıcıları** için akış protokollerini ve şifreleme seçeneklerini (varsa) tanımlamanıza olanak sağlar. Dinamik paketleme, içeriğinizi canlı veya isteğe bağlı olarak akışınızdan bağımsız olarak kullanılır. <br/><br/>Videonuzun yalnızca belirli bir işlemesini veya alt kliplerini akışa almak için **dinamik bildirimleri** Media Services kullanabilirsiniz.|[Dinamik paketleme](encode-dynamic-packaging-concept.md)<br/><br/>[Akış uç noktaları](stream-streaming-endpoint-concept.md)<br/><br/>[Akış Konumlandırıcı](stream-streaming-locators-concept.md)<br/><br/>[Akış Ilkeleri](stream-streaming-policy-concept.md)<br/><br/>[Dinamik bildirimler](filters-dynamic-manifest-concept.md)<br/><br/>[Filtreler](filters-concept.md)|
|İçerik koruma|Media Services ile canlı ve isteğe bağlı içeriğinizi Gelişmiş Şifreleme Standardı (AES-128) veya/ve üç ana DRM sisteminden (Microsoft PlayReady, Google Widevine ve Apple FairPlay) dinamik olarak şifreli olarak dağıtabilirsiniz. Media Services, yetkili istemcilere AES anahtarları ve DRM (PlayReady, Widevine ve FairPlay) lisanslarını sunmaya yönelik bir hizmet sağlar. <br/><br/>Akışınıza şifreleme seçenekleri belirtiyorsanız, **Içerik anahtarı ilkesini** oluşturun ve **akış bulucuınızla** ilişkilendirin. **Içerik anahtarı ilkesi** , içerik anahtarının son istemcilere nasıl teslim edildiğini yapılandırmanızı sağlar.<br/><br/> Aynı seçenek gerektiği her seferinde ilkeleri yeniden kullanmayı deneyin.| [İçerik anahtarı Ilkeleri](drm-content-key-policy-concept.md)<br/><br/>[İçerik koruma](drm-content-protection-concept.md)|
|Canlı akış|Media Services, Azure bulutunda müşterilerinize canlı olaylar sunmanıza olanak sağlar. **Canlı Etkinlikler** sırasında canlı video akışları alınır ve işlenir. **Canlı bir olay** oluşturduğunuzda, uzak bir kodlayıcıdan canlı bir sinyal göndermek için kullanabileceğiniz bir giriş uç noktası oluşturulur. Akışın **canlı olayına** akışını tamamladıktan sonra bir **varlık**, **canlı çıkış** ve **akış Bulucu** oluşturarak akış olayını başlatabilirsiniz. **Canlı çıktı** , akışı **varlığa** Arşivle ve **akış uç noktası** aracılığıyla görüntüleyicilerin kullanımına açık hale getirir. Canlı bir olay, *doğrudan geçiş* (Şirket içi bir Live Encoder çoklu bit hızı akışı gönderir) veya *canlı kodlama* (Şirket içi bir Live Encoder tek bit hızı akışı gönderir) olarak ayarlanabilir. |[Canlı akışa genel bakış](stream-live-streaming-concept.md)<br/><br/>[Canlı Etkinlikler ve Canlı Çıkışlar](live-event-outputs-concept.md)|
|Event Grid ile izleme|İşin ilerlemesini görmek için **Event Grid** kullanın. Media Services Ayrıca canlı olay türlerini de yayar. Event Grid ile uygulamalarınız neredeyse tüm Azure hizmetleri ve özel kaynaklardan gelen olayları takip edip bu olaylara yanıt verebilir. |[Event Grid olaylarını işleme](monitoring/reacting-to-media-services-events.md)<br/><br/>[Şemalar](monitoring/media-services-event-schemas.md)|
|Azure Izleyici ile izleme|Uygulamalarınızın Azure Izleyici ile nasıl çalıştığını anlamanıza yardımcı olan ölçümleri ve tanılama günlüklerini izleyin.|[Ölçümler ve tanılama günlükleri](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Tanılama günlükleri şemaları](monitoring/monitor-media-services-data-reference.md)|
|Yürütücü istemcileri|Çok çeşitli tarayıcılarda ve cihazlarda Media Services tarafından akan medya içeriğini oynatmak için Azure Media Player kullanabilirsiniz. Azure Media Player, zenginleştirilmiş bir uyarlamalı akış deneyimi sağlamak üzere HTML5, medya kaynağı uzantıları (MSE) ve şifreli Medya Uzantıları (EME) gibi sektör standartlarını kullanır. |[Azure Media Player'a genel bakış](player-use-azure-media-player-how-to.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Uzak dosyayı kodlama ve videoyu akışla aktarma – REST](stream-files-tutorial-with-rest.md)
* [Karşıya yüklenen dosyayı kodlama ve videoyu akışla aktarma – .NET](stream-files-tutorial-with-api.md)
* [Canlı akış - .NET](stream-live-tutorial-with-api.md)
* [Videonuzu analiz etme - .NET](analyze-videos-tutorial.md)
* [AES-128 dinamik şifreleme - .NET](drm-playready-license-template-concept.md)
* [Multi-DRM-.NET ile dinamik olarak şifreleyin](drm-protect-with-drm-tutorial.md)