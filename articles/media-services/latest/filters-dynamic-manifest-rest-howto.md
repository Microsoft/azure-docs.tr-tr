---
title: Filtre oluşturma ile Azure Media Services REST API | Microsoft Docs
description: Bu konuda, istemci akışı için bir stream'ın belirli bölümlerine kullanabilmesi için filtreler oluşturmayı açıklar. Media Services, bu seçmeli akış elde etmek için olan dinamik bildirimler oluşturur.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786447"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API'si ile filtre oluşturma

İçeriğinizi müşterilere (Canlı etkinlik veya isteğe bağlı Video akışı) sunarken istemcinizi varsayılan varlığın bildirim dosyasında tanımlanan değerinden daha fazla esneklik gerekebilir. Azure Media Services hesap filtreleri ve içeriğiniz için varlık filtrelerini tanımlamanızı sağlar. 

Bu özellik ve onu kullanıldığı senaryoların ayrıntılı açıklaması için bkz [dinamik bildirimlerini](filters-dynamic-manifest-overview.md) ve [filtreleri](filters-concept.md).

Bu konuda bir Video isteğe bağlı varlık için bir filtre tanımlar ve oluşturmak için REST API'lerini kullanma işlemi gösterilmektedir [hesap filtreleri](https://docs.microsoft.com/rest/api/media/accountfilters) ve [varlık filtreleri](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Gözden geçirdiğinizden emin olun [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Önkoşullar 

Bu konu başlığı altında açıklanan adımları tamamlamak için için gerekenler:

- Gözden geçirme [filtreleri ve dinamik bildirimlere](filters-dynamic-manifest-overview.md).
- [Azure Media Services REST API çağrıları için Postman yapılandırma](media-rest-apis-with-postman.md).

    Konunun son adımı takip edin [Azure AD belirteci Al](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Bir filtre tanımlar  

Aşağıdaki **istek gövdesi** bildirimine eklenmesini izleme seçimi koşullarını tanımlayan örnek. Bu filtre EC-3 olan tüm ses parçalarını ve 0-1000000 hızına sahip olan herhangi bir video parçaları içerir aralığı.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Hesap filtreleri oluşturma

İndirdiğiniz Postman'ın koleksiyonda seçin **hesap filtreleri**->**oluşturma veya güncelleştirme Hesap Filtresi**.

**PUT** HTTP istek yöntemine benzerdir:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Seçin **gövdesi** sekmesi ve Yapıştır json kodunu [daha önce tanımlanan](#define-a-filter).

**Gönder**’i seçin. 

Filtre oluşturuldu.

Daha fazla bilgi için [oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Ayrıca bkz [filtreleri için JSON örnekler](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Varlık filtre oluşturma  

İndirdiğiniz "Media Services v3" Postman koleksiyonu içinde seçin **varlıklar**->**oluşturma veya güncelleştirme varlık filtre**.

**PUT** HTTP istek yöntemine benzerdir:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Seçin **gövdesi** sekmesi ve Yapıştır json kodunu [daha önce tanımlanan](#define-a-filter).

**Gönder**’i seçin. 

Varlık filtre oluşturuldu.

Oluşturulacak veya güncelleştirilecek varlık filtreleri hakkında ayrıntılı bilgi için bkz. [oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Ayrıca bkz [filtreleri için JSON örnekler](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filtreler akış Bulucu ile ilişkilendirme

Bir akış Bulucu için uygulamak varlık veya hesap filtrelerin listesini belirtebilirsiniz. [Dinamik Paketleyici (akış uç noktası)](dynamic-packaging-overview.md) bu olanlar istemcinizin URL'SİNDE belirtir birlikte filtrelerinin listesi için geçerlidir. Bu birleşim oluşturur bir [dinamik bildirim](filters-dynamic-manifest-overview.md), URL'deki filtreleri + akış Bulucu üzerinde belirttiğiniz filtreleri temel. Filtre uygulamak istediğiniz, ancak URL filtresi adlarında kullanıma sunmak istiyorsanız değil, bu özelliği kullanmanızı öneririz.

Oluşturma ve filtreler bir akış REST kullanarak Bulucu ile ilişkilendirmek için kullanmak [akış bulucuları - oluşturma](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API belirtin `properties.filters` içinde [istek gövdesi](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream, filtrelerini kullanma

İstemcilerinize filtreleri tanımladıktan sonra bunları akış URL'SİNDE kullanabilirsiniz. Filtreler hızı Uyarlamalı akış için uygulanabilir: Apple HTTP canlı akış (HLS), MPEG-DASH ve kesintisiz akış.

Aşağıdaki tabloda, filtrelerle URL'leri bazı örnekler gösterilmektedir:

|Protocol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Sonraki adımlar

[Stream videoları](stream-files-tutorial-with-rest.md) 
