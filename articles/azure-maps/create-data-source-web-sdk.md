---
title: Microsoft Azure Maps 'ta eşleme için bir veri kaynağı oluşturma
description: "Bir harita için veri kaynağı oluşturmayı öğrenin. Azure Maps web SDK 'sının kullandığı veri kaynakları hakkında bilgi edinin: GeoJSON kaynakları ve vektör kutucukları."
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678846"
---
# <a name="create-a-data-source"></a>Veri kaynağı oluşturma

Azure Haritalar Web SDK 'Sı verileri veri kaynaklarında depolar. Veri kaynaklarını kullanmak, sorgulamak ve işlemek için veri işlemlerini iyileştirir. Şu anda iki tür veri kaynağı vardır:

* **Geojson kaynağı**: coğrafi konum verilerini yerel olarak geojson biçiminde yönetir. Küçük ve orta ölçekli veri kümeleri için iyi (yüzlerce yüz binlerce şekil).
* **Vektör kutucuk kaynağı**: haritalar döşeme sistemine bağlı olarak, geçerli harita görünümü için vektör kutucukları olarak biçimlendirilen verileri yükler. Büyük ve çok büyük veri kümeleri (milyonlarca veya milyarlarca şekil) için idealdir.

## <a name="geojson-data-source"></a>GeoJSON veri kaynağı

Coğrafi JSON tabanlı veri kaynağı, sınıfını kullanarak verileri yerel olarak yükler ve depolar `DataSource` . GeoJSON verileri, [Atlas. Data](/javascript/api/azure-maps-control/atlas.data) ad alanındaki yardımcı sınıflar kullanılarak el ile oluşturulabilir veya oluşturulabilir. `DataSource`Sınıfı, yerel veya uzak coğrafi JSON dosyalarını içeri aktarmak için işlevler sağlar. Uzak GeoJSON dosyaları CORs etkin bir uç noktada barındırılmalıdır. `DataSource`Sınıfı, kümeleme noktası verileri için işlevsellik sağlar. Ve, veriler kolayca eklenebilir, kaldırılabilir ve `DataSource` sınıfla güncellenir. Aşağıdaki kod, coğrafi JSON verilerinin Azure haritalar 'da nasıl oluşturulagösterdiğini gösterir.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Oluşturulduktan sonra veri kaynakları, `map.sources` bir [sourcemanager](/javascript/api/azure-maps-control/atlas.sourcemanager)olan özelliği aracılığıyla haritaya eklenebilir. Aşağıdaki kod, oluşturma ve eşlemeye ekleme işlemlerinin nasıl yapılacağını gösterir `DataSource` .

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

Aşağıdaki kod, GeoJSON verilerinin bir öğesine eklenebilme yollarını gösterir `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> , Bir içindeki tüm verilerin üzerine yazmak istediğinizi varsayalım `DataSource` . `clear`Then işlevlerine çağrılar yaparsanız `add` , eşleme iki kez yeniden işleyebilir ve bu da gecikmeye neden olabilir. Bunun yerine, `setShapes` veri kaynağındaki tüm verileri kaldıracak ve değiştirecek ve yalnızca haritanın tek bir yeniden işlemesini tetikleyeceği işlevini kullanın.

## <a name="vector-tile-source"></a>Vektör kutucuk kaynağı

Vektör kutucuk kaynağı bir vektör kutucuk katmanına nasıl erişebileceğinizi açıklar. Vektör kutucuk kaynağı oluşturmak için [Vectortilesource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) sınıfını kullanın. Vektör döşeme katmanları döşeme katmanlarına benzerdir, ancak aynı değildir. Döşeme katmanı bir raster görüntüsüdür. Vektör döşeme katmanları, **PBF** biçiminde sıkıştırılmış bir dosyadır. Bu sıkıştırılmış dosya, vektör eşleme verilerini ve bir veya daha fazla katmanı içerir. Dosya, her katmanın stiline bağlı olarak işlenilerek, istemci üzerinde stil oluşturulabilir. Vektör kutucuğunda bulunan veriler, işaret, çizgi ve çokgenler biçimindeki coğrafi özellikler içerir. Raster döşeme katmanları yerine vektör kutucuğu katmanlarını kullanmanın çeşitli avantajları vardır:

* Vektör kutucuğunun dosya boyutu genellikle denk bir raster kutucuğundan çok daha küçüktür. Bu nedenle, daha az bant genişliği kullanılır. Daha düşük gecikme süresi, daha hızlı bir harita ve daha iyi bir kullanıcı deneyimi anlamına gelir.
* Vektör kutucukları istemcide işlendiği için, üzerinde görüntülendikleri cihazın çözünürlüğüne uyarlarlar. Sonuç olarak, işlenen haritalar Crystal Clear etiketleriyle daha iyi tanımlanmış şekilde görünür.
* Yeni stil istemciye uygulanamadığından, vektör eşlemlerdeki verilerin stilini değiştirmek, verilerin yeniden indirilmesini gerektirmez. Buna karşılık, bir raster kutucuk katmanının stilini değiştirmenin genellikle sunucudan kutucukları yükleme ve ardından yeni stili uygulama gerekir.
* Veriler vektör biçiminde teslim edildiğinden, verileri hazırlamak için daha az sunucu tarafı işleme gerekir. Sonuç olarak, daha yeni veriler daha hızlı kullanılabilir hale getirilebilir.

Azure haritalar, açık bir standart olan [Mapbox vektör kutucuk belirtimine](https://github.com/mapbox/vector-tile-spec)uyar. Azure Maps, platformun bir parçası olarak aşağıdaki vektör kutucukları hizmetlerini sağlar:

- Yol kutucukları [belge](/rest/api/maps/renderv2/getmaptilepreview)  |  [verileri biçimi ayrıntıları](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Trafik olayları [belge](/rest/api/maps/traffic/gettrafficincidenttile)  |  [verileri biçimi ayrıntıları](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Trafik akışı [belgeleri](/rest/api/maps/traffic/gettrafficflowtile)  |  [veri biçimi ayrıntıları](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure haritalar Oluşturucu (Önizleme) Ayrıca özel vektör kutucuklarının oluşturma ve [kutucuk oluşturma v2](/rest/api/maps/renderv2/getmaptilepreview) aracılığıyla erişilmesine izin verir

> [!TIP]
> Azure haritalar işleme hizmeti 'nden Web SDK 'Sı ile vektör veya raster görüntü kutucukları kullanırken, `atlas.microsoft.com` yer tutucu ile değiştirebilirsiniz `{azMapsDomain}` . Bu yer tutucu, eşleme tarafından kullanılan aynı etki alanıyla değiştirilmelidir ve aynı kimlik doğrulama ayrıntılarını da otomatik olarak ekler. Bu, Azure Active Directory kimlik doğrulaması kullanırken işleme hizmeti ile kimlik doğrulamasını büyük ölçüde basitleştirir.

Haritadaki bir vektör kutucuk kaynağından verileri göstermek için, kaynağı veri işleme katmanlarından birine bağlayın. Vektör kaynağı kullanan tüm katmanların seçeneklerde bir değer belirtmesi gerekir `sourceLayer` . Aşağıdaki kod, Azure Maps trafik akışı vektör kutucuk hizmetini bir vektör kutucuk kaynağı olarak yükler ve bir çizgi katmanını kullanarak haritada görüntüler. Bu vektör kutucuk kaynağı kaynak katmanda "trafik akışı" adlı tek bir veri kümesine sahiptir. Bu veri kümesindeki satır verilerinde, `traffic_level` rengi seçmek ve çizgilerin boyutunu ölçeklendirmek için bu kodda kullanılan adlı bir özellik vardır.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vektör döşeme çizgisi katmanı" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>vektör kutucuk çizgisi katmanına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Bir veri kaynağını katmana bağlama

Veriler, işleme katmanları kullanılarak haritada işlenir. Tek bir veri kaynağına, bir veya daha fazla işleme katmanı tarafından başvurulabilir. Aşağıdaki işleme katmanları bir veri kaynağı gerektirir:

* [Kabarcık katmanı](map-add-bubble-layer.md) -nokta verilerini haritada ölçeklendirilen daireler olarak işler.
* [Sembol katmanı](map-add-pin.md) -nokta verilerini simge veya metin olarak işler.
* [Isı haritası katmanı](map-add-heat-map-layer.md) -nokta verilerini yoğunluk ısı haritası olarak işler.
* [Çizgi katmanı](map-add-shape.md) -bir çizgiyi işleme ve çokgen ana hattını işleme. 
* [Çokgen katmanı](map-add-shape.md) -bir çokgen alanını düz renk veya görüntü düzeniyle doldurur.

Aşağıdaki kod, bir veri kaynağının nasıl oluşturulacağını, haritaya nasıl ekleneceğini ve bir kabarcık katmanına nasıl bağlanacağını gösterir. Ve sonra, coğrafi JSON noktası verilerini uzak bir konumdan veri kaynağına aktarın. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Bu veri kaynaklarına bağlanmayama verileri doğrudan işleme için yükleyen ek işleme katmanları vardır. 

* [Görüntü katmanı](map-add-image-layer.md) -Haritanın üstündeki tek bir görüntünün üzerine biçimlendirmeler ve köşelerini belirtilen koordinat kümesine bağlar.
* [Döşeme katmanı](map-add-tile-layer.md) -haritanın üzerine bir raster döşeme katmanı uygular.

## <a name="one-data-source-with-multiple-layers"></a>Birden çok katmanlı bir veri kaynağı

Birden çok katman tek bir veri kaynağına bağlanabilir. Bu seçeneğin yararlı olduğu birçok farklı senaryo vardır. Örneğin, bir kullanıcının Çokgen çizdiği senaryoyu düşünün. Kullanıcı haritaya işaret eklediğinden Çokgen alanını işlemeli ve doldurmalıdır. Çokgen ana hattına stil eklemek, Kullanıcı çizerken poligonun kenarlarını daha kolay görmenizi sağlar. Poligon tek bir konumu rahat bir şekilde düzenlemek için her bir konumun üzerine bir pin veya işaret gibi bir tanıtıcı ekleyebiliriz.

![Tek bir veri kaynağından birden çok katman işleme verilerini gösteren eşleme](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Çoğu eşleme platformunda, poligon her konum için bir çokgen nesne, bir çizgi nesnesi ve bir PIN olması gerekir. Çokgen değiştirildiğinde, satırı ve PIN 'leri el ile güncelleştirmeniz gerekir, bu da hızlı bir şekilde karmaşık hale gelebilir.

Azure haritalar ile, bir veri kaynağında aşağıdaki kodda gösterildiği gibi tek bir çokgen olması yeterlidir.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> İşlevi kullanarak haritaya katman eklerken `map.layers.add` , var olan bir KATMANıN kimliği veya örneği ikinci bir parametre olarak geçirilebilir. Bu, eşlemenin mevcut katmanın altına eklenen yeni katmanı eklemesini söyler. Bu yöntem, katman KIMLIĞINI geçirmenin yanı sıra aşağıdaki değerleri de destekler.
>
> * `"labels"` -Harita etiketi katmanlarının altına yeni katmanı ekler.
> * `"transit"` -Harita yolu ve aktarım katmanlarının altına yeni katmanı ekler.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Açılır pencere ekleme](map-add-popup.md)

> [!div class="nextstepaction"]
> [Veri temelli stil ifadeleri kullanma](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Baloncuk katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](map-add-shape.md)

> [!div class="nextstepaction"]
> [Isı haritası ekleme](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](/samples/browse/?products=azure-maps)