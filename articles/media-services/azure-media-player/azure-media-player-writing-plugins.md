---
title: Azure Media Player için eklenti yazma
description: JavaScript ile Azure Media Player eklenti yazma hakkında bilgi edinin
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7f14be29a33a885bfcef27fa9ef2775e72df16d9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449809"
---
# <a name="writing-plugins-for-azure-media-player"></a>Azure Media Player için eklenti yazma #

Bir eklenti, oynatıcıyı genişletmek veya geliştirmek için yazılmış bir JavaScript 'dir. Azure Media Player görünümünü değiştiren eklentiler yazabilir, işlevselliği, hatta diğer hizmetlerle BT arabirimine sahip olur. Bunu iki kolay adımda yapabilirsiniz:

## <a name="step-1"></a>1. Adım ##

JavaScript kodunuzu şöyle bir işlevde yazın:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Kodunuzu doğrudan HTML sayfanıza `<script>` veya bir dış JavaScript dosyasına yazabilirsiniz. İkincisini yaparsanız, `<head>` amp betiği *sonrasında* HTML sayfanıza JavaScript dosyasını eklediğinizden emin olun.

Örnek:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>2. Adım ##
Eklentiyi JavaScript ile iki şekilde başlatın:

Yöntem 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Yöntem 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Eklenti seçenekleri gerekli değildir, bunlar da dahil olmak üzere, kendi eklentisini kullanan geliştiricilerin, kaynak kodu değiştirmeye gerek kalmadan davranışını yapılandırmasına izin verir.

Bir eklenti oluşturmaya yönelik daha fazla örnek için [galerimize](azure-media-player-plugin-gallery.md) göz atın

>[!NOTE]
> Eklenti kodu, görüntüleyicisinin oynatıcı deneyimi süresince DOM 'daki öğeleri dinamik olarak değiştirir, Player 'ın kaynak kodunda hiçbir şekilde kalıcı değişiklik yapmaz. Bu, tarayıcınızın Geliştirici araçlarının anlaşıldığının yararlı olduğu yerdir. Örneğin, Player 'daki bir öğenin görünümünü değiştirmek isterseniz, kendi HTML öğesini sınıf adına göre bulabilir ve ardından buradan öznitelikleri ekleyebilir veya değiştirebilirsiniz. [HTML özniteliklerini değiştirirken](http://www.w3schools.com/js/js_htmldom_html.asp) harika bir kaynak aşağıda verilmiştir.

### <a name="integrated-plugins"></a>Tümleşik eklentiler ###

 Şu anda AMP: [zaman ipucu](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) ve [kısayol tuşlarının](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)bulunduğu iki eklenti vardır. Bu eklentiler, başlangıçta Player için modüler eklentiler olacak şekilde geliştirilmiştir ancak artık oynatıcı kaynak koduna eklenmiştir.

### <a name="plugin-gallery"></a>Eklenti Galerisi ###

[Eklenti galerisinde](https://aka.ms/ampplugins) , topluluğun zaman çizgisi işaretçileri, yakınlaştırma, analiz ve daha fazlası gibi özellikler için zaten katkıda bulunduğu çeşitli eklentileri vardır. Sayfa, eklentiye ve bu eklentinin eylemde bulunduğu bir tanıtıma yönelik olarak nasıl ayarlanalınacağını ve talimatlarına erişim sağlar. Galerimize dahil etmeniz gereken güzel bir eklenti oluşturursanız, bunları kullanıma sunmamız için gönderebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
