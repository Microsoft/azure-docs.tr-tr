---
title: Azure Media Player hata kodları
description: Azure Media Player için bir hata kodu başvurusu.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/05/2021
ms.openlocfilehash: 77bd59782d12d7d73e6e882e929ae9a4c95250a7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449894"
---
# <a name="error-codes"></a>Hata kodları #

Kayıttan yürütme başlayamaz veya durdurulduğunda bir hata olayı tetiklenir ve `error()` uygulama geliştiricisinin daha fazla ayrıntı almasını sağlamak için işlev bir kod ve isteğe bağlı bir ileti döndürür. `error().message` kullanıcıya görüntülenmeyen ileti değildir.  Kullanıcıya gösterilecek ileti, `error().code` bıts 27-20 ' i temel alır, aşağıdaki tabloya bakın.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Hata kodları, bitler [31-28] (4 bit) ##

Hatanın alanını açıkla.

- 0 - Bilinmeyen
- 1-AMP
- 2-AzureHtml5JS
- 3-FlashSS
- 4-SilverlightSS
- 5-HTML5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Hata kodları, bitler [27-0] (28 bit) ##

Hatanın ayrıntılarını açıklama, BITS 27-20 yüksek düzeyde sağlama, BITS 19-0 varsa daha fazla ayrıntı sağlar.


| amp. errorCode. ada | Kodlar, bitler [27-0] (28 bit) | Açıklama |
|---|---:|---|
| **MEDIA_ERR_ABORTED hata aralığı (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Genel iptal hatası |
| abortedErrNotImplemented | 0x0100001 | İptal hatası, uygulanmadı |
| abortedErrHttpMixedContentBlocked | 0x0100002 | İptal hatası, karışık içerik engellendi- `http://` bir sayfadan akış yüklenirken genellikle gerçekleşir `https://` |
| **MEDIA_ERR_NETWORK hatası başlangıç değeri (0x0200000-0x02FFFFF)** | | |
| Networkerrunbilinen | 0x0200000 | Genel ağ hatası |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 hata yanıtı |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 hata yanıtı |
| Networkerrhttpuseryasak | 0x0200193 | Http 403 hata yanıtı |
| networkErrHttpUrlNotFound | 0x0200194 | HTTP 404 hata yanıtı |
| networkErrHttpNotAllowed | 0x0200195 | HTTP 405 hata yanıtı |
| Networkerrhttpkaybedildi | 0x020019A | Http 410 hata yanıtı |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412 hata yanıtı |
| Networkerrhttpınternalserverfailure | 0x02001F4 | Http 500 hata yanıtı
| networkErrHttpBadGateway | 0x02001F6 | Http 502 hata yanıtı |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503 hata yanıtı |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 hata yanıtı |
| networkErrTimeout | 0x0200258 | Ağ zaman aşımı hatası
| networkErrErr | 0x0200259 | Ağ bağlantısı hata yanıtı |
| **MEDIA_ERR_DECODE hataları (0x0300000-0x03FFFFF)** | | |
| bilinen decodeerrun | 0x0300000 | Genel kod çözme hatası |
| **MEDIA_ERR_SRC_NOT_SUPPORTED hataları (0x0400000-0x04FFFFF)** | | |
| Srcerrunbilinen | 0x0400000 | Genel kaynakta desteklenmeyen hata |
| srcErrParsePresentation | 0x0400001 | Sunu ayrıştırma hatası |
| srcErrParseSegment | 0x0400002 | Segment ayrıştırma hatası |
| srcErrUnsupportedPresentation | 0x0400003 | Sunum desteklenmiyor |
| srcErrInvalidSegment | 0x0400004 | Geçersiz segment |
| **MEDIA_ERR_ENCRYPTED hatası başlangıç değeri (0x0500000-0x05FFFFF)** | | |
| Encrypterrunbilinen | 0x0500000 | Genel şifreli hata | 
| encryptErrDecrypterNotFound | 0x0500001 | Şifre çözücü bulunamadı |
| Encrypterrdecrypterınit | 0x0500002 | Şifre çözücü başlatma hatası |
| encryptErrDecrypterNotSupported | 0x0500003 | Şifre çözücü desteklenmiyor |
| encryptErrKeyAcquire | 0x0500004 | Anahtar alma başarısız oldu |
| Encrypterrşifre çözme | 0x0500005 | Kesim şifresi çözülemedi |
| encryptErrLicenseAcquire | 0x0500006 | Lisans alma başarısız |
| **SRC_PLAYER_MISMATCH hatası başlangıç değeri (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Kaynağı oynatmak için genel bir eşleşen teknik oynatıcı yok |
| Srcplayermismatchflashnotyüklü | 0x0600001 |Yüklü kaynak oynatılamayabilir, Flash eklentisi yüklü değil. *Ya da* Flash 30 yüklenir ve AES içeriğini kayıttan yürütüyor.  Bu durumda, lütfen farklı bir tarayıcı deneyin. Flash 30, bugün 7 Haziran itibariyle desteklenmez. Daha ayrıntılı bilgi için bkz. [bilinen sorunlar](azure-media-player-known-issues.md) . Not: 0x00600003 Ise, techOrder içinde belirtilmişse hem Flash hem de Silverlight yüklü değildir.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Kaynak yüklüyse Silverlight eklentisi yüklü değildir. Not: 0x00600003 Ise, techOrder içinde belirtilmişse hem Flash hem de Silverlight yüklü değildir. |
| | 0x00600003 | Teknik siparişte belirtilmişse hem Flash hem de Silverlight yüklü değildir. |
| **Bilinmeyen hatalar (0x0FF00000)** | | |
| Errunbilinen | 0xFF00000 | Bilinmeyen hatalar |

## <a name="user-error-messages"></a>Kullanıcı hata iletileri ##

Görünen kullanıcı iletisi, hata kodunun bit 27-20 ' i temel alır.

- MEDIA_ERR_ABORTED (1)-"video kayıttan yürütmeyi durduruyordu"
- MEDIA_ERR_NETWORK (2)-"ağ hatası, video indirmesinin başarısız olmasına neden oldu."
- MEDIA_ERR_DECODE (3)-"bir bozulma sorunu nedeniyle video kayıttan yürütme işlemi iptal edildi veya tarayıcınızın desteklemediği özellikler tarafından kullanıldı."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"sunucu veya ağ başarısız olduğu ya da biçim desteklenmediğinden, video yüklenemedi."
- MEDIA_ERR_ENCRYPTED (5)-"video şifrelenir ve şifresini çözmek için anahtarlar yoktur."
- SRC_PLAYER_MISMATCH (6)-"Bu video için uyumlu kaynak bulunamadı."
- MEDIA_ERR_UNKNOWN (0xFF)-"bilinmeyen bir hata oluştu."

## <a name="examples"></a>Örnekler ##

### <a name="0x10600001"></a>0x10600001 ##

"Bu video için uyumlu kaynak bulunamadı." son kullanıcıya gösterilir.

İstenen kaynakları çalabileceğiniz bir teknik oynatıcı yoktur, ancak Flash eklentisi yüklüyse bir kaynağın yürütülmesi olasıdır.  

### <a name="0x20200194"></a>0x20200194 ###

"Bir ağ hatası, video indirmesinin başarısız olmasına neden oldu." son kullanıcıya gösterilir.

AzureHtml5JS bir HTTP 404 yanıtından Kayıttan yürütmede başarısız oldu.

### <a name="categorizing-errors"></a>Hataları kategorilere ayırma ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Belirli bir hatayı yakalama ###

Aşağıdaki kod yalnızca 404 hata yakalar:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)