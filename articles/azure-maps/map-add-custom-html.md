---
title: Haritaya HTML Işaretleyicisi ekleme | Microsoft Azure haritaları
description: Haritalar 'a HTML işaretçileri eklemeyi öğrenin. İşaretçileri özelleştirmek ve bir işaretçiye açılan ve fare olayları eklemek için Azure Maps web SDK 'sını kullanma konusuna bakın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 1c4367e2a649f4e239e2dab374afc4fb867e517b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891202"
---
# <a name="add-html-markers-to-the-map"></a>Haritaya HTML işaretçileri ekleyin

Bu makalede, bir HTML Işaretçisi olarak haritaya bir görüntü dosyası gibi özel bir HTML nasıl ekleyeceğiniz gösterilir.

> [!NOTE]
> HTML Işaretçileri veri kaynaklarına bağlanamaz. Bunun yerine, konum bilgisi doğrudan işaretçiye eklenir ve işaret, `markers` bir [Htmlmarkermanager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)olan Maps özelliğine eklenir.

> [!IMPORTANT]
> Azure Haritalar Web denetimindeki birçok katmanın farklı olarak, işlemek için WebGL kullanan çoğu katmanın aksine, HTML Işaretçileri işleme için geleneksel DOM öğeleri kullanır. Bu nedenle, bir sayfaya daha fazla HTML işaretleyicisi eklendiğinde, daha fazla DOM öğesi vardır. Birkaç yüz HTML işaretleyicisi eklendikten sonra performans düşebilir. Daha büyük veri kümeleri için verilerinizi kümelendirmeyi veya sembol ya da kabarcık katmanı kullanmayı düşünün.

## <a name="add-an-html-marker"></a>HTML işaretleyicisi ekleme

[Htmlişaretleyici](/javascript/api/azure-maps-control/atlas.htmlmarker) sınıfının varsayılan bir stili vardır. İşaretin renk ve metin seçeneklerini ayarlayarak işaretçiyi özelleştirebilirsiniz. HTML işaret sınıfının varsayılan stili, `{color}` ve yer tutucusu olan BIR SVG şablonudur `{text}` . Hızlı özelleştirme için HTML işaretleyici seçeneklerinde renk ve metin özelliklerini ayarlayın. 

Aşağıdaki kod bir HTML işaretleyicisi oluşturur ve Color özelliğini "DodgerBlue" olarak ve metin özelliğini "10" olarak ayarlar. Açılan pencere, işaretleyicinin `click` görünürlüğünü değiştirmek için işaretçiye eklenir ve olay kullanılır.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya HTML Işaretleyicisi ekleme' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>bir HARITAYA HTML Işaretleyicisi ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG şablonu oluşturma HTML işaretleyicisi

`htmlContent`HTML işaretleyicisinin varsayılan değer, yer klasörleri ve içindeki BIR SVG şablonudur `{color}` `{text}` . Özel SVG dizeleri oluşturabilir ve bu aynı yer tutucuları SVG 'ye ekleyebilirsiniz ve bu nedenle, `color` `text` işaretleyicinin ve seçeneklerini ayarlayarak, bu yer tutucuları SVG 'de güncelleştirin.

<br/>

<iframe height='500' scrolling='no' title='Özel SVG şablonuyla HTML Işaretleyicisi' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>özel SVG şablonuyla kalem HTML işaretine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

> [!TIP]
> Azure Haritalar Web SDK 'Sı, HTML işaretçileri ile kullanılabilecek çeşitli SVG resim şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

## <a name="add-a-css-styled-html-marker"></a>CSS stilli HTML işaretleyicisi ekleme

HTML işaretlerinin avantajlarından biri, CSS kullanılarak erişilebilecek çok sayıda harika özelleştirme olabilir. Bu örnekte, Htmlmarkın içeriği, bir hareketli PIN oluşturan ve pulsuz olan HTML ve CSS 'den oluşur.

<br/>

<iframe height='500' scrolling='no' title='HTML veri kaynağı' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML veri kaynağına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="draggable-html-markers"></a>Sürüklenebilir HTML işaretçileri

Bu örnek, bir HTML işaretleyici sürüklenebilir nasıl yapılacağını gösterir. HTML işaretçileri `drag` , `dragstart` ve olaylarını destekler `dragend` .

<br/>

<iframe height='500' scrolling='no' title='Sürüklenebilir HTML Işaretleyicisi' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>SÜRÜKLENEBILIR HTML işaretine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>HTML işaretçilerine fare olayları ekleme

Bu örnekler, bir HTML işaretine fare ve sürükleme olaylarının nasıl ekleneceğini gösterir.

<br/>

<iframe height='500' scrolling='no' title='HTML Işaretçilerine fare olayları ekleme' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>HTML Işaretçilerine fare olayları ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Htmlişaretleyici](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

Haritalarınıza eklenecek daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Baloncuk katmanı ekleme](./map-add-bubble-layer.md)