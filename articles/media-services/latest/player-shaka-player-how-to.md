---
title: Azure Media Services Shaka oynatıcıyı kullanma
description: Bu makalede, Azure Media Services Shaka Player 'ın nasıl kullanılacağı açıklanmaktadır
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9699e6ad3f004f94c83440dd39f13c6db887e224
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282207"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Azure Media Services Shaka oynatıcıyı kullanma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Genel Bakış

Shaka oynatıcı, uyarlamalı medya için açık kaynaklı bir JavaScript kitaplığıdır. Eklentiler veya Flash kullanmadan bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) çalar. Bunun yerine, Shaka Player açık Web standartları medya kaynağı uzantılarını ve şifreli medya uzantılarını kullanır.

[Mux.js](https://github.com/videojs/mux.js/) olarak kullanılması önerilir, Shaka oynatıcı, HLS CMAF formatını destekleyecektir, ancak HLS 'leri desteklemez.

Resmi belgeler, [Shaka Player belgelerinde](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)bulunabilir.

## <a name="sample-code"></a>Örnek kod

Bu makaleye örnek kod, [Azure-Samples/Media-Services-3rdpartisi-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)adresinde bulunabilir.

## <a name="implementing-the-player"></a>Oynatıcıyı uygulama

Player örneğinizi uygulamanız gerekiyorsa aşağıdaki yönergeleri izleyin:

1. `index.html`Oynatıcıyı barındırabileceğiniz bir dosya oluşturun. Aşağıdaki kod satırlarını ekleyin (varsa sürümleri daha yeni için değiştirebilirsiniz):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Aşağıdaki kodla bir JavaScript dosyası ekleyin:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. `manifestUrl`Varlıkınızın akış konumlandırıcı Azure Portal sayfasında bulunan HLS veya Dash URL 'si ile değiştirin.
    ![akış Konumlandırıcı URL 'Leri](media/player-shaka-player-how-to/streaming-urls.png)

1. Bir sunucu çalıştırın (örneğin, ile `npm http-server` ) ve oyuncusunun çalışıyor olması gerekir...

## <a name="set-up-captions"></a>Açıklamalı alt yazıları ayarlama

### <a name="set-up-vod-captions"></a>VOD resim yazılarını ayarlama

Aşağıdaki kod satırlarını çalıştırın ve `captionUrl` . VTT dizininizle değiştirin (CORS hatasından kaçınmak için VTT dosyasının aynı konakta olması gerekir), `lang` dil için iki harfli kodla ve ya `type` da ile ile değiştirin `caption` `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Canlı akış başlıklarını ayarlama

Canlı akıştaki açıklamalı alt yazıları etkinleştir aşağıdaki kod satırını ekleyerek yapılandırılır:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Belirteç kimlik doğrulamasını ayarlama

Aşağıdaki kod satırlarını çalıştırın ve yerine `token` belirteç dizeniz ile değiştirin:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128 şifrelemesini ayarlama

Shaka Player şu anda AES-128 şifrelemesini desteklememektedir.

Bu özelliğin durumunu izlemek için bir GitHub [sorununun](https://github.com/google/shaka-player/issues/850) bağlantısı.

## <a name="set-up-drm-protection"></a>DRM korumasını ayarlama

Shaka Player, kullanılacak güvenli bir URL 'yi gerektiren şifreli medya uzantıları 'nı (EME) kullanır. Bu nedenle, tüm DRM korumalı içerikleri test etmek için HTTPS kullanılması gerekir. Site https kullanıyorsa, bildirimin ve her segmentin da https kullanması gerekir. Bunun nedeni, karışık içerik gereksinimleridir.

Lisans sunucuları için URL (ler) in Shaka yönetimi için tercih sırası:

1. Hata ayıklama için kullanılan ClearKey yapılandırması, diğer her şeyi geçersiz kılmalıdır. (Uygulama yine de bir ClearKey lisans sunucusu belirtebilir.)
2. Uygulama tarafından yapılandırılan sunucular varsa, bildirimde bulunan her şeyi geçersiz kılmalıdır.
3. Bildirim tarafından belirtilen lisans sunucuları yalnızca başka hiçbir şey belirtilmemişse kullanılır.

Widevine veya PlayReady için lisans sunucusu URL 'sini belirtmek için aşağıdaki kodu kullanabiliriz:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Tüm FairPlay içeriği bir sunucu sertifikası ayarlanmasını gerektirir. Oynatıcı yapılandırmasında ayarlanır:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Daha fazla bilgi için bkz. [Shaka Player DRM koruması belgeleri](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Media Player kullanın](../azure-media-player/azure-media-player-overview.md)
* [Hızlı başlangıç: içeriği şifreleyin](drm-encrypt-content-how-to.md)
