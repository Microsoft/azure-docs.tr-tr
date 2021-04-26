---
title: Azure Media Services ile filtre oluşturmak için CLı kullanma
description: Bu makalede, Azure Media Services v3 ile filtre oluşturmak için CLı 'nin nasıl kullanılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2e03205ca2bd896aa3a3f1cfca5959144237f88b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112101"
---
# <a name="creating-filters-with-cli"></a>CLı ile filtre oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

İçeriğinizi müşterilere sunarken (canlı etkinlikler veya Isteğe bağlı video akışı), istemciniz varsayılan varlığın bildirim dosyasında açıklananlardan daha fazla esneklik gerektirebilir. Azure Media Services, içeriğiniz için hesap filtrelerini ve varlık filtrelerini tanımlamanızı sağlar.

Bu özelliğin ve kullanılan senaryoların ayrıntılı açıklaması için bkz. [dinamik bildirimler](filters-dynamic-manifest-concept.md) ve [Filtreler](filters-concept.md).

Bu konuda, bir video Isteğe bağlı varlığı için bir filtrenin nasıl yapılandırılacağı ve [hesap filtreleri](/cli/azure/ams/account-filter) ve [varlık filtreleri](/cli/azure/ams/asset-filter)oluşturmak IÇIN Media Services v3 için CLI kullanımı gösterilmektedir.

> [!NOTE]
> [Presentationtimerange](filters-concept.md#presentationtimerange)öğesini gözden geçirdiğinizden emin olun.

## <a name="prerequisites"></a>Önkoşullar

- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını hatırlayacağınızdan emin olun.

## <a name="define-a-filter"></a>Filtre tanımlama

Aşağıdaki örnek, son bildirime eklenen izleme seçim koşullarını tanımlar. Bu filtre, EC-3 olan tüm ses izlerini ve 0-1000000 aralığında bit hızına sahip video izlemelerini içerir.

> [!TIP]
> BEKLEYEN **filtreleri** tanımlamanızı planlıyorsanız, "Özellikler" sarmalayıcı JSON nesnesini eklemeniz gerektiğini unutmayın.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Hesap filtreleri oluşturma

Aşağıdaki [az AMS Account-Filter](/cli/azure/ams/account-filter) komutu, [daha önce tanımlanan](#define-a-filter)filtre izleme seçimleriyle bir hesap filtresi oluşturur.

Komutu, `--tracks` izleme seçimlerini temsil eden JSON içeren isteğe bağlı bir parametreyi geçirmenize olanak sağlar.  JSON 'ı bir dosyadan yüklemek için @ {File} kullanın. Azure CLı 'yi yerel olarak kullanıyorsanız, tüm dosya yolunu belirtin:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Ayrıca bkz. [Filtreler Için JSON örnekleri](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Varlık filtreleri oluşturma

Aşağıdaki [az AMS varlık-Filter](/cli/azure/ams/asset-filter) komutu, [daha önce tanımlanan](#define-a-filter)filtre izleme seçimleriyle bir varlık filtresi oluşturur. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Ayrıca bkz. [Filtreler Için JSON örnekleri](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Filtreleri akış bulucu ile ilişkilendir

Akış Konumlayıcı için uygulanabilecek varlık veya hesap filtrelerinin bir listesini belirtebilirsiniz. [Dinamik Paketleyici (akış uç noktası)](encode-dynamic-packaging-concept.md) , bu filtre listesini ISTEMCINIZDEKI URL 'de belirttiği değişikliklerle birlikte uygular. Bu bileşim, akış Bulucu üzerinde belirlediğiniz URL + filtrelerdeki filtreleri temel alan [dinamik bir bildirim](filters-dynamic-manifest-concept.md)oluşturur. Filtre uygulamak, ancak URL 'de filtre adlarını göstermek istemiyorsanız bu özelliği kullanmanızı öneririz.

Aşağıdaki CLı kodu, bir akış Bulucu oluşturma ve belirtme işlemlerinin nasıl yapılacağını gösterir `filters` . Bu, varlık filtresi adlarının ve/veya hesap filtresi adlarının boşlukla ayrılmış bir listesini alan isteğe bağlı bir özelliktir.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Filtreleri kullanarak akış

Filtreleri tanımladıktan sonra, istemcileriniz bunları akış URL 'sinde kullanabilir. Filtreler, uyarlamalı bit hızı akış protokollerine uygulanabilir: Apple HTTP Canlı Akışı (HLS), MPEG-DASH ve Kesintisiz Akış.

Aşağıdaki tabloda, filtre içeren URL 'lerin bazı örnekleri gösterilmektedir:

|Protokol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Sonraki adım

[Video akışı](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>Ayrıca bkz.

[Azure CLI](/cli/azure/ams)
