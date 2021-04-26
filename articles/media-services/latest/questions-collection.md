---
title: Azure Media Services v3 soru koleksiyonu
description: Bu makale, Azure Media Services v3 hakkında bir soru koleksiyonuna yanıt verir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: dc8f8f7ced1c5915c2ea54390685806cfcdd257f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280318"
---
<!-- NOTE this file is temporary and a placeholder until the FAQ file update is completed. -->

# <a name="media-services-v3-questions-collection"></a>Media Services v3 soru koleksiyonu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede, Azure Media Services v3 hakkında sık sorulan sorulara yanıtlar verilmektedir.

## <a name="general"></a>Genel

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Media Services tüm müşteri verilerini hizmet bölgesi dışında mı depolar?

- Müşteriler kendi depolama hesaplarını Azure Media Services hesaplarına ekler.  Tüm varlık verileri bu ilişkili depolama hesaplarında depolanır ve müşteri bu depolamanın konumunu ve çoğaltma türünü denetler.
- Media Services hesapla ilişkili ek veriler (Içerik şifreleme anahtarları, belirteç doğrulama anahtarları, Jobınputhttp URL 'leri ve diğer varlık meta verileri dahil), Media Services hesabı için seçilen bölge içinde Microsoft 'un sahip olduğu depolama alanında depolanır.
    - Brezilya Güney ve Güneydoğu Asya 'daki [veri fazlalığı](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) nedeniyle ek hesap verileri, bölgesel olarak yedekli bir biçimde depolanır ve tek bir bölgede yer alır. Güneydoğu Asya için, tüm ek hesap verileri Singapur 'da ve Brezilya Güney için veriler Brezilya 'da depolanır.
    - Brezilya Güney ve Güneydoğu Asya dışındaki bölgelerde ek hesap verileri de Microsoft 'un sahip olduğu depolamada [eşleştirilmiş bölgede](../../best-practices-availability-paired-regions.md)depolanabilir.
- Azure Media Services bölgesel bir hizmettir ve [yüksek kullanılabilirlik](architecture-high-availability-encoding-concept.md) veya veri çoğaltması sağlamaz. Bu özelliklere ihtiyaç duyan müşterilerin birden çok bölgede Media Services hesapları kullanarak bir çözüm oluşturması son derece önerilir.  Isteğe bağlı Media Services video ile bir kılavuz olarak kullanılabilir, yüksek kullanılabilirlik için nasıl çözüm oluşturulacağını gösteren bir örnek.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Media Services v3 için Azure portal sınırlamaları nelerdir?

V3 canlı olaylarını yönetmek, v3 varlıklarını ve işleri görüntülemek, API 'Lere erişme hakkında bilgi almak, içeriği şifrelemek için [Azure Portal](https://portal.azure.com/) kullanabilirsiniz. <br/>Diğer tüm yönetim görevleri için (örneğin, dönüşümleri ve işleri yönetme veya v3 içeriğini çözümleme), [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)veya desteklenen [SDK 'lardan](media-services-apis-overview.md#sdks)birini kullanın.

Videonuz daha önce Media Services v3 API kullanılarak Media Services hesabına yüklenmişse veya içerik canlı bir çıkışa göre oluşturulduysa, Azure portal **kodlama**, **Çözümleme** veya **şifreleme** düğmelerini görmezsiniz. Bu görevleri gerçekleştirmek için Media Services v3 API 'Lerini kullanın.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services kaynaklarda hangi Azure rolleri eylemler gerçekleştirebilir? 

[Media Services hesapları için bkz. Azure rol tabanlı erişim denetimi (Azure RBAC)](security-rbac-concept.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Apple iOS cihazlarına akış Nasıl yaparım? mı?

Akış kaynak sunucusuna Apple iOS Native cihazlarda tüketim için HTTP Canlı Akışı (HLS) içeriği döndürmesini bildirmek üzere yolun sonunda (  **Biçim = M3U8-AAPL)** bulunduğundan emin olun. Ayrıntılar için bkz. [Içerik sunma](encode-dynamic-packaging-concept.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Videoları işlemek için önerilen yöntem nedir?

Videoları kodlamaya veya çözümlemeye yönelik ortak görevleri yapılandırmak için [dönüşümler](/rest/api/media/transforms) kullanın. Her dönüşüm, video veya ses dosyalarınızı işlemeye yönelik bir tarif veya bir görev iş akışını açıklar. Bir [iş](/rest/api/media/jobs) , bir giriş video veya ses içeriğine dönüştürmeyi uygulamak için Media Services yapılan gerçek istedir. Dönüşüm oluşturulduktan sonra, Media Services API 'Lerini veya yayımlanmış SDK 'Ları kullanarak Işleri gönderebilirsiniz. Daha fazla bilgi için [Dönüşümler ve İşler](transform-jobs-concept.md) konusuna bakın.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Karşıya yükleme, kodlama ve bir video yayımladım. Akışa neden olan video oynatılsın?

En yaygın nedenlerinden biri, çalışan durumunda kayıttan yürütmeye çalıştığınız akış uç noktasına sahip olmadığınız anlamına gelir.

### <a name="how-does-pagination-work"></a>Sayfalandırma nasıl çalışır?

Sayfalandırma kullanırken, belirli bir sayfa boyutuna bağlı olmaması için her zaman bir sonraki bağlantıyı kullanarak koleksiyonu numaralandırın. Ayrıntılar ve örnekler için bkz. [filtreleme, sıralama, sayfalama](filter-order-page-entitites-how-to.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3 'de henüz kullanılamayan özellikler nelerdir?

Ayrıntılar için bkz. [Geçiş Kılavuzu](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Media Services bir hesabı abonelikler arasında taşıma işlemi nedir?  

Ayrıntılar için bkz. [abonelikler arasında Media Services hesabını taşıma](account-move-account-how-to.md).

## <a name="live-streaming"></a>Canlı akış 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Yayın yapıldıktan sonra canlı akışı durdurmak Nasıl yaparım? mı?

İstemci tarafı veya sunucu tarafında bu uygulamayı deneyebilirsiniz.

#### <a name="client-side"></a>İstemci tarafı

Web uygulamanız, tarayıcıyı kapattıkları için yayını sonlandırmak istiyorlarsa kullanıcıdan istemde bulunmalıdır. Bu, Web uygulamanızın işleyebileceğini bir tarayıcı olayıdır.

#### <a name="server-side"></a>Sunucu tarafı

Canlı olayları, Azure Event Grid olaylarına abone olarak izleyebilirsiniz. Daha fazla bilgi için bkz. [Eventgrid olay şeması](monitoring/media-services-event-schemas.md#live-event-types).

Şunlardan birini yapabilirsiniz:

* Canlı olaylarınızı durdurmak ve silmek için bir süre içinde hiçbir yeniden bağlantı gelmediğinden, akış düzeyi [Microsoft. Media. Liveeventencoderconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) olayları ve Izleyicisine [abone olun](monitoring/reacting-to-media-services-events.md) .
* İzleme düzeyi [sinyal](monitoring/media-services-event-schemas.md#liveeventingestheartbeat) olaylarına [abone olun](monitoring/reacting-to-media-services-events.md) . Tüm izlemelerin 0 ' a bir bit hızı veya son zaman damgası artık artmazsa, canlı olayı güvenle kapatabilirsiniz. Sinyal olayları her iz için her 20 saniyede bir gelir; bu nedenle biraz ayrıntılıdır.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Canlı akış sırasında kesmeler/videolar ve görüntü SLA 'ları eklemek Nasıl yaparım??

Media Services v3 Live Encoding, canlı akış sırasında video veya resim SLA 'larını henüz desteklemiyor. 

Kaynak videoyu değiştirmek için [canlı bir şirket içi kodlayıcı](encode-recommended-on-premises-live-encoders.md) kullanabilirsiniz. Birçok uygulama, Telestream kablolu dönüştürme, değiştirici Studio (iOS 'ta) ve OBS Studio (ücretsiz uygulama) dahil olmak üzere kaynakları değiştirme yeteneği sağlar.

## <a name="content-protection"></a>İçerik koruma

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 şifresiz anahtar şifrelemesi mi yoksa bir DRM sistemi mi kullanmalıyım?

Müşteriler genellikle AES şifrelemesi mi yoksa bir DRM sistemi mi kullanacağınızı merak ediyor. İki sistem arasındaki temel fark, AES şifrelemesi ile, anahtarın aktarım sırasında şifrelenmesi, ancak ek şifreleme ("açık") olmadan, içerik anahtarı TLS üzerinden istemciye iletilir. Sonuç olarak, içeriğin şifresini çözmek için kullanılan anahtar, istemci oynatıcı tarafından erişilebilir olur ve istemci üzerindeki bir ağ izlemede düz metin olarak görüntülenebilir. AES-128 şifresiz anahtar şifrelemesi, görüntüleyicisinin güvenilir bir taraf olduğu (örneğin, bir şirkette şirket içinde dağıtılan şirket videolarını, çalışanlar tarafından görüntülenmek üzere şifreleme) kullanım durumları için uygundur.

PlayReady, Widevine ve FairPlay gibi DRM sistemleri, bir AES-128 şifresiz anahtarla karşılaştırıldığında, içeriğin şifresini çözmek için kullanılan anahtarda ek bir şifreleme düzeyi sağlar. İçerik anahtarı, TLS tarafından sunulan herhangi bir aktarım düzeyi şifrelemeye ek olarak DRM çalışma zamanı tarafından korunan bir anahtara şifrelenir. Ayrıca, şifre çözme, işletim sistemi düzeyinde güvenli bir ortamda işlenir ve bu, kötü niyetli bir kullanıcının saldırılarına karşı daha zordur. Görüntüleyicinin güvenilir bir taraf olmaması ve en yüksek düzeyde güvenlik düzeyi olması gerekebileceği kullanım örnekleri için DRM önerilir.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Yalnızca belirli bir izni olan kullanıcılara Azure AD 'yi kullanmadan Nasıl yaparım? bir video gösterilsin mi?

Azure Active Directory (Azure AD) gibi belirli bir belirteç sağlayıcısını kullanmak zorunda değilsiniz. Asimetrik anahtar şifrelemesi kullanarak kendi [JWT](https://jwt.io/) sağlayıcınızı (yani, güvenli belirteç HIZMETI veya STS olarak adlandırılır) oluşturabilirsiniz. Özel STS 'de iş mantığınızı temel alarak talep ekleyebilirsiniz.

Verenin, hedef kitlesi ve taleplerin tümünün JWT içindeki ve `ContentKeyPolicyRestriction` ' de kullanılan değer arasında tam olarak eşleştiğinden emin olun `ContentKeyPolicy` .

Daha fazla bilgi için bkz. [Media Services dinamik şifrelemeyi kullanarak Içeriğinizi koruma](drm-content-protection-concept.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Bir lisans veya anahtar istemek için kullanmadan önce bir JWT belirteci aldım?

Üretim için, HTTPS isteği üzerinde bir JWT belirteci veren güvenli belirteç hizmeti (yani, bir Web hizmeti) olması gerekir. Test için, `GetTokenAsync` [program. cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)' de tanımlanan yöntemde gösterilen kodu kullanabilirsiniz.

Oynatıcı, bir kullanıcının kimliği doğrulandıktan sonra bu tür bir belirteç için STS 'ye bir istek yapar ve bunu belirtecin değeri olarak atar. [Azure MEDIA Player API](https://amp.azure.net/libs/amp/latest/docs/)'sini kullanabilirsiniz.

STS 'yi bir simetrik anahtarla veya asimetrik anahtarla çalıştırmanın bir örneği için bkz. [JWT aracı](https://aka.ms/jwt). Bu tür bir JWT belirtecini kullanan Azure Media Player bir oynatıcı örneği için bkz. [Azure Media test aracı](https://aka.ms/amtest). (Belirteç girişini görmek için **player_settings** bağlantısını genişletin.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Nasıl yaparım?, AES şifrelemesi ile videoları akışa almak için istekleri yetkilendirir mi?

Doğru yaklaşım, güvenli belirteç hizmeti kullanmaktır. STS 'de, Kullanıcı profiline bağlı olarak farklı talepler ("Premium Kullanıcı," "temel Kullanıcı," "ücretsiz deneme kullanıcısı") ekleyin. JWT içindeki farklı talepler sayesinde kullanıcı farklı içerikleri görebilir. Farklı içerik veya varlıklar için `ContentKeyPolicyRestriction` karşılık gelen `RequiredClaims` değer olacaktır.

Lisans/anahtar teslimini yapılandırmak ve varlıklarınızı şifrelemek için Azure Media Services API 'Leri kullanın ( [Bu örnekte](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)gösterildiği gibi).

Daha fazla bilgi için bkz.

- [İçerik korumasına genel bakış](drm-content-protection-concept.md)
- [Erişim denetimi ile çoklu DRM'ye sahip içerik koruma sistemi tasarlama](architecture-design-multi-drm-system.md)

### <a name="should-i-use-http-or-https"></a>HTTP veya HTTPS kullanmalıdır mi?
ASP.NET MVC oynatıcı uygulamasının şunları desteklemesi gerekir:

* HTTPS altında olan Azure AD aracılığıyla Kullanıcı kimlik doğrulaması.
* HTTPS altında olan istemci ile Azure AD arasında JWT Exchange.
* Lisans teslimi Media Services tarafından sağlanıyorsa, istemci tarafından, HTTPS 'nin altında olması gereken DRM lisans alımı. PlayReady ürün paketi, lisans teslimi için HTTPS 'yi desteklemez. PlayReady lisans sunucunuz Media Services dışındaysa, HTTP ya da HTTPS kullanabilirsiniz.

ASP.NET Player uygulaması en iyi uygulama olarak HTTPS kullanır, bu nedenle Media Player HTTPS altındaki bir sayfada bulunur. Ancak, HTTP akış için tercih edilir, bu nedenle bu sorunları karışık içerikle değerlendirmeniz gerekir:

* Tarayıcı karışık içeriğe izin vermez. Ancak, sorunsuz ve kesik çizgi için Silverlight ve OSMF eklentisi gibi eklentiler buna izin verir. Karma içerik, müşteri verilerini riske koyabilen kötü amaçlı JavaScript ekleme yeteneğinin tehdidi nedeniyle güvenlik konusudur. Tarayıcılar bu özelliği varsayılan olarak engeller. Bu soruna geçici bir çözüm olarak, tüm etki alanlarına izin vererek (HTTPS veya HTTP 'den bağımsız olarak) sunucu (Origin) tarafında çalışır. Bu muhtemelen iyi bir fikir değildir.
* Karışık içerikten kaçının. Oynatıcı uygulaması ve Media Player her ikisi de HTTP veya HTTPS kullanmalıdır. Karışık içerik oynarken, SilverlightSS Tech bir karma içerik uyarısının temizlenmesini gerektirir. FlashSS Tech karışık içeriği bir karma içerik uyarısı olmadan işler.
* Akış uç noktanız 2014 Ağustos 'Tan önce oluşturulduysa, HTTPS 'yi desteklemez. Bu durumda, HTTPS için yeni bir akış uç noktası oluşturun ve kullanın.

### <a name="what-about-live-streaming"></a>Canlı akış hakkında ne olacak?

Bir programla ilişkili varlığı bir VOD varlığı olarak düşünerek Media Services canlı akışı korumaya yardımcı olması için tam olarak aynı tasarımı ve uygulamayı kullanabilirsiniz. Canlı içeriğin birden çok DRM korumasını sağlamak için, varlığı canlı çıkışla ilişkilendirdikten önce, kıymete aynı kurulum/işlemeyi bir VOD varlığı gibi uygulayın.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services dışında lisans sunucuları ne olacak?

Genellikle, müşteriler bir lisans sunucu grubuna kendi veri merkezinde veya DRM hizmet sağlayıcıları tarafından barındırılan bir şekilde yatırım yapmış olur. Media Services içerik korumasıyla karma modda çalıştırabilirsiniz. Media Services, DRM lisansları Media Services dışındaki sunucular tarafından teslim edilirken, içerik barındırılabilir ve dinamik olarak korunabilir. Bu durumda, aşağıdaki değişiklikleri göz önünde bulundurun:

* STS 'nin kabul edilebilir ve lisans sunucusu grubu tarafından doğrulanabildiği belirteçleri vermesi gerekir. Örneğin, Axinom tarafından sunulan Widevine lisans sunucuları, bir yetkilendirme iletisi içeren belirli bir JWT gerektirir. Bu tür bir JWT vermek için bir STS 'ye sahip olmanız gerekir. 
* Artık Media Services lisans teslim hizmetini yapılandırmanız gerekmez. ' I yapılandırırken lisans alımı URL 'Lerini (PlayReady, Widevine ve FairPlay için) sağlamanız gerekir `ContentKeyPolicy` .

> [!NOTE]
> Widevine, Google tarafından sunulan ve Google 'ın hizmet koşullarına ve gizlilik ilkesine tabi olan bir hizmettir.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 ve v3 karşılaştırması 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal v3 kaynaklarını yönetmek için kullanabilir miyim?

Şu anda [Azure Portal](https://portal.azure.com/) şunları yapmak için kullanabilirsiniz:

* Media Services v3 içindeki [canlı olayları](live-event-outputs-concept.md) yönetin. 
* V3 [varlıklarını](assets-concept.md)görüntüleyin (yönetmez). 
* [API 'lere erişme hakkında bilgi alın](./access-api-howto.md). 

Diğer tüm yönetim görevleri (örneğin, [dönüşümler ve işler](transform-jobs-concept.md) ve [içerik koruması](drm-content-protection-concept.md)) IÇIN [REST API](/rest/api/media/), [Azure CLI](/cli/azure/ams)veya desteklenen [SDK 'lardan](media-services-apis-overview.md#sdks)birini kullanın.

### <a name="is-there-an-assetfile-concept-in-v3"></a>V3 'de bir AssetFile kavramı var mı?

`AssetFile`Kavram, depolama SDK bağımlılığıyla Media Services ayırmak için MEDIA SERVICES API 'sinden kaldırılmıştır. Azure depolama, Media Services değil, depolama SDK 'sına ait bilgileri korur. 

Daha fazla bilgi için bkz. [Media Services v3 'ye geçirme](migrate-v-2-v-3-migration-introduction.md).

### <a name="where-did-client-side-storage-encryption-go"></a>İstemci tarafı depolama şifrelemesi nerede?

Artık sunucu tarafı depolama şifrelemesini (varsayılan olarak açık) kullanmanızı öneririz. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Çevrimdışı akış

### <a name="fairplay-streaming-for-ios"></a>İOS için FairPlay Akışı

Aşağıdaki sık sorulan sorular iOS için çevrimdışı FairPlay akışı sorunlarını gidermeye yönelik yardım sağlar.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Çevrimdışı mod sırasında yalnızca ses oynayabilir ancak video değil mi?

Bu davranış, örnek uygulamanın tasarımıyla aynı şekilde görünür. Çevrimdışı modda alternatif bir ses izi varsa (HLS için bu durum söz konusu olduğunda), alternatif ses kanalında hem iOS 10 hem de iOS 11 varsayılandır. Bu davranışı, FPS çevrimdışı modu için dengelemek üzere akıştan alternatif ses parçasını kaldırın. Bunu Media Services yapmak için, dinamik bildirim filtresi **yalnızca ses = false değerini** ekleyin. Diğer bir deyişle, HLS URL 'SI **. ISM/manifest ile biter (format = M3U8-AAPL, Audio-Only = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Yalnızca ses ekle = yanlış olarak çevrimdışı modda video olmadan hala ses çalsın.

İçerik teslim ağı için önbellek anahtarı tasarımına bağlı olarak, içerik önbelleğe alınmış olabilir. Önbelleği temizleme.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>İOS 10 ' a ek olarak, iOS 11 ' de FPS çevrimdışı modu destekleniyor mu?

Evet. FPS çevrimdışı modu, iOS 10 ve iOS 11 için desteklenir.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>"FairPlay streaming ile çevrimdışı kayıttan yürütme ve HTTP Canlı Akışı" belgelerini FPS sunucu SDK 'sında neden bulamıyorum?

Bu belge, FPS sunucu SDK 'Sı sürüm 4 ' te birleştirildiğinden "FairPlay streaming Programlama Kılavuzu" ile birleştirilmiştir.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>İOS cihazlarında indirilen/OFFLINE dosya yapısı nedir?

Bir iOS cihazında indirilen dosya yapısı aşağıdaki ekran görüntüsüne benzer şekilde görünür. `_keys`Klasör, INDIRILEN fps lisanslarını, her bir lisans hizmeti ana bilgisayarı için bir depolama dosyası ile depolar. `.movpkg`Klasör, ses ve video içeriğini depolar. 

Bir kısa çizgi ile biten bir ada sahip ilk klasörün ardından bir sayı, video içeriği içerir. Sayısal değer, video yorumlamaları için en yoğun bant genişliğidir. Bir kısa çizgi ile biten bir ada sahip ikinci klasör, izleyen 0 ile ses içeriği içerir. Adlı üçüncü klasör, `Data` fps içeriğinin ana çalma listesini içerir. Son olarak, boot.xml klasör içeriğinin tamamını açıklama sağlar `.movpkg` . 

![FairPlay iOS örnek uygulaması için çevrimdışı dosya yapısı](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-file-structure.png)

Örnek bir boot.xml dosyası aşağıda verilmiştir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Android için Widevine akışı

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Bazı istemciler/kullanıcılar ve kalıcı olmayan lisanslar (çevrimdışı olarak devre dışı) için, diğerleri için kalıcı lisanslar (çevrimdışı etkin) teslim etme. İçeriği çoğaltmalıyım ve ayrı içerik anahtarları kullanmalıdır mi?

Media Services v3 bir varlığın birden çok örneğe sahip olmasına izin verdiğinden `StreamingLocator` , şunları yapabilirsiniz:

* `ContentKeyPolicy`İle bir örnek `license_type = "persistent"` , `ContentKeyPolicyRestriction` talebe açık `"persistent"` ve `StreamingLocator` .
* İle başka bir `ContentKeyPolicy` örnek `license_type="nonpersistent"` , `ContentKeyPolicyRestriction` `"nonpersistent` "ve" talebiyle birlikte `StreamingLocator` .
* `StreamingLocator`Farklı değerlere sahip iki örnek `ContentKey` .

Özel STS iş mantığına bağlı olarak, JWT belirtecinde farklı talepler verilir. Belirteç ile yalnızca ilgili lisans alınabilir ve yalnızca ilgili URL oynatılabilir.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Wdevine ve Media Services DRM güvenlik düzeyleri arasındaki eşleme nedir?

Google 'ın "Widevine DRM mimarisine genel bakış" üç güvenlik düzeyi tanımlar. Ancak, [Widevine lisans şablonunda Azure Media Services belge](drm-widevine-license-template-concept.md) , beş güvenlik düzeyi (kayıttan yürütme için istemci sağlamlık gereksinimleri) özetlenmektedir. Bu bölümde güvenlik düzeylerinin nasıl eşlendiği açıklanmaktadır.

Her iki güvenlik düzeyi kümesi de Google Widevine tarafından tanımlanır. Fark kullanım düzeyidir: mimari veya API. Wdevine API 'sinde beş güvenlik düzeyi kullanılır. `content_key_specs`Öğesini içeren nesne, `security_level` serisi kaldırıldı ve Azure Media Services Widevine lisans hizmeti tarafından Widevine küresel teslim hizmetine geçirilir. Aşağıdaki tabloda, iki güvenlik düzeyi kümesi arasındaki eşleme gösterilmektedir.

| **Widevine mimarisinde tanımlanan güvenlik düzeyleri** |**Widevine API 'de kullanılan güvenlik düzeyleri**|
|---|---| 
| **Güvenlik düzeyi 1**: tüm içerik işleme, şifreleme ve denetim, güvenilir yürütme ORTAMı (t) içinde gerçekleştirilir. Bazı uygulama modellerinde, güvenlik işlemleri farklı yongalar üzerinde gerçekleştirilebilir.|**security_level = 5**: şifreleme, kod çözme ve medyanın tüm işlenmesi (sıkıştırılmış ve sıkıştırılmamış), donanım tarafından desteklenen bir t içinde işlenmelidir.<br/><br/>**security_level = 4**: içeriğin şifrelenmiş ve kod çözme işlemi, donanım tarafından desteklenen bir t içinde gerçekleştirilmelidir.|
**Güvenlik düzeyi 2**: t içinde şifreleme (ancak video işleme değil) gerçekleştirilir. Şifresi çözülmüş arabellekler uygulama etki alanına döndürülür ve ayrı video donanımı veya yazılımı aracılığıyla işlenir. Ancak, düzey 2 ' de, şifreleme bilgileri yalnızca t içinde işlenir.| **security_level = 3**: Ana malzeme ve şifre işlemlerinin, donanım ile desteklenen bir t içinde gerçekleştirilmesi gerekir. |
| **Güvenlik düzeyi 3**: CIHAZDA bir t yok. Şifreleme bilgilerini ve konak işletim sistemindeki şifresi çözülmüş içeriği korumak için uygun ölçüler alınabilir. 3. düzey bir uygulama, bir donanım şifreleme altyapısı da içerebilir, ancak bu, güvenliği değil yalnızca performansı geliştirir. | **security_level = 2**: yazılım şifreleme ve karıştırılmış bir kod çözücü gereklidir.<br/><br/>**security_level = 1**: yazılım tabanlı beyaz kutu şifreleme gereklidir.|

#### <a name="why-does-content-download-take-so-long"></a>İçerik indirme neden uzun zaman alır?

İndirme hızını artırmanın iki yolu vardır:

* İçerik dağıtım ağını, kullanıcıların, içerik indirmek için kaynak/akış uç noktası yerine daha fazla isabet edebilmesi için etkinleştirin. Bir Kullanıcı bir akış uç noktasına isabet alıyorsa, her HLS segmenti veya DASH parçası dinamik olarak paketlenir ve şifrelenir. Bu gecikme süresi her segment veya parça için milisaniyelik ölçekte olsa da, saat uzunluğunda bir videonuz varsa birikmiş gecikme süresi büyük olabilir ve daha uzun bir indirmeye neden olabilir.
* Kullanıcılara, tüm içerikler yerine video kalitesi katmanlarını ve ses izlerini seçmeli olarak indirme seçeneği sunun. Çevrimdışı modda, tüm kalite katmanlarını indirmede bir nokta yoktur. Bunu başarmanın iki yolu vardır:

  * İstemci denetimli: oynatıcı uygulaması otomatik olarak seçilir veya Kullanıcı, video kalite katmanını ve indirilecek ses izlerini seçer.
  * Hizmet denetimli: HLS çalma listesini veya DASH MPD 'ı tek bir video kalite katmanına ve seçili ses izlemeleriyle sınırlayan bir (Global) filtresi oluşturmak için Azure Media Services içindeki dinamik bildirim özelliğini kullanabilirsiniz. Ardından, kullanıcılara sunulan indirme URL 'SI bu filtreyi içerir.

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3 genel bakış](media-services-overview.md)