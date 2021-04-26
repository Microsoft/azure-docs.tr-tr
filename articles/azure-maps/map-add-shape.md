---
title: Haritaya çokgen katmanı ekleme | Microsoft Azure haritaları
description: Haritalara çokgenler veya daireler eklemeyi öğrenin. Geometrik şekilleri özelleştirmek ve bunların güncelleştirilmesini ve bakımını kolaylaştırmak için bkz. Azure Maps web SDK 'sını kullanma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891006"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Haritaya çokgen katmanı Ekle

Bu makalede, `Polygon` `MultiPolygon` bir çokgen katmanı kullanarak haritada ve özellik geometrileri arasındaki alanların nasıl işleneceğini gösterir. Azure Haritalar Web SDK 'Sı Ayrıca, [genişletilmiş GeoJSON şemasında](extend-geojson.md#circle)tanımlandığı şekilde daire geometrileri oluşturulmasını destekler. Bu daireler haritada işlendiğinde çokgenler halinde dönüştürülür. Tüm özellik geometrileri, Atlas ile sarmalandıktan sonra kolayca güncelleştirilebilen olabilir [. Şekil](/javascript/api/azure-maps-control/atlas.shape) sınıfı.

## <a name="use-a-polygon-layer"></a>Çokgen katmanı kullanma 

Bir çokgen katmanı bir veri kaynağına bağlıyken ve haritada yüklendiğinde, alanı `Polygon` ve `MultiPolygon` özellikleri işler. Bir çokgen oluşturmak için bir veri kaynağına ekleyin ve [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) sınıfını kullanarak Çokgen bir katman ile oluşturun.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Yukarıdaki kodun tamamen ve çalışan örneği aşağıda verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya Çokgen ekleme ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>haritaya Çokgen ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Bir çokgen ve çizgi katmanını birlikte kullanma

Çokgenler ana hattını işlemek için çizgi katmanı kullanılır. Aşağıdaki kod örneği, önceki örnekte olduğu gibi bir çokgen oluşturur, ancak şimdi bir çizgi katmanı ekler. Bu çizgi katmanı, veri kaynağına bağlı ikinci bir katmandır.  

<br/>

<iframe height='500' scrolling='no' title='Çokgen eklenecek Çokgen ve çizgi katmanı' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Çokgen eklemek Için kalem poligonu ve çizgi katmanını</a> inceleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Çokgeni bir desenli doldur

Çokgeni bir renkle doldurmanın yanı sıra, çokgeni doldurmak için de bir resim kalıbı kullanabilirsiniz. Haritalar görüntüsü Sprite kaynaklarına bir resim kalıbı yükleyin ve ardından bu görüntüye `fillPattern` Çokgen katmanının özelliği ile başvurun.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çokgen dolgusu kalıbı" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Çokgen dolgusu düzenine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>


> [!TIP]
> Azure Haritalar Web SDK 'Sı, Fill desenleri olarak kullanabileceğiniz çeşitli özelleştirilebilir görüntü şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

## <a name="customize-a-polygon-layer"></a>Çokgen katmanını özelleştirme

Çokgen katmanın yalnızca birkaç stil seçeneği vardır. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/LXvxpg/'></a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>Codepen</a>'da bkz. Azure Maps () ile kalem LXvxpg.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Haritaya daire ekleme

Azure haritalar, [burada](extend-geojson.md#circle)belirtildiği gibi, daireler için bir tanım sağlayan geojson şemasının genişletilmiş bir sürümünü kullanır. Bir daire, bir özellik oluşturularak haritada işlenir `Point` . Bu, değerine sahip bir `Point` `subType` özelliğe `"Circle"` ve `radius` Ölçü cinsinden yarıçapı temsil eden bir sayı içeren bir özelliğe sahiptir. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Haritalar Web SDK 'Sı bu `Point` özellikleri özelliklerine dönüştürür `Polygon` . Daha sonra, bu özellikler, aşağıdaki kod örneğinde gösterildiği gibi Çokgen ve çizgi katmanları kullanılarak haritada işlenir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya daire ekleme' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>haritaya bir daire ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Bir geometriyi güncellemenin kolay olmasını sağlama

Bir `Shape` sınıf bir [geometriyi](/javascript/api/azure-maps-control/atlas.data.geometry) veya [özelliği](/javascript/api/azure-maps-control/atlas.data.feature) sarmalayan ve bu özellikleri güncelleştirmeyi ve bakımını kolaylaştırır. Şekil değişkeni oluşturmak için, şekil oluşturucusuna bir geometri veya bir özellikler kümesi geçirin.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Aşağıdaki kod örneği, bir daire GeoJSON nesnesinin şekil sınıfıyla nasıl sarılacağını gösterir. Şekildeki yarıçap değeri değiştikçe, daire otomatik olarak haritada işlenir.

<br/>

<iframe height='500' scrolling='no' title='Şekil özelliklerini güncelleştir' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>güncelleştirme Şekil özelliklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Poligon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Haritalarınıza eklenecek daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Açılır pencere ekleme](map-add-popup.md)

> [!div class="nextstepaction"]
> [Veri temelli stil ifadeleri kullanma](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](map-add-line-layer.md)

Ek kaynaklar:

> [!div class="nextstepaction"]
> [Azure haritalar GeoJSON belirtim uzantısı](extend-geojson.md#circle)