---
title: Azure haritalar 'a HTML Işaretleyicisi ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına bir HTML Işaretleyicisi ekleme.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ff18b8646a3845d029e9826c0895b9d3eb16bdde
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976214"
---
# <a name="add-html-markers-to-the-map"></a>Haritaya HTML işaretçileri ekleyin

Bu makalede, bir HTML Işaretçisi olarak haritaya bir görüntü dosyası gibi özel bir HTML nasıl ekleyeceğiniz gösterilir.

> [!NOTE]
> HTML Işaretçileri veri kaynaklarına bağlanamaz. Bunun yerine, konum bilgisi doğrudan işaretçiye eklenir ve işaret, bir [htmlmarkermanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)olan `markers` Maps özelliğine eklenir.

> [!IMPORTANT]
> Azure Haritalar Web denetimindeki birçok katmanın farklı olarak, işlemek için WebGL kullanan çoğu katmanın aksine, HTML Işaretçileri işleme için geleneksel DOM öğeleri kullanır. Bu nedenle, daha fazla HTML işaretleyicisi bir sayfa eklendikçe, daha fazla DOM öğesi vardır. Birkaç yüz HTML işaretleyicisi eklendikten sonra performans düşebilir. Daha büyük veri kümeleri için verilerinizi kümelendirmeyi veya sembol ya da kabarcık katmanı kullanmayı düşünün.

## <a name="add-an-html-marker"></a>HTML işaretleyicisi ekleme

[Htmlişaretleyici](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) sınıfının varsayılan bir stili vardır. İşaretin renk ve metin seçeneklerini ayarlayarak işaretçiyi özelleştirebilirsiniz. HTML işaret sınıfının varsayılan stili, `{color}` ve `{text}` yer tutucusu olan bir SVG şablonudur. Hızlı özelleştirme için HTML işaretleyici seçeneklerinde renk ve metin özelliklerini ayarlayın. 

Aşağıdaki kod bir HTML işaretleyicisi oluşturur ve Color özelliğini "DodgerBlue" olarak ve metin özelliğini "10" olarak ayarlar. Açılan pencere, işaretleyicinin görünürlüğünü değiştirmek için `click` işaretçiye eklenir ve olay kullanılır.

```javascript
//Create a HTML marker and add it to the map.
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

<iframe height='500' scrolling='no' title='Haritaya HTML Işaretleyicisi ekleme' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>bir haritaya HTML işaretleyicisi ekleme</a> kalemine bakın.
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG şablonu oluşturma HTML işaretleyicisi

HTML işaretleyicisinin varsayılan değer `htmlContent` , yer klasörleri `{color}` ve `{text}` içindeki bir SVG şablonudur. Özel SVG dizeleri oluşturabilir ve bu aynı yer tutucuları SVG 'ye ekleyebilirsiniz ve bu nedenle, işaretleyicinin `color` ve `text` seçeneklerini ayarlayarak, bu yer tutucuları SVG 'de güncelleştirin.

<br/>

<iframe height='500' scrolling='no' title='Özel SVG şablonuyla HTML Işaretleyicisi' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>özel SVG şablonuyla kalem HTML işaretine</a> bakın.
</iframe>

> [!TIP]
> Azure Haritalar Web SDK 'Sı, HTML işaretçileri ile kullanılabilecek çeşitli SVG resim şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

## <a name="add-a-css-styled-html-marker"></a>CSS stilli HTML işaretleyicisi ekleme

HTML işaretlerinin avantajlarından biri, CSS kullanılarak erişilebilecek çok sayıda harika özelleştirme olabilir. Bu örnekte, Htmlmarkın içeriği, bir hareketli PIN oluşturan ve pulsuz olan HTML ve CSS 'den oluşur.

<br/>

<iframe height='500' scrolling='no' title='HTML veri kaynağı' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML veri kaynağına</a> bakın.
</iframe>

## <a name="draggable-html-markers"></a>Sürüklenebilir HTML işaretçileri

Bu örnek, bir HTML işaretleyici sürüklenebilir nasıl yapılacağını gösterir. HTML işaretçileri, `drag` `dragstart` ve`dragend` olaylarını destekler.

<br/>

<iframe height='500' scrolling='no' title='Sürüklenebilir HTML Işaretleyicisi' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>sürüklenebilir HTML işaretine</a> bakın.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>HTML işaretçilerine fare olayları ekleme

Bu örnekler, bir HTML işaretine fare ve sürükleme olaylarının nasıl ekleneceğini gösterir.

<br/>

<iframe height='500' scrolling='no' title='HTML Işaretçilerine fare olayları ekleme' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>HTML işaretçilerine fare olayları ekleme</a> kalemine bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Htmlişaretleyici](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Haritalarınıza eklenecek daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Kabarcık katmanı ekleme](./map-add-bubble-layer.md)