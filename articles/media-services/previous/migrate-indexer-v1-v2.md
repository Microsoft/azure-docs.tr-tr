---
title: V1 ve v2 Dizin oluşturucudan Azure Media Services Video Indexer | Microsoft Docs
description: Bu konu, Azure Media Indexer v1 ve v2 'den Azure Media Services Video Indexer geçiş yapılacağını açıklamaktadır.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: d38b11b8fc4351c6b074ccfdf47df06a71e0a0a4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823963"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Media Indexer ve Media Indexer 2 ' den Video Indexer 'e geçiş

[Azure Media Indexer](media-services-index-content.md) medya işlemcisi, 1 Ekim 2020 ' de kullanımdan kaldırılacaktır. [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) medya Işlemcileri 1 Ocak 2020 tarihinde kullanımdan kaldırılacaktır.  [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) bu eski medya işlemcilerinin yerini alır.

Azure Media Services Video Indexer Azure Media Analytics, Azure Bilişsel Arama, bilişsel hizmetler (Yüz Tanıma API'si, Microsoft Translator, Görüntü İşleme API'si ve Özel Konuşma Tanıma hizmeti) üzerinde oluşturulmuştur. Video Indexer’ın görüntülü ve sesli modellerini kullanarak videolarınızdan içgörü ayıklamanıza olanak sağlar. Hangi senaryolar Video Indexer kullanılabileceğini, hangi özellikleri sunduğunu ve nasıl başladıklarınızı görmek için bkz. [video Indexer video ve ses modelleri](../video-indexer/video-indexer-overview.md). 

[Azure Media Services v3 çözümleyici önayarlarını](../latest/analyzing-video-audio-files-concept.md) kullanarak veya doğrudan [video Indexer API 'lerini](https://api-portal.videoindexer.ai/)kullanarak video ve ses dosyalarınızda öngörüleri ayıklayabilirsiniz. Şu anda, Video Indexer API 'leri ve Media Services v3 API 'Leri tarafından sunulan özellikler arasında bir çakışma var.

> [!NOTE]
> Video Indexer ve Media Services çözümleyici önayarlarının ne zaman kullanılacağını anlamak için [karşılaştırma belgesine](../video-indexer/compare-video-indexer-with-media-services-presets.md)göz atın. 

Bu makalede, Azure Media Indexer geçiş adımlarını ve Azure Media Indexer 2 ' ye Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Geçiş seçenekleri 

|İhtiyacınız varsa  |Ni |
|---|---|
|Kapalı açıklamalı altyazı dosyası biçimlerinde herhangi bir medya dosyası biçimi için konuşmayı metne dönüştürme sağlayan bir çözüm: VTT, SRT veya TTML<br/>Aşağıdakiler gibi ek ses içgörüler: anahtar sözcükler, konu ınilleme, akustik etkinlikler, konuşmacı atlama, varlık ayıklama ve çeviri| uygulamalarınızı, Video Indexer v2 REST API veya Azure Media Services v3 ses Çözümleyicisi önceden ayarı aracılığıyla Azure Video Indexer yeteneklerini kullanacak şekilde güncelleştirin.|
|Konuşmadan metne özellikleri| bilişsel hizmetler konuşma API 'sini doğrudan kullanın.|  

## <a name="getting-started-with-video-indexer"></a>Video Indexer kullanmaya başlama

Aşağıdaki bölümde ilgili bağlantılar [gösterilmektedir: video Indexer kullanmaya nasıl başlamalıyım?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 API 'Leri ile çalışmaya başlama

Azure Media Services v3 API, [Azure Media Services v3 Çözümleyicisi ön ayarları](../latest/analyzing-video-audio-files-concept.md)aracılığıyla video ve ses dosyalarından öngörüleri ayıklamanızı sağlar. 

**Audioanalsete önceden ayarlanmış** , bir ses veya video dosyasından birden çok ses öngörülerini ayıklamanızı sağlar. Çıktı, ses dökümü için bir VTT veya TTML dosyası ve bir JSON dosyası (tüm ek ses öngörüleri ile) içerir. Ses öngörüleri, anahtar sözcükleri, konuşmacı dizin oluşturma ve konuşma yaklaşımı analizini içerir. Audioanalönayarı belirli diller için dil algılamayı de destekler. Ayrıntılı bilgi için bkz. [dönüşümler](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Başlarken

Başlamak için bkz:

* [Öğretici](../latest/analyze-videos-tutorial-with-api.md)
* Audioanaliz Zerönayar örnekleri: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) veya [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Videoanaliz Zerönayar örnekleri: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) veya [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Bilişsel hizmetler konuşma Hizmetleri 'ni kullanmaya başlama

[Azure](https://docs.microsoft.com/azure/cognitive-services/) bilişsel hizmetler, uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntülemesi için ses akışlarını gerçek zamanlı olarak metne aktarmak için bir konuşma konuşmadan metne bir hizmet sağlar. [Kendi akustik modelinizi, dil modelinizi veya telaffuz modelinizi özelleştirmek](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)için konuşmayı metne dönüştürme özelliğini kullanabilirsiniz. Daha fazla bilgi için bkz. bilişsel [Hizmetler konuşmayı metne](../../cognitive-services/speech-service/speech-to-text.md)dönüştürme. 

> [!NOTE] 
> Konuşmaya metin hizmeti video dosya biçimlerini almaz ve yalnızca [belirli ses biçimlerini](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)alır. 

Metinden konuşmaya hizmeti ile çalışmaya başlama hakkında daha fazla bilgi için bkz. [konuşmayı metne](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text) dönüştürme nedir?

## <a name="known-differences-from-deprecated-services"></a>Kullanım dışı hizmetler 'den bilinen farklılıklar 

Video Indexer, Azure Media Services v3 Audioanalönayarı ve bilişsel hizmetler konuşma Hizmetleri hizmetlerinin daha güvenilir olduğunu ve kullanımdan kaldırılan Azure Media Indexer 1 ve Azure Media Indexer 2 işlemcinden daha iyi kaliteli çıkış ürettiğini göreceksiniz.  

Bazı bilinen farklılıklar şunlardır: 

* SAMı biçimindeki kapalı açıklamalı altyazı dosyaları artık desteklenmeyecektir. Bu, artık yaygın olarak kullanılmayan eski bir başlık biçimidir. TTML, WebVTT ve SRT ile değiştirilmiştir.  
* Ses dizin oluşturma Blobu (AıB) dosyaları artık desteklenmeyecektir. Bu özellik, Dizin Oluşturucu 1 teknolojisine özeldir ve artık sunulmamaktadır.  
* Bilişsel hizmetler konuşma Hizmetleri anahtar sözcüğünün ayıklanmasını desteklemez. Ancak, Video Indexer ve Media Services v3 Audioanalönayarı, JSON dosya biçiminde daha sağlam bir anahtar sözcük kümesi sunmaktadır. 

## <a name="need-help"></a>Yardım mı gerekiyor?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

* [Eski bileşenler](legacy-components.md)
* [Fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/media-services/#encoding)


