---
title: Harita için çizim araçları araç çubuğunu Ekle | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'sını kullanarak bir haritaya Çizim araç çubuğu ekleme
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896404"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Haritaya bir çizim araçları araç çubuğu ekleme

Bu makalede, çizim araçları modülünü nasıl kullanabileceğiniz ve Çizim araç çubuğunun haritada nasıl görüntüleneceği gösterilmektedir. [Drawingtoolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) denetimi harita üzerine çizim araç çubuğunu ekler. Yalnızca bir ve tüm çizim araçlarıyla eşlemeler oluşturmayı ve çizim şekillerinin çizim şekillerini oluşturmayı nasıl özelleştireceğinizi öğreneceksiniz.

## <a name="add-drawing-toolbar"></a>Çizim araç çubuğu ekleme

Aşağıdaki kod, çizim Yöneticisi 'nin bir örneğini oluşturur ve araç çubuğunu haritada görüntüler.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Yukarıdaki işlevlerin tamamen çalışan kod örneği aşağıda verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çizim araç çubuğu ekleme" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Çizim araç çubuğu ekleme</a> bölümüne bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Görünen araç çubuğu seçeneklerini sınırla

Aşağıdaki kod, çizim Yöneticisi 'nin bir örneğini oluşturur ve araç çubuğunu haritada yalnızca bir çokgen çizim aracıyla görüntüler. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Yukarıdaki işlevlerin tamamen çalışan kod örneği aşağıda verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çokgen çizim aracı ekleme" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>bir çokgen çizim aracı ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


## <a name="change-drawing-rendering-style"></a>Çizim işleme stilini değiştir

Çizilen şekillerin stili, işlevi kullanılarak çizim yöneticisinin temel katmanları `drawingManager.getLayers()` alınarak özelleştirilebilir ve tek tek katmanlarda seçenekler ayarlanarak özelleştirilebilir. Şekil düzenlenirken, koordinat için görüntülenen sürükleme tutamaçları HTML işaretçilerine sahiptir. Sürükleme tutamaçlarının stili, `dragHandleStyle` Çizim yöneticisinin ve SEÇENEKLERINE HTML işaret seçenekleri geçirerek özelleştirilebilir `secondaryDragHandleStyle` .  

Aşağıdaki kod, çizim yöneticisinden işleme katmanlarını alır ve çizim için işleme stilini değiştirecek seçeneklerini değiştirir. Bu durumda, işaret mavi bir işaret simgesiyle işlenir. Satırlar kırmızı ve dört piksel genişliğinde olur. Çokgenler yeşil bir Fill rengine ve turuncu bir anahatta sahip olur. Daha sonra Sürükle tutamaçlarının stillerini kare simgeleri olacak şekilde değiştirir. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Yukarıdaki işlevlerin tamamen çalışan kod örneği aşağıda verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çizim işleme stilini değiştir" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Bkz. Azure <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'></a> Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) Ile <a href='https://codepen.io'>codepen</a>'da çizim işleme stilini değiştirme.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Çizim araçları modülünün ek özelliklerini kullanmayı öğrenin:

> [!div class="nextstepaction"]
> [Şekil verilerini alma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Çizim olaylarına tepki verme](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Etkileşim türleri ve klavye kısayolları](drawing-tools-interactions-keyboard-shortcuts.md)

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)