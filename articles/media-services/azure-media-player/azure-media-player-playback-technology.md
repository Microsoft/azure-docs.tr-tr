---
title: Azure Media Player oynatma teknolojisi
description: Video veya ses çalmak için kullanılan kayıttan yürütme teknolojisi hakkında daha fazla bilgi edinin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 0fadb679260582a788eb6408ff3761f00c2ceb1a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448839"
---
# <a name="playback-technology-tech"></a>Kayıttan yürütme teknolojisi ("Tech") #

Kayıttan yürütme teknolojisi, video veya ses çalmak için kullanılan belirli tarayıcı veya eklenti teknolojisine başvurur.

- **azureHtml5JS**:, MSE ve eme standartlarından yararlanarak, AES-128 bit zarfı şifrelenmiş IÇERIK veya DRM ortak şifrelenmiş içerik (tarayıcının bunu desteklediği sırada PlayReady ve Widevine aracılığıyla Azure Media Services) DESTEĞIYLE kısa çizgi içeriğinin eklenti tabanlı kayıttan yürütülmesi için video öğesiyle birlikte
- **flashss**: Azure MEDIA SERVICES 'den AES-128 bit zarfı şifre çözme desteğiyle sorunsuz içerik çalmak için Flash Player teknolojisini kullanır-11,4 veya üzeri Flash sürümünü gerektirir
- **html5FairPlayHLS**: video öğesiyle HLS aracılığıyla tarayıcı tabanlı kayıttan yürütme teknolojisinde Safari 'ye özel bir işlem kullanır. Bu teknik, Azure Media Services 'den FairPlay korumalı içeriğin kayıttan yürütülmesi ve 10/19/16 itibariyle techOrder 'a eklenmesidir
- **SilverlightSS**: Azure Media Services 'ten PlayReady korumalı Içerik desteğiyle sorunsuz içerik çalmak için Silverlight teknolojisini kullanır.
- **HTML5**: video öğesiyle tarayıcı tabanlı kayıttan yürütme teknolojisinden yararlanır.  Bir Apple iOS veya Android cihazında, bu teknoloji, HLS akışlarının, AES-128 bit zarf şifrelemesi veya DRM içeriği (tarayıcı tarafından desteklendiği zaman FairPlay aracılığıyla) için bazı temel destekle birlikte oynatılmasını sağlar.

## <a name="tech-order"></a>Teknoloji siparişi ##

Varlığınızın çok çeşitli cihazlarda yürütülebilir olmasını sağlamak için aşağıdaki teknik sipariş önerilir ve varsayılan olarak, `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` ve `<video>` seçeneklerinde doğrudan veya program aracılığıyla üzerinde ayarlanabilir:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

veya

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Uyumluluk matrisi ##

Azure Media Services akış içeriğiyle önerilen teknik sipariş verildiğinde, aşağıdaki uyumluluk kayıttan yürütme matrisi beklenmektedir

| Tarayıcı        | İşletim Sistemi                                                       | Beklenen teknik (açık)  | Beklenen teknik (AES)  | Beklenen teknik (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | desteklenmiyor                |
| Edge           | Xbox One<sup>1</sup> (Kas 2015 güncelleştirmesi)                   | azureHtml5JS           | azureHtml5JS         | desteklenmiyor                |
| Chrome 37 +     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | SilverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | HTML5                  | HTML5 (belirteç yok) 3    | desteklenmiyor                |
| Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Sıradağlar LION<sup>1</sup>                           | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| Chrome 37 +     | Android 4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | Android 4,02                                             | HTML5                  | HTML5 (belirteç yok)<sup>3</sup>    | desteklenmiyor                |
| Firefox 42 +    | Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | desteklenmiyor                |
| IE 8, ıE 9, ıE 10  | Windows                                                  | desteklenmiyor          | desteklenmiyor        | desteklenmiyor                |

<sup>1</sup> yapılandırma desteklenmiyor veya test edildi; tamamlama için başvuru olarak listelenir.

<sup>2</sup> Android cihazlarda başarılı kayıttan yürütme, cihaz özellikleri, grafik desteği, codec bileşeni oluşturma, işletim sistemi desteği ve daha fazlası için bir bileşim gerektirir. Android, telefon üreticilerinin Google tarafından sunulan Vanilla Android işletim sistemini değiştirmesine izin veren açık kaynaklı bir platform olduğundan, bu, Android alanında bazı parçalanmaya neden olur ve bazı cihazlarda bazı cihazlar desteklenmeyebilir. Ayrıca, bazı Android cihazlarda tüm codec bileşenleri desteklenmez.  

<sup>3</sup> belirteç desteği olmayan durumlarda, bu işlevselliği eklemek için bir ara sunucu kullanılabilir. Bu çözüm hakkında daha fazla bilgi edinmek için bu [bloga](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) göz atın.

> [!NOTE]
> Beklenen teknik, Flash gibi bir eklenti yüklenmesini gerektiriyorsa ve bu kullanıcının makinesinde yüklü değilse, AMP, teknik listede kaynak türleri ve koruma bilgileriyle birlikte sonraki teknik özellikleri denetlemeye devam edecektir. Örneğin, işletim sistemi X Yosemite üzerinde Safari 8 ' de korunmayan bir isteğe bağlı akış görüntülemeye çalışıyorsanız ve hem Flash hem de Silverlight yüklü değilse, AMP oynatma için yerel HTML5 Tech ' i seçer.<br/><br/>Yeni tarayıcı teknolojileri günlük olarak gelişmektedir ve bu da bu matrisi etkileyebilir.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)