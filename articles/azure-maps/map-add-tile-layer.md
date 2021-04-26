---
title: Haritaya kutucuk katmanı ekleme | Microsoft Azure haritaları
description: Haritalar üzerinde görüntü oluşturmayı öğrenin. Bir haritaya Hava durumu radar kaplaması içeren bir kutucuk katmanı eklemek için Azure Maps web SDK 'sını kullanan bir örneğe bakın.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608111"
---
# <a name="add-a-tile-layer-to-a-map"></a>Haritaya kutucuk katmanı ekleme

Bu makalede, haritada kutucuk katmanının nasıl yer aldığı gösterilmektedir. Döşeme katmanları, Azure Maps temel harita kutucuklarının üzerine görüntü eklemenize olanak tanır. Azure haritalar döşeme sistemi hakkında daha fazla bilgi için bkz. [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md).

Döşeme katmanı bir sunucudan kutucukları yükler. Bu görüntüler önceden işlenmiş ya da dinamik olarak işlenmiş olabilir. Önceden işlenmiş görüntüler, kutucuk katmanının anladığı bir adlandırma kuralını kullanarak bir sunucu üzerindeki diğer herhangi bir görüntü gibi saklanır. Dinamik olarak işlenen görüntüler görüntüleri gerçek zamanlı olarak yakın bir şekilde yüklemek için bir hizmet kullanır. Azure haritalar [Tilelayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) sınıfı tarafından desteklenen üç farklı kutucuk hizmeti adlandırma kuralı vardır:

* X, Y, zoom gösterimi-X sütundur, Y, kutucuk kılavuzundaki döşemenin satır konumudur ve yakınlaştırma düzeyi yakınlaştırma düzeyine göre bir değer gösterimi.
* Quadkey gösterimi-x, y ve zoom bilgilerini tek bir dize değeri olarak birleştirir. Bu dize değeri, tek bir kutucuk için benzersiz bir tanımlayıcı haline gelir.
* Sınırlayıcı kutusu-sınırlayıcı kutu koordinatları biçiminde bir görüntü belirtin: `{west},{south},{east},{north}` . Bu biçim genellikle [Web eşleme Hizmetleri (WMS)](https://www.opengeospatial.org/standards/wms)tarafından kullanılır.

> [!TIP]
> Bir [Tilelayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) , haritada büyük veri kümelerini görselleştirmenin harika bir yoludur. Bir görüntüden yalnızca kutucuk katmanı oluşturulmayabilir, vektör verileri de kutucuk katmanı olarak da oluşturulabilir. Vektör verilerini kutucuk katmanı olarak işleyerek, Map Control yalnızca dosya boyutunda daha küçük olan kutucukları, temsil ettikleri vektör verilerinden yüklemek için gerekir. Bu teknik, haritada milyonlarca veri satırını işlemek için yaygın olarak kullanılır.

Döşeme katmanına geçirilen kutucuk URL 'si, bir TileJSON kaynağına yönelik bir http veya https URL 'SI ya da aşağıdaki parametreleri kullanan bir kutucuk URL şablonu olmalıdır:

* `{x}` -X kutucuğunun konumu. Ayrıca `{y}` , ve gerektirir `{z}` .
* `{y}` -Kutucuğun Y konumu. Ayrıca `{x}` , ve gerektirir `{z}` .
* `{z}` -Kutucuğun yakınlaştırma düzeyi. Ayrıca `{x}` , ve gerektirir `{y}` .
* `{quadkey}` -Bing Haritalar kutucuk sistem adlandırma kuralına bağlı olarak, quadkey tanımlayıcısını Döşe.
* `{bbox-epsg-3857}` - `{west},{south},{east},{north}` EPSG 3857 uzamsal başvuru sisteminde biçim içeren bir sınırlayıcı kutu dizesi.
* `{subdomain}` -Belirtilmişse, alt etki alanı değerleri için bir yer tutucu `subdomain` eklenir.
* `{azMapsDomain}` -Etki alanını ve kutucuk isteklerinin kimlik doğrulamasını harita tarafından kullanılan değerlerle aynı olacak şekilde hizalamak için bir yer tutucudur.

## <a name="add-a-tile-layer"></a>Kutucuk katmanı ekleme

 Bu örnek, bir kutucuk kümesini işaret eden döşeme katmanının nasıl oluşturulacağını gösterir. Bu örnek x, y, yakınlaştırma Döşeme sistemini kullanır. Bu kutucuk katmanının kaynağı, kitle kaynağı grafikleri içeren [openseamap projem](https://openseamap.org/index.php)' dir. Radar verileri görüntülerken, ideal kullanıcılar haritada gezindikleri gibi şehirlerin etiketlerini açıkça görürler. Bu davranış, katmanın altına döşeme katmanı eklenerek uygulanabilir `labels` .

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='X, Y ve Z kullanarak döşeme katmanı' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>X, Y ve Z</a> Ile Azure Maps () kullanarak kalem döşeme katmanına bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC Web eşleme hizmeti (WMS) ekleme

Web eşleme hizmeti (WMTS), harita verilerinin görüntülerini sunan bir Open Geospatial Consortium (OGC) standardıdır. Bu biçimde Azure haritalar ile kullanabileceğiniz çok sayıda açık veri kümesi vardır. Bu hizmet türü, hizmet `EPSG:3857` koordinat başvuru sistemini (CCR) destekliyorsa, bir kutucuk katmanıyla birlikte kullanılabilir. Bir WMS hizmeti kullanırken, genişlik ve yükseklik parametrelerini hizmetin desteklediği aynı değere ayarlayın. bu değeri, seçeneğinde aynı değere ayarladığınızdan emin olun `tileSize` . Biçimlendirilen URL 'de, `BBOX` hizmetin parametresini `{bbox-epsg-3857}` yer tutucu ile ayarlayın.

Aşağıdaki ekran görüntüsünde, etiketlerin altındaki bir haritanın en üstünde bulunan [ABD geolojileri anketine (USGS)](https://mrdata.usgs.gov/) ait bir Web eşleme hizmeti 'nin, yukarıdaki kod gösterilmektedir.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="WMS kutucuk katmanı" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS kutucuk katmanına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC Web eşleme kutucuk hizmeti (WMTS) ekleme

Web eşleme kutucuk hizmeti (WMTS) haritalar için döşeli tabanlı yer paylaşımları sunmak için bir Open Geospatial Consortium (OGC) standardıdır. Bu biçimde Azure haritalar ile kullanabileceğiniz çok sayıda açık veri kümesi vardır. Hizmet `EPSG:3857` veya `GoogleMapsCompatible` koordinat başvuru sistemini (bir) destekliyorsa, bu hizmet türü bir kutucuk katmanıyla birlikte kullanılabilir. Bir WMTS hizmeti kullanırken, genişlik ve yükseklik parametrelerini hizmetin desteklediği aynı değere ayarlayın. bu değeri, seçeneğinde aynı değere ayarladığınızdan emin olun `tileSize` . Biçimlendirilen URL 'de, aşağıdaki yer tutucuları uygun şekilde değiştirin:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Aşağıdaki ekran görüntüsünde, bir haritanın en üstünde bulunan [ABD geoloji (USGS) Ulusal haritalarından](https://viewer.nationalmap.gov/services/) , yolların ve etiketlerin altında bulunan bir Web eşleme kutucuğu hizmeti 'nin bir Web eşleme kutucuk hizmeti 'nin üzerine yerleşimi gösterilmektedir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMTS döşeme katmanı" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () ile ilgili kalemin <a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS döşeme katmanını</a> inceleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customize-a-tile-layer"></a>Döşeme katmanını özelleştirme

Döşeme katmanı sınıfının birçok stil seçeneği vardır. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='Döşeme katmanı seçenekleri' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>kutucuğu katman seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Görüntü katmanı ekleme](./map-add-image-layer.md)
