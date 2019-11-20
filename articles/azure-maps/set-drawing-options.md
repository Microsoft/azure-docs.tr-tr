---
title: Azure haritalar 'da çizim seçeneklerini ayarlama | Microsoft Docs
description: Azure Haritalar Web SDK 'sını kullanarak çizim seçenekleri verilerini ayarlama
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309792"
---
# <a name="set-drawing-options"></a>Çizim seçeneklerini ayarla

Bu makalede, [Çizim Yöneticisi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) 'nin farklı seçeneklerinin Kullanıcı deneyimini nasıl değiştirgösterdiği gösterilmektedir. Çizim Yöneticisi seçeneklerini örneklarken belirtebilir veya alternatif olarak, seçenekleri ayarlamak için **drawingmanager. setOptions ()** işlevini kullanın.


## <a name="set-drawing-mode"></a>Çizim modunu ayarla

Aşağıdaki kod, çizim Yöneticisi 'nin bir örneğini oluşturur ve çizim **modu** seçeneğini ayarlar. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Aşağıdaki kod, çizim Yöneticisi 'nin çizim modunun nasıl ayarlanacağı hakkında tamamen çalışan bir örnektir. Bir çokgen çizmeye başlamak için haritaya tıklayın.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çokgen çizme" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Bodepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>bir çokgen çizme</a> kalemine bakın.
</iframe>


## <a name="set-interaction-type"></a>Etkileşim türünü ayarla

Aşağıdaki kod, çizim yöneticisinin bağlı olması gereken çizim etkileşiminin türünü ayarlar. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Aşağıda, Haritayı serbestçe çizmenize imkan tanıyan, sol fare düğmesini basılı tutarak ve sürükleyerek bu işlevselliği uygulayan kod örneği verilmiştir. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ücretsiz çizim" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>ücretsiz çizim</a> kalemine bakın.
</iframe>


## <a name="customizing-drawing-options"></a>Çizim seçeneklerini özelleştirme

Önceki örneklerde çizim seçeneklerinin örnekleme sırasında nasıl özelleştirileceği gösterilmiştir. Ayrıca, **drawingmanager. setOptions ()** Işlevini kullanarak çizim Yöneticisi seçeneklerini ayarlayabilirsiniz. Aşağıda, setOptions işlevini kullanarak çizim Yöneticisi için tüm seçeneklerin özelleştirilmesini test eden bir araç verilmiştir.

<br/>

<iframe height="685" title="Çizim yöneticisini özelleştirme" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Şekil verileri alın</a> kalemine bakın.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
