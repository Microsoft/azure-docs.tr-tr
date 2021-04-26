---
title: Hızlı başlangıç Azure Media Player
description: Azure Media Player ayarlamak için temel adımları öğrenin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/05/2021
ms.openlocfilehash: a6fd603318a25e15d1d4dcc1e3eaf75f96fc5ade
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448636"
---
# <a name="azure-media-player-quickstart"></a>Azure Media Player hızlı başlangıcı
Azure Media Player kolayca ayarlanabilir. Azure Media Services hesabınızdan medya içeriğinin temel oynatımını almak yalnızca birkaç dakika sürer. Bu bölümde ayrıntılara girilmeden temel adımlar anlatılmaktadır. Aşağıdaki bölümlerde Azure Media Player ayarlama ve yapılandırma hakkında size ilişkin ayrıntılar verilmektedir.  Aşağıdakileri belgenizin `<head>` bölümüne eklemeniz yeterlidir:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Bu,  `latest` isteğe bağlı olarak değişikliğe tabi olduğu için üretimde sürümü kullanmamalısınız. `latest`Azure Media Player bir sürümüyle değiştirin; örneğin `latest` , ile değiştirin `1.0.0` . Azure Media Player sürümler [buradan](https://amp.azure.net/libs/amp/latest/docs/changelog.html)sorgulanabilir.

## <a name="use-the-video-element"></a>Video öğesini kullanma

Ardından, `<video>` öğeyi normalde yaptığınız gibi, ancak herhangi bir seçeneği içeren ek bir öznitelikle kullanmanız yeterlidir `data-setup` . Bu seçenekler, geçerli bir JSON nesnesinde herhangi bir Azure Media Services seçeneği içerebilir.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Otomatik kurulum 'u kullanmak istemiyorsanız, `data-setup` özniteliği atlayabilir ve bir video öğesini el ile başlatabilirsiniz.

```javascript
    var myPlayer = amp('vid1', { /* Options */
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
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Sonraki adımlar ##

- [Azure Media Player tam kurulum](./azure-media-player-full-setup.md)
