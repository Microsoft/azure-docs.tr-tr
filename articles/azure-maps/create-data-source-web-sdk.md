---
title: Azure haritalar 'da veri kaynağı oluşturma | Microsoft Docs
description: Veri kaynağı oluşturma ve Azure Maps web SDK ile kullanma.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36c06182d0807ce3d255477a865023ae7b74e2cb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874920"
---
# <a name="create-a-data-source"></a>Bir veri kaynağı oluşturun

Azure Haritalar Web SDK 'Sı, verileri sorgulamak ve işlemek için en iyi duruma getirmek üzere verileri en iyi duruma getirir. Şu anda iki tür veri kaynağı vardır:

**GeoJSON veri kaynağı**

Coğrafi JSON tabanlı veri kaynağı, `DataSource` sınıfını kullanarak verileri yerel olarak yükleyebilir ve saklayabilir. GeoJSON verileri, [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) ad alanındaki yardımcı sınıflar kullanılarak el ile oluşturulabilir veya oluşturulabilir. Sınıfı `DataSource` , yerel veya uzak coğrafi JSON dosyalarını içeri aktarmaya yönelik işlevler sağlar. Uzak GeoJSON dosyaları CORs etkin bir uç noktada barındırılmalıdır. Sınıfı `DataSource` , kümeleme noktası verileri için işlevsellik sağlar. Veriler, `DataSource` sınıfla kolayca eklenebilir, kaldırılabilir ve güncelleştirilir.


> [!TIP]
> Bir `DataSource`içindeki tüm verilerin üzerine yazmak istiyorsanız, `clear` sonra `add` işlevlerine çağrılar yaparsanız, eşleme bir gecikmeye neden olabilecek iki kez yeniden işlemeye çalışacaktır. Bunun yerine, `setShapes` veri kaynağındaki tüm verileri kaldıracak ve değiştirecek ve yalnızca haritanın tek bir yeniden işlemesini tetikleyeceği işlevi kullanın.

**Vektör kutucuk kaynağı**

Vektör kutucuk kaynağı bir vektör kutucuk katmanına nasıl erişebileceğinizi açıklar ve [Vectortilesource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) sınıfı kullanılarak oluşturulabilir. Azure haritalar, açık bir standart olan [Mapbox vektör kutucuk belirtimiyle](https://github.com/mapbox/vector-tile-spec)hizalanır. Vektör döşeme katmanları döşeme katmanlarına benzerdir, ancak her kutucuk bir raster görüntüsü olacak şekilde, vektör harita verilerini içeren sıkıştırılmış bir dosya (PBF biçimi) ve her katmanın stiline bağlı olarak, istemci üzerinde oluşturulabilen ve Stillenebilir bir veya daha fazla katman bulunur. Vektör kutucuğunda bulunan veriler, işaret, çizgi ve çokgenler biçimindeki coğrafi özellikler içerir. Vektör kutucuğu katmanlarından oluşan raster döşeme katmanları üzerinde birçok avantaj vardır;

 - Vektör kutucuğunun dosya boyutu genellikle denk bir raster kutucuğundan çok daha küçüktür. Bu nedenle, daha düşük gecikme süresi ve daha hızlı bir eşleme anlamına gelir. Bu, daha iyi bir kullanıcı deneyimi oluşturur.
 - Vektör kutucukları istemcide işlendiği için, üzerinde görüntülenmekte olan cihazın çözünürlüğüne uyum sağlayabilir. Bu, çok daha iyi tanımlanmış ve Crystal Clear etiketleri ile görüntülenen işlenen haritaların kullanılmasına olanak sağlar. 
 - Vektör eşlemlerdeki verilerin stilini değiştirmek, yeni stilin istemciye uygulanmasından bu yana verilerin indirilmesini gerektirmez. Buna karşılık, bir raster kutucuk katmanının stilini değiştirmenin genellikle yeni stilin uygulanmış olduğu sunucudan kutucuk yüklemesi gerekir.
 - Veriler vektör biçiminde teslim edildiğinden, verileri hazırlamak için gereken sunucu tarafı işleme, daha yeni verilerin daha hızlı kullanılabilir hale getirilme anlamına gelir.

Vektör kaynağı kullanan tüm katmanların bir `sourceLayer` değer belirtmesi gerekir. 

Oluşturulduktan sonra veri kaynakları, bir `map.sources` [sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)olan özelliği aracılığıyla haritaya eklenebilir. Aşağıdaki kod, oluşturma `DataSource` ve eşlemeye ekleme işlemlerinin nasıl yapılacağını gösterir.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Bir veri kaynağını katmana bağlama

Veriler, işleme katmanları kullanılarak haritada işlenir. Tek bir veri kaynağına, bir veya daha fazla işleme katmanı tarafından başvurulabilir. Aşağıdaki işleme katmanları, bir veri kaynağının güç olmasını gerektirir:

- [Kabarcık katmanı](map-add-bubble-layer.md) -nokta verilerini haritada ölçeklendirilen daireler olarak işler.
- [Sembol katmanı](map-add-pin.md) -nokta verilerini simge ve/veya metin olarak işler.
- [Isı haritası katmanı](map-add-heat-map-layer.md) -nokta verilerini yoğunluk ısı haritası olarak işler.
- [Çizgi katmanı](map-add-shape.md) -, poligonun hattını ve anahatlarını oluşturmak için kullanılabilir. 
- [Çokgen katmanı](map-add-shape.md) -bir çokgen alanını düz renk veya görüntü düzeniyle doldurur.

Aşağıdaki kod, bir veri kaynağının nasıl oluşturulacağını, haritaya nasıl ekleneceğini ve bir kabarcık katmanına nasıl bağlanacağını gösterir ve sonra coğrafi JSON noktası verilerini uzak bir konumdan buna içeri aktarır. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Bu veri kaynaklarına bağlanmadığında, bunun yerine doğrudan oluşturdukları verileri yükleyen ek işleme katmanları vardır. 

- [Görüntü katmanı](map-add-image-layer.md) -Haritanın üstündeki tek bir görüntünün üzerine biçimlendirmeler ve köşelerini belirtilen koordinat kümesine bağlar.
- [Döşeme katmanı](map-add-tile-layer.md) -haritanın üzerine bir raster döşeme katmanı uygular.

## <a name="one-data-source-with-multiple-layers"></a>Birden çok katmanlı bir veri kaynağı

Birden çok katman tek bir veri kaynağına bağlanabilir. Bu, tek bir ses gerektirebilir, ancak bunun yararlı hale geldiği birçok farklı senaryo vardır. Örneğin, çokgen çizim deneyimi oluşturma senaryosunu alın. Bir kullanıcının Çokgen çizmesini sağlamak için, Kullanıcı haritaya işaret eklerken, Fill Çokgen alanını işlemelidir. Poligonu özetleyen stillendirilmiş bir çizgi eklemek, çizmekte olduğu gibi çokgenin kenarlarını daha kolay görmenizi sağlar. Son olarak, bir işleç veya işaret gibi bir dizi tutamacı eklemek, çokgenin her bir konumunun üstünde her bir konumun düzenlenmesinin daha kolay olmasını sağlar. Bu senaryoyu gösteren bir görüntü aşağıda verilmiştir.

![Tek bir veri kaynağından birden çok katman işleme verilerini gösteren eşleme](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Çoğu eşleme platformunda bu senaryoyu başarmak için poligon her konum için bir çokgen nesnesi, bir çizgi nesnesi ve PIN oluşturmanız gerekir. Çokgen değiştirildiğinde, satırı ve PIN 'leri el ile güncelleştirmeniz gerekir ve bu da karmaşık hale gelebilir.

Azure haritalar sayesinde, bir veri kaynağında aşağıdaki kodda gösterildiği gibi tek bir çokgen olması yeterlidir.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Açılan pencere Ekle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Kabarcık katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı Ekle](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı Ekle](map-add-shape.md)

> [!div class="nextstepaction"]
> [Isı haritası ekleme](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](https://docs.microsoft.com/samples/browse/?products=azure-maps)