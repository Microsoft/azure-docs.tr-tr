---
title: Basit veri katmanı ekleme | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'Sı tarafından sunulan uzamsal GÇ modülünü kullanarak basit bir veri katmanı eklemeyi öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896166"
---
# <a name="add-a-simple-data-layer"></a>Basit bir veri katmanı ekleme

Uzamsal GÇ modülü bir sınıf sağlar `SimpleDataLayer` . Bu sınıf, haritada stillendirilmiş özellikleri işlemeyi kolaylaştırır. Aynı hatta, karışık geometri türleri içeren stil özelliklerine ve veri kümelerine sahip veri kümelerini işleyebilir. Basit veri katmanı, birden çok işleme katmanını sarmalayarak ve stil ifadeleri kullanarak bu işlevselliğe ulaşır. Stil ifadeleri, bu sarmalanmış katmanların içindeki özelliklerin ortak stil özelliklerini arar. `atlas.io.read`İşlevi ve işlevi, `atlas.io.write` desteklenen bir dosya biçiminde stilleri okumak ve yazmak için bu özellikleri kullanır. Özellikler desteklenen bir dosya biçimine eklendikten sonra, bu dosya çeşitli amaçlarla kullanılabilir. Örneğin, dosya haritada stillendirilmiş özellikleri göstermek için kullanılabilir.

Stil özelliklerine ek olarak, `SimpleDataLayer` bir açılan şablonla yerleşik bir açılan pencere özelliği sağlar. Açılan pencere, bir özelliğe tıklandığında görüntülenir. İsterseniz varsayılan açılan özellik devre dışı bırakılabilir. Bu katman, kümelenmiş verileri de destekler. Bir kümeye tıklandığında, harita kümeye yakınlaştırıp tek noktalara ve alt kümelere genişletilir.

`SimpleDataLayer`Sınıfı birçok geometri türü ve özelliklere uygulanan birçok stil içeren büyük veri kümelerinde kullanılmak üzere tasarlanmıştır. Kullanıldığında, bu sınıf stil ifadeleri içeren altı katmanın ek yükünü ekler. Bu nedenle, çekirdek işleme katmanlarını kullanmak daha verimli bir durum olabilir. Örneğin, bir özellik üzerinde birkaç geometri türünü ve birkaç stili işlemek için bir çekirdek katman kullanın

## <a name="use-a-simple-data-layer"></a>Basit bir veri katmanı kullanın

`SimpleDataLayer`Sınıfı diğer işleme katmanları gibi kullanılır. Aşağıdaki kod, bir haritada basit bir veri katmanının nasıl kullanılacağını gösterir:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Veri kaynağına özellikler ekleyin. Daha sonra, basit veri katmanı, özelliklerin ne kadar en iyi şekilde işleneceğini anlayabilir. Tek tek özelliklerin stilleri, özellik üzerinde özellikler olarak ayarlanabilir. Aşağıdaki kod, özelliği olarak ayarlanmış bir GeoJSON noktası özelliğini gösterir `color` `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Aşağıdaki kod, basit veri katmanını kullanarak yukarıdaki nokta özelliğini işler. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basit veri katmanını kullanma" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Bkz. kalemin Azure Maps () ile birlikte <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>basit veri katmanını kullanma</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Basit veri katmanının gerçek gücü şu durumlarda gelir:

- Bir veri kaynağında birçok farklı özellik türü vardır; veya
- Veri kümesindeki Özellikler üzerinde tek tek ayarlanan çeşitli stil özelliklerine sahiptir; veya
- Veri kümesinin tam olarak neleri içerdiğinden emin değilsiniz.

Örneğin, XML veri akışlarını ayrıştırırken özelliklerin tam stillerini ve geometri türlerini bilmiyor olabilirsiniz. Aşağıdaki örnek, bir KML dosyasının özelliklerini işlerken basit veri katmanının gücünü gösterir. Ayrıca basit veri katmanı sınıfının sağladığı çeşitli seçenekleri gösterir.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Basit veri katmanı seçenekleri" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>basit veri katmanı seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


> [!NOTE]
> Bu basit veri katmanı, KML balonlarını veya özellik özelliklerini tablo olarak göstermek için [açılan şablon](map-add-popup.md#add-popup-templates-to-the-map) sınıfını kullanır. Varsayılan olarak, açılan pencerede işlenen tüm içerikler bir iframe 'in içinde güvenlik özelliği olarak korumalı olur. Ancak, sınırlamalar vardır:
>
> - Tüm betikler, formlar, işaretçi kilidi ve üst gezinti işlevleri devre dışı bırakıldı. Bağlantıların tıklandığında yeni bir sekmede açık olmasına izin verilir. 
> - `srcdoc`İframe 'ler üzerinde parametresini desteklemeyen eski tarayıcılar, az miktarda içerik işlemeye sınırlandırılmıştır.
> 
> Açılana yüklenen verilere güveniyorsanız ve bu betiklerin açılan pencerede yüklenmesine izin vermek istiyorsanız, açılan şablonlar seçeneğini yanlış olarak ayarlayarak bunu devre dışı bırakabilirsiniz `sandboxContent` . 

## <a name="default-supported-style-properties"></a>Varsayılan desteklenen stil özellikleri

Daha önce belirtildiği gibi, basit veri katmanı, çekirdek işleme katmanlarının birkaçını sarmalar: kabarcık, sembol, çizgi, Çokgen ve yükseltilmiş Çokgen. Ardından, tek tek özelliklerde geçerli stil özelliklerini aramak için ifadeleri kullanır.

Azure haritalar ve GitHub stil özellikleri, desteklenen özellik adlarının iki ana kümesidir. Farklı Azure Maps katman seçeneklerinin çoğu özellik adı, basit veri katmanındaki özelliklerin stil özellikleri olarak desteklenir. Bazı katman seçeneklerine, GitHub tarafından yaygın olarak kullanılan stil özellik adlarını desteklemeye yönelik ifadeler eklenmiştir. Bu özellik adları [GitHub 'ın geojson eşleme desteği](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)tarafından tanımlanır ve platform içinde depolanan ve Işlenen geojson dosyalarını stilleyerek kullanılır. GitHub 'ın stil özellikleri, stil özellikleri dışında basit veri katmanında desteklenir `marker-symbol` .

Okuyucu daha az ortak bir stil özelliği genelinde geliyorsa, bu, en yakın Azure Maps stil özelliğine dönüştürür. Ayrıca, varsayılan stil ifadeleri `getLayers` basit veri katmanının işlevi kullanılarak geçersiz kılınabilir ve katmanların herhangi birinde seçenekleri güncellenebilir.

Aşağıdaki bölümler, basit veri katmanı tarafından desteklenen varsayılan stil özellikleriyle ilgili ayrıntıları sağlar. Desteklenen özellik adının sırası Ayrıca özelliğinin önceliğidir. Aynı katman seçeneği için iki stil özelliği tanımlanmışsa, listedeki ilk bir öncelik daha yüksektir. Renkler herhangi bir CSS3 Color değeri olabilir; ONALTıLı, RGB, RGBA, HSL, HSLA veya adlandırılmış renk değeri.

### <a name="bubble-layer-style-properties"></a>Kabarcık katman stili özellikleri

Bir özellik bir veya ise `Point` `MultiPoint` , ve özelliği bir `image` sembol olarak noktayı işlemek için özel bir simge olarak kullanılacak bir özelliğe sahip değilse, özelliği ile birlikte işlenir `BubbleLayer` .

| Katman seçeneği | Desteklenen özellik adları | Varsayılan değer |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] `size` ve `scale` değerleri skaler değerler olarak değerlendirilir ve bu değerler ile çarpılacak `8`

\[2 \] GitHub `marker-size` seçeneği belirtilmişse, yarıçap için aşağıdaki değerler kullanılacaktır.

| İşaretçi boyutu | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Kümeler kabarcık katmanı kullanılarak da işlenir. Varsayılan olarak, bir kümenin yarıçapı olarak ayarlanır `16` . Kümenin rengi, aşağıda tanımlandığı şekilde kümedeki noktaların sayısına bağlı olarak farklılık gösterir:

| noktaların sayısı | Renk    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; (     | `green`  |

### <a name="symbol-style-properties"></a>Sembol stili özellikleri

Bir özellik bir veya ise `Point` , ve `MultiPoint` özelliği bir `image` sembol olarak noktayı işlemek için özel bir simge olarak kullanılacak bir özelliğe sahipse, özelliği ile birlikte işlenir `SymbolLayer` .

| Katman seçeneği | Desteklenen özellik adları | Varsayılan değer |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] GitHub `marker-size` seçeneği belirtilmişse, simge boyutu seçeneği için aşağıdaki değerler kullanılacaktır.

| İşaretçi boyutu | Sembol boyutu |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Nokta özelliği bir kümeise, `point_count_abbreviated` özellik metin etiketi olarak işlenir. Hiçbir görüntü işlenmez.

### <a name="line-style-properties"></a>Çizgi stili özellikleri

Özelliği bir,, `LineString` veya ise `MultiLineString` , `Polygon` `MultiPolygon` özelliği ile birlikte işlenir `LineLayer` .

| Katman seçeneği | Desteklenen özellik adları | Varsayılan değer |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Çokgen stili özellikleri

Özelliği bir `Polygon` veya ise ve özelliği bir `MultiPolygon` özelliği yoksa veya özelliği `height` `height` sıfır ise, özelliği bir ile işlenir `PolygonLayer` .

| Katman seçeneği | Desteklenen özellik adları | Varsayılan değer |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Yükseltilmiş Çokgen stili özellikleri

Özellik bir `Polygon` veya ise `MultiPolygon` , ve `height` değeri 0 ' dan büyük olan bir özelliğe sahipse, özelliği ile birlikte işlenir `PolygonExtrusionLayer` .

| Katman seçeneği | Desteklenen özellik adları | Varsayılan değer |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Uzamsal verileri okuma ve yazma](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC harita katmanı ekleme](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS hizmetine bağlanma](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Çekirdek işlemlerden yararlanma](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Desteklenen veri biçimi ayrıntıları](spatial-io-supported-data-format-details.md)