---
title: Azure Media Services v3 sürüm notları
description: En son gelişmelerden haberdar olmak için bu makalede, Azure Media Services v3 hakkında en son güncelleştirmeler sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 796abf8506a832c4053b505e903bb24ef9d09004
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279043"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 sürüm notları

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

* En son yayınlar
* Bilinen sorunlar
* Hata düzeltmeleri
* Kullanım dışı işlevsellik

## <a name="march-2021"></a>Mart 2021

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>AudioAnalyzer önayara yeni dil desteği eklendi

Video dökümü ve alt yazı için ek diller artık AudioAnalyzer ön ayarı 'nda (hem temel hem de standart modlar) kullanılabilir.

* İngilizce (Avustralya), ' en-AU '
* Fransızca (Kanada), ' fr-CA '
* Arapça (Bahreyn) modern standart, ' ar-BH '
* Arapça (Mısır), ' ar-EG '
* Arapça (Irak), ' ar-IQ '
* Arapça (Israil), ' ar-IL '
* Arapça (Ürdün), ' ar-JO '
* Arapça (Kuveyt), ' ar-KW '
* Arapça (Lübnan), ' ar-LB '
* Arapça (Umman), ' ar-OM '
* Arapça (Qtor), ' ar-QA '
* Arapça (Suudi Arabistan), ' ar-SA '
* Danca, ' da-DK '
* Norveç dili, ' NB-NO '
* İsveççe, ' SV-o '
* Fince, ' fi-FI '
* Tay dili, ' th '
* Türkçe, ' tr-TR '

[Video ve ses dosyalarını çözümleme kavramı makalesindeki](analyze-video-audio-files-concept.md) kullanılabilir en son dillere bakın.

## <a name="february-2021"></a>Şubat 2021

### <a name="hevc-encoding-support-in-standard-encoder"></a>Standart kodlayıcıda HEVC kodlama desteği

Standart kodlayıcı artık 8 bit HEVC (H. 265) kodlama desteğini desteklemektedir. HEVC içeriği, dinamik Paketleyiciyi ' hev1 ' biçimi kullanılarak teslim edilebilir ve paketlenebilir.  

[Media-Services-v3-DotNet git hub deposunda](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC), HEVC örneği ile yeni bir .NET özel kodlaması bulunmaktadır.
Özel kodlamaya ek olarak, aşağıdaki yeni yerleşik HEVC kodlama önayarları artık kullanılabilir:

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

V2 API 'sindeki Premium Encoder 'da daha önce HEVC kullanan müşterilerin standart Kodlayıcıdaki yeni HEVC kodlama desteğini kullanmak için geçirilmesi gerekir.

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services V2 API ve SDK 'Ları kullanımdan kaldırma duyurusu

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Azure Media Services REST API'si ve SDK 'larınızı, 29 Şubat 2024 ile v3 'e güncelleştirin

.NET ve Java için Azure Media Services REST API'si sürüm 3 ve istemci SDK 'Ları sürüm 2 ' den daha fazla özellik sağladığından, .NET ve Java için Azure Media Services REST API'si ve istemci SDK 'larının 2. sürümünü devre dışı sunuyoruz.

.NET ve Java için Azure Media Services REST API'si sürüm 3 ' ün ve istemci SDK 'larının daha zengin avantajlarından faydalanmak için anahtarı daha önce daha erken avantajlarına geçirmenizi öneririz.
Sürüm 3 şunları sağlar:
 
- 7/24 canlı olay desteği
- ARM REST API 'Leri, .NET Core için istemci SDK 'Ları, Node.js, Python, Java, Go ve Ruby.
- Müşteri tarafından yönetilen anahtarlar, güvenilen depolama tümleştirmesi, özel bağlantı desteği ve [daha fazlası](https://docs.microsoft.com/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Eylem Gerekiyor

İş yüklerinizde kesinti olasılığını en aza indirmek için, sürüm 2 API 'SI ve SDK 'Ları sürüm 3 API 'sine ve SDK 'sına 2024 Şubat ' den önce olan [geçiş kılavuzunu](./migrate-v-2-v-3-migration-introduction.md) gözden geçirin.
**29 şubat 2024**' den sonra, Azure Media Services sürüm 2 REST API, ARM hesap yönetimi apı sürümü 2015-10-01 veya sürüm 2 .NET istemci SDK 'larından trafiği kabul etmez. Buna sürüm 2 API 'sini çağırabilen 3. taraf açık kaynaklı istemci SDK 'LARı dahildir.  

Bkz. resmi [Azure güncelleştirmeleri duyurusu](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

### <a name="standard-encoder-support-for-v2-api-features"></a>V2 API özellikleri için standart kodlayıcı desteği

Yeni bir HEVC (H. 265) için destek desteği 'ne ek olarak, aşağıdaki özellikler kodlama API 'sinin 2020-05-01 sürümünde de mevcuttur.

- Yeni **Jobınputclip** desteği kullanılarak birden çok giriş dosyası kullanımı desteklenmektedir.
    - .NET için [iki varlığı birlikte nasıl birleştirileneceğini](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)gösteren bir örnek vardır.
- Ses izleme seçimi, müşterilerin gelen ses izlerini seçmesini ve eşlemesini ve kodlama için bunları çıkışa yönlendirmesini sağlar
    - **Audiotrackdescriptor** hakkındaki ayrıntılar ve seçimi izlemek için bkz. [REST API openapı](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385)
- Kodlama seçimini izle – müşterilerin bir ABR kaynak dosyasından veya birden çok bit hızı izinin bulunduğu canlı arşivden izler seçmesine olanak sağlar. Canlı olay arşivi dosyalarından MP4 'leri oluşturmaya yönelik son derece yararlı.
    - Bkz. [Videotrackdescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562)
- Redaksiyon (Bulanıklaştırma) FaceDetector 'a eklenen yetenekler
    - FaceDetector önayarının [redaksiyonunu](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) ve [Birleşik](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) modlarını görün

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>Azure Media Services API 'sinin 2020-05-01 sürümü için yeni istemci SDK sürümleri

Tüm kullanılabilir diller için yeni istemci SDK sürümleri, yukarıdaki özelliklerle birlikte kullanılabilir.
Lütfen Paket Yöneticisi 'ni kullanarak kod tabanlarınızın en son istemci SDK 'larına güncelleştirin.

- [.NET SDK paketi 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js TypeScript sürüm 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python Azure-MGMT-Media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-Beta. 2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>Azure Media Services API 'sinin 2020-05-01 sürümünde kullanılabilen yeni güvenlik özellikleri

- **[Müşteri tarafından yönetilen anahtarlar](concept-use-customer-managed-keys-byok.md)**: "2020-05-01" sürümü API 'si ile oluşturulan hesaplarda depolanan içerik anahtarları ve diğer veriler, bir hesap anahtarıyla şifrelenir. Müşteriler, hesap anahtarını şifrelemek için bir anahtar sağlayabilir.

- **[Güvenilen depolama](concept-trusted-storage.md)**: Media Services Media Services hesabıyla ilişkili yönetilen bir kimlik kullanılarak Azure depolama 'ya erişecek şekilde yapılandırılabilir. Depolama hesaplarına yönetilen bir kimlik kullanılarak erişildiğinde müşteriler Media Services senaryolarını engellemeden depolama hesabında daha kısıtlayıcı ağ ACL 'Leri yapılandırabilir.

- **[Yönetilen kimlikler](concept-managed-identities.md)**: müşteriler, anahtar kasalarına (müşteri tarafından yönetilen anahtarlar için) ve depolama hesaplarına (güvenilen depolama için) erişim sağlamak amacıyla bir Media Services hesabı Için sistem tarafından atanan yönetilen kimliği etkinleştirebilir.

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>JavaScript için isomorphic SDK kullanılarak TypeScript Node.js örnekleri güncelleştirildi

Node.js örnekleri en son isomorphic SDK 'sını kullanacak şekilde güncelleştirilmiştir. Örnekler artık TypeScript kullanımını gösterir. Ayrıca, Node.js/Typescriptiçin yeni bir canlı akış örneği eklenmiştir.

**[Media-Services-v3-node-öğreticiler](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** git hub deposunda bulunan en son örneklere bakın.

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>Sıcak durumdan daha hızlı başlangıç desteği için yeni canlı tek yönlü mod

Canlı olaylar artık "Stand" için daha düşük maliyetli faturalandırma modunu desteklemektedir. Bu, müşterilerin "etkin havuzlar" oluşturmak için daha düşük bir maliyetle canlı olayları önceden ayırmasını sağlar. Müşteriler daha sonra, oluşturma sırasında soğuk 'ten başlayarak çalışır duruma geçiş yapmak için tek başına canlı olayları kullanabilir.  Bu, kanalın önemli ölçüde başlatılması için geçen süreyi azaltır ve daha düşük bir fiyat modunda çalışan makinelerin hızlı etkin havuz ayırması için izin verir.
En son [fiyatlandırma ayrıntılarına bakın](https://azure.microsoft.com/pricing/details/media-services).
Bekleme durumu hakkında daha fazla bilgi ve canlı olaylardaki diğer durumlar hakkında daha fazla bilgi için bkz [. canlı olay durumları ve faturalama makalesi.](./live-event-states-billing-concept.md)

## <a name="december-2020"></a>Aralık 2020

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Azure Media Services artık Azure portal Norveç Doğu bölgesinde kullanılabilir.  Bu bölgede restV2 yok.

## <a name="october-2020"></a>Ekim 2020

### <a name="basic-audio-analysis"></a>Temel ses analizi

Ses analizi önceden ayarı artık temel mod fiyatlandırma katmanını içerir. Yeni temel ses Çözümleyicisi modu, konuşma dökümünü ayıklamak ve çıkış başlıklarını ve alt yazıları biçimlendirmek için düşük maliyetli bir seçenek sağlar. Bu mod, bir VTT alt başlık/resim yazısı dosyası için konuşmayı metne dönüştürme ve oluşturma işlemi gerçekleştirir. Bu modun çıktısı yalnızca anahtar sözcükler, döküm ve zamanlama bilgileri dahil olmak üzere bir Öngörüler JSON dosyası içerir. Bu modda otomatik dil algılama ve konuşmacı seçme dahil değildir. [Desteklenen dillerin](analyze-video-audio-files-concept.md#built-in-presets) listesine bakın.

Dizin Oluşturucu v1 ve Dizin Oluşturucu v2 kullanan müşteriler temel ses analizi hazır ayarı 'na geçiş yapılmalıdır.

Temel ses Çözümleyicisi modu hakkında daha fazla bilgi için bkz. [video ve ses dosyalarını çözümleme](analyze-video-audio-files-concept.md).  Temel ses çözümleyici modunu REST API kullanmayı öğrenmek için bkz. [temel ses dönüşümü oluşturma](transform-create-basic-audio-how-to.md).

### <a name="live-events"></a>Canlı Etkinlikler

Canlı olaylar durdurulduğunda çoğu özelliğe yönelik güncelleştirmelere artık izin verilir. Ayrıca, kullanıcıların canlı etkinliğin giriş ve önizleme URL 'Leri için statik ana bilgisayar adı için bir önek belirtmesinin izni vardır. VanityUrl artık `useStaticHostName` özelliğin amacını daha iyi yansıtacak şekilde çağırılır.

Canlı olaylar artık bekleme durumuna sahiptir.  [Media Services 'Da canlı olaylara ve canlı çıkışlara](./live-event-outputs-concept.md)bakın.

Canlı bir olay, çeşitli giriş en boy oranlarını almayı destekler. Esnetme modu, müşterilerin çıkış için uzatma davranışını belirlemesine izin verir.

Canlı kodlama artık 0,5 ile 20 saniye arasında sabit anahtar kare aralığı parçalarının çıktısını almak için özellik ekler.

### <a name="accounts"></a>Hesaplar

> [!WARNING]
> 2020-05-01 API sürümüyle Media Services bir hesap oluşturursanız, RESTv2 ile çalışmaz 

## <a name="august-2020"></a>Ağustos 2020

### <a name="dynamic-encryption"></a>Dinamik şifreleme

Eski PlayReady korumalı birlikte çalışabilen dosya biçimi (pff 1,1) şifrelemesi için destek artık dinamik Paketçde kullanılabilir. Bu, Microsoft tarafından yayımlanan Common Encryption standardının (CENC) erken taslaklarını uygulayan Samsung ve LG 'den eski akıllı TV kümelerine yönelik destek sağlar.  PFF 1,1 biçimi, daha önce Silverlight istemci kitaplığı tarafından desteklenen şifreleme biçimi olarak da bilinir. Bugün, bu şifreleme biçimi için yalnızca kullanım örneği senaryosu, yalnızca pff 1,1 şifrelemesiyle Kesintisiz Akış destekleyen bazı bölgelerde çok sayıda akıllı TV 'nin kaldığı eski akıllı TV pazarını hedeflemelidir.

Yeni pff 1,1 şifreleme desteğini kullanmak için, akış bulucunun URL yolundaki şifreleme değerini ' pff ' olarak değiştirin. Daha fazla ayrıntı için [Content Protection genel bakış](drm-content-protection-concept.md) bölümüne bakın.
Örneğin: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PFF 1,1 desteği, Common Encryption erken "Silverlight" sürümünü uygulayan akıllı TV (Samsung, LG) için geriye dönük olarak uyumlu bir çözüm olarak sunulmaktadır. Yalnızca PlayReady şifrelemesi 'nin pff 1,1 sürümünü destekleyen 2009-2015 arasında sevk edilen eski Samsung veya LG akıllı televizyonlara yönelik destek için gereken pff biçiminin kullanılması önerilir. 

## <a name="july-2020"></a>Temmuz 2020

### <a name="live-transcriptions"></a>Canlı döküm

Canlı döküm artık 19 dili ve 8 bölgeyi destekliyor.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Media Services ve Azure AD ile içeriğinizi koruma

[Azure ad kullanarak uçtan uca içerik koruması](./architecture-azure-ad-content-protection.md)adlı bir öğretici yayımladık.

### <a name="high-availability"></a>Yüksek kullanılabilirlik

Media Services ve Isteğe bağlı video (VOD) hakkında [genel bakış](./architecture-high-availability-encoding-concept.md) ve [örnek](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)ile yüksek kullanılabilirlik yayımladık.

## <a name="june-2020"></a>Haziran 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>IoT Edge Preview sürümünde canlı video analizi

IoT Edge canlı video analizinin önizlemesi herkese açık oldu. Daha fazla bilgi için bkz. [sürüm notları](../live-video-analytics-edge/release-notes.md).

IoT Edge üzerindeki canlı video analizi, Media Service ailesine yönelik bir genişletmektir. Bu, kendi Edge cihazlarınızda seçtiğiniz AI modelleriyle canlı videoyu çözümlemenize ve isteğe bağlı olarak bu videoyu yakalayıp kaydetmenize olanak sağlar. Artık canlı bir video işlem hattı oluşturma ve çalıştırma karmaşıklığı hakkında endişelenmenize gerek kalmadan gerçek zamanlı video analiziyle birlikte uygulamalar oluşturabilirsiniz.

## <a name="may-2020"></a>Mayıs 2020

Azure Media Services artık şu bölgelerde genel kullanıma sunulmuştur: "Almanya Kuzey", "Almanya Orta Batı", "İsviçre Kuzey" ve "İsviçre Batı". Müşteriler, Azure portal kullanarak bu bölgelere Media Services dağıtabilir.

## <a name="april-2020"></a>Nisan 2020

### <a name="improvements-in-documentation"></a>Belgelerde iyileştirmeler

Azure Media Player belgeleri [Azure belgelerine](../azure-media-player/azure-media-player-overview.md)geçirildi.

## <a name="january-2020"></a>Ocak 2020

### <a name="improvements-in-media-processors"></a>Medya işlemcilerinde iyileştirmeler

- Video analizinde aralıklı kaynaklar için geliştirilmiş destek: Bu içerikler artık, çıkarım altyapılarına gönderilmeden önce doğru şekilde uyumlu değildir.
- "En Iyi" modu ile küçük resimler oluştururken, kodlayıcı artık tek parçalı olmayan bir çerçeveyi seçmek için 30 saniyeden daha fazla arama yapar.

### <a name="azure-government-cloud-updates"></a>Azure Kamu bulut güncelleştirmeleri

Media Services şu Azure Kamu bölgelerinde: *Usgov Arizona* ve *Usgov Texas*.

## <a name="december-2019"></a>Aralık 2019

Canlı ve video isteğe bağlı akış için *kaynak yardımı önceden getirme* ÜSTBILGILERI için CDN desteği eklendi; Akamai CDN ile doğrudan sözleşme sahibi olan müşteriler için kullanılabilir. Origin-Assist CDN-Prefetch özelliği, Akamai CDN ile Azure Media Services kaynağı arasında aşağıdaki HTTP üst bilgi alışverişlerini içerir:

|HTTP üstbilgisi|Değerler|Gönderen|Alıcı|Amaç|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-yardım-önceden getirme-etkin | 1 (varsayılan) veya 0 |CDN|Kaynak|CDN 'in önceden getirme etkin olduğunu göstermek için|
|CDN-Origin-yardım-önceden getirme-yol| Örnek: <br/>Parçalar (video = 1400000000, Format = MPD-Time-cmaf)|Kaynak|CDN|CDN 'ye önceden getirme yolu sağlamak için|
|CDN-Origin-yardım-önceden getirme-Istek|1 (önceden getirme isteği) veya 0 (normal istek)|CDN|Kaynak|CDN 'den gelen isteğin bir önceden getirme olduğunu belirtmek için|

Üst bilgi değişimi 'nin bir parçasını eylemde görmek için aşağıdaki adımları deneyebilirsiniz:

1. Bir ses veya video segmenti veya parçası için Media Services kaynağa bir istek vermek üzere Postman veya kıvrımlı kullanın. İstekte CDN-Origin-yardım-önceden getirme-etkin: 1 üst bilgisini eklediğinizden emin olun.
2. Yanıtta, değeri olarak göreli bir yol ile CDN-Origin-yardım-önceden getirme-yol başlığını görmeniz gerekir.

## <a name="november-2019"></a>Kasım 2019

### <a name="live-transcription-preview"></a>Canlı döküm önizlemesi

Canlı döküm artık genel önizlemeye sunuldu ve Batı ABD 2 bölgesinde kullanıma sunulmuştur.

Canlı döküm, canlı olaylarla birlikte eklenti özelliği olarak çalışmak üzere tasarlanmıştır.  Doğrudan geçiş ve standart ya da Premium kodlama canlı olaylarında desteklenir.  Bu özellik etkinleştirildiğinde, hizmet bilişsel hizmetler 'in [konuşmadan metne](../../cognitive-services/speech-service/speech-to-text.md) özelliğini kullanarak gelen seslerdeki konuşulan kelimeleri metne dönüştürür. Bu metin daha sonra MPEG-DASH ve HLS protokollerinde video ve ses ile birlikte teslim için kullanılabilir hale getirilir. Faturalandırma, "çalışıyor" durumundayken canlı etkinliğin ek maliyeti olan yeni bir eklenti ölçmesini temel alır.  Canlı döküm ve faturalandırma hakkında daha fazla bilgi için bkz. [canlı](live-event-live-transcription-how-to.md) döküm

> [!NOTE]
> Şu anda, canlı döküm yalnızca Batı ABD 2 bölgesinde önizleme özelliği olarak kullanılabilir. Konuşulan sözcüklerin yalnızca şu anda Ingilizce (en-US) olarak dökümünü destekler.

### <a name="content-protection"></a>İçerik koruma

Eylül ayının sınırlı bölgelerinde yayınlanan *belirteç yeniden yürütme engellemesi* özelliği artık tüm bölgelerde kullanılabilir.
Media Services müşteriler artık, bir anahtar veya lisans istemek için aynı belirtecin kaç kez kullanılabileceği konusunda bir sınır ayarlayabilirler. Daha fazla bilgi için bkz. [belirteç yeniden yürütme engellemesi](drm-content-protection-concept.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Yeni önerilen canlı kodlayıcı iş ortakları

RTMP canlı akışı için aşağıdaki yeni önerilen iş ortağı kodlayıcıları desteği eklendi:

- [Cambrıa canlı 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 ve maks. eylem kameralar](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Dosya kodlama geliştirmeleri

- Yeni bir Içerik algılayan kodlama önceden ayarı artık kullanılabilir. İçeriğe duyarlı kodlama kullanarak bir GOP hizalı MP4 'leri kümesi üretir. Herhangi bir giriş içeriği verildiğinde, hizmet giriş içeriğinin ilk hafif analizini yapar. Bu sonuçları, en uygun katman sayısını, uygun bit hızını ve uyarlamalı akış tarafından teslim edilmek üzere çözüm ayarlarını belirlemede kullanır. Bu ön ayar özellikle, çıkış dosyalarının daha düşük bit hızlarındaki ancak izleyicilere uygun bir deneyim sunan bir kalitede olduğu düşük karmaşıklık ve orta ölçekli videolar için geçerlidir. Çıktı, video ve ses Aralanmış MP4 dosyaları içerir. Daha fazla bilgi için bkz. [Açık API özellikleri](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Standart Kodlayıcıdaki yeniden Oluşturucu için gelişmiş performans ve çoklu iş parçacığı. Belirli koşullar altında, müşteri% 5-40 VOD kodlaması arasında bir performans artışı görmelidir. Birden çok bit hızında kodlanan düşük karmaşıklık içeriği, en yüksek performans artışına neden olur. 
- Standart kodlama, zaman tabanlı GOP ayarı kullanılırken VOD kodlaması sırasında değişken çerçeve oranı (VFR) için normal bir GOP temposunda sağlar.  Bu, örneğin 15-30 fps arasında değişen karma kare hızı içeriğini gönderen müşterinin, uyarlamalı bit hızlı akış MP4 dosyaları için çıktıda hesaplanan normal GOP uzaklıkları görebildiğine yol gösterir. Bu, HLS veya DASH üzerinden gönderim yaparken parçalar arasında sorunsuzca geçiş yapma yeteneğini geliştirir. 
-  Değişken çerçeve oranı (VFR) kaynak içeriği için iyileştirilmiş AV Sync

### <a name="video-indexer-video-analytics"></a>Video Indexer, video analizi

- VideoAnalyzer önayarı kullanılarak ayıklanan ana kareler artık videonun yeniden boyutlandırılması yerine özgün çözümlenmektedir. Yüksek çözünürlüklü ana kare ayıklama, orijinal kalite görüntüleri sağlar ve Microsoft Görüntü İşleme ve Özel Görüntü İşleme Hizmetleri tarafından sunulan görüntü tabanlı yapay zeka modellerini kullanarak videonuzdan daha da ayrıntılı bilgiler elde etmenizi sağlar.

## <a name="september-2019"></a>Eylül 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Canlı olayların canlı doğrusal kodlaması

Media Services v3, canlı etkinliklerin canlı doğrusal kodlamasının 24 saat x 365 gün önizlemesini duyuruyor.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

*Azure Media Indexer* ve *Azure Media Indexer 2 Preview* kullanım dışı olarak duyuruluyoruz. Kullanımdan kaldırma tarihleri için,  [eski bileşenler](../previous/legacy-components.md) makalesine bakın. [Azure Media Services video Indexer](../video-indexer/index.yml) bu eski medya işlemcilerinin yerini alır.

Daha fazla bilgi için [Azure Media Indexer ve Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](../previous/migrate-indexer-v1-v2.md)konusuna bakın.

## <a name="august-2019"></a>Ağustos 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Güney Afrika bölgesel çifti Media Services için açık 

Media Services artık Güney Afrika Kuzey ve Güney Afrika Batı bölgelerinde kullanılabilir.

Daha fazla bilgi için bkz. [Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Medya işlemcilerinin kullanımdan kaldırılması

Kullanımdan kalkmakta olan *Windows Azure Medya Kodlayıcısı* (WAME) ve *Azure Medya Kodlayıcısı* (AME) medya işlemcilerinin kullanımdan kaldırılması duyuruluyor. Kullanımdan kaldırma tarihleri için, bu [eski bileşenler](../previous/legacy-components.md) makalesine bakın.

Ayrıntılar için bkz. [WAME 'i Media Encoder Standard geçirin](../previous/migrate-windows-azure-media-encoder.md) ve [adı Media Encoder Standard geçirin](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Temmuz 2019

### <a name="content-protection"></a>İçerik koruma

Belirteç kısıtlamasıyla korunan içerik akışı yaparken, son kullanıcılar anahtar teslim isteğinin bir parçası olarak gönderilen bir belirteç elde etmeniz gerekir. *Belirteç yeniden yürütme engellemesi* özelliği, Media Services müşterilerin aynı belirtecin bir anahtar veya lisans istemek için kaç kez kullanılabileceği konusunda bir sınır ayarlamasına olanak tanır. Daha fazla bilgi için bkz. [belirteç yeniden yürütme engellemesi](drm-content-protection-concept.md#token-replay-prevention).

Haziran itibariyle önizleme özelliği yalnızca ABD Orta ve ABD Orta Batı kullanılabilir.

## <a name="june-2019"></a>Haziran 2019

### <a name="video-subclipping"></a>Video alt kırpması

Artık bir [işi](/rest/api/media/jobs)kullanarak kodlarken videoyu kırpabilir veya alt kırpabilirsiniz. 

Bu işlev, [Builtınstandardencoderönayar](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ön ayarları veya [Standardencoderönayar](/rest/api/media/transforms/createorupdate#standardencoderpreset) önayarları kullanılarak oluşturulan [dönüşümlerle](/rest/api/media/transforms) birlikte kullanılır. 

Örneklere bakın:

* [.NET ile videoyu alt kırpın](transform-subclip-video-dotnet-how-to.md)
* [REST ile videoyu alt kırpın](transform-subclip-video-rest-how-to.md)

## <a name="may-2019"></a>Mayıs 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Izleyici Media Services tanılama günlükleri ve ölçümleri desteği

Artık Media Services tarafından yayılan telemetri verilerini görüntülemek için Azure Izleyici 'yi kullanabilirsiniz.

* Media Services anahtar teslim uç noktası tarafından gönderilen istekleri izlemek için Azure Izleyici tanılama günlüklerini kullanın. 
* Media Services [akış uç noktaları](stream-streaming-endpoint-concept.md)tarafından yayılan ölçümleri izleyin.   

Ayrıntılar için bkz. [Media Services ölçümleri ve tanılama günlüklerini izleme](monitoring/monitor-media-services-data-reference.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Dinamik paketlemeyle çoklu ses izleme desteği 

Birden çok codec ve dilde birden çok ses izi bulunan varlıklar akışı yaparken, [dinamik paketleme](encode-dynamic-packaging-concept.md) artık HLS çıkışı (sürüm 4 veya üzeri) için birden çok ses parçasını desteklemektedir.

### <a name="korea-regional-pair-is-open-for-media-services"></a>Kore bölgesel çifti Media Services için açık 

Media Services artık Kore Orta ve Kore Güney bölgelerinde kullanılabilir. 

Daha fazla bilgi için bkz. [Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Performans geliştirmeleri

Media Services performans iyileştirmeleri içeren güncelleştirmeler eklendi.

* İşleme için desteklenen en büyük dosya boyutu güncelleştirildi. Bkz., [Kotalar ve sınırlar](limits-quotas-constraints-reference.md).
* [Kodlama hızı iyileştirmeleri](concept-media-reserved-units.md).

## <a name="april-2019"></a>Nisan 2019

### <a name="new-presets"></a>Yeni Önayarlar

* [Facedetectorönayar](/rest/api/media/transforms/createorupdate#facedetectorpreset) yerleşik çözümleyici önayarlarına eklendi.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) , yerleşik kodlayıcı önayarlarına eklendi. Daha fazla bilgi için bkz. [içeriğe duyarlı kodlama](encode-content-aware-concept.md). 

## <a name="march-2019"></a>Mart 2019

Dinamik paketleme artık Dolby Atmos 'ı destekliyor. Daha fazla bilgi için bkz. [dinamik paketleme tarafından desteklenen ses codec bileşenleri](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging).

Artık akış bulucusu için uygulanacak varlık veya hesap filtrelerinin bir listesini belirtebilirsiniz. Daha fazla bilgi için bkz. [akış bulucu ile filtreleri ilişkilendirme](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Şubat 2019

Media Services v3 artık Azure Ulusal bulutlarında desteklenmektedir. Tüm özellikler henüz tüm bulutlarda kullanılamaz. Ayrıntılar için bkz. [Azure Media Services v3 'nin bulunduğu bulutlar ve bölgeler](azure-clouds-regions.md).

Media Services için Azure Event Grid şemalarına [Microsoft. Media. Joi Putprogress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) olayı eklendi.

## <a name="january-2019"></a>Ocak 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard ve MPı dosyaları 

MP4 dosyaları üretmek için Media Encoder Standard ile kodlarken, yeni bir. MPI dosyası oluşturulur ve çıkış varlığına eklenir. Bu MPı dosyası, [dinamik paketleme](encode-dynamic-packaging-concept.md) ve akış senaryoları için performansı iyileştirmeye yöneliktir.

MPı dosyasını değiştirmemelisiniz veya kaldırmamalıdır ya da bu tür bir dosyanın var olan (veya olmayan) hizmetinize herhangi bir bağımlılığı almalısınız.

## <a name="december-2018"></a>Aralık 2018

V3 API 'sinin GA sürümündeki güncelleştirmeler şunlardır:
       
* **Varlık filtreleri** ve **hesap filtreleri** için **presentationtimerange** özellikleri artık ' gerekli ' değildir. 
* **İşler** ve **dönüşümler** için $top ve $Skip sorgu seçenekleri kaldırılmıştır ve $OrderBy eklenmiştir. Yeni sıralama işlevinin eklenmesinin bir parçası olarak, $top ve $skip seçeneklerinin, uygulanmamasına rağmen daha önce daha önce açığa çıkmıştı.
* Numaralandırma genişletilebilirliği yeniden etkinleştirildi. Bu özellik SDK 'nın önizleme sürümlerinde etkinleştirilmiştir ve GA sürümünde yanlışlıkla devre dışı bırakıldı.
* Önceden tanımlanmış iki akış ilkesi yeniden adlandırıldı. **Securestreaming** artık **Multidrmcencstreaming**. **SecureStreamingWithFairPlay** artık **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Kasım 2018

CLı 2,0 modülü artık [Azure Media Services v3 GA](/cli/azure/ams) – v 2.0.50 için kullanılabilir.

### <a name="new-commands"></a>Yeni komutlar

- [az AMS hesabı](/cli/azure/ams/account)
- [az AMS Account-Filter](/cli/azure/ams/account-filter)
- [az AMS varlık](/cli/azure/ams/asset)
- [az AMS varlık-filtre](/cli/azure/ams/asset-filter)
- [az AMS Content-Key-Policy](/cli/azure/ams/content-key-policy)
- [az AMS Job](/cli/azure/ams/job)
- [az AMS canlı-olay](/cli/azure/ams/live-event)
- [az AMS Live-Output](/cli/azure/ams/live-output)
- [az AMS streaming-Endpoint](/cli/azure/ams/streaming-endpoint)
- [az AMS streaming-Locator](/cli/azure/ams/streaming-locator)
- [az AMS Account MRU](/cli/azure/ams/account/mru) -medya ayrılmış birimlerini yönetmenizi sağlar. Daha fazla bilgi için bkz. [Ölçek medya ayrılmış birimleri](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Yeni özellikler ve son değişiklikler

#### <a name="asset-commands"></a>Varlık komutları

- ```--storage-account``` ve ```--container``` bağımsız değişkenler eklendi.
- Sona erme saati (şimdi + 23h) için varsayılan değerler ve komutta izinler (okuma) ```az ams asset get-sas-url``` eklendi.

#### <a name="job-commands"></a>İş komutları

- ```--correlation-data``` ve ```--label``` eklenen bağımsız değişkenler
- ```--output-asset-names``` olarak yeniden adlandırıldı ```--output-assets``` . Şimdi, ' assetName = Label ' biçiminde varlıkların boşlukla ayrılmış bir listesini kabul eder. Etiketi olmayan bir varlık şu şekilde gönderilebilir: ' assetName = '.

#### <a name="streaming-locator-commands"></a>Akış Bulucu komutları

- ```az ams streaming locator``` temel komut ile değiştirilmiştir ```az ams streaming-locator``` .
- ```--streaming-locator-id``` ve ```--alternative-media-id support``` bağımsız değişkenler eklendi.
- ```--content-keys argument``` bağımsız değişken güncelleştirildi.
- ```--content-policy-name``` olarak yeniden adlandırıldı ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Akış Ilkesi komutları

- ```az ams streaming policy``` temel komut ile değiştirilmiştir ```az ams streaming-policy``` .
- Şifreleme parametreleri desteği ```az ams streaming-policy create``` eklendi.

#### <a name="transform-commands"></a>Dönüşüm komutları

- ```--preset-names``` bağımsız değişken ile değiştirilmiştir ```--preset``` . Artık tek seferde yalnızca 1 çıkış/önayar ayarlayabilirsiniz (çalıştırmanız gerekir daha fazla bilgi için ```az ams transform output add``` ). Ayrıca, yolu özel JSON 'unuza geçirerek özel Standardencoderönayar ayarlayabilirsiniz.
- ```az ams transform output remove``` , kaldırılacak çıkış dizini geçirerek gerçekleştirilebilir.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` ve komutlarına eklenen bağımsız değişkenler ```az ams transform create``` ```az ams transform output add``` .

## <a name="october-2018---ga"></a>Ekim 2018-GA

Bu bölümde Azure Media Services (AMS) Ekim güncelleştirmeleri açıklanmaktadır.

### <a name="rest-v3-ga-release"></a>REST v3 GA sürümü

[Rest v3 GA sürümü](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) , canlı, hesap/varlık düzeyi bildirim FILTRELERI ve DRM desteği için daha fazla API içerir.

#### <a name="azure-resource-management"></a>Azure Kaynak Yönetimi 

Azure Kaynak yönetimi desteği, birleştirilmiş yönetim ve işlemler API 'SI (artık tek bir yerde) sunar.

Bu sürümden itibaren, canlı olaylar oluşturmak için Kaynak Yöneticisi şablonları kullanabilirsiniz.

#### <a name="improvement-of-asset-operations"></a>Varlık işlemlerinin geliştirilmesi 

Aşağıdaki geliştirmeler sunulmuştur:

- HTTP (s) URL 'Leri veya Azure Blob Storage SAS URL 'Lerinden alma.
- Varlıklar için kendi kapsayıcı adlarından istediğinizi belirtin. 
- Azure Işlevleri ile özel iş akışları oluşturmak için daha kolay çıkış desteği.

#### <a name="new-transform-object"></a>Yeni dönüştürme nesnesi

Yeni **dönüşüm** nesnesi kodlama modelini basitleştirir. Yeni nesne, kodlama Kaynak Yöneticisi şablonları ve hazır ayarları oluşturmayı ve paylaşmayı kolaylaştırır. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory kimlik doğrulaması ve Azure RBAC

Azure AD kimlik doğrulaması ve Azure rol tabanlı erişim denetimi (Azure RBAC), Azure AD 'de role veya kullanıcılara göre güvenli dönüşümler, canlı olaylar, Içerik anahtar Ilkeleri veya varlıklar sağlar.

#### <a name="client-sdks"></a>İstemci SDK'ları  

Media Services v3 sürümünde desteklenen diller: .NET Core, Java, Node.js, Ruby, TypeScript, Python, go.

#### <a name="live-encoding-updates"></a>Canlı kodlama güncelleştirmeleri

Aşağıdaki canlı kodlama güncelleştirmeleri sunulmuştur:

- Canlı için yeni düşük gecikme modu (10 saniyelik uçtan uca).
- İyileştirilmiş RTMP desteği (artan kararlılık ve daha fazla kaynak Kodlayıcısı desteği).
- RTMPS güvenli alma.

    Canlı bir olay oluşturduğunuzda, artık 4 alma URL 'Si alırsınız. 4 içe alınan URL 'Ler neredeyse aynıdır, aynı akış belirtecine (AppID) sahiptir, yalnızca bağlantı noktası numarası bölümü farklıdır. URL 'Lerden ikisi, RTMPS için birincil ve yedeğdir. 
- 24 saat kodlamayı kodlama desteği. 
- SCTE35 aracılığıyla RTMP 'de geliştirilmiş ad sinyali desteği.

#### <a name="improved-event-grid-support"></a>İyileştirilmiş Event Grid desteği

Aşağıdaki Event Grid destek geliştirmelerini görebilirsiniz:

- Logic Apps ve Azure Işlevleriyle daha kolay geliştirme için Azure Event Grid tümleştirme. 
- Kodlama, Canlı Kanallar ve daha fazlası için olaylar için abone olun.

### <a name="cmaf-support"></a>CMAF desteği

Apple HLS (iOS 11 +) için CMAF ve ' CBCS ' şifreleme desteği ve CMAF 'yi destekleyen MPEG-DASH çalarlar.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA yayını Ağustos ayında duyuruldu. Şu anda desteklenen özellikler hakkında daha fazla bilgi için bkz. [video Indexer nedir](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Değişiklik planları

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Tüm özelliklerle (canlı, Içerik anahtarı Ilkeleri, hesap/varlık filtreleri, akış Ilkeleri dahil) işlemler içeren Azure CLı 2,0 modülü yakında kullanıma sunulacak. 

### <a name="known-issues"></a>Bilinen sorunlar

Yalnızca varlık veya AccountFilters için Önizleme API 'sini kullanan müşteriler aşağıdaki sorundan etkilenir.

Media Services v3 CLı veya API 'Leri ile 09/28 ve 10/12 arasında varlıklar veya hesap filtreleri oluşturduysanız, tüm varlık ve AccountFilters değerlerini kaldırmalı ve sürüm çakışması nedeniyle bunları yeniden oluşturmanız gerekir. 

## <a name="may-2018---preview"></a>Mayıs 2018-Önizleme

### <a name="net-sdk"></a>.NET SDK

.NET SDK 'da aşağıdaki özellikler mevcuttur:

* Medya içeriğini kodlamak veya çözümlemek için **dönüşümler** ve **işler** . Örnekler için bkz. [akış dosyaları](stream-files-tutorial-with-api.md) ve [Çözümleme](analyze-videos-tutorial.md).
* Son Kullanıcı cihazlarındaki yayımlama ve akış içeriği için **akış Konumlandırıcı**
* İçerik teslim edilirken anahtar teslimi ve içerik korumayı (DRM) yapılandırmak için **akış ilkeleri** ve **içerik anahtarı ilkeleri** .
* Canlı akış içeriğini alma ve arşivlemeyi yapılandırmak için **canlı olaylar** ve **canlı çıktılar** .
* Azure depolama 'da medya içeriğini depolamak ve yayımlamak için **varlıklar** . 
* Canlı ve isteğe bağlı medya içeriği için dinamik paketleme, şifreleme ve akışı yapılandırmak ve ölçeklendirmek için **akış uç noktaları** .

### <a name="known-issues"></a>Bilinen sorunlar

* Bir iş gönderilirken, HTTPS URL 'Leri, SAS URL 'Leri veya Azure Blob depolama alanında bulunan dosyalara yollar kullanarak kaynak videonuzu almak istediğinizi belirtebilirsiniz. Şu anda Media Services v3, HTTPS URL 'Leri üzerinden öbekli aktarım kodlamasını desteklemez.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="see-also"></a>Ayrıca bkz.

[Media Services V2 'den v3 'e geçmek Için geçiş kılavuzu](migrate-v-2-v-3-migration-introduction.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Genel Bakış](media-services-overview.md)
- [Media Services V2 sürüm notları](../previous/media-services-release-notes.md)