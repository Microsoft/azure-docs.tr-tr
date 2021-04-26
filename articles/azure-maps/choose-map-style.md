---
title: Azure Maps Web Harita Denetimi stilini değiştirme
description: Haritanın stilini ve seçeneklerini değiştirmeyi öğrenin. Kullanıcıların farklı stiller arasında geçiş yapabilmesi için bkz. Azure haritalar 'daki haritaya bir stil Seçici denetimi ekleme.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680033"
---
# <a name="change-the-style-of-the-map"></a>Haritanın stilini değiştirme

Harita denetimi birçok farklı harita [stili seçeneğini](/javascript/api/azure-maps-control/atlas.styleoptions) ve [temel harita stillerini](supported-map-styles.md)destekler. Harita denetimi başlatıldığında tüm stiller ayarlanabilir. Ya da harita denetiminin işlevini kullanarak stiller ayarlayabilirsiniz `setStyle` . Bu makalede, bu stil seçeneklerinin haritanın görünümünü özelleştirmek için nasıl kullanılacağı gösterilmektedir. Ayrıca, haritada stil seçici denetimini nasıl uygulayacağınızı öğreneceksiniz. Stil Seçici denetimi, kullanıcının farklı temel stiller arasında geçiş yapmasına olanak tanır.

## <a name="set-map-style-options"></a>Harita stili seçeneklerini ayarla

Stil seçenekleri, Web denetimi başlatma sırasında ayarlanabilir. Ya da harita denetiminin işlevini çağırarak stil seçeneklerini güncelleştirebilirsiniz `setStyle` . Tüm kullanılabilir stil seçeneklerini görmek için bkz. [stil seçenekleri](/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

Aşağıdaki araç, farklı stil seçeneklerinin eşlemenin nasıl işleneceğini gösterir. 3B binaları görmek için, büyük bir şehre yaklaşın.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Harita stili seçenekleri" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>harita stil seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="set-a-base-map-style"></a>Temel harita stili ayarla

Harita denetimini, Web SDK 'sında kullanılabilen [temel harita stillerinden](supported-map-styles.md) biriyle de başlatabilirsiniz. Daha sonra, `setStyle` temel stili farklı bir harita stiliyle güncelleştirmek için işlevini kullanabilirsiniz.

### <a name="set-a-base-map-style-on-initialization"></a>Başlangıçta temel harita stili ayarla

Harita denetiminin temel stilleri başlatma sırasında ayarlanabilir. Aşağıdaki kodda, `style` harita denetimi seçeneği [ `grayscale_dark` temel harita stiline](supported-map-styles.md#grayscale_dark)ayarlanır.  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Harita yükte stili ayarlama' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>harita yükünün stilini ayarlama</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="update-the-base-map-style"></a>Temel harita stilini güncelleştirme

Temel harita stili, `setStyle` işlevi kullanılarak ve `style` farklı bir temel harita stiline geçiş ya da ek stil seçenekleri ekleme seçeneği ayarlanarak güncelleştirilemeyebilir.

```javascript
map.setStyle({ style: 'satellite' });
```

Aşağıdaki kodda, bir eşleme örneği yüklendikten sonra, eşleme stili, ' den `grayscale_dark` `satellite` [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) işlevi kullanılarak güncelleştirilir.

<br/>

<iframe height='500' scrolling='no' title='Stili güncelleştirme' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stili güncelleştirme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="add-the-style-picker-control"></a>Stil seçici denetimini ekleme

Stil Seçici denetimi, son kullanıcı tarafından taban stilleri arasında geçiş yapmak için kullanılabilecek bir açılır pencere paneli ile kullanımı kolay bir düğme sağlar.

Stil seçicisinin iki farklı düzen seçeneği vardır: `icon` ve `list` . Ayrıca, stil Seçici iki farklı stil Seçici denetim seçeneği seçmenizi sağlar `style` : `light` ve `dark` . Bu örnekte, stil Seçici `icon` düzeni kullanır ve simgeler biçiminde temel harita stillerinin seçim listesini görüntüler. Stil denetimi Seçicisi aşağıdaki temel Stil kümesini içerir: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Stil Seçici denetim seçenekleri hakkında daha fazla bilgi için bkz. [Stil denetimi seçenekleri](/javascript/api/azure-maps-control/atlas.stylecontroloptions).

Aşağıdaki görüntüde, düzen içinde gösterilen stil Seçici denetimi gösterilmektedir `icon` .

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Stil Seçici simge düzeni":::

Aşağıdaki görüntüde, düzen içinde gösterilen stil Seçici denetimi gösterilmektedir `list` .

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Stil Seçici liste düzeni":::

> [!IMPORTANT]
> Varsayılan olarak, stil Seçici denetimi Azure haritalar 'ın S0 fiyatlandırma katmanı altında bulunan tüm stilleri listeler. Bu listedeki stil sayısını azaltmak isterseniz, listede görünmesini istediğiniz stillerin bir dizisini `mapStyle` Stil seçicisinin seçeneğine geçirin. S1 kullanıyorsanız ve tüm kullanılabilir stilleri göstermek istiyorsanız, `mapStyles` Stil seçicisinin seçeneğini olarak ayarlayın `"all"` .

Aşağıdaki kod, varsayılan temel stil listesini geçersiz kılmayı gösterir `mapStyles` . Bu örnekte, `mapStyles` stil Seçici denetimi tarafından görüntülenmesini istediğimiz temel stilleri listelemek için seçeneği ayarlıyoruz.

<br/>

<iframe height='500' scrolling='no' title='Stil seçiciyi ekleme' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Stil seçiciyi ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinmek için:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Harita denetimleri ekleme](map-add-controls.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Baloncuk katmanı ekleme](map-add-bubble-layer.md)
