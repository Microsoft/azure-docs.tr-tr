---
title: Varlıkları Azure Media Clipper 'a yükleme | Microsoft Docs
description: Azure Media Clipper 'a varlık yükleme adımları
services: media-services
keywords: klip; alt klip; kodlama; medya
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685048"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Varlıkları Azure Media Clipper 'a yükleme  

Varlıklar, Azure Media Clipper 'a iki yöntemle yüklenebilir:
1. Bir varlık kitaplığında statik olarak geçirme
2. API aracılığıyla bir varlık listesini dinamik olarak oluşturma

## <a name="statically-load-videos-into-clipper"></a>Videoları Clipper 'a statik olarak yükleme
Son kullanıcıların, varlık seçim panelinden video seçmeden küçük resimleri oluşturmasını sağlamak için videoları Clipper 'a statik olarak yükleyin.

Bu durumda, bir statik varlık kümesini Clipper 'a geçitirsiniz. Her varlık bir AMS varlık/filtre KIMLIĞI, ad, yayımlanan akış URL 'SI içerir. Uygulanabiliyorsa, bir içerik koruma kimlik doğrulama belirteci veya küçük resim URL 'si dizisi iletilebilir. Geçirilirse, küçük resimler arabirime doldurulur. Varlık kitaplığının statik ve küçük olduğu senaryolarda, kütüphanedeki her bir varlık için varlık sözleşmesini geçirebilirsiniz.

> [!NOTE]
> Varlıkları Clipper 'a statik olarak yüklerken, varlıklar **doğrudan zaman çizelgesine** eklenir ve **varlık bölmesi işlenmez**. İlk varlık zaman çizelgesine eklenir ve varlıkların geri kalanı zaman çizelgesinin sağ tarafında yığılır.

Statik bir varlık kitaplığını yüklemek için, her bir varlığın JSON gösterimini geçirmek üzere **Load** metodunu kullanın. Aşağıdaki kod örneği, bir varlığın JSON temsilini gösterir.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Load () yöntemi çağrısını, önceki örnekte gösterildiği gibi Ready (handler) yöntemiyle zincirlemesi önerilir. Önceki örnek, varlıkların yüklenmeden önce pencere öğesinin önceden hazırlandığına güvence sağlar.

> [!NOTE]
> Küçük resim URL 'Lerinin, Clipper zaman çizelgesinde beklendiği gibi çalışması için, video üzerinde eşit olarak dağıtılmaları gerekir (süreye göre) ve dizi içinde kronolojik sırada. Aşağıdaki JSON önceden ayarlanmış kod parçacığını, ' Media Encoder Standard ' işlemcisine sahip görüntüler oluşturmak için örnek başvuru olarak kullanabilirsiniz:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Clipper 'da videoları dinamik olarak yükleme
Son kullanıcıların, varlık seçimi panelinden video seçmesini ve küçük resim olarak ölçeklendirmesini sağlamak için videoları Clipper 'a dinamik olarak yükleyin.

Alternatif olarak, bir geri çağırma yoluyla varlıkları dinamik olarak yükleyebilirsiniz. Varlıkların dinamik olarak oluşturulduğu veya kitaplığın büyük olduğu senaryolarda, geri çağırma aracılığıyla yüklemeniz gerekir. Varlığı dinamik olarak yüklemek için isteğe bağlı onLoadAssets geri arama işlevini uygulamanız gerekir. Bu işlev, başlatma sırasında Clipper 'a geçirilir. Çözümlenen varlıkların statik olarak yüklenen varlıklarla aynı sözleşmeye bağlı olması gerekir. Aşağıdaki kod örneği, yöntem imzasını, beklenen girişi ve beklenen çıktıyı gösterir.

> [!NOTE]
> Varlıkları Clipper 'a dinamik olarak yüklerken, varlıklar **varlık seçimi panelinde**işlenir.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```
