---
title: Azure Media Player tanıtımları
description: Bu sayfa Azure Media Player tanıtımları için bağlantıların bir listesini içerir.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/05/2021
ms.openlocfilehash: b67edd55906385f1eddeb576b5f4c97b18e10153
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448500"
---
# <a name="azure-media-player-demos"></a>Azure Media Player tanıtımları

Azure Media Player gösterileri için bağlantıların listesi aşağıda verilmiştir. GitHub 'dan tüm [Azure Media Player örneklerini](https://github.com/Azure-Samples/azure-media-player-samples) indirebilirsiniz.

## <a name="demo-listing"></a>Tanıtım listesi

| Örnek adı | JavaScript aracılığıyla programlama | HTML5 video öğesi aracılığıyla statik | Açıklama |
| ------------|----------------------------|-------------------------------------|--------------|
| Temel |
| Kaynağı ayarla | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Korumasız içeriği kayıttan yürütme.|
| Özellikler |
| VOD ad ekleme-çok büyük | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | Yok | Bir VOD varlığına MIN ve sonrası büyük reklamları ekleyin. |
| Kayıttan yürütme hızı | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| Yok | Görüntüleyicilerin, videosunu ne hızda izliyor olduğunu denetlemesine olanak sağlar. |
| AMP Temizleme kaplaması | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Yeni AMP dış görünümünü sunar. **Note:** AMP temizlemesi yalnızca AMP sürümleri 2.1.0 + içinde desteklenir |
| Açıklamalı alt yazılar ve alt yazılar | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | WebVTT alt yazıları ile kayıttan yürütme.
| Canlı CEA 708 başlıkları | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | Yok | Canlı CEA 708 gelen açıklamalı alt yazıların sola hizalı şekilde kayıttan yürütme. |
| Aşamalı geri dönüş ile akış | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Akış, platformda desteklenmiyorsa, ilerleyen için geri dönüş ile Uyarlamalı kayıttan yürütmeyi temel kurma. |
| İlerleyen video MP4 | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | İlerleyen ses MP4 kayıttan yürütme. |
| Aşamalı ses MP3 | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Aşamalı ses MP3 kayıttan yürütme. |
| GG + | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | Yok | DD + sesle içerik kayıttan yürütme. |
| Seçenekler |
| Buluşsal profil | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | Buluşsal yöntemler profilini değiştirme |
| Yerelleştirme | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Yerelleştirme ayarlanıyor |
| Ses Izleri menüsü | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Ses izleri menüsünün varsayılan kaplamaya nasıl görüntüleneceğini gösterme seçenekleri. |
| Tuşlarının | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | Bu örnek, Player 'da hangi kısayol tuşlarının etkin olduğunu nasıl yapılandıracağınızı gösterir |
| Olaylar, günlüğe kaydetme ve tanılama |
| Olayları kaydetme | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | Yok | Olay dinleyicileri ile kayıttan yürütme. |
| Günlüğe Kaydetme | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | Konsola ayrıntılı günlük kaydı açılıyor. |
| Tanılama | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | Yok | Tanılama verileri alınıyor. Bu örnek yalnızca bazı Techs üzerinde kullanılabilir. |
| AES |
| AES belirteç yok | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Belirteç içermeyen AES içeriğini kayıttan yürütme. |
| AES belirteci | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | Belirteç ile AES içeriği kayıttan yürütme. |
| AES HLS proxy benzetimi | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | Belirtecin iOS cihazlarıyla kullanılabilmesi için, HLS için bir proxy göstererek belirteç ile AES içeriğini kayıttan yürütme. |
| AES belirteci zorla Flash | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | Belirteç ile AES içeriğinin kayıttan yürütülmesi, flashSS Tech 'ı zorluyor. |
| DRM |
| PlayReady, Widevine ve FairPlay ile çoklu DRM | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | PlayReady, Widevine ve FairPlay üstbilgileri ile hiçbir belirteç olmadan DRM içeriğini kayıttan yürütme. |
| PlayReady belirteç yok | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Hiçbir belirteç olmadan PlayReady içeriğini kayıttan yürütme. |
| PlayReady No Token zorlamalı Silverlight | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | Hiçbir belirteç olmadan PlayReady içeriğinin kayıttan yürütülmesi, silverlightSS Tech 'ı zorluyor. |
| PlayReady belirteci | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | PlayReady içeriğini belirteç ile kayıttan yürütme. |
| PlayReady belirteci Silverlight 'ı zorla | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | Belirteç ile PlayReady içeriğinin kayıttan yürütülmesi, silverlightSS Tech 'ı zorluyor. |
| Protokol ve teknoloji |
| TechOrder 'ı değiştirme | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | Teknik siparişi değiştirme |
| Yalnızca UrlRewriter 'da MPEG-DASH zorla | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | Korumasız içeriği yalnızca DASH protokolünü kullanarak kayıttan yürütme. |
| UrlRewriter 'da MPEG-DASH hariç tut | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | Korumasız içeriği yalnızca sorunsuz ve HLS protokollerini kullanarak kayıttan yürütme. |
| Birden çok teslim ilkesi | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Static](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | Kaynağı Azure Media Services birden çok teslim ilkesi olan içerikle ayarlama |
| Program aracılığıyla Seç |
| Metin parçasını Seç | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | Yok | İzleme listesinden bir WebVTT izlemesi seçiliyor. |
| Bit hızı seçin | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | Yok | Bit hızları listesinden bit hızı seçiliyor. Bu örnek yalnızca bazı Techs üzerinde kullanılabilir. |
| Ses akışını seçin | [Dinamik](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | Yok | Kullanılabilir ses akışları listesinden bir ses akışı seçiliyor. Bu örnek yalnızca bazı Techs üzerinde kullanılabilir. |

## <a name="next-steps"></a>Sonraki adımlar

<!---Some context for the following links goes here--->
- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)