---
title: Azure haritalar 'a denetim ekleme | Microsoft Docs
description: Azure haritalar 'daki haritaya yakınlaştırma denetimi, sıklık denetimi, döndürme denetimi ve stil Seçici ekleme.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7267f77ed3d296ac586dcfd0f525b94d5e6eb7a0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976215"
---
# <a name="add-controls-to-a-map"></a>Haritaya denetim ekleme

Bu makalede, bir haritaya denetimleri nasıl ekleyeceğiniz gösterilir. Ayrıca, tüm denetimlerle ve [Stil seçiciyle](https://docs.microsoft.com/azure/azure-maps/choose-map-style)bir harita oluşturmayı öğreneceksiniz.

## <a name="add-zoom-control"></a>Yakınlaştırma denetimi Ekle

Bir yakınlaştırma denetimi, haritada ve dışarı yakınlaştırmak için düğmeler ekler. Aşağıdaki kod örneği, bir [Zoomcontrol](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) sınıfının bir örneğini oluşturur ve haritanın sağ alt köşesine ekler.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Yakınlaştırma denetimi ekleme' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>yakınlaştırma denetimi ekleme</a> kalemine bakın.
</iframe>

## <a name="add-pitch-control"></a>Aralıklı Denetim Ekle

Bir aralıklı denetim, aralığı ufuk ile eşlemek için sıklık için düğmeler ekler. Aşağıdaki kod örneği, bir [ınchcontrol](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) sınıfının bir örneğini oluşturur ve haritanın sağ üst köşesine ekler.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Aralıklı denetim ekleme' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>bir aralıklı denetim ekleme</a> kalemine bakın.
</iframe>

## <a name="add-compass-control"></a>Pusula denetimi Ekle

Pusula denetimi eşlemeyi döndürmek için bir düğme ekler. Aşağıdaki kod örneği, [pusula denetimi](/javascript/api/azure-maps-control/atlas.control.compasscontrol) sınıfının bir örneğini oluşturur ve haritanın sol alt köşesine ekler.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Döndürme denetimi ekleme' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>bir döndürme denetimi ekleme</a> kalemine bakın.
</iframe>

## <a name="a-map-with-all-controls"></a>Tüm denetimlerle bir eşleme

Aşağıdaki kod örneği, haritanın sağ alt köşesine stil Seçici, yakınlaştırma, sıklık ve pusula denetimleri ekler. Bunların nasıl otomatik olarak yığdığına dikkat edin. Betikteki Denetim nesnelerinin sırası haritada görünme sırasını belirler. Haritadaki denetimlerin sırasını değiştirmek için, komut dosyasında sırasını değiştirebilirsiniz.

<br/>

<iframe height='500' scrolling='no' title='Tüm denetimlerle bir harita' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Tüm denetimlerle Haritayı</a> görüntüleyin.
</iframe>

Stil Seçici denetimi [stylecontrol](/javascript/api/azure-maps-control/atlas.control.stylecontrol) sınıfı tarafından tanımlanır. Stil seçici denetimini kullanma hakkında daha fazla bilgi için bkz. [harita stili seçme](choose-map-style.md).

## <a name="customize-controls"></a>Denetimleri özelleştirme

Denetimleri özelleştirmek için çeşitli seçenekleri test eden bir araç aşağıda verilmiştir.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Gezinti denetimi seçenekleri" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Gezinti denetimi seçeneklerine</a> bakın.
</iframe>

Özelleştirilmiş gezinti denetimleri oluşturmak istiyorsanız, `atlas.Control` sınıftan genişleyen bir sınıf oluşturun veya bir HTML öğesi oluşturun ve harita div üzerinde konumlandırın. Bu UI denetiminin Haritayı taşımak için haritalar `setCamera` işlevini çağırması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Pusula denetimi](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [Süchcontrol](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Tam kod için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [PIN Ekle](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Açılan pencere Ekle](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı Ekle](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı Ekle](map-add-shape.md)

> [!div class="nextstepaction"]
> [Kabarcık katmanı ekleme](map-add-bubble-layer.md)

