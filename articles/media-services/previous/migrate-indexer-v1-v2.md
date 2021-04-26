---
title: V1 ve v2 Dizin oluşturucudan Azure Media Services Video Indexer | Microsoft Docs
description: Bu konu, Azure Media Indexer v1 ve v2 'den Azure Media Services Video Indexer geçiş yapılacağını açıklamaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2b1960aa2d08a189e04aea8906c10e761dc1c57a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962654"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Media Indexer ve Media Indexer 2 ' den Video Indexer 'e geçiş

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Müşterilerin Dizin Oluşturucu v1 ve Dizin Oluşturucu v2, [Media Services v3 Audioanalön ayarlı temel modunu](../latest/analyze-video-audio-files-concept.md)kullanarak geçiş yapmanız önerilir. [Azure Media Indexer](media-services-index-content.md) medya işlemcisi ve [Azure Media Indexer 2 Preview](./legacy-components.md) medya işlemcileri kullanımdan kaldırılıyor. Kullanımdan kaldırma tarihleri için, bu [eski bileşenler](legacy-components.md) konusuna bakın.

Azure Media Services Video Indexer Azure Media Analytics, Azure Bilişsel Arama, bilişsel hizmetler (Yüz Tanıma API'si, Microsoft Translator, Görüntü İşleme API'si ve Özel Konuşma Tanıma hizmeti) üzerinde oluşturulmuştur. Video Indexer’ın görüntülü ve sesli modellerini kullanarak videolarınızdan içgörü ayıklamanıza olanak sağlar. Hangi senaryolar Video Indexer kullanılabileceğini, hangi özellikleri sunduğunu ve nasıl başladıklarınızı görmek için bkz. [video Indexer video ve ses modelleri](../video-indexer/video-indexer-overview.md). 

[Azure Media Services v3 çözümleyici önayarlarını](../latest/analyze-video-audio-files-concept.md) kullanarak veya doğrudan [video Indexer API 'lerini](https://api-portal.videoindexer.ai/)kullanarak video ve ses dosyalarınızda öngörüleri ayıklayabilirsiniz. Şu anda, Video Indexer API 'leri ve Media Services v3 API 'Leri tarafından sunulan özellikler arasında bir çakışma var.

> [!NOTE]
> Video Indexer ile Media Services çözümleyici ön ayarları arasındaki farkları anlamak için [karşılaştırma belgesine](../video-indexer/compare-video-indexer-with-media-services-presets.md)bakın.

Bu makalede, Azure Media Indexer geçiş adımlarını ve Azure Media Indexer 2 ' ye Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Geçiş seçenekleri

|İhtiyacınız varsa  |öyleyse |
|---|---|
|Kapalı açıklamalı altyazı dosyası biçimlerinde herhangi bir medya dosyası biçimi için konuşmayı metne dönüştürme sağlayan bir çözüm: VTT, SRT veya TTML<br/>Aşağıdakiler gibi ek ses içgörüler: anahtar sözcükler, konu ınilleme, akustik etkinlikler, konuşmacı atlama, varlık ayıklama ve çeviri| uygulamalarınızı, Video Indexer v2 REST API veya Azure Media Services v3 ses Çözümleyicisi önceden ayarı aracılığıyla Azure Video Indexer yeteneklerini kullanacak şekilde güncelleştirin.|
|Konuşmadan metne özellikleri| bilişsel hizmetler konuşma API 'sini doğrudan kullanın.|  

## <a name="getting-started-with-video-indexer"></a>Video Indexer kullanmaya başlama

Aşağıdaki bölümde ilgili bağlantılar [gösterilmektedir: video Indexer kullanmaya nasıl başlamalıyım?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 API 'Leri ile çalışmaya başlama

Azure Media Services v3 API, [Azure Media Services v3 Çözümleyicisi ön ayarları](../latest/analyze-video-audio-files-concept.md)aracılığıyla video ve ses dosyalarından öngörüleri ayıklamanızı sağlar.

**Audioanalsete önceden ayarlanmış** , bir ses veya video dosyasından birden çok ses öngörülerini ayıklamanızı sağlar. Çıktı, ses dökümü için bir VTT veya TTML dosyası ve bir JSON dosyası (tüm ek ses öngörüleri ile) içerir. Ses öngörüleri, anahtar sözcükleri, konuşmacı dizin oluşturma ve konuşma yaklaşımı analizini içerir. Audioanalönayarı belirli diller için dil algılamayı de destekler. Ayrıntılı bilgi için bkz. [dönüşümler](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>başlarken

Başlamak için bkz:

* [Öğretici](../latest/analyze-videos-tutorial.md)
* Audioanaliz Zerönayar örnekleri: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) veya [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Videoanaliz Zerönayar örnekleri: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) veya [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Bilişsel hizmetler konuşma Hizmetleri 'ni kullanmaya başlama

[Azure](../../cognitive-services/index.yml) bilişsel hizmetler, uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntülemesi için ses akışlarını gerçek zamanlı olarak metne aktarmak için bir konuşma konuşmadan metne bir hizmet sağlar. [Kendi akustik modelinizi, dil modelinizi veya telaffuz modelinizi özelleştirmek](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)için konuşmayı metne dönüştürme özelliğini kullanabilirsiniz. Daha fazla bilgi için bkz. bilişsel [Hizmetler konuşmayı metne](../../cognitive-services/speech-service/speech-to-text.md)dönüştürme. 

> [!NOTE] 
> Konuşmaya metin hizmeti video dosya biçimlerini almaz ve yalnızca [belirli ses biçimlerini](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)alır. 

Metinden konuşmaya hizmeti ile çalışmaya başlama hakkında daha fazla bilgi için bkz. [konuşmayı metne](../../cognitive-services/speech-service/speech-to-text.md) dönüştürme nedir?

## <a name="known-differences-from-deprecated-services"></a>Kullanım dışı hizmetler 'den bilinen farklılıklar

Video Indexer, Azure Media Services v3 Audioanalönayarı ve bilişsel hizmetler konuşma Hizmetleri hizmetlerinin daha güvenilir olduğunu ve kullanımdan kaldırılan Azure Media Indexer 1 ve Azure Media Indexer 2 işlemcinden daha iyi kaliteli çıkış ürettiğini göreceksiniz.  

Bazı bilinen farklılıklar şunlardır:

* Bilişsel hizmetler konuşma Hizmetleri anahtar sözcüğünün ayıklanmasını desteklemez. Ancak, Video Indexer ve Media Services v3 Audioanalönayarı, JSON dosya biçiminde daha sağlam bir anahtar sözcük kümesi sunmaktadır.

## <a name="support"></a>Destek

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

* [Eski bileşenler](legacy-components.md)
* [Fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/media-services/#encoding)