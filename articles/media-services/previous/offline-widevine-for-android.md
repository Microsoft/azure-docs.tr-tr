---
title: Hesabınızı, Widevine korunan içeriğin çevrimdışı akışı için Yapılandırma-Azure
description: Bu konu başlığı altında, Widevine korumalı içeriğin çevrimdışı akışı için Azure Media Services hesabınızın nasıl yapılandırılacağı gösterilmektedir.
services: media-services
keywords: DASH, DRM, Widevine çevrimdışı modu, Exooynatıcı, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 694cdf054f74db50bcf1781e60df0f93810ae60c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875185"
---
# <a name="offline-widevine-streaming-for-android"></a>Android için çevrimdışı Widevine akışı  

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 3](../latest/offline-widevine-for-android.md)
> * [Sürüm 2](offline-widevine-for-android.md)

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Çevrimiçi akış için içeriği korumanın yanı sıra, medya içerik aboneliği ve Kiralama Hizmetleri, internet 'e bağlı değilseniz kullanılan indirilebilir içerik sunar. Ağ bağlantısı kesildiğinde Uçak modunda kayıttan yürütmek için içeriği telefonunuza veya tabletinize indirmeniz gerekebilir. İçeriği indirmek isteyebileceğiniz ek senaryolar:

- Bazı içerik sağlayıcıları, bir ülke/bölge kenarlığının ötesinde DRM lisans teslimine izin verebilir. Kullanıcı yolculukta içerik izlemek istiyorsa, çevrimdışı indirme gerekir.
- Bazı ülkelerde/bölgelerde Internet kullanılabilirliği ve/veya bant genişliği sınırlıdır. Kullanıcılar, tatmin edici görüntüleme deneyimi için yeterince yüksek çözünürlükte izleyebilmek için içerik indirmeyi seçebilirler.

Bu makalede, Android cihazlarda Widevine tarafından korunan DASH içeriği için çevrimdışı modda kayıttan yürütmenin nasıl uygulanacağı açıklanır. Çevrimdışı DRM, içeriğiniz için abonelik, kiralama ve satın alma modelleri sağlamanıza olanak tanıyarak, hizmetlerinizin müşterilerinin internet bağlantısı kesildiğinde kolayca içerik almasına imkan tanır.

Android Player uygulamalarını oluşturmak için üç seçenekten oluşan bir ana hat sunuyoruz:

> [!div class="checklist"]
> * ExoPlayer SDK 'sının Java API 'sini kullanarak oyuncu oluşturma
> * ExoPlayer SDK 'sının Xamarin bağlamasını kullanarak oyuncu oluşturma
> * Chrome Mobile Browser V62 veya üzeri sürümlerde şifreli medya uzantısı 'nı (EME) ve medya kaynak uzantısı 'nı (MSE) kullanarak bir oyuncu oluşturun

Makalede ayrıca Wıdevine korumalı içeriğin çevrimdışı akışı ile ilgili bazı yaygın soruların yanıtları da vardır.

## <a name="requirements"></a>Gereksinimler 

Android cihazlarda Widevine için çevrimdışı DRM uygulamadan önce, önce şunları yapmalısınız:

- Widevine DRM kullanarak çevrimiçi içerik koruma için tanıtılan kavramlarla ilgili bilgi sahibi olun. Bu, aşağıdaki belgelerde/örneklerde ayrıntılı olarak ele alınmıştır:
    - [DRM lisansları veya AES anahtarları sunmak için Azure Media Services kullanın](media-services-deliver-keys-and-licenses.md)
    - [Multi-DRM ve Access Control CENC: Azure ve Azure Media Services başvuru tasarımı ve uygulama](media-services-cenc-with-multidrm-access-control.md)
    - [.NET ile PlayReady ve/veya Widevine dinamik Common Encryption kullanma](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [.NET ile PlayReady ve/veya Widevine lisanslarını teslim etmek için Azure Media Services kullanın](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Çevrimdışı Widevine DRM kayıttan yürütmeyi destekleyebilen açık kaynaklı bir video oynatıcı SDK 'sı olan Android için Google ExoPlayer SDK 'sı hakkında bilgi sahibi olun. 
    - [Exooynatıcı SDK 'Sı](https://github.com/google/ExoPlayer)
    - [ExoPlayer Geliştirici Kılavuzu](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer geliştirici blogu](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Azure Media Services 'de içerik koruma yapılandırması

Media Services bir varlığın Widevine korumasını yapılandırdığınızda, aşağıdaki üç şeyi belirten ContentKeyAuthorizationPolicyOption öğesini oluşturmanız gerekir:

1. DRM sistemi (Widevine)
2. İçerik anahtarı tesliminin lisans teslim hizmetinde nasıl yetkilendirildiğini belirten ContentKeyAuthorizationPolicyRestriction (açık veya belirteç yetkilendirmesi)
3. DRM (Widevine) lisans şablonu

Widevine lisansları için **çevrimdışı** modu etkinleştirmek üzere [Widevine lisans şablonu](media-services-widevine-license-template-overview.md)' nu yapılandırmanız gerekir. **Policy_overrides** nesnesinde, **can_persist** özelliğini **true** olarak ayarlayın (varsayılan değer false). 

Aşağıdaki kod örneği, Widevine lisansları için **çevrimdışı** modu etkinleştirmek üzere .net kullanır. Kod, [.net örneği Ile PlayReady ve/veya Widevine dinamik Common Encryption kullanımı](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) temel alır. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Android Player 'ı çevrimdışı kayıttan yürütme için yapılandırma

Android cihazlar için yerel bir oynatıcı uygulaması geliştirmenin en kolay yolu, açık kaynaklı bir video oynatıcı SDK 'sı olan [Google ExoPlayer SDK 'sını](https://github.com/google/ExoPlayer)kullanmaktır. Exooynatıcı, MPEG-DASH ve Microsoft Kesintisiz Akış teslim protokolleri dahil olmak üzere Android 'in yerel MediaPlayer API 'SI tarafından desteklenmeyen özellikleri destekler.

ExoPlayer sürüm 2,6 ve üzeri, çevrimdışı Widevine DRM kayıttan yürütmeyi destekleyen birçok sınıf içerir. Özellikle, OfflineLicenseHelper sınıfı, çevrimdışı lisansları indirmek, yenilemek ve serbest bırakmak için DefaultDrmSessionManager 'ın kullanımını kolaylaştırmak için yardımcı program işlevlerini sağlar. "Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/" SDK klasöründe belirtilen sınıflar çevrimdışı video içeriğini indirmeyi destekliyor.

Aşağıdaki sınıfların listesi, Android için ExoPlayer SDK 'sında çevrimdışı modu kolaylaştırır:

- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. Java  
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/OFFLINE/SegmentDownloader. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/OFFLINE/DownloaderConstructorHelper. Java 
- Kitaplık/çekirdek/src/Main/Java/com/Google/Android/exoplayer2/OFFLINE/Downloader. Java
- Kitaplık/tire/src/Main/Java/com/Google/Android/exoplayer2/kaynak/tire/OFFLINE/Tireyükleyicisi. Java 

Geliştiriciler, bir uygulamanın geliştirilmesi sırasında [Exoplayer geliştirici kılavuzuna](https://google.github.io/ExoPlayer/guide.html) ve Ilgili [Geliştirici bloguna](https://medium.com/google-exoplayer) başvurmalıdır. Google, şu anda çevrimdışı olarak Widevine 'yı destekleyen ExoPlayer uygulaması için tam belgelenmiş bir başvuru uygulaması veya örnek kod yayımlamamıştır, bu nedenle bilgiler geliştirici kılavuzuyla ve blogla sınırlandırılmıştır. 

### <a name="working-with-older-android-devices"></a>Eski Android cihazlarla çalışma

Bazı eski Android cihazlarda, aşağıdaki **policy_overrides** özellikleri için değerler ayarlamanız gerekir ( [Widevine lisans şablonunda](media-services-widevine-license-template-overview.md)tanımlanmıştır: **rental_duration_seconds**, **playback_duration_seconds**ve **license_ duration_seconds**. Alternatif olarak, sonsuz/sınırsız süre anlamına gelen sıfır olarak ayarlayabilirsiniz.  

Bir tamsayı taşma hatasını önlemek için değerlerin ayarlanması gerekir. Sorun hakkında daha fazla açıklama için bkz https://github.com/google/ExoPlayer/issues/3150 . ve. https://github.com/google/ExoPlayer/issues/3112 <br/>Değerleri açıkça ayarlamazsanız, **PlaybackDurationRemaining** ve **LicenseDurationRemaining** için çok büyük değerler atanır (örneğin, 64 bit tam sayı için en büyük pozitif değer olan 9223372036854775807). Sonuç olarak, Widevine lisansının geçerliliği zaman aşımına uğradı, bu nedenle şifre çözme gerçekleşmeyecektir. 

Android 5,0, ARMv8 ([GELIŞMIŞ RISC makinesi](https://en.wikipedia.org/wiki/ARM_architecture)) ve 64-bit platformlarını tamamen destekleyecek şekilde tasarlanan ilk Android sürümü olduğundan, Android 5,0 Lollipop veya sonrasında bu sorun oluşmaz, çünkü Android 4,4 KitKat özgün olarak tasarlanmıştı diğer eski Android sürümlerinde olduğu gibi ARMv7 ve 32-bit platformları destekler.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Android oynatma uygulaması oluşturmak için Xamarin kullanma

Aşağıdaki bağlantıları kullanarak, ExoPlayer için Xamarin bağlamalarını bulabilirsiniz:

- [Google ExoPlayer kitaplığı için Xamarin bağlamaları kitaplığı](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet için Xamarin bağlamaları](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Ayrıca, aşağıdaki iş parçacığına bakın: [Xamarin bağlama](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Android için Chrome Player uygulamaları

Android v için [Chrome sürümü ile başlayarak. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), eme 'de kalıcı lisans destekleniyor. [Wdevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) artık Android için Chrome 'da da desteklenmektedir. Bu, son kullanıcılarınız Chrome 'un bu (veya üzeri) sürümü varsa, Chrome 'da çevrimdışı oynatma uygulamaları oluşturmanıza olanak tanır. 

Ayrıca, Google bir aşamalı Web uygulaması (PWA) örneği üretti ve açık kaynaklı BT: 

- [Kaynak kod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google hosted sürümü](https://biograf-155113.appspot.com/ttt/episode-2/) (Android cihazlarda yalnızca Chrome v 62 ve üzeri sürümlerde geçerlidir)

Mobil Chrome tarayıcınızı bir Android telefonunda V62 (veya üzeri) sürümüne yükseltir ve yukarıdaki barındırılan örnek uygulamayı test ederseniz hem çevrimiçi akış hem de çevrimdışı oynatma işinin olduğunu görürsünüz.

Yukarıdaki açık kaynaklı PWA uygulaması Node. js ' de yazılır. Bir Ubuntu sunucusunda kendi sürümünüzü barındırmak istiyorsanız, kayıttan yürütmeyi engelleyebilecek aşağıdaki yaygın sorunları göz önünde bulundurun:

1. CORS sorunu: Örnek uygulamadaki örnek video içinde https://storage.googleapis.com/biograf-video-files/videos/ barındırılır. Google, Google bulut depolama demeti içinde barındırılan tüm test örnekleri için CORS 'yi ayarladı. Bunlar, özel olarak CORS girişi https://biograf-155113.appspot.com (Google 'ın örneğini barındırdığı etki alanı) diğer sitelere erişimi engellediği CORS üstbilgileri ile birlikte sunulur. Denerseniz, aşağıdaki HTTP hatasını görürsünüz: https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: İstenen kaynakta ' erişim-denetim-izin-Origin ' üst bilgisi yok. ' Https:\//13.85.80.81:8080 ' başlangıcının erişimine izin verilmiyor. Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.
2. Sertifika sorunu: Chrome v 58 ' den başlayarak Widevine için EME, HTTPS gerektirir. Bu nedenle, örnek uygulamayı bir x509 sertifikasıyla HTTPS üzerinden barındırmanıza gerek duyarsınız. Aşağıdaki gereksinimler nedeniyle olağan bir test sertifikası çalışmaz: Aşağıdaki minimum gereksinimleri karşıladığı bir sertifika edinmeniz gerekir:
    - Chrome ve Firefox, sertifikada SAN konusu alternatif adı ayarının mevcut olmasını gerektirir
    - Sertifika, güvenilir bir CA 'ya sahip olmalı ve kendinden imzalı bir geliştirme sertifikası çalışmıyor
    - Sertifika, Web sunucusunun veya ağ geçidinin DNS adıyla eşleşen bir CN 'ye sahip olmalıdır

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="question"></a>Soru

Bazı istemciler/kullanıcılar ve kalıcı olmayan lisanslar (çevrimdışı olarak devre dışı) için, diğerleri için kalıcı lisanslar (çevrimdışı etkin) teslim etme. İçeriği çoğaltmalıyım ve ayrı içerik anahtarı kullanmalıdır mi?

### <a name="answer"></a>Yanıt
İçeriği çoğaltmak zorunda değilsiniz. Yalnızca bir içeriğin tek bir kopyasını ve tek bir ContentKeyAuthorizationPolicy, ancak iki ayrı ContentKeyAuthorizationPolicyOption ' i kullanabilirsiniz:

1. Itentkeyauthorizationpolicyoption 1: kalıcı lisans ve license_type = "persistent" gibi bir talep içeren ContentKeyAuthorizationPolicyRestriction 1 kullanır
2. Itentkeyauthorizationpolicyoption 2: kalıcı olmayan lisans kullanır ve license_type = "kalıcı olmayan" gibi bir talep içeren ContentKeyAuthorizationPolicyRestriction 2

Bu şekilde, istemci uygulamasından bir lisans isteği geldiğinde, lisans isteğinden bir fark yoktur. Ancak, farklı bir Son Kullanıcı/cihaz için STS, farklı talepler (yukarıdaki iki license_type's biri) içeren farklı JWT belirteçleri vermek için iş mantığına sahip olmalıdır. JWT belirtecindeki talep değeri, lisans hizmeti 'nin ne tür bir lisans (kalıcı veya kalıcı değil) vermesini belirlemek için kullanılır.

Bu, güvenli belirteç hizmeti 'nin (STS) bir belirtece karşılık gelen talep değerini eklemek için iş mantığı ve istemci/cihaz bilgilerine sahip olması gerektiği anlamına gelir.

### <a name="question"></a>Soru

Widevine güvenlik düzeyleri için, Google 'ın [WIDEVINE DRM mimarisine genel bakış belge](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) belgelerinde üç farklı güvenlik düzeyi tanımlanmaktadır. Ancak, [Widevine lisans şablonunda Azure Media Services belgelerde](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), beş farklı güvenlik düzeyi özetlenmiştir. İki farklı güvenlik düzeyi kümesi arasındaki ilişki veya eşleme nedir?

### <a name="answer"></a>Yanıt

Google 'ın [Widevine DRM mimarisine genel bakış](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)' da, aşağıdaki üç güvenlik düzeyini tanımlar:

1.  Güvenlik düzeyi 1: Tüm içerik işleme, şifreleme ve denetim, güvenilir yürütme ortamı (t) içinde gerçekleştirilir. Bazı uygulama modellerinde, güvenlik işlemleri farklı yongalar üzerinde gerçekleştirilebilir.
2.  Güvenlik düzeyi 2: T içinde şifreleme gerçekleştirir (ancak video işleme değil): şifresi çözülmüş arabellekler uygulama etki alanına döndürülür ve ayrı video donanımı veya yazılım aracılığıyla işlenir. Ancak, düzey 2 ' de, şifreleme bilgileri yalnızca t içinde işlenir.
3.  Güvenlik düzeyi 3 ' te cihazda t yok. Şifreleme bilgilerini ve konak işletim sistemindeki şifresi çözülmüş içeriği korumak için uygun ölçüler alınabilir. 3\. düzey bir uygulama de bir donanım şifreleme altyapısı içerebilir, ancak bu yalnızca performansı artırır, güvenliği etkilemez.

Aynı zamanda, [Widevine lisans şablonu 'ndaki Azure Media Services belgelerde](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), content_key_specs 'in security_level özelliği aşağıdaki beş farklı değere sahip olabilir (kayıttan yürütme için istemci sağlamlık gereksinimleri):

1.  Yazılım tabanlı beyaz kutu şifreleme gereklidir.
2.  Yazılım şifreleme ve karıştırılmış bir kod çözücü gereklidir.
3.  Anahtar malzeme ve şifre işlemleri, donanım tarafından desteklenen bir t içinde gerçekleştirilmelidir.
4.  İçeriğin şifrelenmiş ve kod çözme işlemi, donanım tarafından desteklenen bir t içinde gerçekleştirilmelidir.
5.  Şifreleme, kod çözme ve medyanın tüm işlenmesi (sıkıştırılmış ve sıkıştırılmamış), donanım tarafından desteklenen bir t içinde işlenmelidir.

Her iki güvenlik düzeyi de Google Widevine tarafından tanımlanır. Fark, kullanım düzeyidir: mimari düzeyi veya API düzeyi. Wdevine API 'sinde beş güvenlik düzeyi kullanılır. Security_level içeren content_key_specs nesnesi serisi kaldırıldı ve Azure Media Services Widevine lisans hizmeti tarafından Widevine küresel teslim hizmetine geçirilir. Aşağıdaki tabloda, iki güvenlik düzeyi kümesi arasındaki eşleme gösterilmektedir.

| **Widevine mimarisinde tanımlanan güvenlik düzeyleri** |**Widevine API 'de kullanılan güvenlik düzeyleri**|
|---|---| 
| **Güvenlik düzeyi 1**: Tüm içerik işleme, şifreleme ve denetim, güvenilir yürütme ortamı (t) içinde gerçekleştirilir. Bazı uygulama modellerinde, güvenlik işlemleri farklı yongalar üzerinde gerçekleştirilebilir.|**security_level = 5**: Şifreleme, kod çözme ve medyanın tüm işlenmesi (sıkıştırılmış ve sıkıştırılmamış), donanım tarafından desteklenen bir t içinde işlenmelidir.<br/><br/>**security_level = 4**: İçeriğin şifrelenmiş ve kod çözme işlemi, donanım tarafından desteklenen bir t içinde gerçekleştirilmelidir.|
**Güvenlik düzeyi 2**: T içinde şifreleme gerçekleştirir (ancak video işleme değil): şifresi çözülmüş arabellekler uygulama etki alanına döndürülür ve ayrı video donanımı veya yazılım aracılığıyla işlenir. Ancak, düzey 2 ' de, şifreleme bilgileri yalnızca t içinde işlenir.| **security_level = 3**: Anahtar malzeme ve şifre işlemleri, donanım tarafından desteklenen bir t içinde gerçekleştirilmelidir. |
| **Güvenlik düzeyi 3**: Cihazda bir t yok. Şifreleme bilgilerini ve konak işletim sistemindeki şifresi çözülmüş içeriği korumak için uygun ölçüler alınabilir. 3\. düzey bir uygulama de bir donanım şifreleme altyapısı içerebilir, ancak bu yalnızca performansı artırır, güvenliği etkilemez. | **security_level = 2**: Yazılım şifreleme ve karıştırılmış bir kod çözücü gereklidir.<br/><br/>**security_level = 1**: Yazılım tabanlı beyaz kutu şifreleme gereklidir.|

### <a name="question"></a>Soru

İçerik indirme neden uzun zaman alır?

### <a name="answer"></a>Yanıt

İndirme hızını artırmanın iki yolu vardır:

1.  Son kullanıcıların, içerik indirmek için kaynak/akış uç noktası yerine CDN 'ye isabet edebilmesi için CDN 'yi etkinleştirin. Kullanıcı akış uç noktasına isabet alıyorsa, her HLS segmenti veya DASH parçası dinamik olarak paketlenir ve şifrelenir. Bu gecikme süresi her segment/parça için milisaniyelik ölçekte olsa da, saat uzun bir videoya sahip olduğunuzda birikmiş gecikme süresi büyük olabilir.
2.  Son kullanıcılara tüm içerikler yerine, video kalitesi katmanlarını ve ses izlerini seçmeli olarak indirme seçeneği sağlar. Çevrimdışı modda, tüm kalite katmanlarını indirmek için bir nokta yoktur. Bunu başarmanın iki yolu vardır:
    1.  İstemci denetimli: oynatıcı uygulaması otomatik olarak seçilir veya Kullanıcı, indirilecek video kalite katmanını ve ses izlerini seçer;
    2.  Hizmet denetimli: bir adet, HLS çalma listesini veya DASH MPD 'ı tek bir video kalite katmanına ve seçili ses izlemeleriyle sınırlayan bir (genel) filtresi oluşturmak için Azure Media Services dinamik bildirim özelliğini kullanabilir. Ardından, son kullanıcılara sunulan indirme URL 'SI bu filtreyi içerecektir.

## <a name="summary"></a>Özet

Bu makalede, Android cihazlarda Widevine tarafından korunan DASH içeriği için çevrimdışı modda kayıttan yürütmeyi nasıl uygulayacağınızı ele alınmaktadır.  Ayrıca, Widevine korumalı içeriğin çevrimdışı akışı ile ilgili bazı yaygın sorulara da yanıt verdi.
