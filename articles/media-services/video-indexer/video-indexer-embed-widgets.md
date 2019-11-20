---
title: Video Indexer pencere öğelerini uygulamalarınıza ekleyin
titleSuffix: Azure Media Services
description: Uygulamanıza Video Indexer pencere öğeleri eklemeyi öğrenin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 99d6647ab5e7fa8f35cef883dd00ae9fea866370
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839113"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Video Indexer pencere öğelerini uygulamalarınıza ekleyin

Bu makalede, uygulamalarınızda Video Indexer pencere öğelerini nasıl katıştırabileceğinizi gösterir. Video Indexer uygulamanıza üç tür pencere katıştırmayı destekler: bilişsel *Öngörüler*, *oynatıcı*ve *Düzenleyici*. 

Sürüm 2 ' den başlayarak pencere öğesi temel URL 'SI, belirtilen hesabın bölgesini içerir. Örneğin, Batı ABD bölgesindeki bir hesap oluşturulur: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Pencere öğesi türleri

### <a name="cognitive-insights-widget"></a>Bilişsel İçgörüler pencere öğesi

Bilişsel Öngörüler pencere öğesi, video dizin oluşturma sürecinizden ayıklanan tüm görsel öngörüleri içerir. Bilişsel Öngörüler pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler.

|Ad|Tanım|Açıklama|
|---|---|---|
|`widgets`|Virgülle ayrılmış dizeler|, İşlemek istediğiniz öngörüleri denetlemenize olanak tanır. <br/> Örnek: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` yalnızca kişiler ve marka Kullanıcı arabirimi öngörülerini işler.<br/>Mevcut seçenekler: people (kişiler), keywords (anahtar sözcükler), annotations (ek açıklamalar), brands (markalar), sentiments (yaklaşımlar), transcript (transkript), search (arama).<br/>`widgets` URL parametresinin sürüm 2 ' de desteklenmediğini unutmayın.<br/>|
|`locale`|Kısa bir dil kodu|Öngörüler dilini denetler. Varsayılan değer `en` ' dır. <br/> Örnek: `locale=de`.|
|`tab`|Varsayılan seçili sekme|Varsayılan olarak işlenen **Öngörüler** sekmesini denetler. <br/> Örnek: `tab=timeline`, öngörüleri **zaman çizelgesi** sekmesi seçili olarak işler.|

### <a name="player-widget"></a>Yürütücü pencere öğesi

Video akışını Uyarlamalı bit hızı kullanarak akışa almak için oynatıcı pencere öğesini kullanabilirsiniz. Oynatıcı pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler.

|Ad|Tanım|Açıklama|
|---|---|---|
|`t`|Başlangıçtan itibaren saniye|Player 'ın belirtilen zaman noktasından yürütmeye başlamasını sağlar.<br/> Örnek: `t=60`.|
|`captions`|Dil kodu|Resim **yazıları** menüsünde kullanılabilir olacak pencere öğesi yükleme sırasında, belirtilen dilde resim yazısını getirir.<br/> Örnek: `captions=en-US`.|
|`showCaptions`|Boole değeri|Yürütücünün etkin olan açıklamalı alt yazıları yüklemesini sağlar.<br/> Örnek: `showCaptions=true`.|
|`type`||Bir ses oynatıcı kaplamasını etkinleştirir (video bölümü kaldırılır).<br/> Örnek: `type=audio`.|
|`autoplay`|Boole değeri|Player 'ın yüklendiğinde videoyu oynatmaya başlaması gerekip gerekmediğini gösterir. Varsayılan değer `true` ' dır.<br/> Örnek: `autoplay=false`.|
|`language`|Dil kodu|Oynatıcı dilini denetler. Varsayılan değer `en-US` ' dır.<br/>Örnek: `language=de-DE`.|

### <a name="editor-widget"></a>Düzenleyici pencere öğesi

Yeni projeler oluşturmak ve bir videonun öngörülerini yönetmek için düzenleyici pencere öğesini kullanabilirsiniz. Düzenleyici pencere öğesi, aşağıdaki isteğe bağlı URL parametrelerini destekler.

|Ad|Tanım|Açıklama|
|---|---|---|
|`accessToken`<sup>*</sup>|Dize|Yalnızca pencere öğesini katıştırmak için kullanılan hesapta olan videolara erişim sağlar.<br> Düzenleyici pencere öğesi `accessToken` parametresi gerektiriyor.|
|`language`|Dil kodu|Oynatıcı dilini denetler. Varsayılan değer `en-US` ' dır.<br/>Örnek: `language=de-DE`.|
|`locale`|Kısa bir dil kodu|Öngörüler dilini denetler. Varsayılan değer `en` ' dır.<br/>Örnek: `language=de`.|

<sup>*</sup> Sahip `accessToken` dikkatli bir şekilde sağlamalıdır.

## <a name="embedding-public-content"></a>Genel içerik ekleme

1. [Video Indexer](https://www.videoindexer.ai/) Web sitesinde oturum açın.
2. Birlikte çalışmak istediğiniz videoyu seçin.
3. Videonun altında görünen **Ekle** düğmesini seçin.

    ![Pencere öğesi](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    **Ekle** düğmesini seçtikten sonra, uygulamanıza eklemek istediğiniz pencere öğesini seçebilirsiniz. 
4. İstediğiniz pencere öğesi türünü seçin (bilişsel**Öngörüler**, **oynatıcı**veya **Düzenleyici**).
 
5. Ekleme kodunu kopyalayın ve uygulamanıza ekleyin. 

    ![Pencere öğesi](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Video URL 'larınızı paylaşma sorunlarınız varsa, bağlantıya `location` parametresini ekleyin. Parametresi, [video Indexer var olan Azure bölgelerine](regions.md)ayarlanmalıdır. Örneğin: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Özel içerik ekleme

Özel bir video eklemek için iframe 'nin **src** özniteliğinde bir erişim belirteci geçirmeniz gerekir:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Bilişsel Öngörüler pencere öğesi içeriğini almak için aşağıdakilerden birini kullanın:<br/>
- [Öngörüler al pencere öğesi](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API 'si.<br/>
- [Video erişim belirteci al](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). URL 'ye bir sorgu parametresi olarak ekleyin. Daha önce gösterildiği gibi bu URL 'YI iframe için **src** değeri olarak belirtin.

Katıştırılmış pencere öğesinde içgörüler özelliklerini düzenlemenizi sağlamak için, Düzenle izinlerini içeren bir erişim belirteci geçirmeniz gerekir. [Öngörüler al pencere öğesini](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) kullanın veya `&allowEdit=true`[Görüntülü erişim belirteci alın](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) . 

## <a name="widgets-interaction"></a>Pencere öğeleri etkileşimi

Bilişsel Öngörüler pencere öğesi, uygulamanızdaki bir videoyla etkileşime geçebilir. Bu bölümde, bu etkileşimi nasıl elde sağlayabileceğiniz gösterilmektedir.

![Pencere öğesi](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Kaynak noktalar arası iletişim

Diğer bileşenlerle iletişim kurmak üzere Video Indexer pencere öğeleri almak için Video Indexer hizmeti:

- , Çıkış noktaları arası iletişim HTML5 yöntemi **PostMessage**kullanır. 
- İletiyi VideoIndexer.ai kaynağında doğrular. 

Kendi oynatıcı kodunuzu uygular ve bilişsel Öngörüler pencere öğeleri ile tümleştirirseniz, VideoIndexer.ai adresinden gelen iletinin kaynağını doğrulamak sizin sorumluluğunuzdadır.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Uygulamanıza veya blogunuza pencere öğeleri ekleme (önerilir) 

Bu bölümde, bir Kullanıcı uygulamanızda öngörü denetimini seçtiğinde Player ilgili bir süre atdığında, iki Video Indexer pencere öğesi arasında nasıl etkileşim elde edilecek gösterilmektedir.

1. Oynatıcı pencere öğesi ekleme kodunu kopyalayın.
2. Bilişsel Öngörüler ekleme kodunu kopyalayın.
3. İki pencere öğesi arasındaki iletişimi işlemek için [Mediator dosyasını](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) ekleyin:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Artık bir Kullanıcı uygulamanızda Insight denetimini seçtiğinde, Player ilgili bir süre atlar.

Daha fazla bilgi için bkz. [video Indexer-her Iki pencere öğesini ekleme tanıtımı](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bilişsel İçgörüler pencere öğesini ekleyip içeriği oynatmak için Azure Media Player'ı kullanma

Bu bölümde, bir bilişsel Öngörüler pencere öğesi ve bir Azure Media Player örneği ile [amp eklentisini](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)kullanarak nasıl etkileşim elde edilecek gösterilmektedir.
 
1. AMP Player için Video Indexer eklentisi ekleme:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Video Indexer eklentisiyle Azure Media Player örneğini oluşturun.

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
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Bilişsel Öngörüler ekleme kodunu kopyalayın.

Artık Azure Media Player ile iletişim kurabiliyor olmanız gerekir.

Daha fazla bilgi için [Azure Media Player + VI Öngörüler tanıtımı](https://codepen.io/videoindexer/pen/rYONrO)bölümüne bakın.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Video Indexer bilişsel Öngörüler pencere öğesini katıştırma ve farklı bir video oynatıcı kullanma

Azure Media Player dışında bir video oynatıcı kullanıyorsanız, iletişim sağlamak için video yürütücüyü el ile işlemeniz gerekir. 

1. Video oynatıcınızı ekleyin.

    Örneğin, standart bir HTML5 oynatıcı:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Bilişsel İçgörüler pencere öğesini ekleyin.
3. "İleti" olayını dinleyerek yürütücünüz için iletişimi uygulayın. Örneğin:

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

Daha fazla bilgi için [Azure Media Player + VI Öngörüler tanıtımı](https://codepen.io/videoindexer/pen/YEyPLd)bölümüne bakın.

## <a name="adding-subtitles"></a>Alt yazı ekleme

Kendi [Azure Media Player](https://aka.ms/azuremediaplayer)video Indexer öngörüleri eklerseniz, Kapalı açıklamalı alt yazıları (alt yazılar) almak Için **getvtturl** yöntemini kullanabilirsiniz. Ayrıca, Video Indexer AMP eklenti **Getsubtitlesurl** 'Den bir JavaScript yöntemi çağırabilirsiniz (daha önce gösterildiği gibi). 

## <a name="customizing-embeddable-widgets"></a>Eklenebilir pencere öğelerini özelleştirme

### <a name="cognitive-insights-widget"></a>Bilişsel İçgörüler pencere öğesi

İstediğiniz öngörü türlerini seçebilirsiniz. Bunu yapmak için, bunları aldığınız ekleme koduna (API 'den veya Web uygulamasından) eklenen aşağıdaki URL parametresine bir değer olarak belirtin: `&widgets=<list of wanted widgets>`.

Olası değerler şunlardır: **kişiler**, **anahtar sözcükler**, **yaklaşım, döküm**ve **arama**.

Örneğin, yalnızca kişiler ve arama öngörüleri içeren bir pencere öğesi eklemek istiyorsanız iframe ekleme URL 'SI şöyle görünür:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

İframe penceresinin başlığı, iframe URL 'sine `&title=<YourTitle>` sağlayarak de özelleştirilebilir. (HTML \<başlığını > değeri) özelleştirir.
    
Örneğin, iframe pencerenize "Myınsights" başlığına izin vermek istiyorsanız URL şöyle görünür:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Bu seçeneğin yalnızca içgörülerin yeni bir pencerede açılacağı durumlarda kullanılabileceğine dikkat edin.

### <a name="player-widget"></a>Yürütücü pencere öğesi

Video Indexer yürütücüsünü eklerseniz iframe boyutunu belirterek yürütücü boyutunu seçebilirsiniz.

Örneğin:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Varsayılan olarak, Video Indexer oynatıcı videonun dökümünü temel alan, otomatik olarak oluşturulan kapalı açıklamalı alt yazılar içerir. Döküm, video karşıya yüklendiğinde seçilen kaynak dili ile videodan ayıklanır.

Farklı bir dille gömmek istiyorsanız, ekleme oynatıcı URL 'sine `&captions=< Language | "all" | "false" >` ekleyebilirsiniz. Tüm kullanılabilir dillerin resim yazılarında açıklamalı alt yazıların olmasını istiyorsanız `all`değerini kullanın. Başlıkların varsayılan olarak görüntülenmesini istiyorsanız `&showCaptions=true`geçirebilirsiniz.

Ekleme URL 'SI şu şekilde görünür: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Açıklamalı alt yazıları devre dışı bırakmak istiyorsanız, `captions` parametre değerini `false`olarak geçirebilirsiniz.

#### <a name="autoplay"></a>Yürüt
Oynatıcı, varsayılan olarak videoyu oynatmaya başlayacaktır. `&autoplay=false` önceki ekleme URL 'sine geçirmekten seçim yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Video Indexer öngörülerini görüntüleme ve düzenleme hakkında daha fazla bilgi için bkz. [video Indexer öngörülerini görüntüleme ve düzenleme](video-indexer-view-edit.md).

Ayrıca, [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)öğesine göz atın.
