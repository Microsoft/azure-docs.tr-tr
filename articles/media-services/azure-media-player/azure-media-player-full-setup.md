---
title: Azure Media Player tam kurulum
description: Azure Media Player ayarlamayı öğrenin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: d7c103a7eba2e5da7ac040b50fcc0009a0b7237b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449877"
---
# <a name="azure-media-player-full-setup"></a>Eksiksiz Azure Media Player kurulumu #

Azure Media Player kolayca ayarlanabilir. Yalnızca Azure Media Services hesabınızdan medya içeriğinin temel oynatımını almak birkaç dakika sürer. [Örnek](https://github.com/Azure-Samples/azure-media-player-samples) , sürümün örnekler dizininde de sağlanır.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>1. Adım: JavaScript ve CSS dosyalarını sayfanızın baş bir kısmında ekleme ##

Azure Media Player, betiklerine CDN barındırılan sürümünden erişebilirsiniz. Genellikle JavaScript 'in yerine son gövde etiketinden önce JavaScript `<body>` `<head>` yerleştirilmesi önerilir, ancak Azure Media Player, video etiketini geçerli bir öğe olarak bilmesi IÇIN eski IE sürümlerinin başlarında olması gereken bır ' HTML5 Shiv ' içeriyor.

> [!NOTE]
> Zaten [Modernizr](https://modernizr.com/) gibi HTML5 Shiv kullanıyorsanız, Azure Media Player JavaScript 'i dilediğiniz yere ekleyebilirsiniz. Ancak Modernizr sürümünüzün video için Shiv içerdiğinden emin olun.

### <a name="cdn-version"></a>CDN sürümü ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Bu,  `latest` isteğe bağlı olarak değişikliğe tabi olduğu için üretimde sürümü kullanmamalısınız. `latest`Azure Media Player bir sürümü ile değiştirin. Örneğin, ile değiştirin `latest` `2.1.1` . Azure Media Player sürümler [buradan](https://amp.azure.net/libs/amp/latest/docs/changelog.html)sorgulanabilir.

> [!NOTE]
> Yayın sonrasında `1.2.0` , artık bu dosyanın konumu geri dönüş Techs 'ye eklemesi gerekmez (azuremediaplayer.min.js dosyasının göreli yolundan konumu otomatik olarak alır). Yukarıdaki komut dosyalarından sonra aşağıdaki betiği ekleyerek geri dönüş Techs 'nin konumunu değiştirebilirsiniz `<head>` .

> [!NOTE]
> Flash ve Silverlight eklentileri doğası nedeniyle, SWF ve XAP dosyaları herhangi bir hassas bilgi veya veri olmadan bir etki alanında barındırılmalıdır; bu, Azure CDN barındırılan sürümle sizin için otomatik olarak ele alınır.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>2. Adım: sayfanıza HTML5 video etiketi ekleme ##

Azure Media Player, bir video eklemek için HTML5 video etiketini kullanabilirsiniz. Azure Media Player, etiketi okur ve yalnızca HTML5 videosunu destekleyen tüm tarayıcılarda çalışır. Temel biçimlendirmenin ötesinde Azure Media Player birkaç ek parçaya ihtiyaç duyuyor.

1. `<data-setup>`' Deki özniteliği, `<video>` sayfa hazırlandığınızda videoyu otomatik olarak ayarlamaya ve öznitelikten herhangi bırını (JSON biçiminde) okumasına Azure Media Player söyler.
1. `id`Özniteliği: aynı sayfadaki her video için kullanılmalıdır ve benzersiz olmalıdır.
1. `class`Özniteliği iki sınıf içerir:
    - `azuremediaplayer` Azure Media Player UI işlevselliği için gerekli olan stilleri uygular
    - `amp-default-skin` HTML5 denetimlerine varsayılan kaplama uygular
1. , `<source>` Gereken iki özniteliği içerir
    - `src` öznitelik, Azure Media Services eklenen bir **. ISM/manifest* dosyası içerebilir, Azure Media Player OTOMATIK olarak Dash, sorunsuz ve HLS Için URL 'leri yürütücüye ekler
    - `type` öznitelik, akışın gerekli MIME türüdür. *". ISM/manifest"* Ile ilişkili MIME türü *"application/vnd. MS-SSTR + xml"*
1. ' Deki *isteğe bağlı* `<data-setup>` özniteliği, `<source>` Azure Media Services akışı için, şifreleme türü (AES veya PlayReady, Widevine veya Fairplay) ve Token dahil, ancak bunlarla sınırlı olmamak üzere herhangi bir benzersiz teslim ilkesi varsa Azure Media Player söyler.

HTML5 video için yaptığınız gibi öznitelikleri, ayarları, kaynakları ve parçaları tam olarak dahil edin/dışlayın.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Varsayılan olarak, büyük oynatma düğmesi sol üst köşede bulunur, böylece poster 'in ilginç kısımlarını kapsamaz. Büyük oynatma düğmesini ortalamak isterseniz, öğeye ek ekleyebilirsiniz `amp-big-play-centered` `class` `<video>` .

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Dinamik olarak yüklenen HTML için alternatif kurulum ###

Web sayfanız veya uygulamanız, video etiketini dinamik olarak (AJAX, appendChild, vb.) yüklerse, bu sayede sayfa yüklendiğinde, Data-Setup özniteliğine güvenmek yerine oynatıcıyı el ile ayarlamak isteyeceksiniz. Bunu yapmak için önce, Player 'ın başlatıldığı zaman kafa karıştırıcı olmayacak şekilde, Data-Setup özniteliğini etiketinden kaldırın. Sonra, Azure Media Player JavaScript yüklendikten sonra ve video etiketi DOM 'a yüklendikten sonra, aşağıdaki JavaScript 'ı bir kez daha çalıştırın.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

İşlevdeki ilk bağımsız değişken, `amp` video etiketinizin kimliğidir. Kendi kendinizinkini değiştirin.

İkinci bağımsız değişken bir seçenek nesnesidir. Veri kurulumu özniteliğinde yaptığınız gibi ek seçenekler ayarlamanıza olanak sağlar.

Üçüncü bağımsız değişken ' Ready ' geri çağırmasıdır. Azure Media Player başlatıldıktan sonra, bu işlevi çağırır. Ready geri aramada, ' this ' nesnesi oynatıcı örneğine başvurur.

Öğe KIMLIĞI kullanmak yerine, öğenin kendisi için de bir başvuru geçirebilirsiniz.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
