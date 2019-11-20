---
title: Azure haritalar 'a bir çizim araç çubuğu ekleme | Microsoft Docs
description: Azure Haritalar Web SDK 'sını kullanarak bir haritaya Çizim araç çubuğu ekleme
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309839"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Haritaya bir çizim araçları araç çubuğu ekleme

Bu makalede, çizim araçları modülünü nasıl kullanabileceğiniz ve Çizim araç çubuğunun haritada nasıl görüntüleneceği gösterilmektedir. [Drawingtoolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) denetimi harita üzerine çizim araç çubuğunu ekler. Yalnızca bir ve tüm çizim araçlarıyla eşlemeler oluşturmayı ve çizim şekillerinin çizim şekillerini oluşturmayı nasıl özelleştireceğinizi öğreneceksiniz.

## <a name="add-drawing-toolbar"></a>Çizim araç çubuğu ekle

Aşağıdaki kod, çizim Yöneticisi 'nin bir örneğini oluşturur ve araç çubuğunu haritada görüntüler.

```Javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="Çizim araç çubuğu ekle" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Çizim araç çubuğu ekleme</a> bölümüne bakın.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Görünen araç çubuğu seçeneklerini sınırla

Aşağıdaki kod, çizim Yöneticisi 'nin bir örneğini oluşturur ve araç çubuğunu haritada yalnızca bir çokgen çizim aracıyla görüntüler. 

```Javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="Çokgen çizim aracı ekleme" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>bir çokgen çizim aracı ekleme</a> kalemine bakın.
</iframe>


## <a name="change-drawing-rendering-style"></a>Çizim işleme stilini değiştir

Aşağıdaki kod, çizim yöneticisinden işleme katmanlarını alır ve çizim için işleme stilini değiştirecek seçeneklerini değiştirir. Bu durumda, işaret mavi bir işaret simgesiyle işlenir, çizgiler kırmızı ve dört piksel genişliğinde olur, çokgenler yeşil renkli bir renkli ve turuncu bir anahatlara sahip olur.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Yukarıdaki işlevlerin tamamen çalışan kod örneği aşağıda verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çizim işleme stilini değiştir" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bkz. Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io'>codepen</a>'da <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>Çizim işleme stilini değiştirme</a> .
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)