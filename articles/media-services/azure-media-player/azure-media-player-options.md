---
title: Azure Media Player seçenekleri
description: Azure Media Player ekleme kodu yalnızca HTML5 video etiketleridir, bu nedenle seçeneklerin birçoğu için standart etiket özniteliklerini kullanarak seçenekleri ayarlayabilirsiniz.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 261d4710e1c88a89c6dcef06dad430cd996b2869
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448798"
---
# <a name="options"></a>Seçenekler #

## <a name="setting-options"></a>Ayar seçenekleri ##

Azure Media Player ekleme kodu yalnızca HTML5 video etiketleridir, bu nedenle seçeneklerin birçoğu için standart etiket özniteliklerini kullanarak seçenekleri ayarlayabilirsiniz.

`<video controls autoplay ...>`

Alternatif olarak, [JSON](http://json.org/example.html) biçiminde seçenekler sağlamak için Data-Setup özniteliğini de kullanabilirsiniz. Bu Ayrıca, video etiketiyle standart olmayan seçenekleri nasıl ayarlayabileceğinizi de sağlar.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Son olarak, Player kurulumunu tetiklemek için Data-Setup özniteliğini kullanmıyorsanız, JavaScript kurulum işlevindeki ikinci bağımsız değişken olarak oynatıcı seçenekleriyle bir nesne geçirebilirsiniz.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Oluşturucudaki seçenekler, kaynak ayarlamadan önce yalnızca ilk başlatma üzerinde ayarlanır.  Aynı başlatılmış Azure Media Player öğesindeki seçenekleri değiştirmek istiyorsanız, kaynağı değiştirmeden önce seçenekleri güncelleştirmeniz gerekir. JavaScript 'teki seçenekleri kullanarak güncelleştirebilirsiniz `myPlayer.options({/*updated options*/});` . Yalnızca değiştirilen seçeneklerin etkileneceğini, diğer tüm daha önceden ayarlanan seçeneklerin kalıcı olacağını unutmayın.

## <a name="individual-options"></a>Bireysel seçenekler ##

> [!NOTE]
>Video etiketi öznitelikleri yalnızca true veya false (Boolean) olabilir, bunu açmak için özniteliği (eşittir işareti olmadan) dahil edin veya devre dışı bırakmak için hariç tutun. Örneğin, denetimleri açmak için: en `<video controls="true" ...>` `<video controls ...>` büyük sorunun en büyük olması, bu değerleri yanlış olarak doğru olarak ayarlayan (ör. Controls = "false") değer olarak false olarak ayarlamaya çalışıyor ve öznitelik hala dahil edildiğinden değeri true olarak ayartı.

### <a name="controls"></a>denetimler ###

Denetimler seçeneği, oynatıcının kullanıcının etkileşime girebileceği denetimleri içerip içermediğini ayarlar. Denetimleri olmadan, video oynatmayı başlatmak için tek yol, AutoPlay özniteliğiyle veya API aracılığıyla yapılır.

`<video controls ...>` veya `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Otomatik Kullan doğru ise, sayfa yüklendikten hemen sonra (kullanıcıdan herhangi bir etkileşim olmadan) video yürütülmeye başlayacaktır.

> [!NOTE]
> Bu seçenek Windows Phone, Apple iOS ve Android gibi mobil cihazlar tarafından desteklenmez. Mobil cihazlar, tüketicinin aylık veri planlarının kullanımını engellemek için Otomatik Kullan işlevselliğini engeller (genellikle pahalıdır). Bu durumda videoyu başlatmak için bir Kullanıcı dokunması/tıklaması gerekir.

`<video autoplay ...>`veya `{ "autoplay": true }`

### <a name="poster"></a>AF ###
Poster özniteliği, video yürütülmeye başlamadan önce görüntülenen görüntüyü ayarlar. Bu genellikle videonun veya özel bir başlık ekranının bir çerçevesidir. Kullanıcı oynat ' a tıkladığı anda görüntü çalışır.

`<video poster="myPoster.jpg" ...>` veya `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>genişlik ###

Width özniteliği videonun görüntü genişliğini ayarlar.

`<video width="640" ...>` veya `{ "width": 640 }`

### <a name="height"></a>boy ###

Height özniteliği videonun görüntüleme yüksekliğini ayarlar.

`<video height="480" ...>` veya `{ "height": 480 }`

### <a name="plugins"></a>Kaydet ###

Eklenti JSON, bu AMP örneğiyle hangi eklentilerin yüklendiğini belirler, eklentinin sahip olabileceği herhangi bir seçeneği yapılandırmanızı sağlar.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Eklenti geliştirme ve kullanımı hakkında daha fazla bilgi için bkz. eklenti [yazma](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>diğer seçenekler ###

Diğer Seçenekler `<video>` JSON alan parametresi kullanılarak etiketinde ayarlanabilir `data-setup` .
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Bu, açıkça false olarak ayarlanmıştır. Yanlış olarak ayarlayarak Azure Media Player kaplamanın platformlar arasında aynı şekilde işlenmesine izin verir.  Ayrıca, adın aksine dokunma özelliği de etkinleştirilir.

### <a name="fluid"></a>akıcı ###

Bu seçeneği gerçek video öğesine ayarlayarak, Ana kapsayıcının tam genişliği ve yüksekliği standart 16:9 en boy oranına sahip bir videoya uyacak şekilde ayarlanır.

`<video ... data-setup='{"fluid": true}'>`

`fluid` seçenek açık `width` ve ayarları geçersiz kılar `height` . Bu seçenek yalnızca Azure Media Player sürümünde `2.0.0` ve sonrasında kullanılabilir.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` seçenek, Kullanıcı için kullanılabilir playbackSpeed denetimini ve kayıttan yürütme hızı ayarlarını denetler. `playbackSpeed` bir nesnesi alır. Denetim çubuğunda kayıttan yürütme hızı denetimini etkinleştirmek için `enabled` nesnenin özelliğinin true olarak ayarlanması gerekir. Biçimlendirme sırasında kayıttan yürütme hızını etkinleştirmeye bir örnek:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`

Ayarın diğer özellikleri `playbackSpeed` nesnesine göre verilir `PlaybackSpeedOptions` .

JavaScript 'te kayıttan yürütme hızı seçeneklerini ayarlama örneği:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Bu seçenek yalnızca Azure Media Player Version 2.0.0 ve üzeri sürümlerde kullanılabilir.

### <a name="staledatatimelimitinsec"></a>Staledatatimelimitınsec ###

`staleDataTimeLimitInSec`Bu seçenek, mediaSource arabelleklerinde tutmak istediğiniz kaç saniyelik eski verinin sayısını yapılandırmanıza imkan tanıyan bir iyileştirmedir. Bu, varsayılan olarak devre dışıdır.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

True olarak ayarlanması, canlı akışlarınızın ve canlı arşivlerinizin canlı CEA açıklamalı alt görüntüsünü görüntülemenize izin verir. Etiket özniteliği gerekli değildir, yoksa Player varsayılan bir etikete geri dönecektir.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Bu seçenek yalnızca Azure Media Player Version 2.1.1 ve üzeri sürümlerde kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
