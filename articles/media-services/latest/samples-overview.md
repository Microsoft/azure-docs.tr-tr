---
title: Media Services v3 örnekleri
description: Bu makale, yöntem ve SDK tarafından düzenlenen Media Services v3 için kullanılabilen tüm örneklerin bir listesini içerir.  Örnekler arasında .NET, Node.JS, Python ve Java ve Postman ile REST bulunur.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1d827d734c434204ff6b7ec60d27e507ae626abd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227696"
---
# <a name="media-services-v3-samples"></a>Media Services v3 örnekleri

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makale, yöntem ve SDK tarafından düzenlenen Media Services için kullanılabilen tüm örneklerin bir listesini içerir.  Örnekler arasında .NET, Node.JS, Python ve Java ve Postman ile REST bulunur.

## <a name="rest-postman-collection"></a>REST Postman koleksiyonu

[Rest Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) örnekleri bir Postman ortamı ve Postman istemcisine içeri aktarmanız için bir koleksiyon içerir.

## <a name="samples-by-sdk"></a>SDK 'ya göre örnekler

Sekmelerin her birinde Aradığınız örneklere ilişkin açıklama ve bağlantıları bulacaksınız.

## <a name="net"></a>[.NET](#tab/net/)

| Klasör | Description |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Yerleşik bir ön ayar ve HTTP URL 'SI girişi kullanarak bir işi gönderme, akış için çıkış varlığını yayımlama ve doğrulama için sonuçları indirme.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Özel bir H. IBU kodlama ön ayarı ve HTTP URL girişi kullanarak bir işi gönderme, akış için çıkış varlığını yayımlama ve doğrulama için sonuçları indirme.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Özel bir HEVC Encoding ön ayarı ve HTTP URL girişi kullanarak bir işi gönderme, akış için çıkış varlığını yayımlama ve doğrulama için sonuçları indirme.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Başlangıç veya bitiş zamanına göre kırpılabilir varlıklar içeren 2 veya daha fazla varlık birleştirmek için Jobınputsequence kullanarak bir işi gönderme. Elde edilen kodlanmış dosya, tüm varlıkların birlikte bulunduğu tek bir videodır.  Örnek ayrıca çıkış için çıktı varlığını yayımlayacak ve doğrulama için sonuçları indirirler.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Küçük resim sprite ve HTTP URL girişi ile özel bir ön ayar kullanarak bir işi gönderme, akış için çıkış varlığını yayımlama ve doğrulama için sonuçları indirme.|
| [Canlı/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|En fazla 25 saat ve varlık üzerinde 5 dakikalık DVR penceresinde bir filtre içeren bir tam arşiv ile LiveEvent oluşturma. Akış için bir bulucu oluşturmak üzere bir filtre kullanma.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Video çözümleyici dönüşümü oluşturma, bir video dosyasını giriş varlığına yükleme, dönüştürme ile bir iş gönderme ve sonuçları doğrulama için indirme.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Bir ses çözümleyici dönüşümü oluşturma, bir medya dosyasını bir giriş varlığına yükleme, dönüştürme ile bir iş gönderme ve sonuçları doğrulama için indirme.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Yerleşik uyarlamalı akış ön ayarıyla bir dönüşüm oluşturma, bir iş gönderme, gizli anahtar kullanarak ContentKeyPolicy oluşturma, ContentKeyPolicy 'i StreamingLocator ile ilişkilendirme, bir belirteç alma ve Azure Media Player kayıttan yürütmeye yönelik bir URL yazdırma. Bir Player tarafından bir akış istendiğinde, Media Services,, içeriğinizi AES-128 ile dinamik olarak şifrelemek için belirtilen anahtarı kullanır ve Azure Media Player şifresi çözmek için belirteci kullanır.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Yerleşik uyarlamalı akış ön ayarıyla bir dönüşüm oluşturma, bir iş gönderme, bir gizli anahtar kullanarak Widevine yapılandırmasıyla ContentKeyPolicy oluşturma, ContentKeyPolicy ile StreamingLocator ile ilişkilendirme, bir belirteç alma ve Widevine yürütücüsü içinde kayıttan yürütme için URL yazdırma. Bir Kullanıcı Widevine korumalı içerik istediğinde, oynatıcı uygulaması Media Services lisans hizmetinden bir lisans ister. Oynatıcı uygulaması yetkiliyse Media Services lisans hizmeti tarafından oynatıcıya bir lisans verilir. Widevine lisansı, içerik şifresini çözmek ve içeriğin akışını yapmak için istemci yürütücüsü tarafından kullanılabilen şifre çözme anahtarını içerir.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Yerleşik uyarlamalı akış ön ayarıyla bir dönüşüm oluşturma, bir iş gönderme, bir gizli anahtar kullanarak PlayReady yapılandırmasıyla bir ContentKeyPolicy oluşturma, ContentKeyPolicy ile StreamingLocator ile ilişkilendirme, bir belirteç alma ve Azure Media Player kayıttan yürütme için URL yazdırma. Bir Kullanıcı PlayReady korumalı içerik istediğinde, oynatıcı uygulaması Media Services lisans hizmetinden bir lisans ister. Oynatıcı uygulaması yetkiliyse Media Services lisans hizmeti tarafından oynatıcıya bir lisans verilir. PlayReady lisansı, içerik şifresini çözmek ve içeriğin akışını yapmak için istemci yürütücüsü tarafından kullanılabilen şifre çözme anahtarını içerir.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|İçeriğinizi PlayReady ve Widevine DRM ile dinamik olarak şifreleyin ve lisans hizmetinden lisans istemeden içeriği yürütün. Yerleşik uyarlamalı akış ön ayarıyla bir dönüştürme oluşturmayı, bir işi göndermeyi, açık kısıtlama ve PlayReady/Widevine kalıcı yapılandırma ile ContentKeyPolicy oluşturmayı, ContentKeyPolicy 'yi StreamingLocator ile ilişkilendirmeyi ve kayıttan yürütme için bir URL yazdırmayı gösterir.|
| [Akış/Assetfiltreleri](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Yerleşik uyarlamalı akış ön ayarıyla bir dönüşüm oluşturma, bir iş gönderme, bir varlık filtresi oluşturma ve bir hesap filtresi oluşturma, filtreleri akış bulleyicileri ile ilişkilendirme ve kayıttan yürütme için yazdırma URL 'leri.|
| [Akış/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Yerleşik uyarlanabilir akış ön ayarıyla bir dönüşüm oluşturma, iş gönderme, HLS ve DASH akışı için çıkış varlığını yayımlama.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | İsteğe bağlı kodlama veya analiz kullanan bir üretim sistemine yönelik rehberlik ve en iyi uygulamalar. Okuyucular [Media Services ve VOD Ile yüksek kullanılabilirliğe](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept)sahip yardımcı makale ile başlamalıdır. [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) örneği için sunulan ayrı bir çözüm dosyası vardır. |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Klasör|Description|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Varlıkları bağlama ve listeleme |
|[Canlı](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Temel canlı akış nasıl yapılır? **Uyarı**, canlı kullanırken tüm kaynakların temizlendiğinden ve portalda faturalandırılıcağından emin olun.|
|[Streamfilesörneği](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Kaynak URL 'den yerel bir dosya veya kodlama yükleme, içerik indirmek için depolama SDK 'sını kullanma ve bir oyuncunun akışını yapma. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Widevine ve PlayReady DRM kullanarak kodlama ve akış yapma |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Video ve ses çözümleyici ön ayarlarını kullanarak video veya ses dosyasından meta veriler ve Öngörüler oluşturun. |

## <a name="python"></a>[Python](#tab/python)

Şu anda bir Python örneği, [Python Ile temel kodlama](https://github.com/Azure-Samples/media-services-v3-python)vardır.

## <a name="java"></a>[Java](#tab/java)

|Klasör|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Medya dosyasında ses çözümleme. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|İçeriğinizi AES-128 ile dinamik olarak şifreleyin.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|İçeriğinizi PlayReady DRM ile dinamik olarak şifreleme.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Widevine DRM ile içeriğinizi dinamik olarak şifreleme.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|İçeriğinizi FairPlay DRM ile dinamik olarak şifreleme ve lisans hizmetinden lisans istemeden içerik yürütme.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|İçeriğinizi PlayReady ve Widevine DRM ile dinamik olarak şifreleyin ve lisans hizmetinden lisans istemeden içeriği yürütün.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Varlık ve hesap filtrelerini kullanarak içeriği filtreleme.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|VOD içeriğini akış için dinamik olarak paketleme/TIRE.|
|[Liveınest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|En fazla 25 saat ve varlık üzerinde 5 dakikalık DVR penceresi bulunan bir filtre ve akış için bir bulucu oluşturma ve filtreyi kullanma hakkında tam bir arşiv ile canlı bir olay oluşturma.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|En fazla 25 saat ve varlık üzerinde 5 dakikalık DVR penceresi bulunan bir filtre ve akış için bir bulucu oluşturma ve filtreyi kullanma hakkında tam bir arşiv ile canlı bir olay oluşturma.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Standardencoderönayar ayarlarını kullanarak özel bir kodlama dönüşümü oluşturma.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Yerleşik bir ön ayar ve HTTP URL 'SI girişi kullanarak bir işi gönderme, akış için çıkış varlığını yayımlama ve doğrulama için sonuçları indirme.|

---
