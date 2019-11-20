---
title: Azure haritalar 'a bir sembol katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına semboller ekleme.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976169"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Haritaya sembol katmanı ekleme

Bir simge, bir veri kaynağına bağlanabilir ve belirli bir noktada bir simge ve/veya metin işlemek için kullanılabilir. Sembol katmanları WebGL kullanılarak işlenir ve haritadaki büyük noktaların koleksiyonunu işlemek için kullanılabilir. Bu katman, haritada çok daha fazla nokta verisini (ulaşılabilir, HTML işaretçileri kullanılarak) iyi bir performansla işleyebilir. Ancak, sembol katmanı stil için geleneksel CSS ve HTML öğelerini desteklemez.  

> [!TIP]
> Sembol katmanları varsayılan olarak, bir veri kaynağındaki tüm geometrilerin koordinatlarını işler. Katmanı yalnızca nokta geometrisi özelliklerinin oluşturduğu şekilde sınırlamak için `filter` `['==', ['geometry-type'], 'Point']` katmanın özelliğini olarak ayarlayın ve `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` MultiPoint özelliklerini de dahil etmek istiyorsanız.

## <a name="add-a-symbol-layer"></a>Sembol katmanı ekleme

Haritaya bir sembol katmanı eklemek ve verileri işlemek için önce bir veri kaynağının oluşturulması ve haritanın eklenmesi gerekir. Daha sonra bir sembol katmanı, verileri almak için veri kaynağında oluşturulup geçirilebilir. Son olarak, işlenecek bir şey olması için verilerin veri kaynağına eklenmesi gerekir. Aşağıdaki kod, bir sembol katmanı kullanarak haritada tek bir noktayı işlemek için yüklendikten sonra haritaya eklenmesi gereken kodu gösterir. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Haritaya eklenebilecek dört farklı türde nokta verisi vardır:

- GeoJSON nokta geometrisi-bu nesne yalnızca bir noktanın koordinatını içerir ve başka hiçbir şey yapmaz. `atlas.data.Point` Yardımcı sınıfı bu nesneleri kolayca oluşturmak için kullanılabilir.
- GeoJSON MultiPoint geometrisi-bu nesne birden çok noktanın koordinatlarını içerir ancak başka hiçbir şey yapmaz. `atlas.data.MultiPoint` Yardımcı sınıfı bu nesneleri kolayca oluşturmak için kullanılabilir.
- GeoJSON özelliği-bu nesne, tüm GeoJSON geometrisinden ve geometri ile ilişkili meta verileri içeren bir özellik kümesinden oluşur. `atlas.data.Feature` Yardımcı sınıfı bu nesneleri kolayca oluşturmak için kullanılabilir.
- `atlas.Shape`sınıfı, GeoJSON geometrisini ve geometri ile ilişkili meta verileri içeren özelliklerden oluşan bir dizi özelliği içerir. Bir veri kaynağına GeoJSON nesnesi eklenirse, bir katmanda kolayca işlenebilir. ancak, bu GeoJSON nesnesinin koordinatlar özelliği güncelleştirilirse, JSON nesnesinde bir güncelleştirmeyi tetiklemek için bir mekanizma olmadığından veri kaynağı ve eşleme değişmez. Şekil sınıfı, içerdiği verileri güncelleştirmek için işlevler sağlar ve bir değişiklik yapıldığında veri kaynağı ve eşleme otomatik olarak bilgilendirilir ve güncelleştirilir. 

Aşağıdaki kod örneği bir geojson noktası geometrisi oluşturur ve güncelleştirilmesini kolaylaştırmak için `atlas.Shape` sınıfı sınıfına geçirir. Haritanın merkezi başlangıçta bir sembolü işlemek için kullanılır. Bir tıklama olayı, ne zaman tetiklendiğinde, haritada simgenin konumunu güncelleştiren şekil `setCoordinates` işleviyle birlikte, fare tıklamış olduğu gibi bir tıklama olayı eklenir.

<br/>

<iframe height='500' scrolling='no' title='PIN konumunu değiştir' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>anahtar pin konumunu</a> görüntüleyin.
</iframe>

> [!TIP]
> Varsayılan olarak, performans için sembol katmanları örtüşen sembolleri gizleyerek sembolleri işlemeyi iyileştirir. Gizli sembolleri yakınlaştırdığınızda görünür hale gelir. Bu özelliği devre dışı bırakmak ve tüm sembolleri her zaman işlemek için, `allowOverlap` `iconOptions` seçeneklerinin özelliğini olarak `true`ayarlayın.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Sembol katmanına özel simge ekleme

Sembol katmanları WebGL kullanılarak işlenir. Bu nedenle, simge görüntüleri gibi tüm kaynakların WebGL bağlamına yüklenmesi gerekir. Bu örnek, harita kaynaklarına özel bir simgenin nasıl ekleneceğini ve ardından Haritada özel bir sembol ile nokta verisi işlemek için nasıl kullanılacağını gösterir. Sembol `textField` katmanının özelliği bir ifadenin belirtilmesini gerektirir. Bu durumda, sıcaklık özelliğini işlemek istiyoruz, ancak bir sayı olduğundan, bir dizeye dönüştürülmesi gerekir. Ayrıca buna "°F" eklemek istiyoruz. Bunu yapmak için bir ifade kullanılabilir; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Özel sembol resmi simgesi' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>özel sembol görüntüsü simgesine</a> bakın.
</iframe>

> [!TIP]
> Azure Haritalar Web SDK 'Sı, sembol katmanıyla birlikte kullanabileceğiniz çeşitli özelleştirilebilir görüntü şablonları sağlar. Daha fazla bilgi için bkz. [görüntü şablonlarını kullanma](how-to-use-image-templates-web-sdk.md) belgesi.

## <a name="customize-a-symbol-layer"></a>Sembol katmanını özelleştirme 

Sembol katmanında birçok stil seçeneği mevcuttur. Bu çeşitli stil seçeneklerini test etmek için bir araç aşağıda verilmiştir.

<br/>

<iframe height='700' scrolling='no' title='Sembol katmanı seçenekleri' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/PxVXje/'>sembolü katman seçeneklerine</a> bakın.
</iframe>

> [!TIP]
> Yalnızca bir sembol katmanıyla metin işlemek istediğinizde, simge seçeneklerinin `image` özelliğini olarak `'none'`ayarlayarak simgeyi gizleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

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

> [!div class="nextstepaction"]
> [Çokgen katmanı Ekle](map-add-shape.md)

> [!div class="nextstepaction"]
> [Kabarcık katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML üreticileri ekleme](map-add-bubble-layer.md)
