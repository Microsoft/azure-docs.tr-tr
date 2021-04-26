---
title: Açık kaynak medya çerçevesi için Kesintisiz Akış eklentisi
description: Adobe açık kaynak medya çerçevesi için Azure Media Services Kesintisiz Akış eklentisini nasıl kullanacağınızı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e41e33b5f30cd861ae8715e0ad7f6694f8854198
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046955"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Adobe açık kaynak medya çerçevesi için Microsoft Kesintisiz Akış eklentisini kullanma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış
Açık kaynak medya çerçevesi 2,0 (OSMF için SS) için Microsoft Kesintisiz Akış eklentisi OSMF 'nin varsayılan yeteneklerini genişletir ve yeni ve var olan OSMF oynatıcılara Microsoft Kesintisiz Akış içeriğini kayıttan yürütmeyi ekler. Eklenti, Ayrıca medya kayıttan yürütmeyi (SMP) Kesintisiz Akış kayıttan yürütme özellikleri de ekler.

OSMF için SS, eklentinin iki sürümünü içerir:

* OSMF (. SWC) için statik Kesintisiz Akış eklentisi
* OSMF için dinamik Kesintisiz Akış eklentisi (. swf)

Bu belge, okuyucunun OSMF ve OSMF eklentileriyle ilgili genel çalışma bilgisine sahip olduğunu varsayar. OSMF hakkında daha fazla bilgi için lütfen resmi OSMF sitesindeki belgelere bakın.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>OSMF 2,0 için Kesintisiz Akış eklentisi
Eklenti, isteğe bağlı Kesintisiz Akış içeriğini aşağıdaki özelliklerle yüklemeyi ve kayıttan yürütmeyi destekler:

* İsteğe bağlı Kesintisiz Akış kayıttan yürütme (Oynat, Duraklat, ara, Durdur)
* Canlı Kesintisiz Akış kayıttan yürütme (Play)
* Canlı DVR işlevleri (duraklatma, arama, DVR kayıttan yürütme, canlı go)
* Video codec bileşenleri için destek-H.
* Ses codec bileşenleri için destek-AAC
* OSMF yerleşik API 'Leri ile birden çok ses dili değiştirme
* OSMF yerleşik API 'Leri ile en fazla kayıttan yürütme kalitesi seçimi
* OSMF açıklamalı alt yazıları eklentisi ile dışarıdan arabası kapalı açıklamalı altyazı
* Adobe &reg; Flash &reg; Player 11,4 veya üzeri.
* Bu sürüm yalnızca OSMF 2,0 ' i destekler.

## <a name="supported-features-and-known-issues"></a>Desteklenen özellikler ve bilinen sorunlar
Desteklenen özelliklerin tam listesi, desteklenmeyen özellikler ve bilinen sorunlar için [Bu belgeye](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)bakın.

## <a name="loading-the-plugin"></a>Eklenti yükleniyor
OSMF eklentileri statik olarak (derleme zamanında) veya dinamik olarak (çalışma zamanında) yüklenebilir. OSMF indirmesi için Kesintisiz Akış eklentisi hem dinamik hem de statik sürümleri içerir.

* Statik yükleme: statik olarak yüklemek Için bir statik kitaplık (SWC) dosyası gereklidir. Statik Eklentiler, projeye bir başvuru olarak eklenir ve derleme zamanında son çıktı dosyası içinde birleştirilir.
* Dinamik yükleme: dinamik olarak yüklemek Için önceden derlenmiş (SWF) bir dosya gereklidir. Dinamik eklentiler çalışma zamanına yüklenir ve proje çıktısına dahil edilmez. (Derlenmiş çıkış) Dinamik Eklentiler, HTTP ve dosya protokolleri kullanılarak yüklenebilir.

Statik ve dinamik yükleme hakkında daha fazla bilgi için bkz. resmi [OSMF eklentisi sayfası](https://www.ibm.com/support/knowledgecenter/SSLTBW_2.3.0/com.ibm.zos.v2r3.izua300/IZUHPINFO_PluginsPlanning.htm).

### <a name="ss-for-osmf-static-loading"></a>OSMF statik yükleme için SS
Aşağıdaki kod parçacığı, OSMF için SS eklentisinin statik olarak nasıl yükleneceğini ve OSMF MediaFactory sınıfını kullanarak temel bir video oynamasını göstermektedir. OSMF koduna yönelik SS 'yi eklemeden önce lütfen proje başvurusunun "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC" statik eklentisini içerdiğinden emin olun. 

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>OSMF dinamik yüklemesi için SS
Aşağıdaki kod parçacığı, OSMF için SS eklentisinin dinamik olarak nasıl yükleneceğini ve OSMF MediaFactory sınıfını kullanarak temel bir video oynamasını göstermektedir. OSMF koduna yönelik SS 'yi eklemeden önce, dosya protokolünü kullanarak yüklemek istiyorsanız veya HTTP yükü için bir Web sunucusu altına kopyalamak istiyorsanız, "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" dinamik eklentisini proje klasörüne kopyalayın. Proje başvurularına "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC" eklemeniz gerekmez.

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>SS ODMF Dynamic eklentisi ile medya kayıttan yürütme
OSMF dinamik eklentisi için Kesintisiz Akış, STO [medya kayıttan yürütme (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/)ile uyumludur. SMP 'e Kesintisiz Akış içerik yürütme eklemek için OSMF eklentisi için SS kullanabilirsiniz. Bunu yapmak için, aşağıdaki adımları kullanarak HTTP yükü için bir Web sunucusu altına "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" öğesini kopyalayın:

1. [Stroz medya kayıttan yürütme kurulum sayfasına](http://www.koopman.me/bob3/setup.html)gözatamazsınız. 
2. Src 'yi bir Kesintisiz Akış kaynağına ayarlayın (örn. http: \/ /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. İstenen yapılandırma değişikliklerini yapın ve önizleme ve güncelleştirme ' ye tıklayın.
   
   **Göz önünde** İçerik Web sunucunuzun geçerli bir crossdomain.xml olması gerekir. 
4. Aşağıdaki örnekte olduğu gibi, en sevdiğiniz metin düzenleyicisini kullanarak kodu kopyalayıp basit bir HTML sayfasına yapıştırın:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Ekleme koduna Kesintisiz Akış OSMF eklentisi ekleyin ve kaydedin.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. HTML sayfanızı kaydedin ve bir Web sunucusuna yayımlayın. En sevdiğiniz Flash &reg; Player etkin Internet tarayıcısını (Internet Explorer, Chrome, Firefox vb.) kullanarak yayınlanan web sayfasına gidin.
3. Adobe Flash Player içindeki Kesintisiz Akış içeriğin keyfini çıkarın &reg; &reg; .

Genel OSMF geliştirme hakkında daha fazla bilgi için lütfen resmi [OSMF geliştirme sayfasına](https://www.adobe.com/devnet/video/articles1/osmf_overview.html)bakın.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[OSMF güncelleştirmesi için Microsoft Uyarlamalı Akış eklentisi](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

