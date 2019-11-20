---
title: Azure Media Services ile filtre oluşturma için CLI kullanma | Microsoft Docs
description: Bu konuda, Media Services ile filtre oluşturma için CLI kullanma gösterilmektedir.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26350479f0f066f45c143e1a35061b3a409de309
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786487"
---
# <a name="creating-filters-with-cli"></a>CLI ile filtre oluşturma 

İçeriğinizi müşterilere (Canlı etkinlik veya isteğe bağlı Video akışı) sunarken istemcinizi varsayılan varlığın bildirim dosyasında tanımlanan değerinden daha fazla esneklik gerekebilir. Azure Media Services hesap filtreleri ve içeriğiniz için varlık filtrelerini tanımlamanızı sağlar. 

Bu özellik ve onu kullanıldığı senaryoların ayrıntılı açıklaması için bkz [dinamik bildirimlerini](filters-dynamic-manifest-overview.md) ve [filtreleri](filters-concept.md).

Bu konuda bir Video isteğe bağlı varlık için bir filtre yapılandırın ve oluşturmak için Media Services v3 için CLI'yi kullanma işlemi gösterilmektedir [hesap filtreleri](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) ve [varlık filtreleri](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Gözden geçirdiğinizden emin olun [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Önkoşullar 

- [Bir Media Services hesabı oluşturma](create-account-cli-how-to.md). Kaynak grubu adı ve Media Services hesap adını hatırlamak emin olun. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Bir filtre tanımlar 

Aşağıdaki örnek, son bildirimine eklenmesini izleme seçimi koşulları tanımlar. Bu filtre EC-3 olan tüm ses parçalarını ve 0-1000000 hızına sahip olan herhangi bir video parçaları içerir aralığı.

> [!TIP]
> Tanımlamak planlıyorsanız **filtreleri** KALAN, "Özellikler" sarmalayıcı JSON nesnesi eklemeniz gerektiğini unutmayın.  

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

Aşağıdaki [az ams hesabına-filtre](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) komut olan izleme seçimlerini Filtresi ile bir hesabı filtresi oluşturur [daha önce tanımlanan](#define-a-filter). 

Komut isteğe bağlı geçirmenize olanak `--tracks` izleme seçimlerini temsil eden JSON içeren bir parametre.  Kullanım @{file} JSON bir dosyadan yüklenemiyor. Azure CLI'yı yerel olarak kullanıyorsanız, tüm dosya yolu belirtin:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Ayrıca bkz [filtreleri için JSON örnekler](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Varlık filtre oluşturma

Aşağıdaki [az ams varlığı-filtre](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) komut olan izleme seçimlerini Filtresi ile bir varlık filtresi oluşturur [daha önce tanımlanan](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Ayrıca bkz [filtreleri için JSON örnekler](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Filtreler akış Bulucu ile ilişkilendirme

Bir akış Bulucu için uygulamak varlık veya hesap filtrelerin listesini belirtebilirsiniz. [Dinamik Paketleyici (akış uç noktası)](dynamic-packaging-overview.md) bu olanlar istemcinizin URL'SİNDE belirtir birlikte filtrelerinin listesi için geçerlidir. Bu birleşim oluşturur bir [dinamik bildirim](filters-dynamic-manifest-overview.md), URL'deki filtreleri + akış Bulucu üzerinde belirttiğiniz filtreleri temel. Filtre uygulamak istediğiniz, ancak URL filtresi adlarında kullanıma sunmak istiyorsanız değil, bu özelliği kullanmanızı öneririz.

Aşağıdaki CLI kod, bir akış Bulucusu oluşturmak ve belirtmek gösterilmektedir `filters`. Bu varlık filtre adları ve/veya hesap filtre adları, boşlukla ayrılmış listesini alan isteğe bağlı bir özelliktir.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream, filtrelerini kullanma

İstemcilerinize filtreleri tanımladıktan sonra bunları akış URL'SİNDE kullanabilirsiniz. Filtreler hızı Uyarlamalı akış için uygulanabilir: Apple HTTP canlı akış (HLS), MPEG-DASH ve kesintisiz akış.

Aşağıdaki tabloda, filtrelerle URL'leri bazı örnekler gösterilmektedir:

|Protocol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Sonraki adım

[Stream videoları](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Ayrıca bkz.

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
