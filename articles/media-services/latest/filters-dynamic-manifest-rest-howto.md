---
title: Azure Media Services v3 REST API ile Filtreler oluşturma
description: Bu konuda, istemcinizin bir akışın belirli bölümlerini akışa almak için bunları kullanabilmesi için filtrelerin nasıl oluşturulacağı açıklanmaktadır. Media Services v3 REST API, bu seçmeli akışa ulaşmak için dinamik bildirimler oluşturuyor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c2d081ded07b1d32ee7525855c1756e13dfd57aa
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277513"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API ile Filtreler oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

İçeriğinizi müşterilere sunarken (canlı etkinlikler veya Isteğe bağlı video akışı), istemciniz varsayılan varlığın bildirim dosyasında açıklananlardan daha fazla esneklik gerektirebilir. Azure Media Services, içeriğiniz için hesap filtrelerini ve varlık filtrelerini tanımlamanızı sağlar. 

Bu özelliğin ve kullanılan senaryoların ayrıntılı açıklaması için bkz. [dinamik bildirimler](filters-dynamic-manifest-concept.md) ve [Filtreler](filters-concept.md).

Bu konu, Isteğe bağlı bir video için bir filtre tanımlanacağını ve [hesap filtreleri](/rest/api/media/accountfilters) ve [varlık FILTRELERI](/rest/api/media/assetfilters)oluşturmak için REST API 'lerini nasıl kullanacağınızı gösterir. 

> [!NOTE]
> [Presentationtimerange](filters-concept.md#presentationtimerange)öğesini gözden geçirdiğinizden emin olun.

## <a name="prerequisites"></a>Önkoşullar 

Bu konuda açıklanan adımları tamamlayabilmeniz için şunları yapmanız gerekir:

- [Filtreleri ve dinamik bildirimleri](filters-dynamic-manifest-concept.md)gözden geçirin.
- [Azure Media Services REST API'si çağrıları Için Postman 'ı yapılandırın](setup-postman-rest-how-to.md).

    [Azure AD belirtecini al](setup-postman-rest-how-to.md#get-azure-ad-token)konusunun son adımını izlediğinizden emin olun. 

## <a name="define-a-filter"></a>Filtre tanımlama  

Aşağıda, bildirime eklenen izleme seçim koşullarını tanımlayan **İstek gövdesi** örneği verilmiştir. Bu filtre, EC-3 olan tüm ses izlerini ve 0-1000000 aralığında bit hızına sahip video izlemelerini içerir.

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

İndirdiğiniz Postman 'ın koleksiyonunda **hesap filtreleri** -> **Hesap Filtresi Oluştur veya Güncelleştir**' i seçin.

**PUT** http istek yöntemi şuna benzerdir:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

**Gövde** sekmesini seçin ve [daha önce tanımladığınız](#define-a-filter)JSON kodunu yapıştırın.

**Gönder**’i seçin. 

Filtre oluşturuldu.

Daha fazla bilgi için bkz. [oluşturma veya güncelleştirme](/rest/api/media/accountfilters/createorupdate). Ayrıca bkz. [Filtreler Için JSON örnekleri](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Varlık filtreleri oluşturma  

İndirdiğiniz "Media Services v3" Postman koleksiyonunda  -> **varlık Filtresi Oluştur veya Güncelleştir**' i seçin.

**PUT** http istek yöntemi şuna benzerdir:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

**Gövde** sekmesini seçin ve [daha önce tanımladığınız](#define-a-filter)JSON kodunu yapıştırın.

**Gönder**’i seçin. 

Varlık filtresi oluşturuldu.

Varlık filtrelerini oluşturma veya güncelleştirme hakkında daha fazla bilgi için bkz. [Create veya Update](/rest/api/media/assetfilters/createorupdate). Ayrıca bkz. [Filtreler Için JSON örnekleri](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filtreleri akış bulucu ile ilişkilendir

Akış Konumlayıcı için uygulanabilecek varlık veya hesap filtrelerinin bir listesini belirtebilirsiniz. [Dinamik Paketleyici (akış uç noktası)](encode-dynamic-packaging-concept.md) , bu filtre listesini ISTEMCINIZDEKI URL 'de belirttiği değişikliklerle birlikte uygular. Bu bileşim, akış Bulucu üzerinde belirlediğiniz URL + filtrelerdeki filtreleri temel alan [dinamik bir bildirim](filters-dynamic-manifest-concept.md)oluşturur. Filtre uygulamak, ancak URL 'de filtre adlarını göstermek istemiyorsanız bu özelliği kullanmanızı öneririz.

REST kullanarak bir akış bulucu ile filtreler oluşturup ilişkilendirmek için, [akış Konumlandırıcı-API oluştur](/rest/api/media/streaminglocators/create) ' u kullanın ve `properties.filters` [istek gövdesinde](/rest/api/media/streaminglocators/create#request-body)öğesini belirtin.
                                
## <a name="stream-using-filters"></a>Filtreleri kullanarak akış

Filtreleri tanımladıktan sonra, istemcileriniz bunları akış URL 'sinde kullanabilir. Filtreler, uyarlamalı bit hızı akış protokollerine uygulanabilir: Apple HTTP Canlı Akışı (HLS), MPEG-DASH ve Kesintisiz Akış.

Aşağıdaki tabloda, filtre içeren URL 'lerin bazı örnekleri gösterilmektedir:

|Protokol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Sonraki adımlar

[Video akışı](stream-files-tutorial-with-rest.md) 
