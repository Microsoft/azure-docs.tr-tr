---
title: Azure haritalar 'a kalıp çokgen katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına bir extabaya çokgen katmanı ekleme.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170717"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Haritaya kalıp çokgen katmanı Ekle

Bu makalede, `Polygon` ' ın ve `MultiPolygon` Özellik geometrisinin alanlarının haritada yükseltilmiş şekiller olarak işlenmesi için Çokgen katman katmanının nasıl kullanılacağı gösterilmektedir. Azure Haritalar Web SDK 'Sı Ayrıca, [genişletilmiş GeoJSON şemasında](extend-geojson.md#circle)tanımlandığı şekilde daire geometrileri oluşturulmasını destekler. Bu daireler haritada işlendiğinde çokgenler halinde dönüştürülür. Tüm özellik geometrileri, Atlas ile sarmalandıktan sonra da kolayca güncelleştirilebilen olabilir [. Şekil](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) sınıfı.


## <a name="use-a-polygon-extrusion-layer"></a>Çokgen katman kullan

Bir [Çokgen katman katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) veri kaynağına bağlıyken ve haritada yüklendiğinde, bir `Polygon` ve `MultiPolygon` özelliklerinin, yükseltilmiş şekiller olarak bir bölümünü işler. Çokgen katman `height` ve `base` özellikleri, yükseltme şeklinin zemin ve yüksekliğinden **Ölçü**cinsinden temel mesafeyi tanımlar. Aşağıdaki kod, bir çokgen oluşturmayı, bir veri kaynağına eklemeyi ve Çokgen katman sınıfını kullanarak nasıl işleneceğini gösterir.

> [!Note]
> Çokgen katman katmanında tanımlanan `base` değeri, `height` ' den küçük veya buna eşit olmalıdır.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yükseltilmiş Çokgen" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'Da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>yükseltme poligonu</a> inceleyin.</iframe>


## <a name="add-data-driven-multipolygons"></a>Veri temelli MultiPolygon ekleme

Bir choroptath eşlemesi, `height` ve `fillColor` özelliklerini `Polygon` ve `MultiPolygon` özellik geometrileri tarafından istatistiksel değişken ölçüyle orantılı olarak ayarlayarak Çokgen katman katmanı kullanılarak oluşturulabilir. Aşağıdaki kod örneğinde, durum ile popülasyon yoğunluğu ölçüsünün temelinde U. S ' nin yükseltilmiş bir choroptath Haritası gösterilmektedir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yükseltilmiş choroptath Haritası" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'Da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından gösterilen kalem ile <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Haritayı Yükselt</a> sayfasına bakın.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Haritaya daire ekleme

Azure Maps, coğrafi [olarak gösterildiği gibi](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)daireler için bir tanım sağlayan geojson şemasının genişletilmiş bir sürümünü kullanır. Yükseltilmiş bir daire, `Circle` ' nin `subType` özelliği ve **Ölçü**cinsinden yarıçapı temsil eden numaralandırılmış bir `Radius` özelliği ile bir `point` özelliği oluşturularak haritada oluşturulabilir. Örneğin:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Haritalar Web SDK 'Sı, bu `Point` özelliklerini, bir arada bulunan `Polygon` özelliklerine dönüştürür ve aşağıdaki kod örneğinde gösterildiği gibi Çokgen bir katman kullanılarak haritada oluşturulabilir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone hava sahası Çokgen" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'Da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>kuruta hava sahası poligonu</a> görüntüleyin.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Gen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Çokgen kalıp katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Ek kaynaklar:

> [!div class="nextstepaction"]
> [Azure haritalar GeoJSON belirtim uzantısı](extend-geojson.md#circle)
