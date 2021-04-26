---
title: Azure Media Services .NET SDK ile Filtre Oluşturma
description: Bu konuda, istemcinizin bir akışın belirli bölümlerini akışa almak için bunları kullanabilmesi için filtrelerin nasıl oluşturulacağı açıklanmaktadır. Media Services .NET SDK, bu seçmeli akışa ulaşmak için dinamik bildirimler oluşturuyor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin
ms.custom: devx-track-csharp
ms.openlocfilehash: bd5435f7a2969c486042c9447a0fffbb745229f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014120"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Media Services .NET SDK ile Filtreler oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

2,17 sürümünden itibaren, Media Services varlıklarınız için filtreler tanımlamanızı sağlar. Bu filtreler, müşterilerinizin, bir videonun yalnızca bir bölümünü kayıttan yürütme (videonun tamamını çalmak yerine) veya yalnızca müşterinizin cihazının işleyebileceği ses ve video çevirilerinin bir alt kümesini (varlıkla ilişkili tüm yorumlamalar yerine) belirtmek için bir sunucu tarafı kurallarıdır. Varlıklarınızın bu şekilde filtrelenmesi, müşterinizin, belirtilen filtreye göre video akışı isteği üzerine oluşturulan **dinamik bildirimle** gerçekleştirilir.

Filtreler ve dinamik bildirimle ilgili daha ayrıntılı bilgi için bkz. [dinamik bildirimlere genel bakış](media-services-dynamic-manifest-overview.md).

Bu makalede, filtre oluşturmak, güncelleştirmek ve silmek için .NET SDK Media Services nasıl kullanılacağı gösterilmektedir. 

Note bir filtreyi güncelleştirirseniz akış uç noktasının kuralların yenilenmesi için iki dakika sürebilir. İçerik bu filtre kullanılarak sunulduysa (ve proxy 'lerde ve CDN önbelleklerde önbelleğe alınmışsa), bu filtrenin güncelleştirilmesi oynatıcı hatalara neden olabilir. Filtreyi güncelleştirdikten sonra her zaman önbelleğin işaretini kaldırın. Bu seçenek mümkün değilse, farklı bir filtre kullanmayı düşünün. 

## <a name="types-used-to-create-filters"></a>Filtre oluşturmak için kullanılan türler
Şu türler, filtre oluştururken kullanılır: 

* **Imingfilter**.  Bu tür, aşağıdaki REST API [filtreye](/rest/api/media/operations/filter) dayalıdır
* **Imingassetfilter**. Bu tür, aşağıdaki REST API [Assetfilter](/rest/api/media/operations/assetfilter) öğesine dayalıdır
* **Presentationtimerange**. Bu tür, şu REST API [Presentationtimerange](/rest/api/media/operations/presentationtimerange) öğesine dayalıdır
* **Filtertrackselectdeyimin** ve **Ifiltertrackpropertycondition**. Bu türler, şu REST API 'Lerine dayalıdır [Filtertrackselect ve FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Genel Filtreleri oluştur/güncelleştir/oku/Sil
Aşağıdaki kod, varlık filtrelerini oluşturmak, güncelleştirmek, okumak ve silmek için .NET 'in nasıl kullanılacağını gösterir.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Varlık filtrelerini oluştur/güncelleştir/oku/Sil
Aşağıdaki kod, varlık filtrelerini oluşturmak, güncelleştirmek, okumak ve silmek için .NET 'in nasıl kullanılacağını gösterir.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Filtreler kullanan akış URL 'Leri oluşturma
Varlıklarınızı yayımlama ve teslim etme hakkında daha fazla bilgi için bkz. [müşterilere Içerik sunma genel bakış](media-services-deliver-content-overview.md).

Aşağıdaki örneklerde, akış URL 'Lerine filtrelerin nasıl ekleneceği gösterilmektedir.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Canlı Akışı (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Canlı Akışı (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Kesintisiz Akış**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Dinamik bildirimlere genel bakış](media-services-dynamic-manifest-overview.md)
