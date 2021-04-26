---
title: Azure Media Services akış Konumlandırıcı
description: Bu makalede, akış bulucuların ne olduğu ve Azure Media Services tarafından nasıl kullanıldığı hakkında bir açıklama sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773288"
---
# <a name="streaming-locators"></a>Akış Bulucuları

Çıkış Varlığınızdaki videoların istemcilerde kayıttan yürütülebilmesi için bir [Akış Bulucu](/rest/api/media/streaminglocators) ve ardından akış URL'si oluşturmanız gerekir. URL oluşturmak için, akış uç noktası ana bilgisayar adını ve akış Bulucu yolunu birleştirmeniz gerekir. .NET örneği için bkz. [Akış Bulucu edinme](stream-files-tutorial-with-api.md#get-a-streaming-locator).

**Akış Bulucu** oluşturma işlemine yayımlama denir. Varsayılan olarak, **akış Bulucu** , API çağrılarını yaptıktan hemen sonra geçerli olur ve isteğe bağlı başlangıç ve bitiş zamanlarını yapılandırmadıkça silinene kadar sürer. 

Bir **akış Bulucu** oluştururken bir **varlık** adı ve **akış ilkesi** adı belirtmeniz gerekir. Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [Varlıklar](assets-concept.md)
* [Akış İlkeleri](stream-streaming-policy-concept.md)
* [İçerik Anahtarı İlkeleri](drm-content-key-policy-concept.md)

Ayrıca, yalnızca kullanıcının bu saatler arasında içerik oynamasını sağlayan akış bulucuda başlangıç ve bitiş saatini belirtebilirsiniz (örneğin, 5/1/2019 arasında 5/5/2019 arasında).  

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* **Akış Konumlandırıcı** güncelleştirilemez. 
* Tarih saat türünde **akış Konumlandırıcı** özellikleri her zaman UTC biçimindedir.
* Media Service hesabınız için sınırlı bir ilke kümesi tasarlamanızı ve aynı seçenek gerektiğinde bunları akış bulucular için yeniden kullanmanız gerekir. Daha fazla bilgi için bkz. [Kotalar ve sınırlar](limits-quotas-constraints-reference.md).

## <a name="create-streaming-locators"></a>Akış Konumlandırıcı oluşturma  

### <a name="not-encrypted"></a>Şifrelenmedi

Dosyanızı açık (şifrelenmemiş) olarak akışa almak istiyorsanız, önceden tanımlı açık akış ilkesini ayarlayın: ' Predefined_ClearStreamingOnly ' (.NET 'te PredefinedStreamingPolicy. ClearStreamingOnly enum) kullanabilirsiniz.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Şifreli 

İçeriğinizi CENC şifrelemesiyle şifrelemeniz gerekiyorsa, ilkenizi ' Predefined_MultiDrmCencStreaming ' olarak ayarlayın. Widevine şifrelemesi, bir DASH akışına ve PlayReady 'ın pürüzsüz olması için geçerlidir. Anahtar, yapılandırılmış DRM lisanslarına bağlı olarak bir kayıttan yürütme istemcisine gönderilir.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Ayrıca, HLS akışınızı CBCS (FairPlay) ile şifrelemek istiyorsanız ' Predefined_MultiDrmStreaming ' kullanın.

> [!NOTE]
> Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="associate-filters-with-streaming-locators"></a>Filtreleri akış Konumlandırıcı ile ilişkilendir

Bkz. [Filtreler: akış Bulleyicileri ile ilişkilendir](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtre, sipariş, sayfa akışı Bulucu varlıkları

Bkz. [Media Services varlıkların filtrelenmesi, sıralanması, sayfalama](filter-order-page-entitites-how-to.md).

## <a name="list-streaming-locators-by-asset-name"></a>Akış Konumlandırıcı 'yı varlık adına göre Listele

İlişkili varlık adına göre akış Konumlandırıcı almak için aşağıdaki işlemleri kullanın:

|Dil|API|
|---|---|
|REST|[liststreamingkonumlandırıcı](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az AMS varlık listesi-akış-Konumlandırıcı](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[Liststreamingkonumlandırıcı](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[Liststreamingkonumlandırıcı](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Ayrıca bkz.

* [Varlıklar](assets-concept.md)
* [Akış İlkeleri](stream-streaming-policy-concept.md)
* [İçerik Anahtarı İlkeleri](drm-content-key-policy-concept.md)
* [Öğretici: .NET’i kullanarak videoları karşıya yükleme, kodlama ve akışla aktarma](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Sonraki adımlar

[Akış bulucu ve derleme URL 'Leri oluşturma](create-streaming-locator-build-url.md)
