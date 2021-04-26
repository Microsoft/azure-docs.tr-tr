---
title: Video Indexer pencere öğelerini uygulamalarınıza ekleyin
titleSuffix: Azure Media Services
description: Video Indexer pencere öğelerini uygulamalarınıza eklemeyi öğrenin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532884"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Video Indexer pencere öğelerini uygulamalarınıza ekleyin

Bu makalede, uygulamalarınıza Video Indexer pencere öğelerini nasıl katıştırabileceğinizi gösterir. Video Indexer uygulamalarınıza üç tür pencere öğesi eklemeyi destekler: *Bilişsel İçgörüler*, *Yürütücü* ve *Düzenleyici*.

Sürüm 2 ' den başlayarak pencere öğesi temel URL 'SI, belirtilen hesabın bölgesini içerir. Örneğin, Batı ABD bölgesindeki bir hesap şunları oluşturur: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Pencere öğesi türleri

### <a name="cognitive-insights-widget"></a>Bilişsel İçgörüler pencere öğesi

Bilişsel İçgörüler pencere öğeleri, video dizini oluşturma işleminizden elde edilen tüm görsel içgörüleri içerir. Bilişsel Öngörüler pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler:

|Name|Tanım|Description|
|---|---|---|
|`widgets` | Virgülle ayrılmış dizeler | , İşlemek istediğiniz öngörüleri denetlemenize olanak tanır.<br/>Örnek: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` yalnızca kişiler ve anahtar sözcükler UI öngörülerini işler.<br/>Kullanılabilir seçenekler: kişiler, animatedCharacters, anahtar sözcükler, Etiketler, yaklaşım, konu başlıkları, konular, ana kareler, döküm, OCR, hoparlörler, sahneler ve namedEntities.|
|`controls`|Virgülle ayrılmış dizeler|İşlemek istediğiniz denetimleri denetlemenizi sağlar.<br/>Örnek: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` yalnızca arama seçeneğini işler ve İndir düğmesi.<br/>Kullanılabilir seçenekler: arama, indirme, Önayarlar, dil.|
|`language`|Kısa bir dil kodu (dil adı)|Öngörüler dilini denetler.<br/>Örnek: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/> veya `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Kısa bir dil kodu | Kullanıcı arabiriminin dilini denetler. `en` varsayılan değerdir. <br/>Örnek: `locale=de`.|
|`tab` | Varsayılan seçili sekme | Varsayılan olarak işlenen **Öngörüler** sekmesini denetler. <br/>Örnek: `tab=timeline` görüş **zaman çizelgesi** sekmesi seçiliyken öngörüleri işler.|
|`location` ||`location`Parametrenin gömülü bağlantılara dahil olması gerekir, bkz. [bölgenizin adını alma](regions.md). Hesabınız önizlemededir, `trial` konum değeri için kullanılmalıdır. `trial` , parametresi için varsayılan değerdir `location` .| 

### <a name="player-widget"></a>Yürütücü pencere öğesi

Video akışını Uyarlamalı bit hızı kullanarak akışa almak için oynatıcı pencere öğesini kullanabilirsiniz. Oynatıcı pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler.

|Name|Tanım|Description|
|---|---|---|
|`t` | Başlangıçtan itibaren saniye | Player 'ın belirtilen zaman noktasından yürütmeye başlamasını sağlar.<br/> Örnek: `t=60`. |
|`captions` | Dil kodu | Resim **yazıları** menüsünde kullanılabilir olacak pencere öğesi yükleme sırasında, belirtilen dilde resim yazısını getirir.<br/> Örnek: `captions=en-US`. |
|`showCaptions` | Boole değeri | Yürütücünün etkin olan açıklamalı alt yazıları yüklemesini sağlar.<br/> Örnek: `showCaptions=true`. |
|`type`| | Bir ses oynatıcı kaplamasını etkinleştirir (video bölümü kaldırılır).<br/> Örnek: `type=audio`. |
|`autoplay` | Boole değeri | Player 'ın yüklendiğinde videoyu oynatmaya başlaması gerekip gerekmediğini gösterir. `true` varsayılan değerdir.<br/> Örnek: `autoplay=false`. |
|`language`/`locale` | Dil kodu | Oynatıcı dilini denetler. `en-US` varsayılan değerdir.<br/>Örnek: `language=de-DE`.|
|`location` ||`location`Parametrenin gömülü bağlantılara dahil olması gerekir, bkz. [bölgenizin adını alma](regions.md). Hesabınız önizlemededir, `trial` konum değeri için kullanılmalıdır. `trial` , parametresi için varsayılan değerdir `location` .| 

### <a name="editor-widget"></a>Düzenleyici pencere öğesi

Yeni projeler oluşturmak ve bir videonun öngörülerini yönetmek için düzenleyici pencere öğesini kullanabilirsiniz. Düzenleyici pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler.

|Name|Tanım|Description|
|---|---|---|
|`accessToken`<sup>*</sup> | Dize | Yalnızca pencere öğesini katıştırmak için kullanılan hesapta olan videolara erişim sağlar.<br> Düzenleyici pencere öğesi için `accessToken` parametre gereklidir. |
|`language` | Dil kodu | Oynatıcı dilini denetler. `en-US` varsayılan değerdir.<br/>Örnek: `language=de-DE`. |
|`locale` | Kısa bir dil kodu | Öngörüler dilini denetler. `en` varsayılan değerdir.<br/>Örnek: `language=de`. |
|`location` ||`location`Parametrenin gömülü bağlantılara dahil olması gerekir, bkz. [bölgenizin adını alma](regions.md). Hesabınız önizlemededir, `trial` konum değeri için kullanılmalıdır. `trial` , parametresi için varsayılan değerdir `location` .| 

<sup>*</sup>Sahibin dikkatli sağlaması gerekir `accessToken` .

## <a name="embed-videos"></a>Videoları ekleme

Bu bölümde [, portalı kullanarak](#the-portal-experience) veya [URL 'yi uygulamalara el ile](#assemble-the-url-manually) bağlayarak video ekleme ele alınmaktadır. 

`location`Parametrenin gömülü bağlantılara dahil olması gerekir, bkz. [bölgenizin adını alma](regions.md). Hesabınız önizlemededir, `trial` konum değeri için kullanılmalıdır. `trial` , parametresi için varsayılan değerdir `location` . Örneğin: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>Portal deneyimi

Video eklemek için, portalı aşağıda açıklandığı gibi kullanın:

1. [Video Indexer](https://www.videoindexer.ai/) Web sitesinde oturum açın.
1. Birlikte çalışmak istediğiniz videoyu seçip **oynat**' a basın.
1. İstediğiniz pencere öğesi türünü seçin (bilişsel **Öngörüler**, **oynatıcı** veya **Düzenleyici**).
1. **&lt; / &gt; Ekle**' ye tıklayın.
5. Ekleme kodunu kopyalayın ( **Katıştırılmış kodu** **paylaşma & Ekle** iletişim kutusunda görünür).
6. Uygulamanıza kodu ekleyin.

> [!NOTE]
> **Oynatıcı** veya **Öngörüler** pencere öğesi için bir bağlantının paylaşılması, erişim belirtecini içerir ve hesabınıza salt okuma izinleri verir.

### <a name="assemble-the-url-manually"></a>URL 'YI el ile birleştirin

#### <a name="public-videos"></a>Genel videolar

URL 'YI şu şekilde derleyerek genel videoları ekleyebilirsiniz:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Özel videolar

Özel bir video eklemek için bir erişim belirteci geçirmeniz gerekir (IFRAME özniteliğinde [video erişim belirtecini al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) ' ı kullanın) `src` .

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Öngörüleri Düzenle özelliklerini sağlama

Katıştırılmış pencere öğesinde içgörüler özelliklerini düzenlemenizi sağlamak için, Düzenle izinlerini içeren bir erişim belirteci geçirmeniz gerekir. İle [Görüntülü erişim belirteci al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) ' i kullanın `&allowEdit=true` .

## <a name="widgets-interaction"></a>Pencere öğeleri etkileşimi

Bilişsel Öngörüler pencere öğesi, uygulamanızdaki bir videoyla etkileşime geçebilir. Bu bölümde, bu etkileşimi nasıl elde sağlayabileceğiniz gösterilmektedir.

![Bilişsel Öngörüler pencere öğesi Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Akışa genel bakış

Dökümü düzenlerken, aşağıdaki akış oluşur:

1. TRANSCRIPT, zaman çizelgesinde düzenlenir.
1. Video Indexer, bu güncelleştirmeleri alır ve bunları dil modelindeki [TRANSCRIPT düzenlemelerine](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) kaydeder.
1. Resim yazıları güncelleştirilir:

    * Video Indexer oynatıcı pencere öğesi kullanıyorsanız, otomatik olarak güncelleştirilir.
    * Bir dış oynatıcı kullanıyorsanız, yeni bir resim yazısı dosyası kullanıcısına **video resim yazılarını al** araması yapmanız gerekir.

### <a name="cross-origin-communications"></a>Kaynak noktalar arası iletişim

Diğer bileşenlerle iletişim kurmak üzere Video Indexer pencere öğeleri almak için Video Indexer hizmeti:

- , Çıkış noktaları arası iletişim HTML5 yöntemini kullanır `postMessage` .
- İletiyi VideoIndexer.ai kaynağında doğrular.

Kendi oynatıcı kodunuzu uygular ve bilişsel Öngörüler pencere öğeleri ile tümleştirirseniz, VideoIndexer.ai adresinden gelen iletinin kaynağını doğrulamak sizin sorumluluğunuzdadır.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Uygulamanıza veya blogunuza pencere öğeleri ekleme (önerilir)

Bu bölümde, bir Kullanıcı uygulamanızda öngörü denetimini seçtiğinde Player ilgili bir süre atdığında, iki Video Indexer pencere öğesi arasında nasıl etkileşim elde edilecek gösterilmektedir.

1. Yürütücü pencere öğesi ekleme kodunu kopyalayın.
2. Bilişsel İçgörüler ekleme kodunu kopyalayın.
3. İki pencere öğesi arasındaki iletişimi işlemek için [Aracı dosyasını](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ekleyin:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Artık bir Kullanıcı uygulamanızda Insight denetimini seçtiğinde, Player ilgili bir süre atlar.

Daha fazla bilgi için bkz. [video Indexer-her Iki pencere öğesini ekleme tanıtımı](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bilişsel İçgörüler pencere öğesini ekleyip içeriği oynatmak için Azure Media Player'ı kullanma

Bu bölümde, bir bilişsel Öngörüler pencere öğesi ve bir Azure Media Player örneği ile [amp eklentisini](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)kullanarak nasıl etkileşim elde edilecek gösterilmektedir.

1. AMP Player için Video Indexer eklentisi ekleme:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Video Indexer eklentisiyle Azure Media Player örneğini oluşturun.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Bilişsel İçgörüler ekleme kodunu kopyalayın.

Artık Azure Media Player ile iletişim kurabilirsiniz.

Daha fazla bilgi için [Azure Media Player + VI Öngörüler tanıtımı](https://codepen.io/videoindexer/pen/rYONrO)bölümüne bakın.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Video Indexer bilişsel Öngörüler pencere öğesini katıştırma ve farklı bir video oynatıcı kullanma

Azure Media Player dışında bir video oynatıcı kullanıyorsanız, iletişim sağlamak için video yürütücüyü el ile işlemeniz gerekir.

1. Video oynatıcınızı ekleyin.

    Örneğin, standart bir HTML5 oynatıcı:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Bilişsel İçgörüler pencere öğesini ekleyin.
3. "İleti" olayını dinleyerek yürütücünüz için iletişimi uygulayın. Örnek:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Daha fazla bilgi için [Azure Media Player + VI Öngörüler tanıtımı](https://codepen.io/videoindexer/pen/YEyPLd)bölümüne bakın.

## <a name="adding-subtitles"></a>Alt yazı ekleme

Kendi [Azure Media Player](https://aka.ms/azuremediaplayer)video Indexer öngörüleri eklerseniz, `GetVttUrl` Kapalı açıklamalı alt yazıları (alt yazılar) almak için yöntemini kullanabilirsiniz. Ayrıca, Video Indexer AMP eklentisinden bir JavaScript yöntemi çağırabilirsiniz `getSubtitlesUrl` (daha önce gösterildiği gibi).

## <a name="customizing-embeddable-widgets"></a>Eklenebilir pencere öğelerini özelleştirme

### <a name="cognitive-insights-widget"></a>Bilişsel İçgörüler pencere öğesi

İstediğiniz öngörü türlerini seçebilirsiniz. Bunu yapmak için, bunları aldığınız ekleme koduna (API 'den veya Web uygulamasından) eklenen aşağıdaki URL parametresine bir değer olarak belirtin: `&widgets=<list of wanted widgets>` .

Olası değerler şunlardır: `people` , `animatedCharacters` ,, `keywords` , `labels` , `sentiments` `emotions` , `topics` , `keyframes` , `transcript` , `ocr` , `speakers` , `scenes` , ve `namedEntities` .

Örneğin, yalnızca kişiler ve anahtar sözcük öngörüleri içeren bir pencere öğesi eklemek istiyorsanız iframe ekleme URL 'SI şöyle görünür:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

İframe penceresinin başlığı `&title=<YourTitle>` iframe URL 'si sağlanarak da özelleştirilebilir. (HTML `<title>` değerini özelleştirir).
   
Örneğin, iframe pencerenize "Myınsights" başlığına izin vermek istiyorsanız URL şöyle görünür:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Bu seçeneğin yalnızca içgörülerin yeni bir pencerede açılacağı durumlarda kullanılabileceğine dikkat edin.

### <a name="player-widget"></a>Yürütücü pencere öğesi

Video Indexer yürütücüsünü eklerseniz iframe boyutunu belirterek yürütücü boyutunu seçebilirsiniz.

Örnek:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Video Indexer oynatıcı varsayılan olarak videonun dökümünü temel alan kapalı açıklamalı alt yazıları içerir. Döküm, video karşıya yüklendiğinde seçilen kaynak dili ile videodan ayıklanır.

Farklı bir dille gömmek istiyorsanız, ekleme `&captions=<Language Code>` oynatıcı URL 'sine ekleyebilirsiniz. Açıklamalı alt yazıların varsayılan olarak görüntülenmesini istiyorsanız, &Showaçıklamalı altyazıları = true olarak geçirebilirsiniz.

Ekleme URL 'SI şu şekilde görünür:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Yürüt

Oynatıcı, varsayılan olarak videoyu oynatmaya başlayacaktır. önceki ekleme URL 'sine geçirilerek seçim yapabilirsiniz `&autoplay=false` .

## <a name="code-samples"></a>Kod örnekleri

Video Indexer API 'SI ve pencere öğeleri için örnekler içeren [kod örnekleri](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) deposuna bakın:

| Dosya/klasör                       | Açıklama                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Video Indexer videosunu özel bir Azure Media Player yükleyin.                        |
| `azure-media-player-vi-insights`  | Özel bir Azure Media Player VI öngörüleri ekleyin.                             |
| `control-vi-embedded-player`      | VI oynatıcı ekleyin ve dışarıdan kontrol edin.                                    |
| `custom-index-location`           | Özel bir dış konumdan VI öngörüleri ekleyin (bir blob müşterisi olabilir).     |
| `embed-both-insights`             | Hem Player hem de Öngörüler için ana VI öngörüleri kullanımı.                            |
| `embed-insights-with-AMP`         | Özel bir Azure Media Player VI Öngörüler pencere öğesi ekleyin.                      |
| `customize-the-widgets`           | Özel seçeneklere sahip VI pencere öğelerini katıştır.                                     |
| `embed-both-widgets`              | VI oynatıcı ve Öngörüler ekleyin ve bunlar arasında iletişim kurun.                      |
| `url-generator`                   | Kullanıcı tarafından belirtilen seçeneklere göre pencere öğesi özel ekleme URL 'SI oluşturur.             |
| `html5-player`                    | Varsayılan HTML5 video oynatıcı ile VI Öngörüler ekleyin.                           |

## <a name="supported-browsers"></a>Desteklenen tarayıcılar

Daha fazla bilgi için bkz. [Desteklenen tarayıcılar](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Sonraki adımlar

Video Indexer öngörülerini görüntüleme ve düzenleme hakkında daha fazla bilgi için bkz. [video Indexer öngörülerini görüntüleme ve düzenleme](video-indexer-view-edit.md).

Ayrıca, [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)öğesine göz atın.
