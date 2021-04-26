---
title: Video.js oynatıcıyı Azure Media Services ile kullanma
description: Bu makalede, Azure Media Services ile HTML video nesnesinin ve JavaScript 'In nasıl kullanılacağı açıklanmaktadır
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
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282214"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Video.js oynatıcıyı Azure Media Services ile kullanma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Genel Bakış

Video.js HTML5 World için oluşturulmuş bir web video oyuncusu. Eklentiler veya Flash kullanmadan bir tarayıcıda Uyarlamalı medya biçimlerini (DASH ve HLS gibi) çalar. Bunun yerine, Video.js Open Web standartları MediaSource uzantılarını ve şifreli medya uzantılarını kullanır. Üstelik, masaüstleri ve mobil cihazlarda video oynatmayı destekler.

Resmi belgelerini adresinde bulabilirsiniz [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Örnek kod
Bu makaleye örnek kod, [Azure-Samples/Media-Services-3rdpartisi-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)adresinde bulunabilir.

## <a name="implement-the-player"></a>Oynatıcıyı uygulama

1. `index.html`Oynatıcıyı barındırabileceğiniz bir dosya oluşturun. Aşağıdaki kod satırlarını ekleyin (varsa sürümleri daha yeni için değiştirebilirsiniz):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. `index.js`Aşağıdaki kodla bir dosya ekleyin:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. `manifestUrl`Azure Portal akış Bulucu sayfasında bulunan, varlığınızın akış bulucunun içindeki HLS veya Dash URL 'siyle değiştirin.
    ![akış Konumlandırıcı URL 'Leri](media/player-shaka-player-how-to/streaming-urls.png)

4. `protocolType`Aşağıdaki seçeneklerle değiştirin:

    - HLS protokolleri için "application/x-mpegURL"
    - DASH protokolleri için "Application/Dash + xml"

### <a name="set-up-captions"></a>Açıklamalı alt yazıları ayarlama

Yöntemini çalıştırın `addRemoteTextTrack` ve şunu değiştirin:

- `subtitleKind` ,, `"captions"` , `"subtitles"` `"descriptions"` ya da `"metadata"`  
- `caption` . VTT dosya yolu ile (VTT dosyasının CORS hatasından kaçınmak için aynı konakta olması gerekir)
- `subtitleLang` dil için BCP 47 kodu, örneğin, `"eng"` İngilizce veya `"es"` İspanyolca için
- `subtitleLabel` istediğiniz ekran yazısının adı ile

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Belirteç kimlik doğrulamasını ayarlama

Belirtecin, istek üstbilgisinin yetkilendirme alanında ayarlanması gerekir. CORS ile ilgili sorunlardan kaçınmak için, bu belirtecin yalnızca URL 'sinde bulunan isteklerde ayarlanması gerekir `'keydeliver'` . Aşağıdaki kod satırları şu şekilde çalışır:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Daha sonra yukarıdaki işlev `videojs.Hls.xhr.beforeRequest` olaya eklenmelidir.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128 şifrelemesini ayarlama

Video.js ek bir yapılandırma olmadan AES-128 şifrelemesini destekler. 

> [!NOTE] 
> Şifreleme ve HLS/DASH CMAF içeriğiyle ilgili bir [sorun](https://github.com/videojs/video.js/issues/6717) var ve bu durumda yürütülemeyebilir.

### <a name="set-up-drm-protection"></a>DRM korumasını ayarlama

DRM korumasını desteklemek için, [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) resmi uzantısını eklemeniz gerekir. CDN sürümü de kullanılır.

1. `index.js`Yukarıdaki ayrıntılı dosyada, `videoJS.eme();` videonun kaynağını eklemeden *önce* ekleyerek eme uzantısını başlatmalısınız:

   ```javascript
    videoJS.eme();
   ```

2. Artık DRM hizmetlerinin URL 'Lerini ve ilgili lisansların URL 'lerini aşağıdaki gibi tanımlayabilirsiniz:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Lisans URL 'SI alınıyor

Lisans URL 'sini almak için şunları yapabilirsiniz:

- DRM sağlayıcı yapılandırmanıza başvurun
- ya da örneği kullanıyorsanız, `output.json` VODs için *setup-vod.ps1* PowerShell betiğini veya canlı akışlar için *start-live.ps1* betiğini çalıştırdığınızda oluşturulan belgeye başvurun. Bu dosyanın içinde de çocukları bulacaksınız.

#### <a name="using-tokenized-drm"></a>Simgeleştirilmiş DRM kullanma

Simgeleştirilmiş DRM korumasını desteklemek için, Player özelliğine aşağıdaki satırı eklemeniz gerekir `src` :

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Media Player kullanın](../azure-media-player/azure-media-player-overview.md)  
- [Hızlı başlangıç: içeriği şifreleyin](drm-encrypt-content-how-to.md)
