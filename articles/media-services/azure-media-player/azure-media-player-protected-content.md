---
title: Azure Media Player korumalı Içerik
description: Azure Media Player şu anda AES-128 bit zarfı şifrelenmiş içeriği ve ortak şifrelenmiş içeriği desteklemektedir.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 8c04a3847ce1005b530042926efb8fbbe75f9cfe
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448737"
---
# <a name="protected-content"></a>Korumalı içerik #

Azure Media Player şu anda AES-128 bit zarfı şifrelenmiş içeriği ve ortak şifrelenmiş içeriği (PlayReady ve Widevine aracılığıyla) veya FairPlay aracılığıyla şifrelenmiş içeriği desteklemektedir. Korumalı içeriği doğru bir şekilde kayıttan yürütmek için Azure Media Player bildirmeniz gerekir `protectionInfo` . Bu bilgiler kaynak başına mevcuttur ve `<source>` aracılığıyla doğrudan etiketine eklenebilir `data-setup` .  Ayrıca, `protectionInfo` kaynağı dinamik olarak ayarlarsanız doğrudan bir parametre olarak ekleyebilirsiniz.

`protectionInfo` JSON nesnesini kabul eder ve şunları içerir:

- `type`: `AES` veya `PlayReady` veya veya `Widevine``FairPlay`
- `certificateUrl`: Bu, barındırılan FairPlay CERT için doğrudan bir bağlantı olmalıdır

- `authenticationToken`: Bu, kodlanmamış bir kimlik doğrulama belirteci eklemek için bir seçenek alanıdır

> [!IMPORTANT]
> **Certificateurl** nesnesi yalnızca FairPlay DRM için gereklidir. * * *
>[!NOTE]
> Varsayılan teknik sipariş, `html5FairPlayHLS` Fairplay içeriğini destekleyen tarayıcılarda yerel olarak kayıttan yürütmek için (OSX 8 + üzerinde Safari) yeni teknoloji ile uyumlu olacak şekilde değiştirilmiştir. Kayıttan yürütmek için FairPlay içeriğiniz varsa **ve** varsayılan techorder öğesini uygulamanızda özel bir tane olarak değiştirdiyseniz, bu yeni teknik, techorder nesneniz içine eklemeniz gerekecektir. İçeriğinizi PlayReady aracılığıyla kayıttan yürütmemesi için silverlightSS önce eklemeniz önerilir.

## <a name="code-sample"></a>Kod örneği ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

veya birden çok DRM ile

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Tüm tarayıcılar/platformlar, korunan içeriği kayıttan yürütebilme yeteneğine sahip değildir. Desteklenen özellikler hakkında daha fazla bilgi için [kayıttan yürütme teknolojisi](azure-media-player-playback-technology.md) bölümüne bakın.
> [!IMPORTANT]
> Player 'a geçirilen belirteç güvenli içerik amaçlıdır ve yalnızca kimliği doğrulanmış kullanıcılar için kullanılır. Uygulamanın SSL veya başka bir güvenlik ölçüsü biçimi kullanıldığı varsayılır. Ayrıca, son kullanıcının belirtecin kötüye kullanımı için güvenilir olması gerekir; Böyle bir durum yoksa, lütfen güvenlik uzmanlarınızı dahil edin.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)