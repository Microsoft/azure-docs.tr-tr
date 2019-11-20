---
title: Azure Medya Clipper 'ı kullanmaya başlama | Microsoft Docs
description: AMS varlıklarından video klipleri oluşturmaya yönelik bir araç olan Azure Media Clipper 'ı kullanmaya başlama
services: media-services
keywords: klip; alt klip; kodlama; medya
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685084"
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Medya Clipper ile klipler oluşturma
Bu bölümde, Azure Media Clipper ile çalışmaya başlama hakkında temel adımlar gösterilmektedir. Aşağıdaki bölümlerde Azure Medya Clipper 'ın nasıl yapılandırılacağı hakkında bilgiler sağlanmaktadır.

- İlk olarak, Azure Media Player ve Azure Media Clipper için belge başınızdan aşağıdaki bağlantıları ekleyin. Doğrudan bir Clipper sürümü ve URL 'lerde Azure Media Player belirtilmesini öneririz. İsteğe bağlı olarak değişikliğe tabi olduklarından üretimde bu kaynakların en son sürümünü kullanmayın.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Ardından, aşağıdaki sınıfları, Clipper 'ın örneğini oluşturmak istediğiniz div öğesine ekleyin.

```javascript
<div id="root" class="azure-subclipper" />
```

İsteğe bağlı olarak, koyu temayı etkinleştirmek için koyu-Skin sınıfını ekleyin:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Ardından, aşağıdaki API çağrısıyla Clipper örneğini oluşturun:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Başlatma yöntemi çağrısının parametreleri şunlardır:
- `selector` {REQUIRED, String}: pencere öğesinin oluşturulması gereken eşleşen HTML öğesinin CSS Seçicisi.
- `restVersion` {REQUIRED, String}: hedeflenecek Azure Media Services REST API'si sürümü. REST sürümü pencere öğesi tarafından oluşturulan çıktının biçimini tanımlar. Şu anda yalnızca 2,0 desteklenir.
- {REQUIRED, Promise} `submitSubclipCallback` pencere öğesinin "Gönder" düğmesine tıklandığında çağrılan geri çağırma işlevi. Geri çağırma işlevi pencere öğesi (bir işleme iş yapılandırması veya bir filtre tanımı) tarafından oluşturulan çıktıyı beklemelisiniz. Daha fazla bilgi için bkz. alt klip geri çağırması gönderme.
- `logLevel` {OPTIONAL, {' Info ', ' warn ', ' Error '}}: tarayıcı konsolunda görüntülenecek günlük düzeyi. Varsayılan değer: hata
- `minimumMarkerGap` {OPTIONAL, int}: alt klibin en küçük boyutu (saniye cinsinden). Note: değer 6 ' dan büyük veya buna eşit olmalıdır, bu da varsayılandır.
- pencere öğesi tarafından oluşturulan işleme işi yapılandırması için kullanılacak {OPTIONAL, JSON nesnesi} tek bit hızı MP4 profilini `singleBitrateMp4Profile`. Sağlanmazsa, [varsayılan tek bit hızlı MP4 profilini](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)kullanır.
- ' Isteğe bağlı, JSON nesnesi} `multiBitrateMp4Profile` pencere öğesi tarafından oluşturulan işleme iş yapılandırması için kullanılacak çoklu bit hızı MP4 profili. Sağlanmazsa, [varsayılan çoklu bit hızı MP4 profilini](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)kullanır.
- {OPTIONAL, JSON nesnesi} `keymap` pencere öğesinin klavye kısayollarının özelleştirilmesine Izin verir. Daha fazla bilgi için bkz. [özelleştirilebilir klavye kısayolları](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, Promise} geri çağırma işlevi, Kullanıcı bölmenin en altına her kaydırıldığında varlıklar bölmesine bir varlık sayfasına yüklemek için çağrılır (zaman uyumsuz). Daha fazla bilgi için bkz. varlık bölmesi yükleyici geri çağırma.
- {OPTIONAL, Number} `height` pencere öğesinin toplam yüksekliği (minimum yükseklik, varlıklar bölmesi olmayan 600 px ve varlıklar bölmesi ile 850 px).
- `subclippingMode` (Isteğe bağlı, {' All ', ' render ', ' Filter '}): alt kırpma moduna izin verilir. Varsayılan değer tümü ' dir.
- `filterAssetsTypes` (Isteğe bağlı, bool): filterAssetsTypes varlıklar bölmesinden filtreler açılan listesini gösterip gizlemenize izin verir. Varsayılan değer true 'dur.
- `speedLevels` (Isteğe bağlı, dizi): speedLevels, video oynatıcı için farklı hız düzeyleri ayarlamaya olanak tanır. daha fazla bilgi için bkz. [Azure Media Player belgeleri](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) .
- `resetOnJobDone` (Isteğe bağlı, bool): resetOnJobDone, bir iş başarıyla gönderildiğinde, Clipper 'ın alt Clipper 'ı bir başlangıç durumuna sıfırlamasına izin verir.
- `autoplayVideo` (Isteğe bağlı, bool): autoplayVideo, Clipper 'ın yükleme sırasında videoyu otomatik olarak kullanmasına izin verir. Varsayılan değer true 'dur.
- `language` {OPTIONAL, String}: dil pencere öğesinin dilini ayarlar. Belirtilmemişse pencere öğesi, tarayıcı diline bağlı olarak iletileri yerelleştirmeye çalışır. Tarayıcıda bir dil algılanmazsa pencere öğesi varsayılan olarak Ingilizce olur. Daha fazla bilgi için bkz. [Yerelleştirme yapılandırma](media-services-azure-media-clipper-localization.md) bölümü.
- {OPTIONAL, JSON} `languages`: diller parametresi, Kullanıcı tarafından tanımlanan özel bir sözlükten dillerin varsayılan sözlüğünün yerini alır. Daha fazla bilgi için bkz. [Yerelleştirme yapılandırma](media-services-azure-media-clipper-localization.md) bölümü.
- `extraLanguages` (Isteğe bağlı, JSON): extraLanguages parametresi varsayılan sözlüğe yeni diller ekler. Daha fazla bilgi için bkz. [Yerelleştirme yapılandırma](media-services-azure-media-clipper-localization.md) bölümü.

## <a name="typescript-definition"></a>TypeScript tanımı
[Buraya](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts), Clipper Için bir [TypeScript](https://www.typescriptlang.org/) tanım dosyası bulabilirsiniz.

## <a name="azure-media-clipper-api"></a>Azure Medya Clipper API 'SI
Bu bölüm, Clipper tarafından sunulan API yüzeyini belgeler.

- `ready(handler)`:, Clipper tam olarak yüklenip kullanıma sunulduktan hemen sonra JavaScript 'ı çalıştırmanın bir yolunu sunar.
- `load(assets)`: bir varlık listesini pencere öğesi zaman çizelgesine yükler (assetsPanelLoaderCallback ile birlikte kullanılmamalıdır). Varlıkları Clipper 'a yükleme hakkında ayrıntılı bilgi için bu [makaleye](media-services-azure-media-clipper-load-assets.md) bakın.
- `setLogLevel(level)`: tarayıcı konsolunda görüntülenecek günlüğe kaydetme düzeyini ayarlar. Olası değerler şunlardır: `info`, `warn`, `error`.
- `setHeight(height)`: pencere öğesinin toplam yüksekliğini piksel cinsinden ayarlar (varlık bölmesi olmayan minimum yükseklik 600 px ve varlıklar bölmesi ile 850 piksel).
- `version`: pencere öğesi sürümünü alır.

## <a name="next-steps"></a>Sonraki adımlar
Azure Medya Clipper 'ı yapılandırmaya yönelik sonraki adımlara bakın:
- [Varlıkları Azure Media Clipper 'a yükleme](media-services-azure-media-clipper-load-assets.md)
- [Özel klavye kısayollarını yapılandırma](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Clipper 'dan kırpma işleri gönderme](media-services-azure-media-clipper-submit-job.md)
- [Yerelleştirmeyi yapılandırma](media-services-azure-media-clipper-localization.md)
