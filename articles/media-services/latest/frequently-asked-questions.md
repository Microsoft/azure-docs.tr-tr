---
title: Azure Media Services v3 sık sorulan sorular | Microsoft Docs
description: Bu makale, Azure Media Services v3 hakkında sık sorulan soruların yanıtlarını vermektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 6e52a51c82529a98ef679dd747b9c8b7d177b660
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338825"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 hakkında sık sorulan sorular

Bu makale, Azure Media Services (AMS) v3 hakkında sık sorulan soruların yanıtlarını verir.

## <a name="general"></a>Genel

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services kaynaklarda hangi Azure rolleri eylemler gerçekleştirebilir? 

[Media Services hesapları Için rol tabanlı erişim denetimi (RBAC)](rbac-overview.md)konusuna bakın.

### <a name="how-do-i-configure-media-reserved-units"></a>Medya ayrılmış birimleri Nasıl yaparım? yapılandırmak mi?

Ses analizi ve Video analizi işleri, Media Services v3 tarafından tetiklenen veya Video Indexer için 10 S3 MRU hesabınızla sağlama önemle tavsiye edilir. 10'dan fazla S3 MRU gerekiyorsa, kullanarak bir destek bileti açın [Azure portalında](https://portal.azure.com/).

Ayrıntılar için bkz. [CLI ile medya Işlemeyi ölçeklendirme](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Videoları işlemek için önerilen yöntem nedir?

Videoları kodlamaya veya çözümlemeye yönelik ortak görevleri yapılandırmak için [dönüşümler](https://docs.microsoft.com/rest/api/media/transforms) kullanın. Her **dönüşüm** , video veya ses dosyalarınızı işlemeye yönelik bir tarif veya bir görev iş akışını açıklar. Bir [iş](https://docs.microsoft.com/rest/api/media/jobs) , belirli bir giriş videosunun veya ses içeriğinin **dönüşümünü** uygulamak için Media Services yapılan gerçek istedir. Dönüşüm oluşturulduktan sonra, Media Services API 'Leri veya yayımlanmış SDK 'Lardan herhangi birini kullanarak işleri gönderebilirsiniz. Daha fazla bilgi için [Dönüşümler ve İşler](transforms-jobs-concept.md) konusuna bakın.

### <a name="how-does-pagination-work"></a>Sayfalandırma nasıl çalışır?

Sayfalandırma kullanırken, belirli bir sayfa boyutuna bağlı olmaması için her zaman bir sonraki bağlantıyı kullanarak koleksiyonu numaralandırın. Ayrıntılar ve örnekler için bkz. [filtreleme, sıralama, sayfalama](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3 'de henüz kullanılamayan özellikler nelerdir?

Ayrıntılar için bkz. [v2 API 'lerine göre Özellik boşlukları](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Media Services bir hesabı abonelikler arasında taşıma işlemi nedir?  

Ayrıntılar için bkz. [abonelikler arasında Media Services hesabını taşıma](media-services-account-concept.md).

## <a name="live-streaming"></a>Canlı akış 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Canlı akış sırasında kesmeler/videolar ve resim SLA 'ları ekleme

Media Services v3 Live Encoding, canlı akış sırasında video veya resim SLA 'larını henüz desteklemiyor. 

Kaynak videoyu değiştirmek için [canlı bir şirket içi kodlayıcı](recommended-on-premises-live-encoders.md) kullanabilirsiniz. Birçok uygulama, Telestream kablolu dönüştürme, değiştirici Studio (iOS 'ta), OBS Studio (ücretsiz uygulama) ve birçok daha fazlası dahil olmak üzere kaynakları değiştirme yeteneği sağlar.

## <a name="content-protection"></a>Content protection

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 şifresiz anahtar şifrelemesi mi yoksa bir DRM sistemi mi kullanmalıyım?

Müşteriler genellikle bunlar AES şifrelemesi veya DRM sistem kullanması gerekip gerekmediğini merak ediyor. İki sistem arasındaki birincil fark, AES şifrelemesi ile, anahtarın aktarım sırasında şifrelenmesi, ancak ek şifreleme ("açık") olmadan olması için içerik anahtarı TLS üzerinden istemciye iletilir. Sonuç olarak, içeriğin şifresini çözmek için kullanılan anahtar, istemci oynatıcı tarafından erişilebilir olur ve istemci üzerindeki bir ağ izlemede düz metin olarak görüntülenebilir. AES-128 şifresiz anahtar şifrelemesi, görüntüleyicinin güvenilir bir taraf olduğu (örneğin, çalışanlar tarafından görüntülenmek üzere şirket içinde dağıtılan şirket videolarını şifrelemek) kullanım örneklerine uygundur.

PlayReady, Widevine ve FairPlay gibi DRM sistemleri, bir AES-128 şifresiz anahtarla karşılaştırılan içeriğin şifresini çözmek için kullanılan anahtarda ek bir şifreleme düzeyi sağlar. İçerik anahtarı, TLS tarafından sunulan herhangi bir aktarım düzeyi şifrelemesine ek olarak DRM çalışma zamanı tarafından korunan bir anahtara şifrelenir. Ayrıca, şifre çözme, kötü niyetli bir kullanıcı saldırı daha zor olduğu işletim sistemi düzeyinde güvenli bir ortamda ele alınır. DRM burada Görüntüleyicisi güvenilen taraf olmayabilir ve yüksek düzeyde güvenlik gerektiren kullanım durumları için önerilir.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD kullanmadan yalnızca belirli bir izne sahip olan kullanıcılar için bir video nasıl gösterilir?

Belirli bir belirteç sağlayıcısını (Azure AD gibi) kullanmanız gerekmez. Asimetrik anahtar şifrelemesi kullanarak kendi [JWT](https://jwt.io/) sağlayıcınızı (yani STS, güvenli belirteç hizmeti) oluşturabilirsiniz. Özel STS 'de iş mantığınızı temel alarak talep ekleyebilirsiniz.

Verenin, hedef kitlenin ve taleplerin, JWT içinde olanlar ve ContentKeyPolicy içinde kullanılan ContentKeyPolicyRestriction arasında tam olarak eşleştiğinden emin olun.

Daha fazla bilgi için bkz. [Media Services dinamik şifrelemeyi kullanarak Içeriğinizi koruma](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Nasıl ve nereye isteği lisans veya anahtar için kullanmadan önce JWT belirteci almak?

1. Üretim için bir HTTPS isteği üzerinde JWT belirteci veren bir güvenli belirteç Hizmetleri (STS) (Web hizmeti) olmalıdır. Test için gösterilen kod kullanabileceğinizi **GetTokenAsync** içinde tanımlanan yöntem [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Oynatıcı bir kullanıcı sts'ye böyle bir belirteç için doğrulandıktan sonra istekte bulunmak ve belirteç değeri olarak atamanız gerekir. Kullanabileceğiniz [Azure Media Player API'sine](https://amp.azure.net/libs/amp/latest/docs/).

* STS, hem simetrik hem de asimetrik anahtar ile çalışan bir örnek için bkz. Lütfen [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Böyle JWT belirteci kullanarak Azure Media Player temel bir yürütücü örneği için bkz [ https://aka.ms/amtest ](https://aka.ms/amtest) (belirteç giriş görmek için "player_settings" bağlantıyı genişletme).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Nasıl video akışı AES şifreleme ile istek yetki veriyor musunuz?

STS (güvenli belirteç hizmeti) yararlanmak için doğru yaklaşımdır bakın:

STS 'de, Kullanıcı profiline bağlı olarak farklı talepler ("Premium Kullanıcı", "temel Kullanıcı", "ücretsiz deneme kullanıcısı") ekleyin. JWT'nin farklı Taleplerde ile kullanıcı farklı içeriğini görebilir. Elbette, farklı içerik/varlık için karşılık gelen RequiredClaims ContentKeyPolicyRestriction olacaktır.

Lisans/anahtar teslimini yapılandırmak ve varlıklarınızı şifrelemek için Azure Media Services API 'Leri kullanın ( [Bu örnekte](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)gösterildiği gibi).

Daha fazla bilgi için bkz.

- [İçerik korumaya genel bakış](content-protection-overview.md)
- [Erişim denetimi ile çoklu DRM'ye sahip içerik koruma sistemi tasarlama](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP veya HTTPS?
ASP.NET MVC oynatıcı uygulaması aşağıdaki desteklemesi gerekir:

* HTTPS altında olduğunda Azure AD aracılığıyla kullanıcı kimlik doğrulaması.
* JWT exchange istemciyle HTTPS altında olduğunda Azure AD arasında.
* Media Services tarafından lisans teslim verdiyse, HTTPS altında olması gerekir istemci tarafından DRM lisans edinme. PlayReady ürün paketi HTTPS lisans dağıtımı için zorunlu değildir. Dışında Media Services PlayReady lisans sunucusu ise, HTTP veya HTTPS kullanabilirsiniz.

Media Player HTTPS altında sayfasında, bu nedenle ASP.NET oynatıcı uygulaması bir en iyi uygulama, HTTPS kullanır. Ancak, bu yüzden karışık içerikle ilgili bir sorun göz önünde bulundurmanız gerekir HTTP Akış için tercih edilir.

* Tarayıcı karışık içerik izin vermez. Ancak, eklentileri gibi Silverlight ve OSMF eklentisini için kesintisiz ve tire sağlar. Karışık içerik, risk müşteri verilerini oluşabilir kötü amaçlı JavaScript ekleme yeteneği kesilmeyen nedeniyle güvenlik konusudur. Tarayıcı varsayılan olarak bu özelliği engelleyin. Bunu çözmek için tek sunucu (kaynak) tarafında tüm etki alanları (bakılmaksızın, HTTPS veya HTTP) sağlayarak yoludur. Bu fikir ya da geçerli büyük olasılıkla değildir.
* Karışık içerik kaçının. Oynatıcı uygulaması ve Media Player, HTTP veya HTTPS kullanmanız gerekir. Karışık içerik yürütürken, bir karışık içerik uyarı temizleme silverlightSS teknik gerektirir. Karışık içerik karışık içerik uyarmadan flashSS teknik işler.
* Akış uç noktanızı Ağustos 2014 tarihinden önce oluşturulduysa, HTTPS'yi destekleyecek olmaz. Bu durumda, oluşturun ve yeni bir akış uç noktası için HTTPS kullanın.

### <a name="what-about-live-streaming"></a>Peki canlı akış?

Canlı Media Services akış VOD varlık olarak bir programla ilişkili varlığı düşünerek korumak için tam olarak aynı tasarımını ve uygulamasını kullanabilirsiniz. Canlı içeriğin birden çok DRM korumasını sağlamak için, varlığı canlı çıkışla ilişkilendirdikten önce, kıymete aynı kurulum/işlemeyi bir VOD varlığı gibi uygulayın.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services dışında lisans sunucuları hakkında neler diyeceksiniz?

Genellikle, müşterilere bir lisans sunucusu grubundaki DRM hizmet sağlayıcıları tarafından barındırılan bir ya da kendi veri merkezinde yatırım. Media Services content protection ile karma modda çalışabilir. İçeriği, barındırılan ve Media Services dışında sunucuları tarafından DRM lisanslarını teslim ederken dinamik olarak Media Services'de korumalı. Bu durumda, aşağıdaki değişiklikleri göz önünde bulundurun:

* STS, kabul edilebilir ve lisans sunucusu grubu tarafından doğrulanan belirteçleri vermek gerekiyor. Örneğin, bir yetkilendirme iletisini içeren belirli bir JWT Axinom tarafından sağlanan Widevine lisans sunucuları gerektirir. Bu nedenle, böyle bir JWT'nin vermek için bir STS'ye olması gerekir. 
* Artık, Media Services lisans teslimat hizmetinin yapılandırma gerekmez. Lisans edinme URL'leri (PlayReady, Widevine ve FairPlay) sağlamanız gereken ContentKeyPolicies yapılandırdığınızda.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal v3 kaynaklarını yönetmek için kullanabilir miyim?

Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

### <a name="is-there-an-assetfile-concept-in-v3"></a>V3 'de bir AssetFile kavramı var mı?

Assetdosyaları, depolama SDK bağımlılığıyla Media Services ayırmak için AMS API 'sinden kaldırılmıştır. Artık Media Services değil, depolama alanında yer alan bilgileri korur. 

Daha fazla bilgi için bkz. [Media Services v3 'ye geçirme](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>İstemci tarafı depolama şifrelemesi nerede?

Artık sunucu tarafı depolama şifrelemesi (varsayılan olarak açık) kullanılması önerilir. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3 genel bakış](media-services-overview.md)
