---
title: Azure Media Services kavramlar | Microsoft Docs
description: Bu makalede, Ayrıntılar için Microsoft Azure Media Services kavramlara ve diğer makalelere yönelik bağlantılarla ilgili kısa bir genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d5743b3328248617b9158cfb1f8960549784c564
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056003"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services kavramlar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Bu konu, en önemli Media Services kavramlara genel bakış sunar.

## <a name="assets-and-storage"></a><a name="assets"></a>Varlıklar ve depolama
### <a name="assets"></a>Varlıklar
Bir [varlık](/rest/api/media/operations/asset) , dijital dosyalar (video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı altyazı dosyaları dahil) ve bu dosyalarla ilgili meta veriler içerir. Dijital dosyalar bir varlığa yüklendikten sonra, Media Services Encoding ve akış iş akışlarında kullanılabilirler.

Bir varlık, Azure depolama hesabındaki bir blob kapsayıcısına eşlenir ve varlık içindeki dosyalar bu kapsayıcıda blok Bloblar olarak depolanır. Sayfa Blobları Azure Media Services tarafından desteklenmez.

Bir varlık içinde karşıya yüklenecek ve depolanacak medya içeriğine karar verirken aşağıdaki noktalar geçerlidir:

* Bir varlık, medya içeriğinin yalnızca tek ve benzersiz bir örneğini içermelidir. Örneğin, bir TV bölümü, film veya tanıtım için tek bir düzenleme.
* Bir varlık, bir audiogörsel dosyası için birden çok yorumlama veya düzenleme içermemelidir. Bir varlığın hatalı kullanımının bir örneği bir varlık içinde tek bir üretimden birden fazla TV bölümünü, tanıtımı veya birden çok kamera açısını depolamaya çalışıyor olabilir. Birden çok yorumlandırma veya bir audiogörsel dosyasının düzenlemelerini bir varlık içinde depolamak, iş akışında daha sonra varlık teslim etmek, veri akışı ve güvenli hale getirme sorunları oluşmasına neden olabilir.  

### <a name="asset-file"></a>Varlık dosyası
Bir [Assetdosyası](/rest/api/media/operations/assetfile) bir blob kapsayıcısında depolanan gerçek bir videoyu veya ses dosyasını temsil eder. Bir varlık dosyası her zaman bir varlıkla ilişkilendirilir ve bir varlık bir veya daha fazla dosya içerebilir. Bir varlık dosya nesnesi bir blob kapsayıcısındaki dijital dosyayla ilişkilendirilmediğinde Media Services kodlayıcı görevi başarısız olur.

**Assetfile** örneği ve gerçek medya dosyası iki ayrı nesne. Maldosya örneği medya dosyası hakkındaki meta verileri içerir, ancak medya dosyası gerçek medya içeriğini içerir.

Media Service API 'Lerini kullanmadan Media Services tarafından oluşturulan blob kapsayıcılarının içeriğini değiştirmeyi denememelisiniz.

### <a name="asset-encryption-options"></a>Varlık şifreleme seçenekleri
Karşıya yüklemek, depolamak ve teslim etmek istediğiniz içerik türüne bağlı olarak Media Services, aralarından seçim yapabileceğiniz çeşitli şifreleme seçenekleri sağlar.

>[!NOTE]
>Şifreleme kullanılmaz. Varsayılan değer budur. Bu seçeneği kullandığınızda, içeriğiniz aktarım sırasında veya depolamadaki bekleyen sırada korunmaz.

Aşamalı indirme kullanarak bir MP4 teslim etmeyi planlıyorsanız içeriğinizi karşıya yüklemek için bu seçeneği kullanın.

**Storageencryptıon** : açık içeriğinizi AES 256 bit şifrelemeyi kullanarak yerel olarak şifrelemek için bu seçeneği kullanın ve ardından geri kalanı şifreli olarak depolandığı Azure depolama 'ya yükleyin. Depolama şifrelemesi ile korunan varlıklar, kodlama öncesinde otomatik olarak şifrelenmez ve şifreli bir dosya sistemine yerleştirilir ve yeni bir çıkış varlığı olarak geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama şifrelemesi için birincil kullanım örneği, yüksek kaliteli giriş medya dosyalarınızı diskte bekleyen bir şekilde güçlü şifreleme ile güvenli hale getirmek istediğinizde kullanılır. 

Depolama şifrelenmiş bir varlık teslim etmek için varlığın teslim ilkesini yapılandırmanız gerekir, böylece içeriğinizi nasıl teslim etmek istediğinizi bilir Media Services. Varlığınızın akışı yapılmadan önce, akış sunucusu depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini (örneğin, AES, PlayReady veya şifreleme olmadan) kullanarak içeriğinizi akışa alabilir. 

**CommonEncryptionProtected** -Common Encryption veya PlayReady DRM ile korumalı (örneğin, PlayReady DRM ile korunan kesintisiz akış) içeriği şifrelemek (veya önceden şifrelenmiş olarak yüklemek) istiyorsanız bu seçeneği kullanın.

**EnvelopeEncryptionProtected** – GELIŞMIŞ ŞIFRELEME standardı (AES) ile şifrelenen http canlı akışı (veya zaten korumalı) () korumak istiyorsanız bu seçeneği kullanın. HLS 'leri zaten AES ile şifrelendiğinden karşıya yüklüyorsanız, bu, Transform Manager tarafından şifrelenmelidir.

### <a name="access-policy"></a>Erişim ilkesi
Bir [AccessPolicy](/rest/api/media/operations/accesspolicy) , izinleri (okuma, yazma ve listeleme gibi) ve bir varlığa erişim süresini tanımlar. Genellikle bir AccessPolicy nesnesini bir varlık içinde yer alan dosyalara erişmek için kullanılacak bir Konumlandırıcı 'ya geçitirsiniz.

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Uzun süre boyunca kullanılmak için oluşturulan bulucu ilkeleri gibi aynı günleri / erişim izinlerini sürekli olarak kullanıyorsanız, aynı ilke kimliğini kullanmalısınız (karşıya yükleme olmayan ilkeler için). Daha fazla bilgi için [bu](media-services-dotnet-manage-entities.md#limit-access-policies) konu başlığına bakın.

### <a name="blob-container"></a>Blob kapsayıcı
Blob kapsayıcısı bir blob kümesi gruplandırması sağlar. Blob kapsayıcıları, erişim denetimi için sınır noktası olarak Media Services ve varlıklar üzerindeki paylaşılan erişim Imzası (SAS) Konumlandırıcı ' da kullanılır. Azure depolama hesabı, sınırsız sayıda blob kapsayıcısı içerebilir. Kapsayıcıda sınırsız sayıda blob depolanabilir.

>[!NOTE]
> Media Service API 'Lerini kullanmadan Media Services tarafından oluşturulan blob kapsayıcılarının içeriğini değiştirmeyi denememelisiniz.
> 
> 

### <a name="locators"></a><a name="locators"></a>Bulucuları
[Bulmalar](/rest/api/media/operations/locator)bir varlık içinde bulunan dosyalara erişmek için bir giriş noktası sağlar. Bir istemcinin belirli bir varlığa erişimi olan izinleri ve süreyi tanımlamak için bir erişim ilkesi kullanılır. Farklı belirleyicilerinin aynı izin ve süre ayarlarını kullanarak farklı istemcilere farklı başlangıç zamanları ve bağlantı türleri sağlaması gibi, konum belirleyicilerinin bir erişim ilkesiyle çok sayıda ilişkisi olabilir. Ancak, Azure depolama hizmetleri tarafından ayarlanan bir paylaşılan erişim ilkesi kısıtlaması nedeniyle, belirli bir varlıkla aynı anda en fazla beş benzersiz konum belirleyiciyle ilişkili olamaz. 

Media Services iki tür Konumlandırıcı destekler: OnDemandOrigin Konumlandırıcı, medyayı akışa almak için kullanılır (örneğin, MPEG DASH, HLS veya Kesintisiz Akış) veya aşamalı olarak indirme medya ve SAS URL 'SI Konumlandırıcı, Azure depolama hizmetinden medya dosyalarını karşıya yüklemek veya indirmek için kullanılır. 

>[!NOTE]
>Bir OnDemandOrigin Bulucu oluşturulurken List izni (AccessPermissions. List) kullanılmamalıdır. 

### <a name="storage-account"></a>Depolama hesabı
Tüm Azure depolama erişimi bir depolama hesabı üzerinden yapılır. Bir medya hizmeti hesabı, bir veya daha fazla depolama hesabıyla ilişkilendirebilir. Toplam boyutu depolama hesabı başına 500 TB altında olduğu sürece bir hesap sınırsız sayıda kapsayıcı içerebilir.  Media Services, birden fazla depolama hesabını yönetmenize olanak tanımak için SDK düzeyi araçları sağlar ve bu hesaplara ölçümler ya da rastgele dağıtım temelinde yük dengeleyebilir. Daha fazla bilgi için bkz. [Azure depolama](/previous-versions/azure/dn767951(v=azure.100))ile çalışma. 

## <a name="jobs-and-tasks"></a>İşler ve görevler
Bir [iş](/rest/api/media/operations/job) , genellikle bir ses/video sunumu işlemek için kullanılır (örneğin, dizin veya kodlama). Birden çok video işediyorsanız, her videonun kodlanması için bir iş oluşturun.

Bir iş, gerçekleştirilecek işleme ilişkin meta veriler içerir. Her iş bir atomik işleme görevi, kendi giriş varlıkları, çıkış varlıkları, medya işlemcisi ve ilişkili ayarları belirten bir veya daha fazla [görev](/rest/api/media/operations/task)içerir. Bir iş içindeki görevler, bir görevin çıkış varlığı bir sonraki göreve giriş varlığı olarak verildiğinde birlikte zincirleme yapılabilir. Bu şekilde bir iş, bir medya sunusu için gereken tüm işlemleri içerebilir.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services, buluttaki medya kodlaması için birden çok seçenek sağlar.

Media Services ile Başlarken, codec bileşenleri ve dosya biçimleri arasındaki farkı anlamak önemlidir.
Codec bileşenleri, sıkıştırma/açma algoritmalarını uygulayan yazılımdır, ancak dosya biçimleri sıkıştırılmış videoyu tutan kapsayıcılardır.

Media Services, bu akış biçimlerine yeniden paketlemenize gerek kalmadan, uyarlamalı bit hızı MP4 veya Kesintisiz Akış kodlanmış içerikleri Media Services (MPEG DASH, HLS, Kesintisiz Akış) tarafından desteklenen akış biçimlerinde sunmanıza olanak tanıyan dinamik paketleme sağlar.

[Dinamik paketlemeden](media-services-dynamic-packaging-overview.md)yararlanmak için, Mezzanine (kaynak) dosyanızı bir uyarlamalı BIT hızı MP4 dosyası ya da Uyarlamalı bit hızı kesintisiz akış dosyaları olarak kodlamak ve başlatılmış durumda en az bir standart veya Premium akış uç noktası olması gerekir.

Media Services, bu makalede açıklanan aşağıdaki isteğe bağlı kodlayıcıyı destekler:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Desteklenen bu kodlayıcı hakkında daha fazla bilgi için bkz. [kodlayıcı](media-services-encode-asset.md).

## <a name="live-streaming"></a>Canlı Akış
Azure Media Services, kanal canlı akış içeriğini işlemek için bir işlem hattını temsil eder. Kanal, canlı giriş akışlarını iki şekilde alır:

* Şirket içi bir Live Encoder, kanala çoklu bit hızlı RTMP veya Kesintisiz Akış (parçalanmış MP4) gönderir. Çoklu bit hızı Kesintisiz Akış: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco ve elete çıkış yapan aşağıdaki canlı kodlayıcıları kullanabilirsiniz. Aşağıdaki Live kodlayıcılar çıktıyı RTMP: Adobe Flash Live Encoder, [Telestream kablolu dönüştürme](media-services-configure-wirecast-live-encoder.md), teradek, haivision kodlayıcıları. Alınan akışlar, başka bir dönüştürme ve kodlama olmadan kanalları geçer. İstendiğinde, Media Services akışı müşterilere teslim eder.
* Tek bit hızlı bir akış (aşağıdaki biçimlerden birinde: RTMP veya Kesintisiz Akış (parçalanmış MP4)) Media Services ile canlı kodlama gerçekleştirmek için etkinleştirilen kanala gönderilir. Ardından Kanal, gelen tek bit hızlı akışın çoklu bit hızlı (uyarlamalı) bir video akışına gerçek zamanlı kodlanmasını gerçekleştirir. İstendiğinde, Media Services akışı müşterilere teslim eder.

### <a name="channel"></a>Kanal
Media Services, [Kanal](/rest/api/media/operations/channel)s 'ler canlı akış içeriğini işlemeden sorumludur. Kanal, daha sonra canlı bir transcoder için sağladığınız bir giriş uç noktası (alma URL 'SI) sağlar. Kanal Canlı geçiş aşamasından canlı giriş akışları alır ve bir veya daha fazla StreamingEndpoints aracılığıyla akış için kullanılabilir hale getirir. Kanallar Ayrıca, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar.

Kanalı oluştururken alma URL 'sini ve önizleme URL 'sini alabilirsiniz. Bu URL 'Leri almak için kanalın başlatılmış durumda olması gerekmez. Canlı bir transleyici 'den kanala veri göndermeye başlamak için, kanalın başlatılmış olması gerekir. Canlı dönüştürücü verileri almaya başladıktan sonra, akışınızı önizleyebilirsiniz.

Her Media Services hesap birden çok kanal, birden çok program ve birden çok StreamingEndpoints içerebilir. Bant genişliği ve güvenlik ihtiyaçlarına bağlı olarak, StreamingEndpoint Hizmetleri bir veya daha fazla kanala ayrılabilir. Herhangi bir Streammingendpoint herhangi bir kanaldan çekme yapabilir.

### <a name="program-event"></a>Program (olay)
Bir [Program (olay)](/rest/api/media/operations/program) canlı bir akışta parçaların yayımlanmasını ve depolanmasını denetlemenize olanak sağlar. Kanalları yönetme programları (olaylar). Kanal ve program ilişkisi, bir kanalın sabit bir içerik akışına ve bir programın bu kanalda zaman aşımına uğramış bir olay kapsamına sahip olduğu geleneksel medyaya benzerdir.
**ArchiveWindowLength** özelliğini ayarlayarak program için kaydedilen içeriği bekletmek istediğiniz saat sayısını belirtebilirsiniz. Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir.

ArchiveWindowLength Ayrıca, istemcilerin geçerli canlı konumdan zaman içinde arayamayacak maksimum süreyi belirler. Olaylar belirtilen süre miktarından uzun sürebilir, ancak pencere uzunluğunun gerisine düşen içerik sürekli olarak atılır. Bu özelliğin bu değeri, istemci bildiriminin ne kadar uzayabileceğini de belirler.

Her program (olay) bir varlıkla ilişkilendirilir. Programı yayımlamak için ilişkili varlık için bir bulucu oluşturmanız gerekir. Bu bulucuya sahip olmak, istemcilerinize sağlayabileceğiniz bir akış URL’si oluşturmanıza olanak tanır.

Bir kanal eşzamanlı çalışan üçe kadar olayı destekler, böylece aynı gelen akışın birden fazla arşivini oluşturabilirsiniz. Bu özellik, gerektiğinde bir olayın farklı kısımlarını yayımlamanıza ve arşivlemenize olanak tanır. Örneğin, iş gereksiniminiz bir programın 6 saatini arşivlemek ancak son 10 dakikasını yayınlamak olabilir. Bunu yapmak için, eşzamanlı olarak çalışan iki program oluşturmanız gerekir. Bir program olayı 6 saat arşivlemek için ayarlanır ancak program yayımlanmaz. Diğer program 10 dakika arşivlenecek şekilde ve bu program yayımlanır.

Daha fazla bilgi için bkz.

* [Azure Media Services Live Encoding gerçekleştirmek üzere etkinleştirilen kanallarla çalışma](media-services-manage-live-encoder-enabled-channels.md)
* [Şirket İçi Kodlayıcılardan Çoklu Bit Hızlı Canlı Akış Alan Kanallar ile Çalışma](media-services-live-streaming-with-onprem-encoders.md)
* [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>İçerik koruma
### <a name="dynamic-encryption"></a>Dinamik şifreleme
Azure Media Services, medyanızın depolama, işleme ve teslim aracılığıyla bilgisayarınızdan ayrıldığı zamandan korunmasına olanak sağlar. Media Services içeriğinizi Gelişmiş Şifreleme Standardı (AES) ile (128 bit şifreleme anahtarlarını kullanarak) ve PlayReady ve/veya Widevine DRM kullanarak ortak şifreleme (CENC) ile şifreli olarak sunmanıza olanak tanır. Media Services Ayrıca, yetkili istemcilere yönelik AES anahtarları ve PlayReady lisanslarını sunmaya yönelik bir hizmet sağlar.

Şu anda şu akış biçimlerini şifreleyebilirsiniz: HLS, MPEG DASH ve Kesintisiz Akış. Aşamalı İndirmeleri şifrelenemez.

Media Services bir varlığı şifrelemesine istiyorsanız, bir şifreleme anahtarını (CommonEncryption veya EnvelopeEncryption) varlığınızla ilişkilendirmeniz ve ayrıca anahtar için yetkilendirme ilkeleri yapılandırmanız gerekir.

Depolama şifrelenmiş bir varlık akışı yapmak istiyorsanız, varlığınızı nasıl teslim etmek istediğinizi belirtmek için varlığın teslim ilkesini yapılandırmanız gerekir.

Bir Player tarafından bir akış istendiğinde, Media Services, bir zarf şifrelemesini (AES ile) veya ortak şifrelemeyi (PlayReady veya Widevine) kullanarak içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Akışın şifresini çözmek için, Player anahtar teslim hizmetinden anahtarı ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmeyeceğine karar vermek için, hizmet anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

### <a name="token-restriction"></a>Belirteç kısıtlaması
İçerik anahtarı Yetkilendirme ilkesinde bir veya daha fazla yetkilendirme kısıtlaması olabilir: açık, belirteç kısıtlaması veya IP kısıtlaması. Belirteç kısıtlamalı ilkenin beraberinde bir Güvenli Belirteç Hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services, Basit Web Belirteçleri (SWT) ve JSON Web Token (JWT) biçimlerindeki belirteçleri destekler. Media Services, güvenli belirteç Hizmetleri sağlamıyor. Özel bir STS oluşturabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Media Services anahtar teslim hizmeti, belirteç geçerliyse ve belirteçteki talepler anahtar (veya lisans) için yapılandırılananlarla eşleşiyorsa istemciye istenen anahtarı (veya lisansı) döndürür.

Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı, belirtecin imzalandığı anahtarı içerir ve veren, belirteci veren güvenli belirteç hizmetidir. Hedef kitle (bazen kapsam olarak adlandırılır) belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar teslim hizmeti, belirteçteki bu değerlerin şablondaki değerlerle eşleştiğini doğrular.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
- [İçeriğe karşı korumaya genel bakış](media-services-content-protection-overview.md)
- [AES ile koruma-128](media-services-playready-license-template-overview.md)
- [PlayReady/Widevine ile koruma](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Sunma
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dinamik paketleme
Media Services ile çalışırken, Mezzanine dosyalarını Uyarlamalı bit hızı olan bir MP4 kümesine kodlamak ve sonra [dinamik paketleme](media-services-dynamic-packaging-overview.md)kullanarak kümeyi istenen biçime dönüştürmeniz önerilir.

### <a name="streaming-endpoint"></a>Akış uç noktası
Streammingendpoint, bir istemci oynatıcı uygulamasına doğrudan içerik teslim edebilen bir akış hizmetini veya daha fazla dağıtım için bir Content Delivery Network (CDN) (Azure Media Services artık Azure CDN tümleştirmesini sağlar.) Akış uç noktası hizmetinden giden akış, canlı bir akış veya Media Services hesabınızda bir isteğe bağlı video varlığı olabilir. Media Services müşterileri ihtiyaçlarına göre **Standart** bir akış uç noktası veya bir veya daha fazla **Premium** akış uç noktası seçer. Standart akış uç noktası çoğu akış iş yükü için uygundur. 

Standart Akış Uç Noktası çoğu akış iş yükü için uygundur. Standart akış uç noktaları, içeriğinizi her cihaza dinamik paketleme, MPEG-DASH ve Kesintisiz Akış, Microsoft PlayReady, Google Widevine, Apple Fairplay ve AES128 için dinamik şifreleme aracılığıyla içeriğinizi sunma esnekliği sunar.  Ayrıca, Azure CDN tümleştirme aracılığıyla binlerce eşzamanlı izleyicilerle çok küçük ve çok büyük kitlelere ölçeklendirirler. Gelişmiş bir iş yükünüz varsa veya akış kapasitesi gereksinimleriniz standart akış uç noktası işleme hedeflerine sığmazsa veya artan bant genişliği ihtiyaçlarını işlemek üzere StreamingEndpoint hizmetinin kapasitesini denetlemek istiyorsanız, ölçek birimleri (Premium akış birimleri olarak da bilinir) ayırmanız önerilir.

Dinamik paketleme ve/veya dinamik şifrelemeyi kullanmanız önerilir.

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 

Daha fazla bilgi için [bu](media-services-portal-manage-streaming-endpoints.md) konu başlığına bakın.

Varsayılan olarak, Media Services hesabınızda en fazla 2 akış uç noktası olabilir. Daha yüksek bir sınır istemek için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).

Yalnızca StreamingEndpoint çalışır durumda olduğunda faturalandırılırsınız.

### <a name="asset-delivery-policy"></a>Varlık teslim ilkesi
Media Services içerik teslimi iş akışındaki adımlardan biri, akışını yapmak istediğiniz [varlıklar için teslim ilkelerini](/rest/api/media/operations/assetdeliverypolicy)yapılandırıyorsunuz. Varlık teslim ilkesi, varlığınızın nasıl sunulmasını istediğinizi Media Services söyler: varlığınızı dinamik olarak paketlemeniz gereken (örneğin, MPEG DASH, HLS, Kesintisiz Akış veya All), varlığınızı ve (zarf veya ortak şifreleme) nasıl (zarf veya ortak şifrelemeyi) bir şekilde şifrelemek isteyip istemediğiniz.

Depolama şifrelenmiş bir varlığınız varsa, varlığınızın akışı, depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini kullanarak içeriğinizi akışa alabilir. Örneğin, varlığınızı Gelişmiş Şifreleme Standardı (AES) şifreleme anahtarıyla şifreli olarak sunmak için, ilke türünü DynamicEnvelopeEncryption olarak ayarlayın. Depolama şifrelemesini kaldırmak ve varlığı açık olarak akışa almak için, ilke türünü NoDynamicEncryption olarak ayarlayın.

### <a name="progressive-download"></a>Aşamalı indirme
Aşamalı indirme, tüm dosya indirilmeden önce medya çalmaya başlayabilmeniz için izin verir. Bir MP4 dosyasını yalnızca aşamalı olarak indirebilirsiniz.

>[!NOTE]
>Bu kaynakların aşamalı indirme için kullanılabilir olmasını istiyorsanız şifrelenmiş varlıkların şifresini çözmeniz gerekir.

Kullanıcılara aşamalı indirme URL 'Leri sağlamak için, önce bir OnDemandOrigin Bulucu oluşturmanız gerekir. Bulucunun oluşturulması, size varlığın temel yolunu sağlar. Daha sonra MP4 dosyasının adını eklemeniz gerekir. Örnek:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Akış URL 'Leri
İçeriğinizi istemcilere akışa alma. Kullanıcılara akış URL 'Leri sağlamak için önce bir OnDemandOrigin Bulucu oluşturmanız gerekir. Bulucunun oluşturulması, size akışa almak istediğiniz içeriği içeren varlığın temel yolunu sağlar. Ancak, bu içeriği akışa almak için bu yolu daha fazla değiştirmeniz gerekir. Akış bildirim dosyasına tam URL oluşturmak için, Konumlandırıcı 'nın yol değerini ve bildirim (filename. ISM) dosya adını birleştirmeniz gerekir. Ardından, konum belirleyici yoluna/manifest ve uygun bir biçim (gerekliyse) ekleyin.

Ayrıca içeriğinizi bir TLS bağlantısı üzerinden de akışla aktarabilirsiniz. Bunu yapmak için, akış URL 'Lerinin HTTPS ile başlayıp başlamadığınızdan emin olun. Şu anda, AMS özel etki alanları ile TLS 'yi desteklemez.  

>[!NOTE]
>Yalnızca içeriğinizi teslim ettiğiniz akış uç noktası 10 Eylül 2014 ' den sonra oluşturulduysa TLS üzerinden akış yapabilirsiniz. Akış URL 'larınız, 10 Eylül 'tan sonra oluşturulan akış uç noktalarına dayıyorsa, URL "streaming.mediaservices.windows.net" (yeni biçim) içerir. "Origin.mediaservices.windows.net" (eski biçim) içeren akış URL 'Leri TLS 'yi desteklemez. URL 'niz eski biçimindeyse ve TLS üzerinden akış oluşturabilmek istiyorsanız yeni bir akış uç noktası oluşturun. İçeriğinizi TLS üzerinden akışa almak için yeni akış uç noktasına göre oluşturulan URL 'Leri kullanın.

Aşağıdaki listede farklı akış biçimleri açıklanmakta ve örnekler verilmiştir:

* Kesintisiz Akış

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest

* MPEG DASH

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = MPD-Time-CSF)

* Apple HTTP Canlı Akışı (HLS) v4

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = M3U8-AAPL)

* Apple HTTP Canlı Akışı (HLS) v3

{Akış uç noktası adı-Media Services hesap adı}. Stream. mediaservices. Windows. net/{Locator ID}/{filename}.exe (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46F6-490d-8B70-203e86b0df58/BigBuckBunny.ism/manifest (format = M3U8-AAPL-v3)

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
