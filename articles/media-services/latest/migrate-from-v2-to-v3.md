---
title: Azure Media Services V2 'den v3 'e geçiş | Microsoft Docs
description: Bu makalede, Azure Media Services v3 sürümünde tanıtılan ve iki sürüm arasındaki farklılıklar gösteren değişiklikler açıklanmaktadır. Makale Ayrıca Media Services V2 'den v3 'e geçiş için geçiş kılavuzu sağlar.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, akış, yayın, canlı, çevrimdışı
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: fb36387764efbdaa1ad3d164ba419bee49770871
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048996"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Media Services V2 'den v3 'e geçmek için geçiş kılavuzu

Bu makalede, Azure Media Services v3 sürümünde tanıtılan değişiklikler açıklanmakta, iki sürüm arasındaki farklılıklar gösterilmektedir ve geçiş kılavuzu sağlanmıştır.

Bugün [eski Media Services V2 API 'lerinde](../previous/media-services-overview.md)geliştirilen bir video hizmetiniz varsa, v3 API 'lerine geçiş yapmadan önce aşağıdaki yönergeleri ve konuları gözden geçirmeniz gerekir. V3 API 'de Media Services Geliştirici deneyimini ve yeteneklerini geliştiren birçok avantaj ve yeni özellik vardır. Ancak, bu makalenin [bilinen sorunlar](#known-issues) bölümünde ÇAĞRıLDıKÇA, API sürümleri arasındaki değişikliklerden dolayı bazı sınırlamalar de vardır. Media Services ekibi, v3 API 'Lerinde devam eden geliştirmeler yaptığında ve sürümler arasındaki boşlukları adresleyen Bu sayfa korunacak. 

> [!NOTE]
> Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) veya desteklenen [SDK'lardan](media-services-apis-overview.md#sdks) birini kullanın.

## <a name="benefits-of-media-services-v3"></a>Media Services v3 avantajları
  
### <a name="api-is-more-approachable"></a>API daha ulaşılabilir

*  v3, Azure Resource Manager'da yerleşik olan yönetim ve işlem işlevselliğini kullanıma sunan, birleşik bir API yüzeyini temel alır. Azure Resource Manager şablonlar, dönüşümler, akış uç noktaları, canlı olaylar ve daha fazlasını oluşturmak ve dağıtmak için kullanılabilir.
* [Openapı belirtimi (eski adı Swagger)](https://aka.ms/ams-v3-rest-sdk) belgesi.
    Dosya tabanlı kodlama da dahil olmak üzere tüm hizmet bileşenleri için şemayı gösterir.
* [.Net](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node. js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)ve Ruby için kullanılabilir SDK 'lar.
* Basit betik desteği için [Azure CLI](https://aka.ms/ams-v3-cli-ref) tümleştirmesi.

### <a name="new-features"></a>Yeni özellikler

* Dosya tabanlı Iş işleme için, giriş olarak bir HTTP (S) URL 'SI kullanabilirsiniz.<br/>Azure 'da zaten depolanmış içeriklere sahip olmanız veya varlık oluşturmanız gerekmez.
* Dosya tabanlı Iş işleme için [dönüşüm](transforms-jobs-concept.md) kavramını tanıtır. Bir dönüşüm yeniden kullanılabilir yapılandırma oluşturmak, Azure Resource Manager şablonları oluşturmak ve birden çok müşteri veya kiracı arasında işlem ayarlarını yalıtmak için kullanılabilir.
* Bir varlık, her biri farklı [dinamik paketleme](dynamic-packaging-overview.md) ve dinamik şifreleme ayarlarına sahip birden fazla [akış Konumlandırıcı](streaming-locators-concept.md) olabilir.
* [İçerik koruması](content-key-policy-concept.md) çoklu anahtar özelliklerini destekler.
* Tek bir bit hızlı katkı akışını birden çok bitme sahip bir çıkış akışına dönüştürmek için Media Services kullanırken, 24 saate kadar olan canlı olayları akışlara aktarabilirsiniz.
* Canlı olaylarda yeni düşük gecikmeli canlı akış desteği. Daha fazla bilgi için bkz. [gecikme](live-event-latency.md).
* Canlı etkinlik önizlemesi, [dinamik paketleme](dynamic-packaging-overview.md) ve dinamik şifrelemeyi destekler. Bu, önizleme sırasında içerik korumasının yanı sıra DASH ve HLS ambalajını de sunar.
* Canlı çıktı, v2 API 'Lerinde program varlığından daha basittir. 
* İyileştirilmiş RTMP desteği (artan kararlılık ve daha fazla kaynak Kodlayıcısı desteği).
* RTMPS güvenli alma.<br/>Canlı bir olay oluşturduğunuzda 4 alma URL 'Si alırsınız. 4 içe alınan URL 'Ler neredeyse aynıdır, aynı akış belirtecine (AppID) sahiptir, yalnızca bağlantı noktası numarası bölümü farklıdır. URL 'Lerden ikisi, RTMPS için birincil ve yedeğdir.   
* Varlıklarınız üzerinde rol tabanlı erişim denetimi (RBAC) vardır. 

## <a name="changes-from-v2"></a>V2 'deki değişiklikler

* V3 ile oluşturulan varlıklar için Media Services yalnızca [Azure Storage sunucu tarafı depolama şifrelemesini](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)destekler.
    * V3 API 'Leri, Media Services tarafından sağlanmış [depolama şifrelemesi](../previous/media-services-rest-storage-encryption.md) (AES 256) olan v2 API 'Leriyle oluşturulmuş varlıklarla birlikte kullanabilirsiniz.
    * V3 API 'Leri kullanarak eski AES 256 [depolama şifrelemesi](../previous/media-services-rest-storage-encryption.md) Ile yeni varlıklar oluşturamazsınız.
* [Varlıkların](assets-concept.md)v3 'deki özellikleri v2 'den farklıdır, bkz. [Özellikler nasıl eşlenir](assets-concept.md#map-v3-asset-properties-to-v2).
* V3 SDK 'Ları artık, kullanmak istediğiniz depolama SDK 'Sı üzerinde daha fazla denetim sağlayan ve sürüm oluşturma sorunlarını önleyip depolama SDK 'sının içine ayrılır. 
* V3 API 'Lerinde, tüm kodlama bit hızları saniyede bit/saniye cinsinden. Bu, v2 Media Encoder Standard önayarlarından farklıdır. Örneğin, v2 'deki bit hızı 128 (Kbps) olarak belirtilir, ancak v3 'de 128000 (bit/saniye) olur. 
* AssetFiles, AccessPolicies ve IngestManifests varlıkları v3 'te bulunmamaktadır.
* Ivarlık. ParentAssets özelliği v3 içinde yok.
* ContentKeys artık bir varlık değil, artık akış bulucunun bir özelliğidir.
* Event Grid desteği, NotificationEndpoints yerini alır.
* Aşağıdaki varlıklar yeniden adlandırıldı
    * İş çıkışı görevin yerini alır ve artık bir Işin parçasıdır.
    * Akış Bulucu Konumlandırıcı 'nın yerini alır.
    * Canlı olay kanal yerini alır.<br/>Canlı olaylar, canlı kanal ölçümlerine göre faturalandırılır. Daha fazla bilgi için bkz. [faturalandırma](live-event-states-billing.md) ve [fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/).
    * Canlı çıkış programın yerini alır.
* Canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur. Programlar v2 API 'Lerinde farklı çalıştık, oluşturulduktan sonra başlatılmaları gerekiyordu.
* Bir iş hakkında bilgi almak için işin altında oluşturulduğu dönüştürme adını bilmeniz gerekir. 
* V2 'de XML [giriş](../previous/media-services-input-metadata-schema.md) ve [Çıkış](../previous/media-services-output-metadata-schema.md) meta verileri dosyaları, bir kodlama işinin sonucu olarak üretilir. V3 'de meta veri biçimi XML olarak JSON olarak değiştirilmiştir. 

> [!NOTE]
> [Media Services v3 kaynaklarına](media-services-apis-overview.md#naming-conventions)uygulanan adlandırma kurallarını gözden geçirin. Ayrıca [adlandırma bloblarını](assets-concept.md#naming-blobs)gözden geçirin.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>V2 API 'Lerine göre Özellik boşlukları

V3 API 'si, v2 API 'sine göre aşağıdaki özellik boşluklarını içerir. Boşlukların kapatılması devam ediyor.

* [Premium kodlayıcı](../previous/media-services-premium-workflow-encoder-formats.md) ve eski [Medya analizi Işlemcileri](../previous/media-services-analytics-overview.md) (Azure Media Services Dizin Oluşturucu 2 Preview, yüz Redactor vb.) v3 aracılığıyla erişilemez.<br/>Media Indexer 1 veya 2 Preview 'dan geçiş yapmak isteyen müşteriler, v3 API 'sindeki AudioAnalyzer ön ayarını hemen kullanabilir.  Bu yeni önayar, eski Media Indexer 1 veya 2 ' den daha fazla özellik içeriyor. 
* V2 API 'lerinde [Media Encoder Standard gelişmiş özelliklerin](../previous/media-services-advanced-encoding-with-mes.md) birçoğu şu anda v3 'de bulunmamaktadır:
  
    * Kıymetlerin dikiş
    * Yer paylaşımları
    * Manın
    * Küçük resim Sprites
    * Girişte ses olmadığında sessiz ses izi ekleme
    * Girişte video yoksa video izlemesi ekleme
* Transkodlamaya sahip canlı olaylar şu anda kurşun ekleme orta akış ve ad işaretçisi ekleme işlemini API çağrısı aracılığıyla desteklemez. 

> [!NOTE]
> Lütfen bu makaleye yer işareti ekleyin ve güncelleştirmeleri kontrol edin.
 
## <a name="code-differences"></a>Kod farklılıkları

Aşağıdaki tabloda yaygın senaryolar için v2 ve v3 arasındaki kod farklılıkları gösterilmektedir.

|Senaryo|V2 APı 'SI|V3 APı 'SI|
|---|---|---|
|Bir varlık oluşturun ve bir dosyayı karşıya yükleyin |[v2 .NET örneği](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET örneği](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|İş gönder|[v2 .NET örneği](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET örneği](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>İlk olarak bir dönüştürme oluşturmayı ve sonra bir Işi göndermeyi gösterir.|
|AES şifrelemesi ile varlık yayımlama |1. ContentKeyAuthorizationPolicyOption oluşturun<br/>2. ContentKeyAuthorizationPolicy oluşturma<br/>3. AssetDeliveryPolicy oluşturma<br/>4. varlık oluşturma ve içerik yükleme veya işi gönderme ve çıkış varlığını kullanma<br/>5. AssetDeliveryPolicy 'i varlıkla ilişkilendir<br/>6. ContentKey oluştur<br/>7. varlığa ContentKey iliştirme<br/>8. AccessPolicy oluşturma<br/>9. Bulucu oluştur<br/><br/>[v2 .NET örneği](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Içerik anahtar Ilkesi oluştur<br/>2. varlık oluştur<br/>3. içeriği karşıya yükleyin veya varlığı Joi put olarak kullanın<br/>4. akış Bulucu oluşturma<br/><br/>[v3 .NET örneği](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|İş ayrıntılarını alma ve işleri yönetme |[V2 ile işleri yönetme](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3 ile işleri yönetme](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Bilinen sorunlar

* Şu anda, v3 kaynaklarını yönetmek için Azure portalını kullanamıyorsunuz. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI veya desteklenen SDK 'lardan birini kullanın.
* Işlerinizin eşzamanlılık ve performansını denetlemek için, özellikle video veya ses analizine sahip olanlar için hesabınıza medya ayrılmış birimleri (MRU) sağlamanız gerekir. Daha fazla bilgi için bkz. [Medya İşlemeyi Ölçeklendirme](../previous/media-services-scale-media-processing-overview.md). [Media Services v3 Için clı 2,0](media-reserved-units-cli-how-to.md)kullanarak, [Azure Portal](../previous/media-services-portal-scale-media-processing.md)veya [v2 API 'lerini](../previous/media-services-dotnet-encoding-units.md)kullanarak MRUs 'yi yönetebilirsiniz. Media Services v2 veya v3 API 'Leri kullanıp kullanmayacağınızı MRU sağlamanız gerekir.
* V3 API ile oluşturulan Media Services varlıkları v2 API 'SI tarafından yönetilemez.  
* V3 API 'leri aracılığıyla v2 API 'Leriyle oluşturulan varlıkların yönetilmesi önerilmez. Aşağıda, iki sürümündeki varlıkların uyumsuz hale getirilme farklılıklarının örnekleri verilmiştir:   
    * V2 'de oluşturulan işler ve görevler, bir dönüşümle ilişkilendirilmediği için v3 'de gösterilmez. Öneri, v3 dönüştürmelerini ve Işlerine geçiş yapmak. Geçiş sırasında değerlendirmedeki v2 işlerini izlemeye ihtiyaç duyan görece kısa bir zaman dilimi olacaktır.
    * V2 ile oluşturulan kanallar ve programlar (yani, canlı olaylar ve v3 'de canlı çıktılar ile eşlenir) v3 ile yönetilmeye devam edemez. Kullanışlı bir kanal durdurulduğunda v3 canlı olaylara ve canlı çıkışlara geçiş yapma önerisi.<br/>Şu anda, sürekli olarak çalışan kanalları geçiremezsiniz.  

> [!NOTE]
> Media Services ekibi, v3 API 'Lerinde devam eden geliştirmeler yaptığında ve sürümler arasındaki boşlukları adresleyen Bu sayfa korunacak.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

Video dosyalarını kodlamaya ve akışa almaya başlamanın ne kadar kolay olduğunu görmek için [Dosyaları akışa alma](stream-files-dotnet-quickstart.md) bölümüne göz atın. 

