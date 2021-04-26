---
title: Azure Media Services v3 .NET SDK ile Filtreler oluşturma
description: Bu konuda, istemcinizin bir akışın belirli bölümlerini akışa almak için bunları kullanabilmesi için filtrelerin nasıl oluşturulacağı açıklanmaktadır. Media Services v3 .NET SDK, bu seçmeli akışa ulaşmak için dinamik bildirimler oluşturuyor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 93634d8168d4870d6a97428ee538e465c6aad2cb
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112071"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Media Services .NET SDK ile filtre oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

İçeriğinizi müşterilere sunarken (canlı etkinlikler veya Isteğe bağlı video akışı), istemciniz varsayılan varlığın bildirim dosyasında açıklananlardan daha fazla esneklik gerektirebilir. Azure Media Services, içeriğiniz için hesap filtrelerini ve varlık filtrelerini tanımlamanızı sağlar.

Bu özelliğin ve kullanılan senaryoların ayrıntılı açıklaması için bkz. [dinamik bildirimler](filters-dynamic-manifest-concept.md) ve [Filtreler](filters-concept.md).

Bu konu başlığı altında, Isteğe bağlı bir video için filtre tanımlamak ve [hesap filtreleri](/dotnet/api/microsoft.azure.management.media.models.accountfilter) ve [varlık filtreleri](/dotnet/api/microsoft.azure.management.media.models.assetfilter)oluşturmak için Media Services .NET SDK 'nın nasıl kullanılacağı gösterilmektedir. 

> [!NOTE]
> [Presentationtimerange](filters-concept.md#presentationtimerange)öğesini gözden geçirdiğinizden emin olun.

## <a name="prerequisites"></a>Önkoşullar 

- [Filtreleri ve dinamik bildirimleri](filters-dynamic-manifest-concept.md)gözden geçirin.
- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını hatırlayacağınızdan emin olun. 
- [API 'lere erişmek](./access-api-howto.md) için gereken bilgileri alın
- [.NET SDK 'yı kullanmaya başlama](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk) hakkında bilgi için [Azure Media Services kullanarak karşıya yükleme, kodlama ve akışı](stream-files-tutorial-with-api.md) gözden geçirin

## <a name="define-a-filter"></a>Filtre tanımlama  

.NET ' te, izleme seçimlerini [Filtertrackselection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection) ve [Filtertrackpropertycondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition) sınıflarıyla birlikte yapılandırırsınız. 

Aşağıdaki kod, EC-3 olan herhangi bir ses parçasını ve 0-1000000 aralığında bit hızına sahip herhangi bir video parçasını içeren bir filtre tanımlar.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Hesap filtreleri oluşturma

Aşağıdaki kod, [yukarıda tanımlanan](#define-a-filter)tüm izleme seçimlerini içeren bir hesap filtresi oluşturmak için .net 'in nasıl kullanılacağını gösterir. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Varlık filtreleri oluşturma

Aşağıdaki kod, [yukarıda tanımlanan](#define-a-filter)tüm izleme seçimlerini içeren bir varlık filtresi oluşturmak için .net 'in nasıl kullanılacağını gösterir. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Filtreleri akış bulucu ile ilişkilendir

Akış Konumlayıcı için uygulanabilecek varlık veya hesap filtrelerinin bir listesini belirtebilirsiniz. [Dinamik Paketleyici (akış uç noktası)](encode-dynamic-packaging-concept.md) , bu filtre listesini ISTEMCINIZDEKI URL 'de belirttiği değişikliklerle birlikte uygular. Bu bileşim, akış Bulucu üzerinde belirlediğiniz URL + filtrelerdeki filtreleri temel alan [dinamik bir bildirim](filters-dynamic-manifest-concept.md)oluşturur. Filtre uygulamak, ancak URL 'de filtre adlarını göstermek istemiyorsanız bu özelliği kullanmanızı öneririz.

Aşağıdaki C# kodu, bir akış Bulucu oluşturmayı ve belirtmeyi gösterir `StreamingLocator.Filters` . Bu, filtre adlarından birini alan isteğe bağlı bir özelliktir `IList<string>` .

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Filtreleri kullanarak akış

Filtreleri tanımladıktan sonra, istemcileriniz bunları akış URL 'sinde kullanabilir. Filtreler, uyarlamalı bit hızı akış protokollerine uygulanabilir: Apple HTTP Canlı Akışı (HLS), MPEG-DASH ve Kesintisiz Akış.

Aşağıdaki tabloda, filtre içeren URL 'lerin bazı örnekleri gösterilmektedir:

|Protokol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Sonraki adımlar

[Video akışı](stream-files-tutorial-with-api.md) 
