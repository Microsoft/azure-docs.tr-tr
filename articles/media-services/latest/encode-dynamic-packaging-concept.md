---
title: Azure Media Services v3 'de dinamik paketleme
description: Bu makale, Azure Media Services içindeki dinamik paketlemeye genel bakış sunar.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: c2ffda7400109e16cf1110a4e14ecbc3604a7ecf
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492316"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Media Services v3 'de dinamik paketleme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Microsoft Azure Media Services, birçok medya kaynak dosya biçimini kodlamak için kullanılabilir. Bu, tüm büyük cihazlara (iOS ve Android cihazlar gibi) erişmek için, içerik koruma ile veya olmayan farklı akış protokolleriyle bu cihazları sunar. Bu istemciler farklı protokolleri anladım. Örneğin, iOS HTTP Canlı Akışı (HLS) biçiminde ve Android cihazlarında HLS 'yi ve MPEG DASH 'i desteklemek için akışların teslim edilmesini gerektirir.

Media Services, bir [akış uç noktası](stream-streaming-endpoint-concept.md) (Origin), canlı ve isteğe bağlı içeriğinizi doğrudan bir istemci oynatıcı uygulamasına teslim edebilen bir dinamik (tam zamanında) paketleme ve kaynak hizmetini temsil eder. Aşağıdaki bölümde bahsedilen ortak akış medya protokollerinden birini kullanır. *Dinamik paketleme* , tüm akış uç noktalarında standart olarak gelen bir özelliktir.

## <a name="to-prepare-your-source-files-for-delivery"></a>Kaynak dosyalarınızı teslim edilmek üzere hazırlamak için

Dinamik paketlemeden yararlanmak için, Mezzanine (kaynak) dosyanızı çoklu bit hızı MP4 (ISO tabanlı medya 14496-12) dosyaları kümesine [kodlamanız](encode-concept.md) gerekir. Media Services dinamik paketleme için, kodlanmış MP4 ve akış yapılandırma [dosyalarını içeren bir](assets-concept.md) varlığınız olması gerekir. Bu MP4 dosyaları kümesinden, aşağıda açıklanan akış medya protokolleri aracılığıyla video teslim etmek için dinamik paketleme kullanabilirsiniz.

Azure Media Services dinamik paketleme yalnızca MP4 kapsayıcı biçimindeki video ve ses dosyalarını destekler. Ses dosyalarının, Dolby gibi alternatif codec bileşenleri kullanılırken bir MP4 kapsayıcısına kodlanması gerekir.  

> [!TIP]
> MP4 ve akış yapılandırma dosyalarını almanın bir yolu, [Media Services ile Mezzanine dosyanızı kodlayakullanmaktır](#encode-to-adaptive-bitrate-mp4s). 

Kodlanmış varlıktaki videoları kayıttan yürütme için kullanılabilir hale getirmek için bir [akış Bulucu](stream-streaming-locators-concept.md) oluşturmanız ve akış URL 'leri oluşturmanız gerekir. Daha sonra akış istemci bildiriminde (HLS, MPEG DASH veya Kesintisiz Akış) belirtilen biçime göre akışı seçtiğiniz protokolde alırsınız.

Bunu sonucunda, dosyaları yalnızca tek bir depolama biçiminde depolamanız ve buna göre ödeme yapmanız gerekir. Media Services hizmeti, istemciden gelen isteklere göre uygun yanıtı derler ve sunar.

İçeriğinizi Media Services dinamik şifrelemeyi kullanarak korumayı planlıyorsanız, bkz. [akış protokolleri ve şifreleme türleri](drm-content-protection-concept.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>HLS Protokolü

Akış istemciniz aşağıdaki HLS biçimlerini belirtebilir:

|Protokol|Örnek|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|

> [!NOTE]
> Apple 'dan gelen önceki yönergeler, düşük bant genişliğine sahip ağların geri dönüşü için yalnızca ses akımı sağlamalarından önce önerilir.  Media Services Kodlayıcısı, her seferinde otomatik olarak bir ses parçası oluşturur.  Apple yönergeleri artık yalnızca ses izlemenin, özellikle Apple TV dağıtımı için dahil *edilmemelidir.*  Player 'ın yalnızca bir ses izlemesine izin almasını engellemek için, URL 'de, HLS 'de yalnızca ses işlemesini kaldıran "salt ses = false" etiketini kullanmanızı veya yalnızca HLS-v3 ' i kullanmanızı öneririz. Örneğin, `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>MPEG-DASH Protokolü

Akış istemciniz aşağıdaki MPEG-DASH biçimlerini belirtebilir:

|Protokol|Örnek|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |

### <a name="smooth-streaming-protocol"></a>Kesintisiz Akış Protokolü

Akış istemciniz aşağıdaki Kesintisiz Akış biçimlerini belirtebilir:

|Protokol|Notlar/örnekler| 
|---|---|
|Kesintisiz Akış| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Kesintisiz Akış 2,0 (eski bildirim)|Varsayılan olarak, Kesintisiz Akış bildirim biçimi Yinele etiketini (r-Tag) içerir. Ancak, bazı oyuncular desteklemez `r-tag` . Bu oyunculara sahip istemciler, r-Tag ' i devre dışı bırakan bir biçim kullanabilir:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Kesintisiz Akış, akışlarınızın hem ses hem de videonun mevcut olmasını gerektirir.

## <a name="on-demand-streaming-workflow"></a>İsteğe bağlı akış iş akışı

Aşağıdaki adımlarda, Azure Media Services içindeki standart kodlayıcıyla birlikte dinamik ambalajın kullanıldığı ortak bir Media Services akış iş akışı gösterilmektedir.

1. MP4, QuickTime/MOV veya desteklenen başka bir dosya biçimi gibi [bir giriş dosyasını karşıya yükleyin](job-input-from-http-how-to.md) . Bu dosya, Mezzanine veya kaynak dosya olarak da adlandırılır. Desteklenen biçimlerin listesi için bkz. [Standart kodlayıcı tarafından desteklenen biçimler](encode-media-encoder-standard-formats-reference.md).
1. Mezzanine dosyanızı H. bir H.,/AAC MP4 Uyarlamalı bit hızı kümesine [kodlayın](#encode-to-adaptive-bitrate-mp4s) .

    Zaten kodlanmış dosyalarınız varsa ve yalnızca dosyaları kopyalayıp akışa almak istiyorsanız şunu kullanın: [Copyvideo](/rest/api/media/transforms/createorupdate#copyvideo) ve [Copyaudio](/rest/api/media/transforms/createorupdate#copyaudio) API 'leri. Sonuç olarak, akış bildirimi (. ISM dosyası) içeren yeni bir MP4 dosyası oluşturulur.
1. Uyarlamalı bit hızı MP4 kümesini içeren çıkış varlığını yayımlayın. Bir [akış Bulucu](stream-streaming-locators-concept.md)oluşturarak yayımlayabilirsiniz.
1. Farklı biçimleri (HLS, MPEG-DASH ve Kesintisiz Akış) hedefleyen derleme URL 'Leri. *Akış uç noktası* , tüm bu farklı biçimlere yönelik doğru bildirime ve isteklere hizmet vermeye yöneliktir.
    
Aşağıdaki diyagramda, dinamik paketleme iş akışı ile isteğe bağlı akış gösterilmektedir.

![Dinamik paketleme ile isteğe bağlı akış için bir iş akışının diyagramı](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

İndirme yolu yukarıdaki görüntüde bulunur ve yalnızca bir MP4 dosyasını doğrudan *akış uç noktası* (Origin) aracılığıyla indirebileceğiniz (akış bulucuda indirilebilir [akış ilkesini](stream-streaming-policy-concept.md) belirtirsiniz).<br/>Dinamik Paketleyici dosyayı değiştirmez. İsteğe bağlı olarak, *akış uç noktası* (başlangıç) özelliklerini atlamak Istiyorsanız, Azure Blob depolama API 'lerini kullanarak doğrudan aşamalı indirme IÇIN bir MP4 yükleyebilirsiniz. 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Uyarlamalı bit hızı MP4 'leri kodlama

Aşağıdaki makalelerde [Media Services bir videonun nasıl kodlanacağı](encode-concept.md)örnekleri gösterilmektedir:

* [Yerleşik ön ayarları kullanarak BIR https URL 'Sinden kodlama](job-input-from-http-how-to.md).
* [Yerleşik ön ayarları kullanarak yerel bir dosya kodlayın](job-input-from-local-file-how-to.md).
* [Belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir önayar oluşturun](transform-custom-presets-how-to.md).

Standart kodlayıcı [biçimleri ve codec bileşenleri](encode-media-encoder-standard-formats-reference.md)listesine bakın.

## <a name="live-streaming-workflow"></a>Canlı akış iş akışı

Canlı bir olay, *doğrudan geçiş* (Şirket içi bir Live Encoder çoklu bit hızı akışı gönderir) veya *canlı kodlama* (Şirket içi bir Live Encoder tek bit hızı akışı gönderir) olarak ayarlanabilir. 

*Dinamik paketleme* ile canlı akış için ortak bir iş akışı aşağıda verilmiştir:

1. Canlı bir [olay](live-event-outputs-concept.md)oluşturun.
1. Alma URL 'sini alın ve şirket içi kodlayıcınızı, katkı akışını göndermek için URL 'YI kullanacak şekilde yapılandırın.
1. Kodlayıcıdan alınan girişin alındığını doğrulamak için önizleme URL 'sini alın ve kullanın.
1. Yeni bir varlık oluşturun.
1. Canlı bir çıktı oluşturun ve oluşturduğunuz varlık adını kullanın.<br />Canlı çıktı, akışı varlığa arşivler.
1. Yerleşik akış ilkesi türleriyle bir akış Bulucu oluşturun.<br />İçeriğinizi şifrelemeyi düşünüyorsanız, [içerik korumasına genel bakış](drm-content-protection-concept.md)' ı inceleyin.
1. Kullanılacak URL 'Leri almak için akış bulucunun yollarını listeleyin.
1. Akışa almak istediğiniz akış uç noktası için ana bilgisayar adını alın.
1. Farklı biçimleri (HLS, MPEG-DASH ve Kesintisiz Akış) hedefleyen derleme URL 'Leri. *Akış uç noktası* , farklı biçimlere yönelik doğru bildirime ve isteklere hizmet vermeye önem kazanır.

Bu diyagramda, *dinamik paketleme* ile canlı akış için iş akışı gösterilmektedir:

![Dinamik paketleme ile geçiş kodlaması için iş akışının diyagramı](./media/live-streaming/pass-through.svg)

Media Services v3 sürümünde canlı akış hakkında daha fazla bilgi için bkz. [canlı akışa genel bakış](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Dinamik paketleme tarafından desteklenen video codec bileşenleri

Dinamik paketleme, MP4 kapsayıcı dosya biçimindeki video dosyalarını destekler ve [h.](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC veya avc1) veya [h. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 veya hvc1) ile kodlanmış bir video içerir.

> [!NOTE]
> En fazla 4K/60 kare hızına kadar olan çözünürlükler, *dinamik paketleme* ile test edilmiştir.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Dinamik paketleme tarafından desteklenen ses codec bileşenleri

Dinamik paketleme Ayrıca, aşağıdaki codec bileşenlerinden birinde kodlanmış ses akışını içeren MP4 dosya kapsayıcısı biçiminde depolanan ses dosyalarını da destekler:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 veya HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Gelişmiş AC-3 veya E-AC3).  Kodlanmış sesin, dinamik paketleme ile çalışması için MP4 kapsayıcı biçiminde depolanması gerekir.
* Dolby Atmos

   Ortak akış biçimi (CSF) veya ortak medya uygulaması biçimi (CMAF) ile MPEG-DASH protokolü, CMAF ile HTTP Canlı Akışı (HLS) aracılığıyla akış Dolby Atmos içeriği
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH-CSF, DASH-CMAF, HLS-M2TS ve HLS-CMAF paketleme biçimleri tarafından desteklenen DTS codec bileşenleri şunlardır:  

    * DTS dijital surround (DTSC)
    * DTS-HD yüksek çözünürlüklü ve DTS-HD ana ses (DTSH)
    * DTS Express (DTO)
    * DTS-HD kayıpsız (çekirdek yok) (dtsl)

Dinamik paketleme birden çok codec ve dilde birden çok ses parçası olan akış varlıkları için DASH veya HLS (sürüm 4 veya üzeri) ile birden çok ses parçasını destekler.

Yukarıdaki tüm ses codec bileşenleri için, kodlama sesinin, dinamik paketleme ile çalışmak için MP4 kapsayıcı biçiminde depolanması gerekir. Hizmet, blob depolamada ham basit akış dosya biçimlerini desteklemez (örneğin, aşağıdakiler desteklenmez-. Dts,. AC3.) 

Ses paketleme için yalnızca. mp4 of. MP4A uzantısına sahip dosyalar desteklenir. 

### <a name="limitations"></a>Sınırlamalar

#### <a name="ios-limitation-on-aac-51-audio"></a>AAC 5,1 Audio üzerinde iOS sınırlaması

Apple iOS cihazları 5,1 AAC ses codec bileşenini desteklemez. Çok kanallı ses, Dolby Digital veya Dolby Digital Plus codec bileşenleri kullanılarak kodlanmalıdır.

Ayrıntılı bilgi için bkz. [Apple cihazları Için HLS yazma belirtimi](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Media Services Dolby Digital, Dolby Digital Plus veya Dolby Digital Plus 'ın, Dolby Atmos çok kanallı ses biçimleriyle kodlamasını desteklemez.

#### <a name="dolby-digital-audio"></a>Dolby Digital ses

Dinamik paketleme Media Services, şu anda [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ses içeren dosyaları desteklememektedir (Bu, Dolby tarafından eski bir codec olarak kabul edilir).

## <a name="manifests"></a>Listeleri

*Dinamik paketleme* Media Services, HLS, MPEG-DASH ve kesintisiz akış için akış istemci BILDIRIMLERI, URL 'deki biçim seçicisine göre dinamik olarak oluşturulur.  

Bildirim dosyası, izleme türü (ses, video veya metin), parça adı, başlangıç ve bitiş zamanı, bit hızı (kaliteleri), izleme dilleri, sunum penceresi (Sabit sürenin kayan penceresi) ve video codec (FourCC) gibi akış meta verilerini içerir. Ayrıca, Player 'ın bir sonraki parçayı almasına, kullanılabilir sonraki yürütülebilir video parçaları ve bunların konumlarını hakkında bilgi sağlayıp sağlamamasını söyler. Parçalar (veya segmentler), video içeriğinin gerçek "öbekleridir".

### <a name="examples"></a>Örnekler

#### <a name="hls"></a>HLS

HLS ana çalma listesi olarak da bilinen bir HLS bildirim dosyası örneği aşağıda verilmiştir: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-DASH medya sunumu açıklaması (MPD) olarak da adlandırılan MPEG-DASH bildirim dosyası örneği aşağıda verilmiştir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Kesintisiz Akış

Aşağıda bir Kesintisiz Akış bildirim dosyası örneği verilmiştir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Bildirimde parçaların adlandırılması

. ISM dosyasında bir ses izi adı belirtilmişse Media Services, `Label` `AdaptationSet` belirli ses izi için textürel bilgilerini belirtmek üzere bir öğesi ekler. Çıkış DASH bildirimine bir örnek:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Oynatıcı, `Label` Kullanıcı arabiriminde göstermek için öğesini kullanabilir.

### <a name="signaling-audio-description-tracks"></a>Ses açıklaması izlerinin sinyali

Görsel açıdan görme istemcilerinin, konuşmayı dinleyerek video kaydını izlemesini sağlamak için videonuza bir anlatım izi ekleyebilirsiniz. Bildirimde ses açıklaması olarak bir ses izlemesine açıklama eklemeniz gerekir. Bunu yapmak için. ISM dosyasına "Erişilebilirlik" ve "rol" parametreleri ekleyin. Bu parametreleri bir ses izini ses açıklaması olarak bildirmek üzere doğru şekilde ayarlamak sizin sorumluluğunuzdadır. Örneğin, `<param name="accessibility" value="description" />` `<param name="role" value="alternate"` belirli bir ses izi için. ISM dosyası ekleyin. 

Daha fazla bilgi için bkz. [açıklayıcı bir ses izleme](signal-descriptive-audio-howto.md) örneği.

#### <a name="smooth-streaming-manifest"></a>Kesintisiz Akış bildirimi

Bir Kesintisiz Akış akışı yürütüyorsanız, bildirim, `Accessibility` `Role` Bu ses parçasının değerlerini ve özniteliklerini taşır. Örneğin, `Role="alternate" Accessibility="description"` `StreamIndex` bir ses açıklaması olduğunu göstermek için öğesine eklenir.

#### <a name="dash-manifest"></a>Kesik çizgi bildirimi

DASH bildirimi için aşağıdaki iki öğe, ses açıklamasına işaret etmek için eklenir:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS çalma listesi

HLS v7 ve üzeri için `(format=m3u8-cmaf)` , ses açıklaması izlemesine işaret edildiğinde, onun çalma listesi devam edecektir `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` .

#### <a name="example"></a>Örnek

Daha fazla bilgi için bkz. [ses açıklaması izlemelerinin sinyali](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dinamik bildirim

Oyunculara gönderilen iz, biçim, bitme ve sunum süresi pencerelerinin sayısını denetlemek için dinamik filtrelemeyi Media Services dinamik Paketleyiciyi kullanarak kullanabilirsiniz. Daha fazla bilgi için bkz. [dinamik paketlemede ön filtreleme bildirimleri](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption"></a>Dinamik şifreleme

Etkin veya isteğe bağlı içeriğinizi AES-128 veya üç ana dijital hak yönetimi (DRM) sisteminden dinamik olarak şifrelemek için *dinamik şifrelemeyi* kullanabilirsiniz: Microsoft PlayReady, Google Widevine ve Apple FairPlay. Media Services Ayrıca, yetkili istemcilere yönelik AES anahtarları ve DRM lisansları sunmaya yönelik bir hizmet sağlar. Daha fazla bilgi için bkz. [dinamik şifreleme](drm-content-protection-concept.md).

> [!NOTE]
> Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="more-information"></a>Daha fazla bilgi

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) 'ye göz atın.

## <a name="need-help"></a>Yardıma mı ihtiyacınız var?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)giderek bir destek bileti açabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Videoları karşıya yükleme, kodlama ve akışla aktarma](stream-files-tutorial-with-api.md)
