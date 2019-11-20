---
title: Azure haritalar 'a çizgi katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına bir çizgi katmanı ekleme.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977332"
---
# <a name="add-a-line-layer-to-the-map"></a>Haritaya bir çizgi katmanı ekleyin

Bir çizgi katmanı, haritada yollar veya rotalar `LineString` olarak `MultiLineString` işlemek ve özellikleri oluşturmak için kullanılabilir. Bir çizgi katmanı, `Polygon` ve `MultiPolygon` özelliklerinin anahatlarını işlemek için de kullanılabilir. Veri kaynağı, işlemek için veri sağlamak üzere bir çizgi katmanına bağlanır. 

> [!TIP]
> Çizgi katmanları, varsayılan olarak çokgenler koordinatlarını ve bir veri kaynağındaki satırları de işler. Katmanı yalnızca, LineString özelliklerinin, `filter` `['==', ['geometry-type'], 'LineString']` katman özelliğini olarak ayarlayın veya `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` çok kanallı dize özelliklerini de dahil etmek istiyorsanız, bu katmanı sınırlamak için.

Aşağıdaki kod, bir çizgi oluşturma, bir veri kaynağına ekleme ve [linelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) sınıfını kullanarak bir çizgi katmanı ile işleme işlemlerinin nasıl yapılacağını gösterir.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya çizgi ekleme' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/qomaKv/'>bir haritaya çizgi ekleme</a> kalemine bakın.
</iframe>

Çizgi katmanları, [Linelayeroptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) kullanılarak biçimlendirilebilir ve [veri odaklı stil ifadeleri kullanabilir](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Çizgi üzerinde semboller ekleme

Bu örnek, haritada bir çizgi üzerinde ok simgelerinin nasıl ekleneceğini gösterir. Sembol katmanını kullanırken, "yerleştirme" seçeneğini "satır" olarak ayarlayın, bu, simgeleri satır üzerinde işler ve simgeleri döndürür (0 derece = sağ).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Oku çizgi üzerinde göster" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile birlikte, kalemin <a href='https://codepen.io/azuremaps/pen/drBJwX/'>göster okuna</a> bakın.
</iframe>

> [!TIP]
> Azure Haritalar Web SDK 'Sı, sembol katmanıyla birlikte kullanabileceğiniz çeşitli özelleştirilebilir görüntü şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Çizgiye vuruş gradyanı ekleme

Bir çizgiye tek bir kontur rengi uygulayabilmenin yanı sıra, bir çizgi segmentinden diğerine geçişi göstermek için bir renk degradesi olan bir satırı da doldurabilirsiniz. Örneğin, çizgi degradeleri zaman ve mesafe üzerinde yapılan değişiklikleri veya bağlı bir nesne satırındaki farklı sıcaklıkları temsil etmek için kullanılabilir. Bu özelliği bir satıra uygulamak için veri kaynağı `lineMetrics` seçeneğinin true olarak ayarlanmış olması gerekir ve ardından çizgi `strokeColor` seçeneğine bir renk gradyanı ifadesi geçirilebilir. Vuruş gradyanı ifadesi, hesaplanan satır ölçümlerini ifade `['line-progress']` eden veri ifadesine başvurmalıdır.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kontur degradesi olan çizgi" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>vuruş degradesi ile kalem hattına</a> bakın.
</iframe>

## <a name="customize-a-line-layer"></a>Çizgi katmanını özelleştirme

Çizgi katmanı çeşitli stil oluşturma seçenekleri. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='Çizgi katmanı seçenekleri' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>çizgisi katmanı seçeneklerine</a> bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Açılan pencere Ekle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı Ekle](map-add-shape.md)