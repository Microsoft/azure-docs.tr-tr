---
title: Azure haritalar 'a kutucuk katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına kutucuk katmanı ekleme.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3f047ec1aced55038384cbe29bd3a4b8a948dce9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976448"
---
# <a name="add-a-tile-layer-to-a-map"></a>Haritaya kutucuk katmanı ekleme

Bu makalede, haritada bir kutucuk katmanının nasıl kaplamasıyla ilgili yönergeler verilmektedir. Döşeme katmanları, Azure Maps temel harita kutucuklarının üzerine görüntü eklemenize olanak tanır. Azure haritalar döşeme sistemi hakkında daha fazla bilgi [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md) belgelerinde bulunabilir.

Döşeme katmanı bir sunucudan kutucukları yükler. Bu görüntüler, kutucuk katmanının anladığı bir adlandırma kuralına veya anında görüntüleri oluşturan dinamik bir hizmete sahip bir sunucu üzerindeki herhangi bir görüntü gibi önceden işlenmiş ve depolanmış olabilir. Azure haritalar [Tilelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) sınıfı tarafından desteklenen üç farklı kutucuk hizmeti adlandırma kuralı vardır: 

* X, Y, yakınlaştırma gösterimi-yakınlaştırma düzeyine göre x, sütun ise döşeme kılavuzundaki döşemenin satır konumudur.
* Quadkey gösterimi-x, y, zoom bilgilerini bir kutucuk için benzersiz bir tanımlayıcı olan tek bir dize değerine birleşimi.
* Sınırlayıcı kutusu-sınırlayıcı kutu koordinatları, [Web eşleme Hizmetleri (WMS)](https://www.opengeospatial.org/standards/wms)tarafından yaygın olarak `{west},{south},{east},{north}` kullanılan biçimde bir görüntü belirtmek için kullanılabilir.

> [!TIP]
> Bir [Tilelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) , haritada büyük veri kümelerini görselleştirmenin harika bir yoludur. Bir görüntüden yalnızca bir kutucuk katmanı oluşturulmayabilir, ancak vektör verileri de kutucuk katmanı olarak da oluşturulabilir. Vektör verilerini kutucuk katmanı olarak işleyerek, harita denetiminin yalnızca dosya boyutunda, temsil ettikleri vektör verilerinden çok daha küçük olabilen kutucukları yüklemesi gerekir. Bu teknik, haritada milyonlarca veri satırı oluşturması gereken birçok kişi tarafından kullanılır.

Döşeme katmanına geçirilen kutucuk URL 'si, bir TileJSON kaynağına veya aşağıdaki parametreleri kullanan bir kutucuk URL şablonuna yönelik bir http/https URL 'SI olmalıdır: 

* `{x}`-X kutucuğunun konumu. Ayrıca, `{y}` ve `{z}`gerektirir.
* `{y}`-Kutucuğun Y konumu. Ayrıca, `{x}` ve `{z}`gerektirir.
* `{z}`-Kutucuğun yakınlaştırma düzeyi. Ayrıca, `{x}` ve `{y}`gerektirir.
* `{quadkey}`-Bing Haritalar kutucuk sistem adlandırma kuralına bağlı olarak, quadkey tanımlayıcısını Döşe.
* `{bbox-epsg-3857}`-EPSG 3857 uzamsal başvuru sisteminde `{west},{south},{east},{north}` biçim içeren bir sınırlayıcı kutu dizesi.
* `{subdomain}`-Belirtilen alt etki alanı değerlerinin ekleneceği yer tutucu.

## <a name="add-a-tile-layer"></a>Kutucuk katmanı ekleme

 Bu örnek, x, y, yakınlaştırma döşeme sistemi kullanan bir kutucuk kümesini işaret eden döşeme katmanının nasıl oluşturulacağını gösterir. Bu kutucuk katmanının kaynağı, [Iowa çevresel Mesonet 'in Iowa çevre](https://mesonet.agron.iastate.edu/ogc/)bir hava durumu radar kaplamasıyla. Radar verileri görüntülerken, kullanıcılar haritada gezindikleri gibi şehirlerin etiketlerini açıkça görebilir ve bu da `labels` katmanın altına döşeme katmanı eklenerek yapılabilir.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='X, Y ve Z kullanarak döşeme katmanı' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>X, Y ve Z</a> ile Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) kullanarak kalem döşeme katmanına bakın.
</iframe>

## <a name="customize-a-tile-layer"></a>Döşeme katmanını özelleştirme

Döşeme katmanı sınıfının birçok stil seçeneği vardır. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='Döşeme katmanı seçenekleri' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>kutucuğu katman seçeneklerine</a> bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Görüntü katmanı ekleme](./map-add-image-layer.md)
