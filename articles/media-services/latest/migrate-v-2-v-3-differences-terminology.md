---
title: Media Services v3 terimleri ve varlık değişiklikleri
description: Bu makale, Azure Media Services V2 ile v3 arasındaki terminoloji farklarını açıklamaktadır.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 42c4c91b6715ffec4c734632c69623206bc6dbae
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076011"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Media Services V2 ve v3 arasındaki terminoloji ve varlık değişiklikleri

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-2.svg)

Bu makale, Azure Media Services V2 ile v3 arasındaki terminoloji farklarını açıklamaktadır.

## <a name="naming-conventions"></a>Adlandırma kuralları

Media Services v3 kaynaklarına uygulanan adlandırma kurallarını gözden geçirin. Ayrıca [adlandırma bloblarını](assets-concept.md#naming) gözden geçirin

## <a name="terminology-changes"></a>Terminoloji değişiklikleri

- Bir *Bulucu* artık *akış Bulucu* çağırdı.
- *Kanal* artık *canlı etkinlik* olarak adlandırılır.
- Bir *programa* artık *canlı çıkış* denir.
- Bir *görev* artık bir işin parçası olan *Joi put* olarak adlandırılır.

## <a name="entity-changes"></a>Varlık değişiklikleri
| **V2 varlığı**<!-- row --> | **V3 varlığı** | **Rehber** | **V3 'e erişilebilir** | **V3 tarafından güncelleştirildi** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Varlık `AccessPolicies` v3 'de yok. | Hayır | Hayır |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Yes | Yes |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Yes | Hayır |
| `AssetFile`<!-- row --> | <!-- empty --> |Varlık `AssetFiles` v3 'de yok. Karşıya yüklediğiniz dosyalar (depolama Blobları) hala dosyalar olarak kabul edilir.<br/><br/> Bunun yerine bir kapsayıcıdaki Blobları numaralandırmak için Azure depolama API 'Lerini kullanın. Bir işi olan dosyalara dönüşüm uygulamak için iki yol vardır:<br/><br/>Depolama alanına zaten yüklenmiş dosyalar: URI, bir depolama hesabındaki varlıklarda yapılacak iş varlık KIMLIĞINI içerir.<br/><br/>Dönüştürme ve iş işlemi sırasında yüklenecek dosyalar: varlık depolama alanında oluşturulur, bir SAS URL 'SI döndürülür, Dosyalar depoya yüklenir ve ardından dönüştürme dosyalara uygulanır. | Hayır | Hayır |
| `Channel`<!-- row --> | `LiveEvent` | Canlı olaylar, v2 API 'sindeki kanalları değiştirir. Birçok özelliği taşır ve canlı döküm, tek başına modu ve RTMPS alma desteği gibi daha yeni özelliklere sahiptir. <br/><br/>Bkz. [senaryo tabanlı canlı akış 'da canlı etkinlik](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Hayır | Hayır |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` Artık bir varlık değil, artık akış bulucunun bir özelliğidir.<br/><br/>  V3 'de, içerik anahtarı verileri `StreamingLocator` (çıkış şifrelemesi için) veya varlığın kendisi (istemci tarafı depolama şifrelemesi için) ile ilişkilendirilir. | Yes | Hayır |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Yes | Hayır |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` uygulamasına dahildir `ContentKeyPolicy` . | Yes | Hayır |
| `IngestManifest`<!-- row --> | <!-- empty --> | Varlık `IngestManifests` v3 'de yok. V3 'de dosya yükleme işlemi Azure Storage API 'sini içerir. Varlıklar önce oluşturulur ve ardından dosyalar ilişkili depolama kapsayıcısına yüklenir. Bunun yerine kullanılabilecek bir Azure depolama kapsayıcısına veri almanın birçok yolu vardır. `JobInputHttp` Ayrıca, istenirse verilen bir URL 'den bir iş girişi indirmesi için bir yol sağlar. | Hayır | Hayır |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Bunun yerine kullanılabilecek bir Azure depolama kapsayıcısına veri almanın birçok yolu vardır. `JobInputHttp` Ayrıca, istenirse verilen bir URL 'den bir iş girişi indirmesi için bir yol sağlar. | Hayır | Hayır |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Bunun yerine kullanılabilecek bir Azure depolama kapsayıcısına veri almanın birçok yolu vardır. `JobInputHttp` Ayrıca, istenirse verilen bir URL 'den bir iş girişi indirmesi için bir yol sağlar. | Hayır | Hayır |
| `Job`<!-- row --> | `Job` | `Transform`Oluşturmadan önce oluşturun `Job` . | Hayır | Hayır |
| `JobTemplate`<!-- row --> | `Transform` | `Transform`Bunun yerine bir kullanın. Bir dönüşüm, bir işten alınan ayrı bir varlıktır ve yeniden kullanılıyor. | Hayır | Hayır |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Yes | Hayır |
| `MediaProcessor`<!-- row --> | <!-- empty --> | `MediaProcessor`Adına göre kullanmak için ' i aramak yerine, bir dönüşüm tanımlarken istenen ön ayarı kullanın. Kullanılan ön ayar, iş sistemi tarafından kullanılan medya işlemcisini belirlemek için kullanılır. Bkz. [senaryo tabanlı kodlama](migrate-v-2-v-3-migration-scenario-based-encoding.md)içindeki kodlama konuları. <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | NA (v2 'de ReadOnly) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3 içindeki bildirimler Azure Event Grid aracılığıyla işlenir. , `NotificationEndpoint` Alınan Event Grid abonelik kaydıyla değiştirilmiştir (v2 'de `JobNotificationSubscription` Işin, `TaskNotificationSubscription` görevin ve telemetrisi tarafından işlendiği) yapılandırma için yapılandırmayı de Kapsüller `ComponentMonitoringSetting` . V2 telemetrisi, büyük Azure ekosisteminin geliştirmeleriyle uyum sağlamak için Azure Event Grid ile Azure Izleyici arasında bölündü. | Hayır | Hayır |
| `Program`<!-- row --> | `LiveOutput` | Canlı çıktılar artık v3 API 'sindeki programları değiştirir.  | Hayır | Hayır |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Akış uç noktaları, birincil olarak aynı kalır. Bunlar, doğrudan kaynaktan veya CDN aracılığıyla hem canlı hem de isteğe bağlı akış için HLS ve DASH içeriğinin dinamik paketleme, şifreleme ve teslim edilmesi için kullanılır. Yeni özellikler daha iyi Azure Izleyici tümleştirmesi ve grafik oluşturma desteği içerir. |  Yes | Yes |
| `Task`<!-- row --> | `JobOutput` | İle değiştirilmiştir `JobOutput` (API 'de artık ayrı bir varlık değildir).  Bkz. [senaryo tabanlı kodlama](migrate-v-2-v-3-migration-scenario-based-encoding.md)içindeki kodlama konuları. | Hayır | Hayır |
| `TaskTemplate`<!-- row --> | `TransformOutput` | İle değiştirilmiştir `TransformOutput` (API 'de artık ayrı bir varlık değildir). Bkz. [senaryo tabanlı kodlama](migrate-v-2-v-3-migration-scenario-based-encoding.md)içindeki kodlama konuları. | Hayır | Hayır |
| `Inputs`<!-- row --> | `Inputs` | Girişler ve çıktılar artık Iş düzeyindedir. Bkz. [senaryo tabanlı kodlamada](migrate-v-2-v-3-migration-scenario-based-encoding.md) kodlama konuları | Hayır | Hayır |
| `Outputs`<!-- row --> | `Outputs` | Girişler ve çıktılar artık Iş düzeyindedir.  V3 'de meta veri biçimi XML olarak JSON olarak değiştirilmiştir.  Canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur. Bkz. [senaryo tabanlı kodlamada](migrate-v-2-v-3-migration-scenario-based-encoding.md) kodlama konuları | Hayır | Hayır |


| **Diğer değişiklikler** | **V2**  | **Yüklemesinde** |
|---|---|---|
| **Depolama** <!--new row --> |||
| Depolama <!--new row --> | | V3 SDK 'Ları artık, kullanmak istediğiniz depolama SDK 'Sı üzerinde daha fazla denetim sağlayan ve sürüm oluşturma sorunlarını önleyip depolama SDK 'sının içine ayrılır.                      |
| **Kodlama** <!--new row --> |||
| Kodlama bit hızları <!--new row --> | kbps EX cinsinden ölçülen bit hızları: 128 (Kbps)| saniyedeki bit/sn: 128000 (bit/saniye)|
| DRM FairPlay kodlaması <!--new row --> | Media Services V2 'de, başlatma vektörü (IV) belirtilebilir. | Media Services v3 'de, FairPlay IV belirtilemez.|
| Premium kodlayıcı <!--new row --> | Premium Kodlayıcısı ve eski Dizin Oluşturucu| [Premium kodlayıcı](../previous/media-services-encode-asset.md) ve eski [Medya analizi Işlemcileri](../previous/legacy-components.md) (Azure Media Services Dizin Oluşturucu 2 Preview, yüz Redactor vb.) v3 aracılığıyla erişilemez. Standart kodlayıcıya ses kanalı eşleme desteği ekledik.  [Media Services kodlaması Swagger belgelerindeki ses '](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)i inceleyin.  <br/> Bkz. [senaryo tabanlı kodlamada](migrate-v-2-v-3-migration-scenario-based-encoding.md) kodlama konuları |
| **Dönüşümler ve işler** <!--new row -->|||
| İş tabanlı işlem HTTPS <!--new row --> |<!-- empty -->| Dosya tabanlı Iş işleme için, giriş olarak bir HTTPS URL 'SI kullanabilirsiniz. Azure 'da zaten depolanmış içeriklere sahip olmanız veya varlık oluşturmanız gerekmez. |
| İşler için ARM şablonları <!--new row --> | V2 'de ARM şablonları yoktu. | Bir dönüşüm yeniden kullanılabilir yapılandırma oluşturmak, Azure Resource Manager şablonları oluşturmak ve birden çok müşteri veya kiracı arasında işlem ayarlarını yalıtmak için kullanılabilir. |
| **Canlı olaylar** <!--new row --> |||
| Akış uç noktası <!--new row --> | Akış uç noktası, içeriği doğrudan bir istemci oynatıcı uygulamasına veya daha fazla dağıtım için bir Content Delivery Network (CDN) teslim edebilen bir akış hizmetini temsil eder. | Akış uç noktaları, birincil olarak aynı kalır. Bunlar, doğrudan kaynaktan veya CDN aracılığıyla hem canlı hem de isteğe bağlı akış için HLS ve DASH içeriğinin dinamik paketleme, şifreleme ve teslim edilmesi için kullanılır.  Yeni özellikler daha iyi Azure Izleyici tümleştirmesi ve grafik oluşturma desteği içerir. |
| Canlı olay kanalları <!--new row --> | Kanallar canlı akış içeriğini işlemekten sorumludur. Kanal, daha sonra canlı bir transcoder için sağladığınız bir giriş uç noktası (alma URL 'SI) sağlar. Kanal Canlı geçiş aşamasından canlı giriş akışları alır ve bir veya daha fazla akış uç noktası aracılığıyla akış için kullanılabilir hale getirir. Kanallar Ayrıca, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar.| Canlı olaylar, v2 API 'sindeki kanalları değiştirir. Birçok özelliği taşır ve canlı döküm, tek başına modu ve RTMPS alma desteği gibi daha yeni özelliklere sahiptir. |
| Canlı olay programları <!--new row --> | Program, canlı akıştaki segmentlerin yayımlanması ve depolanmasını denetlemenizi sağlar. Kanallar, Programları yönetir. Kanal ve program ilişkisi, bir kanalın sabit bir içerik akışına ve bir programın bu kanalda zaman aşımına uğramış bir olay kapsamına sahip olduğu geleneksel medyaya benzerdir. Özelliği ayarlayarak program için kaydedilen içeriği tutmak istediğiniz saat sayısını belirtebilirsiniz `ArchiveWindowLength` . Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir.| Canlı çıktılar artık v3 API 'sindeki programları değiştirir. |
| Canlı etkinlik uzunluğu <!--new row --> |<!-- empty -->| Tek bir bit hızlı katkı akışını birden çok bitme sahip bir çıkış akışına dönüştürmek için Media Services kullanırken 24/7 canlı olaylarını akışla aktarabilirsiniz.|
| Canlı etkinlik gecikmesi <!--new row --> |<!-- empty -->| Canlı olaylarda yeni düşük gecikmeli canlı akış desteği. |
| Canlı etkinlik önizlemesi <!--new row --> |<!-- empty -->| Canlı etkinlik önizlemesi, dinamik paketleme ve dinamik şifrelemeyi destekler. Bu, önizleme sırasında içerik korumasının yanı sıra DASH ve HLS ambalajını de sunar. |
| Canlı etkinlik RTMPS <!--new row --> |<!-- empty-->| Daha fazla kararlılık ve daha fazla kaynak Kodlayıcısı desteğiyle geliştirilmiş RTMPS desteği. |
| Canlı olay RTMPS güvenli alma <!--new row --> | | Canlı bir olay oluşturduğunuzda 4 alma URL 'Si alırsınız. 4 içe alınan URL 'Ler neredeyse aynıdır, aynı akış belirtecine sahiptir `AppId` , yalnızca bağlantı noktası numarası kısmı farklıdır. URL 'Lerden ikisi, RTMPS için birincil ve yedeğdir.| 
| Canlı etkinlik dökümü <!--new row --> |<!-- empty--> | Azure Medya hizmeti, farklı protokollerde video, ses ve metin sağlar. MPEG-DASH veya HLS/CMAF kullanarak canlı akışınızı yayımladığınızda ve video ve sesle birlikte hizmetimiz, IMSC 1.1 uyumlu TTML 'de el ile metin sağlar.|
| Canlı olay bekleme modu <!--new row --> | V2 için bekleme modu yoktu. | Tek başına modu, canlı olayların etkin havuzlarının yönetilmesine yardımcı olan yeni bir v3 özelliğidir. Müşteriler artık canlı bir etkinliği, çalışma durumuna geçmeden önce daha düşük maliyetli bir şekilde başlatabilir. Bu, kanal başlangıç zamanlarını geliştirir ve daha hızlı başlangıç havuzları için işletim sıcak havuzların maliyetlerini azaltır. |
| Canlı etkinlik Faturalaması <!--new row --> | <!-- empty-->| Canlı olaylar, canlı kanal ölçümlerine göre faturalandırılır. |
| Canlı çıktılar <!--new row --> | Programın oluşturulduktan sonra başlatılması gerekiyordu. | Canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur. |
