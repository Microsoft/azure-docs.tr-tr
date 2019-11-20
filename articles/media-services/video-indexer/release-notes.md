---
title: Azure Media Services Video Indexer sürüm notları | Microsoft Docs
description: En son gelişmelerden haberdar olmak için, bu makalede Azure Media Services Video Indexer en son güncelleştirmeleri sunulmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 0583c0093d240026e3ebcdad7b14494f07986ec2
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968724"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer sürüm notları

En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

* En son yayınlar
* Bilinen sorunlar
* Hata düzeltmeleri
* Kullanım dışı işlev

## <a name="october-2019"></a>2019 Ekim
 
* Galerideki animasyonlu karakterleri arama

    Animasyonlu karakterleri dizinlerken, artık hesabın video Gale ' i içinde arama yapabilirsiniz. Daha fazla bilgi için bkz. [animasyonlu karakter tanıma](animated-characters-recognition.md).

## <a name="september-2019"></a>Eylül 2019
 
IBC 2019 ' de birden çok terfi duyurusu:
 
* Animasyonlu karakter tanıma (Genel Önizleme)

    Özel Vision ile tümleştirme yoluyla, animasyonlu içerikte grup ad tanıma karakterlerini algılama özelliği. Daha fazla bilgi için bkz. [animasyonlu karakter algılama](animated-characters-recognition.md).
* Çoklu dil tanımlama (Genel Önizleme)

    Ses kanalında birden çok dildeki segmentleri algılayın ve bunlara göre çok dilli bir döküm oluşturun. İlk destek: Ingilizce, Ispanyolca, Almanca ve Fransızca. Daha fazla bilgi için bkz. [Çoklu dil Içeriğini otomatik olarak tanımla ve yeniden tanımla](multi-language-identification-transcription.md).
* Kişiler ve konum için adlandırılmış varlık ayıklama

    Marka, konum ve kişileri, doğal dil işleme (NLP) aracılığıyla konuşma ve görsel metinden ayıklar.
* Düzenleme görüntüsü türü sınıflandırması

    Yakın, orta görüntü, iki görüntü, ınkapılı, dış mek, vb. gibi düzenleme türleri ile anlık görüntü etiketleme. Daha fazla bilgi için bkz. [düzenleme görüntüsü türü algılama](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Konu düğümü Netleştirme geliştirme-artık düzey 2 ' ye sahip
    
    Bu konu başlığı altında, ıPTC sınıflandırmanın daha derin ayrıntı düzeyi artık desteklenir. [Yeni Azure MEDIA SERVICES AI destekli yeniliklerin](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)tüm ayrıntılarını okuyun.

## <a name="august-2019"></a>Ağustos 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>UK Güney Video Indexer dağıtıldı

Artık UK Güney bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz.

### <a name="new-editorial-shot-type-insights-available"></a>Yeni düzenleme resmi türü Öngörüler mevcuttur

Video görüntüleriyle eklenen yeni Etiketler, bunları, içerik oluşturma iş akışında kullanılan yaygın düzenleme tümcecikleriyle tanımlamak için düzenleme "görüntü türleri" sağlar: en çok closeup, wiup, geniş, orta, iki görüntü, dış mek, ınkapılı, sol yüz ve sağ yüz ( JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Yeni kişiler ve konumlar varlıkları ayıklama kullanılabilir

Video Indexer, videonun OCR ve dökümünü kullanarak doğal dil işleme (NLP) aracılığıyla adlandırılmış konumları ve kişileri tanımlar. Video Indexer, belirli konumların (örneğin, Eiffel kulede) veya kişilerden (örneğin John tikan) bir videoda nasıl çağrılmakta olduğunu tanımak için makine öğrenimi algoritmasını kullanır.

### <a name="keyframes-extraction-in-native-resolution"></a>Yerel çözünürlükte ana kare ayıklama

Video Indexer tarafından ayıklanan ana kareler, videonun özgün çözünürlüğünde kullanılabilir.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Görüntülerdeki özel yüz modellerini eğitmek için GA

Önizleme modundan aşağı (API ile ve portalda kullanılabilir) taşınan görüntülerden eğitim yüzeyleri.

> [!NOTE]
> "GA Önizleme" geçişi ile ilgili bir fiyatlandırma etkisi yoktur.

### <a name="hide-gallery-toggle-option"></a>Galeri değiştirme seçeneğini gizle

Kullanıcı, portalda Galeri sekmesini gizlemeyi seçebilir (örnekler sekmesini gizlemeye benzer).
 
### <a name="maximum-url-size-increased"></a>En büyük URL boyutu artırıldı

Video dizinleme sırasında 4096 (2048 yerine) URL sorgu dizesi desteği.
 
### <a name="support-for-multi-lingual-projects"></a>Çok dilli projeler için destek

Projeler artık farklı dillerde (yalnızca API) dizin oluşturulmuş videolar temel alınarak oluşturulabilir.

## <a name="july-2019"></a>Temmuz 2019

### <a name="editor-as-a-widget"></a>Pencere öğesi olarak düzenleyici

Video Indexer AI Düzenleyicisi, artık müşteri uygulamalarına katıştırılacak bir pencere öğesi olarak sunulmaktadır.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Özel dil modelini portaldan kapalı açıklamalı altyazı dosyasından Güncelleştir

Müşteriler, portalın Özelleştirme sayfasında dil modelleri için giriş olarak VTT, SRT ve TTML dosya biçimleri sağlayabilir.

## <a name="june-2019"></a>Haziran 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer Japonya Doğu dağıtıldı

Artık Japonya Doğu bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz.

### <a name="create-and-repair-account-api-preview"></a>Hesap API 'SI oluşturma ve onarma (Önizleme)

[Azure Media Service bağlantı noktasını veya anahtarını güncelleştirmenizi](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)sağlayan yenı bir API eklendi.

### <a name="improve-error-handling-on-upload"></a>Karşıya yükleme sırasında hata işlemeyi geliştirme 

Temel alınan Azure Media Services hesabının yanlış yapılandırılması durumunda açıklayıcı bir ileti döndürülür.

### <a name="player-timeline-keyframes-preview"></a>Oyuncu zaman çizelgesi ana kare önizlemesi 

Artık Player 'ın zaman çizelgesinde her seferinde bir görüntü önizlemesi görebilirsiniz.

### <a name="editor-semi-select"></a>Düzenleyici yarı seçim

Artık düzenleyicide belirli bir öngörü zaman dilimini seçme sonucu olarak seçilen tüm öngörülerin önizlemesini görebilirsiniz.

## <a name="may-2019"></a>Mayıs 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Özel dil modelini kapalı açıklamalı altyazı dosyasından Güncelleştir

[Özel dil modeli oluşturma](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) ve [özel dil modellerini güncelleştirme](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API 'leri artık dil modellerı için girdi olarak VTT, SRT ve ttml dosya biçimlerini desteklemektedir.

[Update video döküm API 'si](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)çağrılırken, döküm otomatik olarak eklenir. Videoyla ilişkili eğitim modeli de otomatik olarak güncelleştirilir. Dil modellerinizi özelleştirme ve eğitme hakkında daha fazla bilgi için bkz. [video Indexer bir dil modelini özelleştirme](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Yeni karşıdan yükleme TRANSCRIPT biçimleri – TXT ve CSV

Zaten desteklenen (SRT, VTT ve TTML) kapalı açıklamalı alt yazı biçiminin yanı sıra, Video Indexer artık kodu TXT ve CSV biçimlerinde indirmeyi desteklemektedir.

## <a name="next-steps"></a>Sonraki adımlar

[Genel Bakış](video-indexer-overview.md)
