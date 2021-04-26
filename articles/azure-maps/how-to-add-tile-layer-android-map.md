---
title: Android Maps 'e kutucuk katmanı ekleme | Microsoft Azure haritaları
description: Bir haritaya döşeme katmanı eklemeyi öğrenin. Bir haritaya Hava durumu radar kaplaması eklemek için Azure Maps Android SDK kullanan bir örneğe bakın.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608994"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Haritaya döşeme katmanı ekleme (Android SDK)

Bu makalede, Azure Maps Android SDK kullanarak bir haritada kutucuk katmanının nasıl işleneceğini gösterilmektedir. Döşeme katmanları, Azure Maps temel harita kutucuklarının üzerine görüntü eklemenize olanak tanır. Azure haritalar döşeme sistemi hakkında daha fazla bilgi [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md) belgelerinde bulunabilir.

Döşeme katmanı bir sunucudan kutucukları yükler. Bu görüntüler, kutucuk katmanının anladığı adlandırma kuralını kullanarak, bir sunucu üzerindeki herhangi bir görüntü gibi önceden işlenebilir ve depolanabilir. Ya da bu görüntüler gerçek zamanlı görüntüleri üreten dinamik bir hizmetle birlikte oluşturulabilir. Azure haritalar TileLayer sınıfı tarafından desteklenen üç farklı kutucuk hizmeti adlandırma kuralı vardır:

* X, Y, yakınlaştırma gösterimi-yakınlaştırma düzeyine göre x, sütun ise döşeme kılavuzundaki döşemenin satır konumudur.
* Quadkey gösterimi-x, y, zoom bilgilerini bir kutucuk için benzersiz bir tanımlayıcı olan tek bir dize değerine birleşimi.
* Sınırlayıcı kutu-sınırlayıcı kutu koordinatları `{west},{south},{east},{north}` , yaygın olarak [Web eşleme HIZMETLERI (WMS)](https://www.opengeospatial.org/standards/wms)tarafından kullanılan biçimde bir görüntü belirtmek için kullanılabilir.

> [!TIP]
> Bir TileLayer, haritada büyük veri kümelerini görselleştirmenin harika bir yoludur. Bir görüntüden yalnızca bir kutucuk katmanı oluşturulmayabilir, ancak vektör verileri de kutucuk katmanı olarak da oluşturulabilir. Vektör verilerini kutucuk katmanı olarak işleyerek, harita denetiminin yalnızca kutucukları yüklemesi gerekir. Bu, dosya boyutunda, temsil ettikleri vektör verilerinden çok daha küçük olabilir. Bu teknik, haritada milyonlarca veri satırı oluşturması gereken birçok kişi tarafından kullanılır.

Döşeme katmanına geçirilen kutucuk URL 'si, bir TileJSON kaynağına veya aşağıdaki parametreleri kullanan bir kutucuk URL şablonuna yönelik bir http/https URL 'SI olmalıdır:

* `{x}` -X kutucuğunun konumu. Ayrıca `{y}` , ve gerektirir `{z}` .
* `{y}` -Kutucuğun Y konumu. Ayrıca `{x}` , ve gerektirir `{z}` .
* `{z}` -Kutucuğun yakınlaştırma düzeyi. Ayrıca `{x}` , ve gerektirir `{y}` .
* `{quadkey}` -Bing Haritalar kutucuk sistem adlandırma kuralına bağlı olarak, quadkey tanımlayıcısını Döşe.
* `{bbox-epsg-3857}` - `{west},{south},{east},{north}` EPSG 3857 uzamsal başvuru sisteminde biçim içeren bir sınırlayıcı kutu dizesi.
* `{subdomain}` -Alt etki alanı değeri belirtilmişse alt etki alanı değerleri için bir yer tutucu.
* `azmapsdomain.invalid` -Etki alanını ve kutucuk isteklerinin kimlik doğrulamasını harita tarafından kullanılan değerlerle aynı olacak şekilde hizalamak için bir yer tutucudur. Azure haritalar tarafından barındırılan bir kutucuk hizmetini çağırırken bunu kullanın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki işlemi gerçekleştirmek için, bir harita yüklemek üzere [Azure Maps Android SDK](how-to-use-android-map-control-library.md) yüklemeniz gerekir.

## <a name="add-a-tile-layer-to-the-map"></a>Haritaya kutucuk katmanı ekleyin

Bu örnek, bir kutucuk kümesini işaret eden döşeme katmanının nasıl oluşturulacağını gösterir. Bu örnek, "x, y, Zoom" Döşeme sistemini kullanır. Bu kutucuk katmanının kaynağı, kitle kaynağı grafikleri içeren [openseamap projem](https://openseamap.org/index.php)' dir. Genellikle kutucuk katmanlarını görüntülerken haritadaki şehirlerin etiketlerini açıkça görmeniz istenebilir. Bu davranış, harita etiketi katmanlarının altına kutucuk katmanını ekleyerek elde edilebilir.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Aşağıdaki ekran görüntüsünde, koyu gri tonlamalı bir stile sahip bir haritada yer alan bir kutucuk katmanını görüntüleyen Yukarıdaki kod gösterilmektedir.

![Kutucuk katmanını görüntüleyen Android Haritası](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC Web eşleme hizmeti (WMS) ekleme

Web eşleme hizmeti (WMTS), harita verilerinin görüntülerini sunan bir Open Geospatial Consortium (OGC) standardıdır. Bu biçimde Azure haritalar ile kullanabileceğiniz çok sayıda açık veri kümesi vardır. Bu hizmet türü, hizmet `EPSG:3857` koordinat başvuru sistemini (CCR) destekliyorsa, bir kutucuk katmanıyla birlikte kullanılabilir. Bir WMS hizmeti kullanırken, genişlik ve yükseklik parametrelerini hizmetin desteklediği aynı değere ayarlayın. bu değeri, seçeneğinde aynı değere ayarladığınızdan emin olun `tileSize` . Biçimlendirilen URL 'de, `BBOX` hizmetin parametresini `{bbox-epsg-3857}` yer tutucu ile ayarlayın.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Aşağıdaki ekran görüntüsünde, etiketlerin altındaki bir haritanın en üstünde bulunan [ABD geolojileri anketine (USGS)](https://mrdata.usgs.gov/) ait bir Web eşleme hizmeti 'nin, yukarıdaki kod gösterilmektedir.

![WMS kutucuk katmanını görüntüleyen Android Haritası](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC Web eşleme kutucuk hizmeti (WMTS) ekleme

Web eşleme kutucuk hizmeti (WMTS) haritalar için döşeli tabanlı yer paylaşımları sunmak için bir Open Geospatial Consortium (OGC) standardıdır. Bu biçimde Azure haritalar ile kullanabileceğiniz çok sayıda açık veri kümesi vardır. Hizmet `EPSG:3857` veya `GoogleMapsCompatible` koordinat başvuru sistemini (bir) destekliyorsa, bu hizmet türü bir kutucuk katmanıyla birlikte kullanılabilir. Bir WMTS hizmeti kullanırken, genişlik ve yükseklik parametrelerini hizmetin desteklediği aynı değere ayarlayın. bu değeri, seçeneğinde aynı değere ayarladığınızdan emin olun `tileSize` . Biçimlendirilen URL 'de, aşağıdaki yer tutucuları uygun şekilde değiştirin:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Aşağıdaki ekran görüntüsünde, bir haritanın en üstünde bulunan [ABD geoloji (USGS) Ulusal haritalarından](https://viewer.nationalmap.gov/services/) , yolların ve etiketlerin altında bulunan bir Web eşleme kutucuğu hizmeti 'nin bir Web eşleme kutucuk hizmeti 'nin üzerine yerleşimi gösterilmektedir.

![WMTS kutucuk katmanını görüntüleyen Android eşlemesi](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Sonraki adımlar

Haritada görüntü kaplama yolları hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın.

> [!div class="nextstepaction"]
> [Görüntü katmanı](map-add-image-layer-android.md)
