---
title: Haritaya kabarcık katmanı ekleme | Microsoft Azure haritaları
description: Haritalar üzerinde noktaları sabit boyutlarla daire olarak nasıl işleyebileceğinizi öğrenin. Bu amaçla kabarcık katmanları eklemek ve özelleştirmek için bkz. Azure Maps web SDK 'sını kullanma.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895337"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Haritaya kabarcık katmanı ekleme

Bu makalede, bir veri kaynağındaki nokta verilerinin haritada kabarcık katmanı olarak nasıl oluşturulduğu gösterilir. Kabarcık katmanları, bir sabit piksel yarıçapı ile haritada daire olarak işleme noktaları. 

> [!TIP]
> Kabarcık katmanları varsayılan olarak bir veri kaynağındaki tüm geometrilerin koordinatlarını işler. Katmanı yalnızca nokta geometrisi özelliklerinin oluşturduğu şekilde sınırlamak için `filter` katmanın özelliğini olarak ayarlayın `['==', ['geometry-type'], 'Point']` ve `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` MultiPoint özelliklerini de dahil etmek istiyorsanız.

## <a name="add-a-bubble-layer"></a>Baloncuk katmanı ekleme

Aşağıdaki kod, bir dizi noktayı bir veri kaynağına yükler. Ardından, veri noktalarını bir [kabarcık katmanına](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)bağlar. Kabarcık katmanı, her kabarcığun yarıçapını beş piksel ve bir de beyaz dolgulu bir renk oluşturur. Ve, mavi renkli bir kontur rengi ve altı piksellik bir kontur kalınlığı. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer veri kaynağı' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer veri kaynağına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Kabarcık katmanı içeren etiketleri göster

Bu kod, haritada bir noktayı işlemek için balon katmanını nasıl kullanacağınızı gösterir. Ve bir etiketi işlemek için bir sembol katmanını kullanma. Sembol katmanının simgesini gizlemek için, `image` simge seçeneklerinin özelliğini olarak ayarlayın `'none'` .

<br/>

<iframe height='500' scrolling='no' title='Çoklu katman veri kaynağı' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Multilayer veri kaynağına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customize-a-bubble-layer"></a>Balon katmanını özelleştirme

Kabarcık katmanının yalnızca birkaç stil seçeneği vardır. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='Kabarcık katmanı seçenekleri' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>kabarcık katmanı seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Veri temelli stil ifadeleri kullanma](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](/samples/browse/?products=azure-maps)