---
title: Azure haritalar 'a çokgen katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına çokgen katmanı ekleme.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976134"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Haritaya çokgen katmanı Ekle

Bu makalede, bir çokgen katmanı kullanarak haritada `Polygon` ve `MultiPolygon` Özellik geometrileri arasındaki alanların nasıl işleneceğini gösterir. Azure Haritalar Web SDK 'Sı Ayrıca, [genişletilmiş GeoJSON şemasında](extend-geojson.md#circle)tanımlandığı şekilde daire geometrileri oluşturulmasını destekler. Bu daireler haritada işlendiğinde çokgenler halinde dönüştürülür. Tüm özellik geometrileri, Atlas ile sarmalandıktan sonra da kolayca güncelleştirilebilen olabilir [. Şekil](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) sınıfı.

## <a name="use-a-polygon-layer"></a>Çokgen katmanı kullanma 

Bir çokgen katmanı bir veri kaynağına bağlıyken ve haritada yüklendiğinde, `Polygon` ve `MultiPolygon` özelliklerinin alanını işler. Aşağıdaki kod, bir çokgen oluşturma, bir veri kaynağına ekleme ve [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) sınıfını kullanarak bir Çokgen katman ile işleme işlemlerinin nasıl yapılacağını gösterir.

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya Çokgen ekleme ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>haritaya Çokgen ekleme</a> kalemine bakın.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Bir çokgen ve çizgi katmanını birlikte kullanma

Çokgenler ana hattını işlemek için bir çizgi katmanı kullanılabilir. Aşağıdaki kod örneği, önceki örnekte olduğu gibi bir çokgen oluşturur, ancak şimdi veri kaynağına bağlı ikinci bir katman olarak bir çizgi katmanı ekler.  

<iframe height='500' scrolling='no' title='Çokgen eklenecek Çokgen ve çizgi katmanı' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Çokgen eklemek için kalem poligonu ve çizgi katmanını</a> inceleyin.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Çokgeni bir desenli doldur

Bir çokgen bir renkle doldurulmasına ek olarak, bir resim deseninin de kullanılabilir olması. Haritalar görüntüsü Sprite kaynaklarına bir resim kalıbı yükleyin ve ardından bu görüntüye Çokgen katmanının `fillPattern` özelliği ile başvurun.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Çokgen dolgusu kalıbı" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Çokgen dolgusu düzenine</a> bakın.
</iframe>


> [!TIP]
> Azure Haritalar Web SDK 'Sı, Fill desenleri olarak kullanabileceğiniz çeşitli özelleştirilebilir görüntü şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

## <a name="customize-a-polygon-layer"></a>Çokgen katmanını özelleştirme

Çokgen katmanın yalnızca birkaç stil seçeneği vardır. İşte deneyebileceğiniz bir araç.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da bkz. Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() ile kalem <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> .
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Haritaya daire ekleme

Azure Maps, coğrafi olarak gösterildiği gibi daireler için bir tanım sağlayan GeoJSON şemasının genişletilmiş bir sürümünü kullanır [](extend-geojson.md#circle). Bir daire `Point` , değerine sahip bir `subType` `"Circle"` özelliği ve `radius` ölçü cinsinden yarıçapı temsil eden bir sayı olan bir özelliği oluşturarak haritada oluşturulabilir. Örneğin:

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

Azure Haritalar Web SDK 'sı, bu `Point` `Polygon` özellikleri kapakların altındaki özelliklere dönüştürür ve aşağıdaki kod örneğinde gösterildiği gibi Çokgen ve çizgi katmanları kullanılarak haritada oluşturulabilir.

<br/>

<iframe height='500' scrolling='no' title='Haritaya daire ekleme' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>haritaya bir daire ekleme</a> kalemine bakın.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Bir geometriyi güncellemenin kolay olmasını sağlama

Bir `Shape` sınıf bir [geometriyi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) veya [özelliği](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) sarmalayan ve bunların güncelleştirilmesini ve bakımını kolaylaştırır. Bir şekil, bir geometri ve bir özellikler kümesi geçirerek ya da aşağıdaki kodda gösterildiği gibi bir özelliğe geçirerek oluşturulabilir.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Aşağıdaki kod örneği, bir daire GeoJSON nesnesinin bir şekil sınıfıyla nasıl kaydırılacağını ve bir kaydırıcı kullanarak RADIUS özelliğinin kolayca güncelleştirilmesini gösterir. Şekildeki yarıçap değeri değiştikçe, dairenin işlenmesi haritada otomatik olarak güncelleştirilir.

<br/>

<iframe height='500' scrolling='no' title='Şekil özelliklerini güncelleştir' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>güncelleştirme Şekil özelliklerine</a> bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Poligon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Haritalarınıza eklenecek daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Açılan pencere Ekle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı Ekle](map-add-line-layer.md)

Ek kaynaklar:

> [!div class="nextstepaction"]
> [Azure haritalar GeoJSON belirtim uzantısı](extend-geojson.md#circle)