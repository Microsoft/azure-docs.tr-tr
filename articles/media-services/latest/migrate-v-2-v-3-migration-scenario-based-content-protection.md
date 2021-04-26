---
title: İçerik koruma geçiş kılavuzu
description: Bu makale, Azure Media Services V2 'den v3 'e geçiş yaparken size yardımcı olacak içerik koruma senaryosu tabanlı kılavuzunuzu sağlar.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490956"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Content Protection senaryo tabanlı geçiş kılavuzu

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-4.svg)

Bu makalede, v2 API 'sindeki içerik koruma kullanım örneklerinin yeni Azure Media Services v3 API 'sine geçirilmesine ilişkin ayrıntılar ve yönergeler sağlanmaktadır.

## <a name="protect-content-in-v3-api"></a>V3 API 'de içeriği koruma

Yeni v3 API 'sindeki [çok tuşlu](architecture-design-multi-drm-system.md) özellikler için desteği kullanın.

Belirli adımlar için bu makalenin sonundaki içerik koruma kavramları, öğreticiler ve nasıl yapılır kılavuzlarından bakın.

> [!NOTE]
> Bu makalenin geri kalanında v2 içerik korumanızı .NET ile v3 'e nasıl geçirebileceğiniz ele alınmaktadır.  Farklı bir dil veya yöntem için yönergelerin veya örnek kodun olması gerekiyorsa lütfen Bu sayfa için bir GitHub sorunu oluşturun.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v2 varlıklarının, Streamingkonumlandırıcı ve özellikler için v3 görünürlüğü

V2 API 'sinde,, `Assets` `StreamingLocators` ve `ContentKeys` akış içeriğinizi korumak için kullanılmıştır. V3 API 'sine geçiş yaparken, v2 API 'niz, `Assets` `StreamingLocators` ve `ContentKeys` her IKISI de v3 API 'sinde otomatik olarak kullanıma sunulmuştur ve bunlara erişebilirsiniz.

Ancak v2 varlıklarındaki özellikleri v2 'de oluşturulan v3 API aracılığıyla *güncelleştiremezsiniz* .

V2 varlıklarında depolanan içeriği güncelleştirmeniz, değiştirmeniz veya değiştirmeniz gerekiyorsa, bunları geçiş için v2 API 'siyle güncelleştirin veya yeni v3 API varlıkları oluşturun.

## <a name="asset-identifier-differences"></a>Varlık tanımlayıcı farklılıkları

Geçiş yapmak için, v2 varlıklarınızdan özelliklere veya içerik anahtarlarına erişmeniz gerekir.  V2 API 'sinin birincil kimlik anahtarı olarak kullandığını anlamak önemlidir, `AssetId` ancak yeni v3 API 'si, birincil tanımlayıcı olarak varlığın *Azure Kaynak Yönetimi adını* kullanır.  (V2 `Asset.Name` özelliği benzersiz bir tanımlayıcı olarak kullanılmaz.) V3 API 'SI ile v2 varlık adınız artık olarak görüntülenir `Asset.Description` .

Örneğin, daha önce KIMLIKLI bir v2 varlığınız varsa `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` , tanımlayıcı artık GUID 'in sonunda olur `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Bunu, v2 varlıklarınızı v3 API aracılığıyla listelerken görürsünüz.

V2 API kullanılarak oluşturulan ve yayınlanan varlıkların `ContentKeyPolicy` `ContentKey` , üzerinde varsayılan içerik anahtar ilkesi yerine v3 API 'sinde hem a hem de a 'sı olur `StreamingPolicy` .

Daha fazla bilgi için [içerik anahtarı ilkesi](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) belgelerine ve [akış ilkesi](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) belgelerine bakın.

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Azure Media Services Explorer (amo) v2 ve AMDE v3 araçlarını yan yana kullanın

V2 API 'Leri aracılığıyla oluşturulan ve yayınlanan bir varlık için verileri yan yana karşılaştırmak için, [v3 Azure Media Services Gezgini aracıyla](https://github.com/Azure/Azure-Media-Services-Explorer) birlikte [v2 Azure Media Services gezgin aracını](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) kullanın. Özelliklerin hepsi, farklı konumlarda görünür olmalıdır.

## <a name="use-the-net-content-protection-migration-sample"></a>.NET Content Protection geçiş örneğini kullanma

Media Services kodu örneklerindeki ContentProtection altında bulunan [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) kullanarak varlık tanımlayıcılardaki farklılıkları karşılaştırmak için bir kod örneği bulabilirsiniz.

## <a name="list-the-streaming-locators"></a>Akış Konumlandırıcı 'yı listeleyin

`StreamingLocators`Varlık varlığındaki Yeni v3 yöntemi [Liststreamingkonumlandırıcı](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) ' yı kullanarak v2 API 'sinde oluşturulan varlıklarla ilişkili bir sorgu oluşturabilirsiniz.  Ayrıca [Liststreaminglocatorsasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) öğesinin .NET istemci SDK sürümüne başvur

Yönteminin sonuçları, `ListStreamingLocators` `Name` `StreamingLocatorId` ile birlikte bulucunun ve ' i sağlar `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>İçerik anahtarlarını bulma

`ContentKeys`İle kullanılan öğesini bulmak Için `StreamingLocators` [Streaminglocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) yöntemini çağırabilirsiniz.  

V3 API 'sindeki içerik koruması hakkında daha fazla bilgi için, [Media Services dinamik şifreleme ile Içeriğinizi koruma](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept) makalesine bakın.

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>V2 ContentKeyPolicy öğesini aynı ContentKey değerini koruyarak değiştirme

İlk olarak, varlık üzerinde v2 SDK 'Sı aracılığıyla yayımdan kaldırmanız gerekir (tüm akış Bulıcıları kaldırmanız). Aşağıdaki adımları uygulayın:

1. Konumlandırıcı 'yi silin.
1. Bağlantısını kaldırın `ContentKeyAuthorizationPolicy` .
1. Bağlantısını kaldırın `AssetDeliveryPolicy` .
1. Bağlantısını kaldırın `ContentKey` .
1. Öğesini silin `ContentKey` .
1. V3 `StreamingLocator` `StreamingPolicy` ve `ContentKeyPolicy` belirli içerik anahtarı tanımlayıcısını ve gerekli anahtar değerini belirterek v3 'de yeni bir oluşturma.

> [!NOTE]
> V3 API kullanılarak v2 Konumlandırıcı silinebilir, ancak bu, v2 API 'sinde oluşturulduklarında içerik anahtarını veya içerik anahtarı ilkesini kaldırmaz.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>İçerik koruma kavramları, öğreticiler ve nasıl yapılır kılavuzlarından

### <a name="concepts"></a>Kavramlar

- [Media Services dinamik şifreleme ile içeriğinizi koruyun](drm-content-protection-concept.md)
- [Erişim denetimi ile çoklu DRM'ye sahip içerik koruma sistemi tasarlama](architecture-design-multi-drm-system.md)
- [PlayReady lisans şablonuyla v3 Media Services](drm-playready-license-template-concept.md)
- [Wıdevine lisans şablonuna genel bakış ile Media Services v3](drm-widevine-license-template-concept.md)
- [Apple FairPlay lisansı gereksinimleri ve yapılandırması](drm-fairplay-license-overview.md)
- [Akış Ilkeleri](stream-streaming-policy-concept.md)
- [İçerik anahtarı Ilkeleri](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Öğreticiler

[Hızlı başlangıç: içeriği şifrelemek için portalı kullanma](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Nasıl yapılır kılavuzları

- [Var olan ilkeden imzalama anahtarı alma](drm-get-content-key-policy-dotnet-how-to.md)
- [İOS için Media Services v3 ile çevrimdışı FairPlay Akışı](drm-offline-fairplay-for-ios-concept.md)
- [Android için Media Services v3 ile çevrimdışı Widevine akışı](drm-offline-widevine-for-android.md)
- [Media Services v3 ile Windows 10 için çevrimdışı PlayReady akışı](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Örnekler

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- Ayrıca [kod örneklerinde v2 ve v3 kodunu da karşılaştırabilirsiniz](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Araçlar

- [v3 Azure Media Services gezgin aracı](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services gezgin aracı](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
