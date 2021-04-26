---
title: Uzamsal verileri okuma ve yazma | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'Sı tarafından sunulan uzamsal GÇ modülünü kullanarak verileri okumayı ve yazmayı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891338"
---
# <a name="read-and-write-spatial-data"></a>Uzamsal verileri okuma ve yazma

Aşağıdaki tabloda, uzamsal GÇ modülü ile okuma ve yazma işlemleri için desteklenen uzamsal dosya biçimleri listelenmektedir.

| Veri biçimi       | Okuma | Yazma |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Uzamsal CSV       | ✓  |  ✓  |
| Well-Known metin   | ✓  |  ✓  |

Bu sonraki bölümlerde, uzamsal GÇ modülünü kullanarak uzamsal verileri okumak ve yazmak için tüm farklı araçlar ana hatlarıyla verilmektedir.

## <a name="read-spatial-data"></a>Uzamsal verileri okuma

`atlas.io.read`İşlevi, uzamsal VERILERLE KML, GPX, GeoRSS, GeoJSON ve CSV dosyaları gibi yaygın uzamsal veri biçimlerini okumak için kullanılan ana işlevdir. Bu işlev, ZIP dosyası veya bir KMZ dosyası olarak bu biçimlerin sıkıştırılmış sürümlerini de okuyabilir. KMZ dosya biçimi, görüntü gibi varlıkları da içerebilen, KML 'in sıkıştırılmış bir sürümüdür. Alternatif olarak, Read işlevi bu biçimlerden herhangi birinde bir dosyaya işaret eden bir URL 'yi alabilir. URL 'Ler CORS etkin bir uç noktada barındırılmalıdır veya okuma seçeneklerinde bir proxy hizmeti sağlanmalıdır. Proxy hizmeti, CORS 'nin etkinleştirildiği etki alanlarında kaynakları yüklemek için kullanılır. Read işlevi, haritaya görüntü simgeleri eklemek için bir Promise döndürür ve UI iş parçacığı üzerindeki etkiyi en aza indirmek için verileri zaman uyumsuz olarak işler.

Sıkıştırılmış bir dosyayı ZIP veya bir KMZ olarak okurken, dosyanın sıkıştırması kaldırılır ve ilk geçerli dosya için taranmaz. Örneğin, Doc. kml veya geçerli uzantıya sahip bir dosya; örneğin:. kml,. xml, GeoJSON,. JSON,. csv,. tsv veya. txt. Daha sonra, KML ve GeoRSS dosyalarında başvurulan görüntüler erişilebilir olduklarından emin olmak için önceden yüklenir. Erişilemeyen görüntü verileri alternatif bir geri dönüş görüntüsü yükleyebilir veya stillerden kaldırılacak. KMZ dosyalarından ayıklanan görüntüler, veri URI 'Leri olarak dönüştürülür.

Read işlevinin sonucu bir `SpatialDataSet` nesnedir. Bu nesne GeoJSON FeatureCollection sınıfını genişletir. `DataSource`Özelliklerini bir haritada işlemek için kolayca olarak bir olarak geçirilebilir. `SpatialDataSet`Yalnızca özellik bilgilerini içermez, ancak aşağıdaki tabloda ÖZETLENEN KML zemin yer paylaşımlarını, işleme ölçümlerini ve diğer ayrıntıları da içerebilir.

| Özellik adı | Tür | Description | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Veri kümesindeki tüm verilerin sınırlayıcı kutusu. |
| `features` | `Feature[]` | Veri kümesi içindeki GeoJSON özellikleri. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Bir KML Groundoveryerleştirme dizisi. |
| `icons` | Kayıt &lt; dizesi, dize&gt; | Bir simge URL 'Si kümesi. Anahtar = simge adı, değer = URL. |
| properties | herhangi biri | Uzamsal veri kümesinin belge düzeyinde sunulan özellik bilgileri. |
| `stats` | `SpatialDataSetStats` | Uzamsal veri kümesinin içerik ve işlem süresi hakkında istatistikler. |
| `type` | `'FeatureCollection'` | Salt okunurdur GeoJSON türü değeri. |

## <a name="examples-of-reading-spatial-data"></a>Uzamsal verileri okuma örnekleri

Aşağıdaki kod, uzamsal veri kümesinin nasıl okunacağını ve sınıfını kullanarak haritada nasıl işleneceğini gösterir `SimpleDataLayer` . Kod, URL tarafından işaret edilen bir GPX dosyası kullanır.

<br/>

<iframe height='500' scrolling='no' title='Uzamsal verileri basit yükleme' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından basit olan kalem <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>Yük uzamsal verilerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

Sonraki kod tanıtımı, KML veya KMZ 'in haritaya nasıl okunacağını ve yükleneceğini gösterir. KML, veya biçiminde olacak olan zemin Yerpaylaşımları içerebilir `ImageLyaer` `OgcMapLayer` . Bu yer paylaşımları eşlemde özelliklerden ayrı eklenmelidir. Ayrıca, veri kümesinde özel simgeler varsa, Özellikler yüklenmeden önce bu simgelerin haritalar kaynaklarına yüklenmesi gerekir.

<br/>

<iframe height='500' scrolling='no' title='KML 'i haritaya yükle' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () ile harita için bkz. Pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Load KML</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

İsteğe bağlı olarak, CORS 'nin etkinleştirildiği etki alanları arası varlıklara erişim için bir proxy hizmeti sağlayabilirsiniz. Read işlevi, önce CORS kullanarak başka bir etki alanındaki dosyalara erişmeye çalışacaktır. CORS kullanarak başka bir etki alanındaki herhangi bir kaynağa ilk kez erişemediğinde, bu, yalnızca bir proxy hizmeti sağlanmışsa ek dosyalar ister. Read işlevi, belirtilen proxy URL 'sinin sonuna dosya URL 'sini ekler. Bu kod parçacığı, bir Proxy hizmetinin okuma işlevine nasıl geçirileceğini gösterir:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Aşağıdaki demo, ayrılmış bir dosyanın nasıl okunacağını ve haritada nasıl işleneceğini gösterir. Bu durumda, kod uzamsal veri sütunları olan bir CSV dosyası kullanır.

<br/>

<iframe height='500' scrolling='no' title='Ayrılmış bir dosya ekleyin' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. Azure Maps () ile birlikte, Codepen ile <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>ayrılmış bir dosya ekleme</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="write-spatial-data"></a>Uzamsal verileri yazma

Uzamsal GÇ modülünde iki ana yazma işlevi vardır. İşlev `atlas.io.write` bir dize oluşturur, ancak `atlas.io.writeCompressed` işlev sıkıştırılmış bir ZIP dosyası oluşturur. Sıkıştırılmış ZIP dosyası, içindeki uzamsal verileri içeren metin tabanlı bir dosya içerir. Bu işlevlerin her ikisi de verileri dosyaya eklemek için bir Promise döndürür. Ayrıca, her ikisi de şu verilerden birini yazabilir: `SpatialDataSet` ,,, `DataSource` `ImageLayer` `OgcMapLayer` , özellik koleksiyonu, özellik, geometri veya bu veri türlerinin herhangi bir birleşiminin dizisi. Her iki işlevi kullanarak yazarken istenen dosya biçimini belirtebilirsiniz. Dosya biçimi belirtilmemişse, veriler KML olarak yazılır.

Aşağıdaki araç, işleviyle kullanılabilecek yazma seçeneklerinin çoğunluğunu gösterir `atlas.io.write` .

<br/>

<iframe height='700' scrolling='no' title='Uzamsal veri yazma seçenekleri' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>uzamsal verileri yazma seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="example-of-writing-spatial-data"></a>Uzamsal veri yazma örneği

Aşağıdaki örnek, haritada uzamsal dosyaları sürükleyip bırakmayı ve daha sonra yüklemeyi sağlar. Formatjson verilerini eşlemden dışa aktarabilir ve desteklenen uzamsal veri biçimlerinden birine dize veya sıkıştırılmış bir dosya olarak yazabilirsiniz.

<br/>

<iframe height='700' scrolling='no' title='Uzamsal dosyaları harita üzerine sürükleyip bırakma' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Padepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>haritalara sürükleme ve bırakma uzamsal dosyalarını</a> görüntüleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

İsteğe bağlı olarak, CORS 'nin etkinleştirildiği etki alanları arası varlıklara erişim için bir proxy hizmeti sağlayabilirsiniz. Bu kod parçacığı, bir proxy hizmetini birleştirebilmeniz gerektiğini gösterir:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Well-Known metin (WKT) okuma ve yazma

[Iyi bilinen metin](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (wkt), uzamsal geometrilerin metin olarak temsil edilmesi için bir Open Geospatial Consortium (ogc) standardıdır. Birçok Jeo uzamsal sistem PostGIS eklentisini kullanarak Azure SQL ve Azure PostgreSQL gibi WKT 'yi destekler. Çoğu OGC standartları gibi, koordinatlar "x y" kuralına uyum sağlamak için "Boylam Enlem" olarak biçimlendirilir. Örnek olarak, Boylam-110 ve Latitude 45 ' deki bir nokta, `POINT(-110 45)` WKT biçimi kullanılarak yazılabilir.

İyi bilinen metin işlevini kullanarak okunabilir `atlas.io.ogc.WKT.read` ve işlevi kullanılarak yazılabilir `atlas.io.ogc.WKT.write` .

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Well-Known metin (WKT) okuma ve yazma örnekleri

Aşağıdaki kod, iyi bilinen metin dizesinin nasıl okunacağını `POINT(-122.34009 47.60995)` ve bir kabarcık katmanı kullanarak haritada nasıl işleneceğini gösterir.

<br/>

<iframe height='500' scrolling='no' title='Well-Known metin oku' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>okunan Well-Known metnini</a> görüntüleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

Aşağıdaki kod, iyi bilinen metinleri okumayı ve okumayı ve geri yazmayı gösterir.

<br/>

<iframe height='700' scrolling='no' title='Well-Known metin oku ve yaz' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () ile ilgili <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>okuma ve yazma Well-Known metin</a> başlığına bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="read-and-write-gml"></a>GML oku ve yaz

GML, genellikle diğer XML belirtimlerine uzantı olarak kullanılan bir uzamsal XML dosya belirtimidir. GeoJSON verileri, işlevi kullanılarak GML etiketleriyle XML olarak yazılabilir `atlas.io.core.GmlWriter.write` . GML içeren XML, işlevi kullanılarak okunabilir `atlas.io.core.GmlReader.read` . Read işlevinin iki seçeneği vardır:

- `isAxisOrderLonLat`Seçenek-"enlem, Boylam" veya "boylam, enlem" koordinatlarının eksen sırası veri kümeleri arasında farklılık gösterebilir ve her zaman iyi tanımlı değildir. Varsayılan olarak, GML okuyucu koordinat verileri "enlem, Boylam" olarak okur, ancak bu seçeneğin true olarak ayarlanması bunu "boylam, enlem" olarak okuyacaktır.
- `propertyTypes`Seçenek-bu seçenek, anahtarın veri kümesindeki bir özelliğin adı olduğu bir anahtar değer arama tablosudur. Değer, ayrıştırılırken değeri dönüştürmek için nesne türüdür. Desteklenen tür değerleri şunlardır: `string` , `number` , `boolean` , ve  `date` . Bir özellik arama tablosunda değilse veya tür tanımlanmamışsa, özelliği bir dize olarak ayrıştırılacaktır.

`atlas.io.read`İşlevi, giriş VERILERININ XML olduğunu algıladığında işlevi varsayılan olarak görür `atlas.io.core.GmlReader.read` , ancak veriler diğer destek uzamsal XML biçimlerinden biri değildir.

, `GmlReader` Aşağıdaki SRID 'lerin birine sahip olan koordinatları ayrıştıracaktır:

- EPSG: 4326 (tercih edilen)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674 muhtemelen küçük bir kenar boşluğu olabilir.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>Diğer kaynaklar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

[atlas.io statik işlevleri](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[Atlas. IO. ogc. WKT işlevleri](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[WFS hizmetine bağlanma](spatial-io-connect-wfs-service.md)

[Çekirdek işlemlerden yararlanma](spatial-io-core-operations.md)

[Desteklenen veri biçimi ayrıntıları](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Sonraki adımlar

Haritalarınıza eklemek için daha fazla kod örneği için aşağıdaki makalelere bakın:

[OGC harita katmanı ekleme](spatial-io-add-ogc-map-layer.md)