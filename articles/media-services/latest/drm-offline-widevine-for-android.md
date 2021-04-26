---
title: Akış Widevine Android çevrimdışı
description: Bu konu başlığı altında, Wıdevine korunan içeriğin çevrimdışı akışı için Azure Media Services v3 hesabınızı yapılandırma gösterilmektedir.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 297520764272e2d6df54fe3a8ad734088163638d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069164"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Android için Media Services v3 ile çevrimdışı Widevine akışı

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Çevrimiçi akış için içeriği korumanın yanı sıra, medya içerik aboneliği ve Kiralama Hizmetleri, internet 'e bağlı değilseniz kullanılan indirilebilir içerik sunar. Ağ bağlantısı kesildiğinde Uçak modunda kayıttan yürütmek için içeriği telefonunuza veya tabletinize indirmeniz gerekebilir. İçeriği indirmek isteyebileceğiniz ek senaryolar:

- Bazı içerik sağlayıcıları, bir ülke/bölge kenarlığının ötesinde DRM lisans teslimine izin verebilir. Kullanıcı yolculukta içerik izlemek istiyorsa, çevrimdışı indirme gerekir.
- Bazı ülkelerde/bölgelerde Internet kullanılabilirliği ve/veya bant genişliği sınırlıdır. Kullanıcılar, tatmin edici görüntüleme deneyimi için yeterince yüksek çözünürlükte izleyebilmek için içerik indirmeyi seçebilirler.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Bu makalede, Android cihazlarda Widevine tarafından korunan DASH içeriği için çevrimdışı modda kayıttan yürütmenin nasıl uygulanacağı açıklanır. Çevrimdışı DRM, içeriğiniz için abonelik, kiralama ve satın alma modelleri sağlamanıza olanak tanıyarak, hizmetlerinizin müşterilerinin internet bağlantısı kesildiğinde kolayca içerik almasına imkan tanır.

Android Player uygulamalarını oluşturmak için üç seçenekten oluşan bir ana hat sunuyoruz:

> [!div class="checklist"]
> * ExoPlayer SDK 'sının Java API 'sini kullanarak oyuncu oluşturma
> * ExoPlayer SDK 'sının Xamarin bağlamasını kullanarak oyuncu oluşturma
> * Chrome Mobile Browser V62 veya üzeri sürümlerde şifreli medya uzantısı 'nı (EME) ve medya kaynak uzantısı 'nı (MSE) kullanarak bir oyuncu oluşturun

Makalede ayrıca Wıdevine korumalı içeriğin çevrimdışı akışı ile ilgili bazı yaygın soruların yanıtları da vardır.

> [!NOTE]
> Çevrimdışı DRM yalnızca içeriği indirdiğinizde lisans için tek bir istek yapmak üzere faturalandırılır. Tüm hatalar faturalandırılmaz.

## <a name="prerequisites"></a>Önkoşullar

Android cihazlarda Widevine için çevrimdışı DRM uygulamadan önce, önce şunları yapmalısınız:

- Widevine DRM kullanarak çevrimiçi içerik koruma için tanıtılan kavramlarla ilgili bilgi sahibi olun. Bu, aşağıdaki belgelerde/örneklerde ayrıntılı olarak ele alınmıştır:
    - [Erişim denetimi ile çoklu DRM'ye sahip içerik koruma sistemi tasarlama](architecture-design-multi-drm-system.md)
    - [DRM dinamik şifreleme ve lisans teslim hizmetini kullanma](drm-protect-with-drm-tutorial.md)
- Kopyala https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git .

    Widevine yapılandırması eklemek için [.NET kullanarak DRM ile birlikte şifrelemeden](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) kodu değiştirmeniz gerekir.  
- Çevrimdışı Widevine DRM kayıttan yürütmeyi destekleyebilen açık kaynaklı bir video oynatıcı SDK 'sı olan Android için Google ExoPlayer SDK 'sı hakkında bilgi sahibi olun. 
    - [Exooynatıcı SDK 'Sı](https://github.com/google/ExoPlayer)
    - [ExoPlayer Geliştirici Kılavuzu](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer geliştirici blogu](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services 'da içerik korumasını yapılandırma

[Getorcreatecontentkeypolicyasync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) yönteminde aşağıdaki gerekli adımlar mevcuttur:

1. İçerik anahtarı tesliminin lisans teslim hizmetinde nasıl yetkilendirildiğini belirtin: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine lisans şablonunu yapılandırma:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions oluştur:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Çevrimdışı modunu etkinleştir

Widevine lisansları için **çevrimdışı** modu etkinleştirmek üzere [Widevine lisans şablonu](drm-widevine-license-template-concept.md)' nu yapılandırmanız gerekir. **Policy_overrides** nesnesinde, **Can_persist** özelliğini, [Configurewidevinelicensetempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)içinde gösterildiği gibi **true** (varsayılan değeri false) olarak ayarlayın. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

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

Geliştiriciler, bir uygulamanın geliştirilmesi sırasında [Exoplayer geliştirici kılavuzuna](https://google.github.io/ExoPlayer/guide.html) ve Ilgili [Geliştirici bloguna](https://medium.com/google-exoplayer) başvurmalıdır. Google, şu anda çevrimdışı olarak Widevine 'yı destekleyen ExoPlayer uygulaması için tam olarak belgelenmiş bir başvuru uygulaması veya örnek kod yayımlamamıştır, bu nedenle bilgiler geliştiricilerin kılavuzuyla ve bloguyla sınırlandırılmıştır. 

### <a name="working-with-older-android-devices"></a>Eski Android cihazlarla çalışma

Bazı eski Android cihazlarda, aşağıdaki **policy_overrides** özellikleri için değerler ayarlamanız gerekir ( [Widevine lisans şablonunda](drm-widevine-license-template-concept.md)tanımlanmıştır: **rental_duration_seconds**, **playback_duration_seconds** ve **license_duration_seconds**. Alternatif olarak, sonsuz/sınırsız süre anlamına gelen sıfır olarak ayarlayabilirsiniz.  

Bir tamsayı taşma hatasını önlemek için değerlerin ayarlanması gerekir. Sorun hakkında daha fazla açıklama için bkz https://github.com/google/ExoPlayer/issues/3150 . ve https://github.com/google/ExoPlayer/issues/3112 . <br/>Değerleri açıkça ayarlamazsanız,  **PlaybackDurationRemaining** ve **LicenseDurationRemaining** için çok büyük değerler atanır (örneğin, 64 bit tam sayı için en büyük pozitif değer olan 9223372036854775807). Sonuç olarak, Widevine lisansının geçerliliği zaman aşımına uğradı, bu nedenle şifre çözme gerçekleşmeyecektir. 

Android 5,0, ARMv8 ([GELIŞMIŞ RISC makinesi](https://en.wikipedia.org/wiki/ARM_architecture)) ve 64-bit platformlarını tamamen destekleyecek şekilde tasarlanan ilk Android sürümü olduğundan, Android 5,0 Lollipop veya sonrasında bu sorun oluşmaz, çünkü Android 4,4 KitKat ilk olarak diğer eski Android sürümleriyle aynı şekilde ARMv7 ve 32 bit platformları desteklemek üzere tasarlanmıştır.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Android oynatma uygulaması oluşturmak için Xamarin kullanma

Aşağıdaki bağlantıları kullanarak, ExoPlayer için Xamarin bağlamalarını bulabilirsiniz:

- [Google ExoPlayer kitaplığı için Xamarin bağlamaları kitaplığı](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet için Xamarin bağlamaları](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Ayrıca, aşağıdaki iş parçacığına bakın: [Xamarin bağlama](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Android için Chrome Player uygulamaları

[Android v. 62 Için Chrome](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)sürümü ile başlayarak, eme 'de kalıcı lisans desteklenir. [Wdevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) artık Android için Chrome 'da da desteklenmektedir. Bu, son kullanıcılarınız Chrome 'un bu (veya üzeri) sürümü varsa, Chrome 'da çevrimdışı oynatma uygulamaları oluşturmanıza olanak tanır. 

Ayrıca, Google bir aşamalı Web uygulaması (PWA) örneği üretti ve açık kaynaklı BT: 

- [Kaynak kodu](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google hosted sürümü](https://biograf-155113.appspot.com/ttt/episode-2/) (Android cihazlarda yalnızca Chrome v 62 ve üzeri sürümlerde geçerlidir)

Mobil Chrome tarayıcınızı bir Android telefonunda V62 (veya üzeri) sürümüne yükseltir ve yukarıdaki barındırılan örnek uygulamayı test ederseniz hem çevrimiçi akış hem de çevrimdışı oynatma işinin olduğunu görürsünüz.

Yukarıdaki açık kaynaklı PWA uygulaması Node.js yazılır. Bir Ubuntu sunucusunda kendi sürümünüzü barındırmak istiyorsanız, kayıttan yürütmeyi engelleyebilecek aşağıdaki yaygın sorunları göz önünde bulundurun:

1. CORS sorunu: örnek uygulamadaki örnek video içinde barındırılır https://storage.googleapis.com/biograf-video-files/videos/ . Google, Google bulut depolama demeti içinde barındırılan tüm test örnekleri için CORS 'yi ayarladı. Bunlar, özel olarak CORS girişi `https://biograf-155113.appspot.com` (Google 'ın örneğini barındırdığı etki alanı) diğer sitelere erişimi ENGELLEDIĞI CORS üstbilgileri ile birlikte sunulur. Denerseniz, aşağıdaki HTTP hatasını görürsünüz: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Sertifika sorunu: Chrome v 58 ' den başlayarak Widevine için EME, HTTPS gerektirir. Bu nedenle, örnek uygulamayı bir x509 sertifikasıyla HTTPS üzerinden barındırmanıza gerek duyarsınız. Aşağıdaki gereksinimler nedeniyle olağan bir test sertifikası çalışmıyor: aşağıdaki minimum gereksinimleri karşılayan bir sertifika edinmeniz gerekir:
    - Chrome ve Firefox, sertifikada SAN-Subject alternatif ad ayarının mevcut olmasını gerektirir
    - Sertifika, güvenilir bir CA 'ya sahip olmalı ve kendinden imzalı bir geliştirme sertifikası çalışmıyor
    - Sertifika, Web sunucusunun veya ağ geçidinin DNS adıyla eşleşen bir CN 'ye sahip olmalıdır

## <a name="more-information"></a>Daha fazla bilgi

Daha fazla bilgi için bkz. [sorular koleksiyonundaki Widevine](questions-collection.md#widevine-streaming-for-android).

Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.
