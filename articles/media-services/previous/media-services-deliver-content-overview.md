---
title: Müşterilere içerik sunma
description: Bu konu, Azure Media Services ile içeriğinizi sunmaya nelerin ilgili olduğuna ilişkin genel bir bakış sunar.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016620"
---
# <a name="deliver-content-to-customers"></a>Müşterilere içerik sunma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Akışa veya isteğe bağlı video içeriğinizi müşterilere sunarken, amacınız farklı ağ koşulları altında çeşitli cihazlara yüksek kaliteli video sunmaktır.

Bu hedefe ulaşmak için şunları yapabilirsiniz:

* Akışınızı çoklu bit hızında (Uyarlamalı bit hızı) bir video akışına kodlayın. Bu işlem kalite ve ağ koşullarından faydalandı.
* Akışınızı farklı protokollere dinamik olarak yeniden paketlemek için Microsoft Azure Media Services [dinamik paketleme](media-services-dynamic-packaging-overview.md) kullanın. Bu işlem, farklı cihazlarda akışa alınır. Media Services, aşağıdaki Uyarlamalı bit hızı akış teknolojilerinin teslimini destekler: <br/>
    * **Http canlı akışı** (HLS)-"(format = M3U8-AAPL)" yolunu, akış kaynak sunucusuna **Apple iOS** Native cihazlarda tüketim için içerik GERI dönmesini söylemek için URL 'nin "/manifest" bölümüne ekleyin (Ayrıntılar için bkz. [Konum Belirleyicisi](#locators) ve [URL 'ler](#URLs)),
    * **MPEG-Dash** -"(format = MPD-Time-CSF)" yolunu, akış kaynak sunucusuna MPEG-Dash geri döndürmesini söylemek için URL 'nin "/manifest" bölümüne ekleyin (Ayrıntılar için bkz: [Konumlandırıcı](#locators) ve [URL 'ler](#URLs)),
    * **Kesintisiz akış**.

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 

Bu makale, önemli içerik teslim kavramlarına genel bir bakış sunar.

Bilinen sorunları denetlemek için bkz. [bilinen sorunlar](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dinamik paketleme
Media Services sağladığı dinamik paketleme sayesinde, uyarlamalı bit hızı MP4 veya Kesintisiz Akış kodlanmış içeriğinizi, bu akış biçimlerine yeniden paketlemenize gerek kalmadan Media Services (MPEG-DASH, HLS, Kesintisiz Akış,) tarafından desteklenen akış biçimlerinde teslim edebilirsiniz. Dinamik paketleme ile içeriğinizi teslim etmenizi öneririz.

Dinamik paketlemeden yararlanmak için, Mezzanine (kaynak) dosyanızı bir uyarlamalı bit hızı MP4 dosyası ya da Uyarlamalı bit hızı Kesintisiz Akış dosyalarına kodlamanız gerekir.

Dinamik paketleme ile dosyaları tek depolama biçiminde depolayıp ödeyin. Media Services, isteklerinizi temel alarak uygun yanıtı derler ve sunar.

Dinamik paketleme standart ve Premium akış uç noktaları için kullanılabilir. 

Daha fazla bilgi için bkz. [dinamik paketleme](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtreler ve dinamik bildirimler
Media Services olan varlıklarınız için filtreler tanımlayabilirsiniz. Bu filtreler, müşterilerinizin bir videonun belirli bir bölümünü oynamasını veya müşterinizin cihazının işleyebileceği ses ve video çevirilerinin bir alt kümesini (varlıkla ilişkili tüm yorumlamaları yerine) belirtmesini sağlayan sunucu tarafı kurallarıdır. Bu filtreleme, müşteriniz bir veya daha fazla belirtilen filtreye göre video akışı istediğinde oluşturulan *dinamik bildirimler* aracılığıyla gerçekleştirilir.

Daha fazla bilgi için bkz. [filtreler ve dinamik bildirimler](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a name="locators"></a>Bulucuları
İçeriğinizi içeriğinizi akışa almak veya indirmek için kullanılabilecek bir URL 'yi sağlamak için, önce bir bulucu oluşturarak varlığınızı yayımlamanız gerekir. Bir bulucu, bir varlık içinde bulunan dosyalara erişmek için bir giriş noktası sağlar. Media Services iki tür bulucuyu destekler:

* OnDemandOrigin Konumlandırıcı. Bunlar, medyayı (örneğin, MPEG-DASH, HLS veya Kesintisiz Akış) veya aşamalı olarak indirme dosyalarını akışa almak için kullanılır.
* Paylaşılan erişim imzası (SAS) URL Konumlandırıcı. Bunlar, medya dosyalarını yerel bilgisayarınıza indirmek için kullanılır.

*Erişim ilkesi* , izinleri (okuma, yazma ve listeleme gibi) ve istemcinin belirli bir varlık için erişimi olan süresini tanımlamak için kullanılır. Liste izninin (AccessPermissions. List) bir OnDemandOrigin Bulucu oluşturma bölümünde kullanılmamalıdır.

Konum belirleyicilerinin süre sonu tarihleri vardır. Azure portal, gelecekte bir süre sonu tarihi 100 yıl sonra konum belirleyicilerde ayarlanır.

> [!NOTE]
> 2015 Mart ' den önceki Konumlandırıcı oluşturmak için Azure portal kullandıysanız, bu belirleyicileri iki yıl sonra sona ermek üzere ayarlanmıştı.
> 
> 

Bir bulucunun sona erme tarihini güncelleştirmek için [REST](/rest/api/media/operations/locator#update_a_locator) ya da [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator) API’lerini kullanın. SAS bulucunun sona erme tarihini güncelleştirdiğinizde URL’nin değiştiğini unutmayın.

Konumlandırıcı, Kullanıcı başına erişim denetimini yönetmek için tasarlanmamıştır. Dijital Rights Management (DRM) çözümlerini kullanarak, bireysel kullanıcılara farklı erişim hakları verebilirsiniz. Daha fazla bilgi için bkz. [medyayı güvenli hale getirme](/previous-versions/azure/dn282272(v=azure.100)).

Bir bulucu oluşturduğunuzda, Azure depolama 'daki gerekli depolama ve yayma işlemlerine bağlı olarak 30 saniyelik bir gecikme olabilir.

## <a name="adaptive-streaming"></a>Uyarlamalı akış
Uyarlamalı bit hızı teknolojileri, video oynatıcı uygulamalarının ağ koşullarını belirlemesine ve çeşitli bitoranlar arasından seçim yapmasına imkan tanır. Ağ iletişimi düştüğünde, istemci daha düşük bir bit hızı seçerek kayıttan yürütmenin daha düşük video kalitesiyle devam etmesini sağlayabilirsiniz. Ağ koşulları arttıkça, istemci geliştirilmiş video kalitesiyle daha yüksek bir bit hızına geçiş yapabilir. Azure Media Services, aşağıdaki Uyarlamalı bit hızı teknolojilerini destekler: HTTP Canlı Akışı (HLS), Kesintisiz Akış ve MPEG-DASH.

Kullanıcılara akış URL 'Leri sağlamak için önce bir OnDemandOrigin Bulucu oluşturmanız gerekir. Bulucunun oluşturulması, akışa almak istediğiniz içeriği içeren varlığın temel yolunu sağlar. Ancak, bu içeriği akıyabilmek için bu yolu daha fazla değiştirmeniz gerekir. Akış bildirim dosyasına tam URL oluşturmak için, Konumlandırıcı 'nın yol değerini ve bildirim (filename. ISM) dosya adını birleştirmeniz gerekir. Ardından, konum belirleyici yoluna **/manifest** ve uygun bir biçim (gerekliyse) ekleyin.

> [!NOTE]
> Ayrıca içeriğinizi bir TLS bağlantısı üzerinden de akışla aktarabilirsiniz. Bunu yapmak için, akış URL 'Lerinin HTTPS ile başlayıp başlamadığınızdan emin olun. Şu anda AMS 'nin özel etki alanları ile TLS 'yi desteklemediğine unutmayın.  
> 

Yalnızca içeriğinizi teslim ettiğiniz akış uç noktası 10 Eylül 2014 ' den sonra oluşturulduysa TLS üzerinden akış yapabilirsiniz. Akış URL 'larınız 10 Eylül 2014 ' den sonra oluşturulan akış uç noktalarına dayıyorsa, URL "streaming.mediaservices.windows.net" içerir. "Origin.mediaservices.windows.net" (eski biçim) içeren akış URL 'Leri TLS 'yi desteklemez. URL 'niz eski biçimindeyse ve TLS üzerinden akış oluşturabilmek istiyorsanız yeni bir akış uç noktası oluşturun. İçeriğinizi TLS üzerinden akışındaki yeni akış uç noktasına göre URL 'Leri kullanın.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Akış URL biçimleri

### <a name="mpeg-dash-format"></a>MPEG-DASH biçimi
{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = MPD-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Canlı Akışı (HLS) V4 biçimi
{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = M3U8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Canlı Akışı (HLS) v3 biçimi
{Akış uç noktası adı-Media Services hesap adı}. Stream. mediaservices. Windows. net/{Locator ID}/{filename}.exe (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = M3U8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Yalnızca ses filtresiyle Apple HTTP Canlı Akışı (HLS) biçimi
Varsayılan olarak, HLS bildiriminde yalnızca ses parçaları bulunur. Bu, hücresel ağlarda Apple Store sertifikası için gereklidir. Bu durumda, bir istemcinin yeterli bant genişliği yoksa veya bir 2G bağlantısı üzerinden bağlanmazsa, kayıttan yürütme geçişleri salt ses 'a geçirir. Bu, içerik akışını arabelleğe almadan tutmaya yardımcı olur, ancak video yoktur. Bazı senaryolarda, Player arabelleğe alma yalnızca ses üzerinden tercih edilebilir. Yalnızca ses parçasını kaldırmak istiyorsanız, URL 'ye **yalnızca ses = false** ekleyin.

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = M3U8-AAPL-v3, yalnızca ses = false)

Daha fazla bilgi için bkz. [dinamik bildirim oluşturma desteği ve HLS çıkış ek özellikleri](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Kesintisiz Akış biçimi
{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest

Örnek:

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Kesintisiz Akış 2,0 bildirimi (eski bildirim)
Varsayılan olarak, Kesintisiz Akış bildirim biçimi Yinele etiketini (r-Tag) içerir. Ancak, bazı oyuncular r-Tag ' i desteklemez. Bu oyunculara sahip istemciler, r-Tag ' i devre dışı bırakan bir biçim kullanabilir:

{Akış uç noktası adı-Media Services hesap adı}. Stream. mediaservices. Windows. net/{Locator ID}/{filename}.exe (format = fmp4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Aşamalı indirme
Aşamalı indirme sayesinde, dosyanın tamamı indirilmeden önce medyayı yürütmeye başlayabilirsiniz. . ISM * (ISMV, ISMT veya ısmc) dosyalarını aşamalı olarak indirebilirsiniz.

İçeriği aşamalı olarak indirmek için, Konumlandırıcı 'nın OnDemandOrigin türünü kullanın. Aşağıdaki örnek, Konumlandırıcı 'nın OnDemandOrigin türünü temel alan URL 'YI göstermektedir:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

Aşamalı indirme için kaynak hizmetten akışa almak istediğiniz depolama ile şifrelenmiş tüm varlıkların şifresini çözmeniz gerekir.

## <a name="download"></a>İndir
İçeriğinizi bir istemci cihazına indirmek için bir SAS Bulucu oluşturmanız gerekir. SAS bulucu, dosyanızın bulunduğu Azure depolama kapsayıcısına erişmenizi sağlar. İndirme URL 'sini oluşturmak için, konak ve SAS imzası arasına dosya adını eklemeniz gerekir.

Aşağıdaki örnek SAS Konumlandırıcı 'sını temel alan URL 'YI gösterir:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

Aşağıdaki noktalara dikkat edilmelidir:

* Aşamalı indirme için kaynak hizmetten akışa almak istediğiniz depolama ile şifrelenmiş tüm varlıkların şifresini çözmeniz gerekir.
* 12 saat içinde bitmedi bir indirme başarısız olur.

## <a name="streaming-endpoints"></a>Akış uç noktaları

Akış uç noktası, daha fazla dağıtım için doğrudan bir istemci oynatıcı uygulamasına veya bir içerik teslim ağına (CDN) içerik teslim edebilen bir akış hizmetini temsil eder. Akış uç noktası hizmetinden giden akış, Media Services hesabınızda canlı bir akış veya bir isteğe bağlı video varlığı olabilir. İki tür akış uç noktası vardır, **Standart** ve **Premium**. Daha fazla bilgi için bkz. [Akış uç noktalarına genel bakış](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 

## <a name="known-issues"></a>Bilinen sorunlar
### <a name="changes-to-smooth-streaming-manifest-version"></a>Kesintisiz Akış manifest sürümündeki değişiklikler
Temmuz 2016 hizmet sürümünden önce--Media Encoder Standard, Media Encoder Premium Workflow veya daha önceki Azure Medya Kodlayıcısı tarafından oluşturulan varlıklar dinamik paketleme kullanılarak akışa alındı--döndürülen Kesintisiz Akış bildirimi sürüm 2,0 ' e uygundur. Sürüm 2,0 ' de, parça süreleri so-çağrılan yineleme (' r ') etiketlerini kullanmaz. Örnek:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Temmuz 2016 hizmet sürümünde, üretilen Kesintisiz Akış bildirimi sürüm 2,2 ' ye uygundur. Bu, yineleme etiketlerini kullanarak parça sürelerle uyumludur. Örnek:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Eski Kesintisiz Akış istemcilerinden bazıları yineleme etiketlerini desteklemeyebilir ve bildirimi yükleyemeyecektir. Bu sorunu azaltmak için eski bildirim biçimi parametresini **(format = fmp4-V20)** kullanabilir veya istemcinizi, yineleme etiketlerini destekleyen en son sürüme güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [Kesintisiz Akış 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>İlgili konular
[Depolama anahtarları alındıktan sonra Media Services bulıcıları Güncelleştir](media-services-roll-storage-access-keys.md)