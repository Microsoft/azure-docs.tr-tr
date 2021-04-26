---
title: Azure Media Player URL yeniden yazıcı
description: Azure Media Player, sorunsuz, DASH, HLS v3 ve HLS v4 için akışlar sağlamak üzere Azure Media Services verilen bir URL 'yi yeniden yazmaktır.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: efa77ce7416b94331dce2bb4e7f77dd50c20d450
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448585"
---
# <a name="url-rewriter"></a>URL yeniden yazıcı #

Azure Media Player, düz, TIRE, HLS v3 ve HLS v4 için akışlar sağlamak üzere Azure Media Services verilen bir URL 'yi yeniden yazmaktır. Örneğin, kaynak aşağıdaki gibi verildiğinde, Azure Media Player yukarıdaki protokollerin tümünü yürütmeye çalıştığını güvence altına alacak:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Bununla birlikte, URL yeniden yazıcısını kullanmak istemiyorsanız, özelliği parametresine ekleyerek bunu yapabilirsiniz `disableUrlRewriter` . Bu, kaynaklara iletilen tüm bilgilerin doğrudan Player 'a değişiklik yapılmadan geçirilme anlamına gelir.  İşte, DASH ve tek bir KESINTISIZ akış üzerinde iki kaynak eklemeye örnek verilmiştir.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

veya

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Ayrıca, isterseniz, parametresini kullanarak yeniden yazmak Azure Media Player istediğiniz belirli akış biçimlerini belirtebilirsiniz `streamingFormats` . Seçenekler şunlardır,,,, `DASH` `SMOOTH` `HLSv3` `HLSv4` `HLS` . HLS ve HLSv3 & v4 arasındaki fark, HLS biçiminin FairPlay içeriğinin kayıttan yürütmesini destekliyor olması şeklindedir. v3 ve v4, FairPlay desteklemez. Kullanılabilir belirli bir protokol için teslim ilkeniz yoksa, bu faydalıdır.  İşte, varlığınız için bir DASH protokolünün etkin olmadığı bir örnektir.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

veya

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Yukarıdaki ikisi, belirli varlığınıza bağlı olarak birden çok koşulda birbirleriyle birlikte kullanılabilir.

> [!NOTE]
> Wıdevine koruma bilgileri yalnızca DASH protokolünde devam ettirir.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)