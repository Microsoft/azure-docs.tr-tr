---
title: "Öğretici: Bing Haritalar 'dan bir Web uygulaması geçirme | Microsoft Azure haritaları"
description: Bir Web uygulamasını Bing Haritalar 'dan Microsoft Azure Maps 'a geçirmeye yönelik öğretici.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 2b072107275fba1ff83ab3ddac63ed8bf7766356
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389119"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Öğretici: Bing Haritalar 'dan bir Web uygulaması geçirme

Bing Haritalar kullanan Web uygulamaları genellikle Bing Haritalar V8 JavaScript SDK 'sını kullanır. Azure Haritalar Web SDK 'Sı, geçirilecek Azure tabanlı uygun SDK 'dir. Azure Haritalar Web SDK 'Sı, etkileşimli haritaları, Web veya mobil uygulamalarınızda görüntülenmek üzere kendi içeriklerinizi ve Imagery 'yi özelleştirmenize olanak tanır. Bu denetimde büyük veri kümelerini yüksek performansla oluşturmanızı sağlayan WebGL bileşeni kullanılmaktadır. JavaScript veya TypeScript kullanarak bu SDK ile geliştirin. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Harita yükleme
> * Bir eşlemeyi yerelleştirin
> * Pushpin, polylines ve çokgenler ekleyin.
> * Bilgileri bir açılan pencerede veya bilgi kutusunda görüntüle
> * KML ve GeoJSON verilerini yükleme ve görüntüleme
> * Küme Pushpin 'leri
> * Döşeme katmanını kaplama
> * Trafik verilerini gösterme
> * Zemin kaplama ekleme

Var olan bir Web uygulamasını geçiriyorsanız, Cesium, leaflet ve Openkatmanları gibi bir açık kaynak eşleme denetim kitaplığı kullanıp kullanmın olup olmadığını kontrol edin. Bu durumda, bu kitaplığı kullanmaya devam etmeyi tercih ediyorsanız, Azure haritalar kutucuk Hizmetleri 'ne ([yol kutucukları](/rest/api/maps/render/getmaptile) \| [uydu kutucukları](/rest/api/maps/render/getmapimagerytile)) bağlayabilirsiniz. Aşağıdaki bağlantılar, yaygın olarak kullanılan bazı açık kaynaklı harita denetim kitaplıklarında Azure Haritalarını kullanma hakkında ayrıntılı bilgi sağlar.

* [Cesium](https://cesiumjs.org/) -Web için 3B harita denetimi. [Kod örnekleri](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| [Eklenti deposu]()
* [Leaflet](https://leafletjs.com/) – Web Için hafif 2B harita denetimi. [Kod örnekleri](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| [Eklenti deposu]()
* [Openkatmanlar](https://openlayers.org/) -Web için projeksiyonları destekleyen bir 2B harita denetimi. [Kod örnekleri](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| [Eklenti deposu]()

Bir JavaScript çerçevesi kullanılarak geliştirilirken, aşağıdaki açık kaynaklı projelerden biri yararlı olabilir:

* [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -Azure haritaları etrafında angular 10 sarmalayıcı.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) -bir Azure Maps Blazor bileşeni.
* [Azure Maps 'e tepki verme bileşeni](https://github.com/WiredSolutions/react-azure-maps) -Azure Maps denetimi için bir tepki düzeyi.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) -Vue uygulaması Için bir Azure Maps bileşeni.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Anahtar özellikleri desteği

Aşağıdaki tabloda, Bing Maps V8 JavaScript SDK 'sindeki temel API özellikleri ve Azure Maps web SDK 'sında benzer bir API 'nin desteği listelenmektedir.

| Bing Haritalar özelliği        | Azure Maps web SDK desteği                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Raptiyeler                 | ✓                                                                                      |
| İğne Kümelemesi       | ✓                                                                                      |
| Polylines & çokgenler     | ✓                                                                                      |
| Zemin Yerpaylaşımları          | ✓                                                                                      |
| Isı haritaları                | ✓                                                                                      |
| Döşeme katmanları              | ✓                                                                                      |
| KML katmanı                | ✓                                                                                      |
| Dağılım katmanı            | [Örnekler](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Veri atma katmanı       | Açık kaynaklı Azure Maps [gridded veri kaynağı modülüne](https://github.com/Azure-Samples/azure-maps-gridded-data-source) dahildir       |
| Hareketli kutucuk katmanı      | Açık kaynaklı Azure Maps [animasyon modülüne](https://github.com/Azure-Samples/azure-maps-animations) dahildir |
| Çizim araçları            | ✓                                                                                      |
| Geocoder hizmeti         | ✓                                                                                      |
| Yönergeler hizmeti       | ✓                                                                                      |
| Uzaklık matrisi hizmeti  | ✓                                                                                      |
| Uzamsal veri hizmeti     | Yok                                                                                    |
| Uydu/havadan imay | ✓                                                                                      |
| Birler göz ımamali         | Yok                                                                                |
| Streetside canlandırın       | Yok                                                                                |
| GeoJSON desteği          | ✓                                                                                      |
| GeoXML desteği           | ✓ [Uzamsal GÇ modülü](how-to-use-spatial-io-module.md)                                                                                     |
| Well-Known metin desteği  | ✓                                                                                      |
| Özel harita stilleri        | Kısmi                                                                                |

Azure Maps [, Web SDK 'sının](open-source-projects.md#open-web-sdk-modules) yeteneklerini genişleten birçok ek açık kaynaklı modüle da sahiptir.

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK 'lerinde önemli farklılıklar

Aşağıda, Bing Haritalar ve Azure Maps web SDK 'Ları arasındaki önemli farklılıklar aşağıda verilmiştir:

* Azure Maps web SDK 'sına erişim için barındırılan bir uç nokta sağlamaya ek olarak, tercih edilen durumlarda Web SDK 'sını uygulamalara katıştırmak için de bir NPM paketi de mevcuttur. Daha fazla bilgi için bu [belgelere](./how-to-use-map-control.md) bakın. Bu paket TypeScript tanımlarını da içerir.
* Bing Haritalar, SDK 'sının iki barındırılan dalını sağlar; Yayın ve deneysel. Deneysel dal, yeni geliştirme gerçekleşirken günde birden çok güncelleştirme alabilir. Azure Maps yalnızca bir sürüm dalı barındırır, ancak deneysel Özellikler açık kaynaklı Azure Maps kod örnekleri projesinde özel modüller olarak oluşturulur. Bing Haritalar, dondurulmuş bir dala sahip olmak ve daha az sıklıkta güncelleştirildiğinden, bir yayın nedeniyle değişiklikleri bozmak riskini azaltmak için kullanılır. Azure haritalar 'da, NPM modülünü kullanabilir ve önceki bir alt sürüm sürümüne işaret edebilirsiniz.

> [!TIP]
> Azure haritalar, SDK 'nın hem küçültülmüş hem de küçültülmüş sürümlerini yayımlar. `.min`Dosya adlarından basit kaldırma. Mini karşılaşılan sürüm, hata ayıklama sırasında faydalıdır ancak küçük dosya boyutundan faydalanmak için üretimde küçültülmüş sürümü kullandığınızdan emin olun.

* Azure haritalar 'da harita sınıfının bir örneğini oluşturduktan sonra, kodunuz `ready` `load` eşleme ile etkileşimde bulunmadan önce haritaların veya etkinliğin tetiklenmesi için beklemelidir. Bu olaylar, tüm harita kaynaklarının yüklenmiş ve erişilmeye hazırsa emin olmanızı sağlar.
* Her iki platform da temel haritalar için benzer bir döşeme sistemi kullanır, ancak Bing Haritalar 'daki kutucuklar boyut olarak 256 pikseldir, ancak Azure haritalar 'daki kutucuklar boyut olarak 512 pikseldir. Bu nedenle, Azure haritalar 'daki aynı harita görünümünü Bing Haritalar olarak almak için, Bing Haritalar 'da kullanılan bir yakınlaştırma düzeyinin Azure Maps 'ta bir tane tarafından çıkarılan olması gerekir.
* Bing Haritalar 'daki koordinatlar `latitude, longitude` Azure haritalar 'ın kullandığı sırada olarak adlandırılır `longitude, latitude` . Bu biçim `[x, y]` , en fazla GIS platformundan sonra gelen standart ile hizalanır.

* Azure Haritalar Web SDK 'sindeki şekiller GeoJSON şemasına dayalıdır. Yardımcı sınıflar, [Atlas. Data ad alanı](/javascript/api/azure-maps-control/atlas.data)aracılığıyla sunulur. Ayrıca Atlas de vardır [. ](/javascript/api/azure-maps-control/atlas.shape) GeoJSON nesnelerini kaydırmak ve veri bağlanabilir bir şekilde güncellemenin ve bakımını kolaylaştırmak için kullanılabilecek şekil sınıfı.
* Azure haritalar 'daki Koordinatlar, veya biçiminde basit bir sayı dizisi olarak belirtime konumu nesneleri olarak tanımlanır `[longitude, latitude]` `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> Konum sınıfı, biçiminde olan koordinatları içeri aktarmaya yönelik statik bir yardımcı işleve sahiptir `latitude, longitude` . [Atlas. Data. Position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position)Işlevi genellikle `new Microsoft.Maps.Location` Bing Haritalar kodundaki işlevin yerini alabilir.

* Haritaya eklenen her bir şekil üzerinde stil bilgilerini belirtmek yerine, Azure Maps stilleri verilerden ayırır. Veriler veri kaynaklarında depolanır ve Azure haritalar kodunun verileri işlemek için kullandığı katmanları işlemek üzere bağlanır. Bu yaklaşım, gelişmiş performans avantajı sağlar. Ayrıca, birçok katman, çalışma mantığının şekil içinde tanımlanan özelliklere bağlı olarak bir katman içinde nasıl işleneceğini değiştirecek katman stili seçeneklerine eklenebileceği veri tabanlı stillendirme desteği sağlar.
* Azure haritalar, ad alanında çok sayıda kullanışlı uzamsal matematik işlevleri sağlar `atlas.math` , ancak bunlar Bing Haritalar uzamsal matematik modülünden farklıdır. Birincil fark, Azure haritalar 'ın birleşim ve kesişim gibi ikili işlemler için yerleşik işlevler sağlamamasından, ancak Azure Maps, açık bir standart olan GeoJSON 'ı temel aldığı için kullanılabilir çok sayıda açık kaynak kitaplık vardır. Azure haritalar ile iyi bir şekilde çalışacak ve çok sayıda uzamsal matematik özelliği içeren bir popüler seçenek [Türf js](http://turfjs.org/)' dir.

Azure haritalar ile ilişkili terminolojinin derinlemesine bir listesi için bkz. [Azure Maps sözlüğü](./glossary.md) .

## <a name="web-sdk-side-by-side-examples"></a>Web SDK yan yana örnekleri

Aşağıda, Web uygulamanızı Bing Haritalar V8 JavaScript SDK 'sından Azure Maps web SDK 'sına geçirmenize yardımcı olacak genel kullanım durumlarını kapsayan her bir platform için kod örnekleri koleksiyonu verilmiştir. Web uygulamalarıyla ilgili kod örnekleri JavaScript 'te verilmiştir; Ancak Azure Maps, bir [NPM modülü](./how-to-use-map-control.md)aracılığıyla ek bir seçenek olarak TypeScript tanımları da sağlar.

**Konu başlıkları**

* [Harita yükleme](#load-a-map)
* [Haritayı yerelleştirme](#localizing-the-map)
* [Harita görünümü ayarlanıyor](#setting-the-map-view)
* [Raptiye ekleme](#adding-a-pushpin)
* [Özel iğne ekleme](#adding-a-custom-pushpin)
* [Çoklu çizgi ekleme](#adding-a-polyline)
* [Çokgen ekleme](#adding-a-polygon)
* [Bilgi kutusu görüntüle](#display-an-infobox)
* [İğne Kümelemesi](#pushpin-clustering)
* [Isı haritası ekleme](#add-a-heat-map)
* [Döşeme katmanını kaplama](#overlay-a-tile-layer)
* [Trafik verilerini gösterme](#show-traffic-data)
* [Zemin kaplama ekleme](#add-a-ground-overlay)
* [KML verilerini haritaya ekleme](#add-kml-data-to-the-map)
* [Çizim araçları ekleme](#add-drawing-tools)

### <a name="load-a-map"></a>Harita yükleme

Her iki SDK 'da da bir haritanın yüklenmesi aynı adım kümesini izler;

* Map SDK 'sına bir başvuru ekleyin.
* `div`Sayfanın gövdesine harita için yer tutucu olarak davranacak bir etiket ekleyin.
* Sayfa yüklendiğinde çağrılan bir JavaScript işlevi oluşturun.
* Karşılık gelen eşleme sınıfının bir örneğini oluşturun.

**Bazı önemli farklılıklar**

* Bing Haritalar, API 'nin betik başvurusunda veya bir eşleme seçeneği olarak bir hesap anahtarının belirtilmesini gerektirir. Azure haritalar için kimlik doğrulama kimlik bilgileri, Map sınıfının seçenekleri olarak belirtilir. Bu bir abonelik anahtarı veya Azure Active Directory bilgileri olabilir.
* Bing Haritalar, Haritayı yüklemek üzere bir başlatma işlevi çağırmak için kullanılan API 'nin betik başvurusunda bir geri çağırma işlevi alır. Azure haritalar ile sayfanın OnLoad olayı kullanılmalıdır.
* Eşleme içinde işlenecek öğeye başvurmak için bir KIMLIK kullanırken `div` Bing Haritalar BIR HTML seçicisi (ör. `#myMap` ) kullanır, ancak Azure Maps yalnızca kimlik değerini kullanır (ör. `myMap` ).
* Azure haritalar 'daki Koordinatlar, biçimde bir basit sayı dizisi olarak belirtime konum nesneleri olarak tanımlanır `[longitude, latitude]` .
* Azure haritalar 'daki yakınlaştırma düzeyi, platformlar arasındaki sistem boyutlarını döşeme farklılığı nedeniyle Bing Haritalar örneğinden daha düşük bir düzeydir.
* Varsayılan olarak, Azure Maps harita tuvaline yakınlaştırma düğmeleri ve harita stili düğmeleri gibi herhangi bir gezinti denetimi eklemez. Ancak harita stili seçici, yakınlaştırma düğmeleri, pusula veya döndürme denetimi ve bir aralıklı denetim ekleme denetimleri vardır.
* Harita örneğinin olayını izlemek için Azure Maps 'a bir olay işleyicisi eklenir `ready` . Bu, eşleme WebGL bağlamını ve gerekli tüm kaynakları yüklemeyi tamamladığında harekete geçmeyecektir. Bu olay işleyicisine herhangi bir yükleme sonrası kodu eklenebilir.

Aşağıdaki örneklerde, New York üzerinden ortalanan bir temel haritanın koordinatları (Boylam:-73,985, Latitude: 40,747) ve Bing Haritalar 'daki yakınlaştırma düzeyi 12 ' de nasıl yükleneceği gösterilmektedir.

**Önce: Bing Haritalar**

Aşağıdaki kod, bir konum üzerinde ortalanan ve Yakınlaştırılan bir Bing haritasının nasıl görüntüleneceğini gösteren bir örnektir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Bu kodun bir tarayıcıda çalıştırılması, aşağıdaki görüntü gibi görünen bir eşlem görüntüler:

![Bing Haritalar Haritası](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Sonrasında: Azure Maps**

Aşağıdaki kod, harita stili denetim ve yakınlaştırma düğmeleriyle birlikte Azure Maps 'ta aynı görünümle bir haritanın nasıl yükleneceğini gösterir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Bu kodun bir tarayıcıda çalıştırılması, aşağıdaki görüntü gibi görünen bir eşlem görüntüler:

![Azure haritalar Haritası](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

Azure haritalar harita denetimini ayarlama ve kullanma hakkında ayrıntılı belgeler [burada](how-to-use-map-control.md)bulunabilir.

> [!TIP]
> Azure haritalar, SDK 'nın hem küçültülmüş hem de küçültülmüş sürümlerini yayımlar. `.min`Dosya adlarından kaldırın. Mini karşılaşılan sürüm, hata ayıklama sırasında faydalıdır ancak küçük dosya boyutundan faydalanmak için üretimde küçültülmüş sürümü kullandığınızdan emin olun.

**Ek Kaynaklar**

* Azure haritalar Ayrıca, [burada](map-add-controls.md)açıklandığı gibi harita görünümünü döndürme ve ele döndürme için de gezinti denetimleri sağlar.

### <a name="localizing-the-map"></a>Haritayı yerelleştirme

Hedef kitleniz birden çok ülkede yayıldığında veya farklı dillerde konuşabilçalışıyorsa, yerelleştirme önemlidir.

**Önce: Bing Haritalar**

Bing Haritalar 'ı yerelleştirmek için, ve parametreleri kullanılarak belirtilen dil ve bölge, `setLang` `UR` `<script>` API 'ye etiket başvurusuna eklenir. Bing haritalardaki bazı özellikler yalnızca belirli pazarlarda kullanılabilir; Bu nedenle kullanıcının pazarı parametresi kullanılarak belirtilir `setMkt` .

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Dilde "fr-FR" olarak ayarlanmış Bing Haritalar örneği aşağıda verilmiştir.

![Yerelleştirilmiş Bing Haritalar Haritası](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Sonrasında: Azure Maps**

Azure haritalar yalnızca haritanın dilini ve bölgesel görünümünü ayarlamaya yönelik seçenekler sağlar. Bir pazar parametresi özellikleri sınırlandırmak için kullanılmaz. Haritanın dilini ve bölgesel görünümünü ayarlamanın iki farklı yolu vardır. İlk seçenek bu bilgileri genel ad alanına eklemektir. Bu, `atlas` uygulamanızdaki tüm harita denetim örneklerinin bu ayarlara göre varsayılan olarak oluşmasına neden olur. Aşağıdaki, dili Fransızca ("fr-FR") ve bölgesel görünüm olarak belirler `"Auto"` :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

İkinci seçenek, aşağıdaki gibi eşleme yüklenirken bu bilgileri eşleme seçeneklerine iletmektir:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Azure haritalar ile aynı sayfada farklı dil ve bölge ayarlarıyla birden çok eşleme örneği yüklemek mümkündür. Ayrıca, bu ayarları haritadan yüklendikten sonra güncelleştirmek de mümkündür. Azure Maps 'ta desteklenen dillerin ayrıntılı bir listesi [burada](./supported-languages.md)bulunabilir.

Aşağıda dil "fr" olarak ayarlanan ve Kullanıcı bölgesinin "fr-FR" olarak ayarlandığı bir Azure Maps örneği verilmiştir.

![Yerelleştirilmiş Azure haritalar Haritası](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>Harita görünümü ayarlanıyor

Hem Bing hem de Azure haritalarındaki dinamik haritalar, JavaScript 'te uygun işlevler çağırarak programlı bir şekilde yeni coğrafi konumlara taşınabilir. Aşağıdaki örneklerde haritanın uydu hava imagery görüntüsünü nasıl görüntüleyeceği, haritanın koordinatlarla bir konum üzerinden ortalanıyor (Boylam:-111,0225, Latitude: 35,0272) ve Bing Haritalar 'da yakınlaştırma düzeyini 15 olarak değiştirme gösterilmektedir.

> [!NOTE]
> Bing Haritalar, Azure haritalar daha büyük bir 512 piksellik kutucuk kullandığında, boyutlara göre 256 piksel olan kutucukları kullanır. Bu, Azure haritalar 'ın aynı harita alanını Bing Haritalar ile yüklemek için gereken ağ isteklerinin sayısını azaltır. Ancak, Tile Pyramids 'in harita denetimlerinde çalışma yöntemi nedeniyle, Azure haritalar 'daki daha büyük Kutucuklar, Bing Haritalar 'daki bir eşleme olarak aynı görüntülenebilir alana ulaşmak için, Azure haritalar kullanırken Bing Haritalar 'da kullanılan yakınlaştırma düzeyini 1 ' e çıkarmanız gerekir.

**Önce: Bing Haritalar**

Bing Haritalar harita denetimi, `setView` haritanın merkezini ve yakınlaştırma düzeyini belirtmenize olanak sağlayan işlevi kullanılarak programlı bir şekilde taşınabilir.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Bing Haritalar harita görünümü ayarla](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da harita konumu, eşlemenin işlevi kullanılarak programlı bir şekilde değiştirilebilir `setCamera` ve bu işlev kullanılarak harita stili değiştirilebilir `setStyle` . Azure haritalar 'daki koordinatların "boylam, enlem" biçiminde ve yakınlaştırma düzeyi değerinin 1 ' den çıkarılan olduğunu unutmayın.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Azure haritalar harita görünümü ayarla](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**Ek Kaynaklar**

-   [Harita stili seçme](./choose-map-style.md)
-   [Desteklenen eşleme stilleri](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Raptiye ekleme

Azure haritalar 'da, nokta verilerinin haritada işlenebildiğine ilişkin birden çok yol vardır;

* HTML Işaretçileri: geleneksel DOM öğelerini kullanarak noktaları Işler. HTML Işaretçileri sürüklemeyi destekler.
* Sembol katmanı: bir simge ve/veya metin içeren noktaları WebGL bağlamı içinde Işler.
* Kabarcık katmanı: noktaları haritada daireler olarak Işler. Dairelerin yarıçapı, verilerdeki özelliklere göre ölçeklendirilebilir.

Hem sembol hem de kabarcık katmanları WebGL bağlamında işlenir ve haritada çok büyük noktaların kümelerini işleyebilir. Bu katmanlar, verilerin bir veri kaynağında depolanmasını gerektirir. Veri kaynakları ve işleme katmanları, `ready` olay tetiklendikten sonra haritaya eklenmelidir. HTML Işaretçileri, sayfada DOM öğeleri olarak işlenir ve veri kaynağı kullanmaz. Bir sayfada daha fazla DOM öğesi varsa, sayfa ne kadar yavaş olur. Bir haritada birkaç yüz noktasından fazlasını işliyorsa, bunun yerine işleme katmanlarından birini kullanmanız önerilir.

Aşağıdaki örnekler, (Boylam:-0,2, Latitude: 51,5) konumundaki haritaya bir etiket olarak bulunan 10 sayısıyla bir işaret ekler.

**Önce: Bing Haritalar**

Bing Haritalar ile, işaretçiler * sınıfı kullanılarak haritaya eklenir `Microsoft.Maps.Pushpin` . Pushpin 'ler, iki işlevden birini kullanarak haritaya eklenir.

İlk işlev bir katman oluşturmak, bu için raptiye eklemek ve ardından Haritayı haritanın `layers` özelliğine eklemektir.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

İkincisi, eşlemenin özelliğini kullanarak eklemektir `entities` . Bu işlev, Bing Haritalar için belgelerde kullanım dışı olarak işaretlenir V8 ancak temel senaryolar için kısmen işlevsel olarak çalışır.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Bing Haritalar puspin Ekle](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Sonra: HTML Işaretçileri kullanarak Azure Maps**

Azure haritalar 'da HTML işaretçileri, haritada kolayca bir noktayı göstermek için kullanılabilir ve yalnızca haritada az sayıda nokta görüntülemesi gereken basit uygulamalar için önerilir. Bir HTML işaretleyicisi kullanmak için, sınıfın bir örneğini oluşturun `atlas.HtmlMarker` , metin ve konum seçeneklerini ayarlayın ve işlevi kullanarak, işaretçiyi haritaya ekleyin `map.markers.add` .

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure haritalar ekleme işaretleyici](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Sonra: bir sembol katmanı kullanarak Azure Maps**

Bir sembol katmanı kullanırken, verilerin bir veri kaynağına eklenmesi ve katmana eklenmiş veri kaynağı olması gerekir. Ayrıca, olay tetiklendikten sonra veri kaynağı ve katman haritaya eklenmelidir `ready` . Bir simgenin üzerinde benzersiz bir metin değeri işlemek için metin bilgilerinin, veri noktasının bir özelliği olarak depolanması gerekir ve bu özellik `textField` katmanın seçeneğinde başvurulmalıdır. Bu, HTML işaretçilerini kullanmaktan biraz daha çalışır, ancak birçok performans avantajı sağlar.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure haritalar sembol katmanı Ekle](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Ek Kaynaklar**

-   [Veri kaynağı oluşturma](./create-data-source-web-sdk.md)
-   [Sembol katmanı ekleme](./map-add-pin.md)
-   [Kabarcık katmanı ekleme](./map-add-bubble-layer.md)
-   [Küme noktası verileri](./clustering-point-data-web-sdk.md)
-   [HTML Işaretçileri Ekle](./map-add-custom-html.md)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)
-   [Sembol katmanı simgesi seçenekleri](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Sembol katmanı metin seçeneği](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML işaret sınıfı](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML işaret seçenekleri](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Özel iğne ekleme

Özel görüntüler, bir haritadaki noktaları temsil etmek için kullanılabilir. Aşağıdaki örnek, aşağıdaki örneklerde kullanılır ve (Latitude: 51,5, Boylam:-0,2) eşlemesindeki haritada bir nokta göstermek için özel bir görüntü kullanır ve işaretçinin konumunu, raptiye simgesinin noktasının haritada doğru konuma göre hizalanmasını sağlayacak şekilde kaydırır.

| ![Azure haritalar puspin Ekle](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Önce: Bing Haritalar**

Bing Haritalar 'da, bir görüntüye bir URL 'YI bir raptiçın seçeneklerine geçirerek özel bir işaret oluşturulur `icon` . `anchor`Seçeneği, raptiye görüntüsünün noktasını haritadaki koordinatla hizalamak için kullanılır. Bing haritadaki tutturucu değeri görüntünün sol üst köşesine göre.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Bing Haritalar özel puspin Ekle](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Sonra: HTML Işaretçileri kullanarak Azure Maps**

Azure 'da bir HTML işaretçisi özelleştirmek için HTML `string` veya `HTMLElement` `htmlContent` işaret seçeneğine geçirilebilir. Azure haritalar 'da, `anchor` belirlenen dokuz başvuru noktasından birini kullanarak konum koordinatına göre işaretin göreli konumunu belirtmek için bir seçenek kullanılır; "Orta", "üst", "alt", "sol", "sağ", "üst-sol", "üst-sağ", "alt-sol", "alt-sağ". İçerik bağlantılıdır ve varsayılan olarak "alt" olarak ayarlanır. Bu, HTML içeriğinin alt ortatır. Bing Haritalar 'dan kod geçirmeyi kolaylaştırmak için, bağlayıcıyı "üst-sol" olarak ayarlayın ve ardından bu `offset` seçeneği Bing Haritalar 'da kullanılan aynı uzaklığa göre kullanın. Azure haritalar 'daki uzaklıklar, Bing Haritalar 'ın ters yönünde taşınır, bu nedenle bunları eksi bir ile çarpın.

> [!TIP]
> `pointer-events:none`İstenmeyen bir simge görüntüleyecek şekilde, MS Edge 'de varsayılan sürükleme davranışını devre dışı bırakmak IÇIN HTML içeriğine bir stil olarak ekleyin.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure haritalar özel işaretleyici Ekle](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Sonra: bir sembol katmanı kullanarak Azure Maps**

Azure haritalar 'daki sembol katmanları özel görüntüleri de destekler, ancak görüntünün önce harita kaynaklarına yüklenmesi ve benzersiz bir KIMLIK ataması gerekir. Sembol katmanı daha sonra bu KIMLIĞE başvurabilir. Simge, simge seçeneği kullanılarak görüntüde doğru noktaya hizalanacak şekilde kaydırılır `offset` . Azure haritalar 'da, `anchor` sembolün, belirlenen dokuz başvuru noktasından birini kullanarak konum koordinatına göre göreli konumunu belirtmek için bir seçenek kullanılır. "Orta", "üst", "alt", "sol", "sağ", "üst-sol", "üst-sağ", "alt-sol", "alt-sağ". İçerik bağlantılıdır ve varsayılan olarak HTML içeriğinin alt merkezi olan "alt" olarak ayarlanır. Bing Haritalar 'dan kod geçirmeyi kolaylaştırmak için, bağlayıcıyı "üst-sol" olarak ayarlayın ve ardından bu `offset` seçeneği Bing Haritalar 'da kullanılan aynı uzaklığa göre kullanın. Azure haritalar 'daki uzaklıklar, Bing Haritalar 'ın ters yönünde taşınır, bu nedenle bunları eksi bir ile çarpın.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Haritalar özel sembol katmanı Ekle](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> Gelişmiş özel noktayla işleme oluşturmak için birden çok işleme katmanını birlikte kullanın. Örneğin, farklı renkli daireler üzerinde aynı simgeye sahip birden fazla Pushpin 'e sahip olmak istiyorsanız, her renk için bir sembol katmanı oluşturmak yerine bir kabarcık katmanının üzerine bir sembol katmanını ve aynı veri kaynağına başvurmalarını sağlar. Bu, oluşturmaktan çok daha verimlidir ve haritanın farklı görüntüleri korumasını sağlar.

**Ek Kaynaklar**

-   [Veri kaynağı oluşturma](./create-data-source-web-sdk.md)
-   [Sembol katmanı ekleme](./map-add-pin.md)
-   [HTML Işaretçileri Ekle](./map-add-custom-html.md)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)
-   [Sembol katmanı simgesi seçenekleri](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Sembol katmanı metin seçeneği](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML işaret sınıfı](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML işaret seçenekleri](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Çoklu çizgi ekleme

Polylines, haritada bir çizgiyi veya yolu göstermek için kullanılır. Aşağıdaki örneklerde, haritada kesik çizgili bir çoklu çizginin nasıl oluşturulacağı gösterilmektedir.

**Önce: Bing Haritalar**

Bing Haritalar 'da, çoklu çizgi sınıfı bir konum dizisi ve bir seçenek kümesi alır.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Bing Haritalar çoklu çizgi](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, çoklu çizgilerin çizgisini daha yaygın Jeo uzamsal hüküm `LineString` veya nesneler olarak adlandırılır `MultiLineString` . Bu nesneler bir veri kaynağına eklenebilir ve çizgi katmanı kullanılarak oluşturulabilir. Kontur rengi, Width ve Dash dizi seçenekleri platformlar arasında neredeyse aynıdır.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Azure haritalar çizgisi](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**Ek Kaynaklar**

-   [Haritaya satır ekleme](./map-add-line-layer.md)
-   [Çizgi katmanı seçenekleri](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Çokgen ekleme

Çokgenler, haritadaki bir alanı temsil etmek için kullanılır. Azure haritalar ve Bing Haritalar, çokgenler için çok benzer destek sağlar. Aşağıdaki örneklerde haritanın orta koordinatı temelinde bir üçgen oluşturan bir çokgen oluşturma gösterilmektedir.

**Önce: Bing Haritalar**

Bing Haritalar 'da Çokgen sınıfı, koordinat veya koordinat halkalarını ve bir dizi seçeneği alır.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Bing Haritalar polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da Çokgen ve MultiPolygon nesneleri bir veri kaynağına eklenebilir ve katmanlar kullanılarak haritada işlenebilir. Çokgenin alanı çokgen katmanında işlenebilir. Bir çokgenin ana hattı bir çizgi katmanı kullanılarak oluşturulabilir.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Azure haritalar polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Ek Kaynaklar**

-   [Haritaya Çokgen ekleme](./map-add-shape.md#use-a-polygon-layer)
-   [Haritaya daire ekleme](./map-add-shape.md#add-a-circle-to-the-map)
-   [Çokgen katman seçenekleri](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Çizgi katmanı seçenekleri](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Bilgi kutusu görüntüle

Bir varlık için ek bilgiler, Bing Haritalar 'da bir sınıf olarak haritada görüntülenebilir `Microsoft.Maps.Infobox` , Azure haritalar 'da bu, sınıfı kullanılarak elde edilebilir `atlas.Popup` . Aşağıdaki örneklerde haritaya bir iğne/işaret eklenir ve tıklandığı zaman bir bilgi kutusu/açılan pencere görüntülenir.

**Önce: Bing Haritalar**

Bing Haritalar ile, Oluşturucu kullanılarak bir bilgi kutusu oluşturulur `Microsoft.Maps.Infobox` .

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Bing Haritalar bilgi kutusu](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da bir konum için ek bilgileri göstermek üzere bir açılan pencere kullanılabilir. Bir HTML `string` veya `HTMLElement` nesne `content` açılan pencere seçeneğine geçirilebilir. Açılır pencereler, istenirse herhangi bir şekilden bağımsız olarak görüntülenebilir ve bu nedenle bir `position` değer belirtilmesini gerektirir. Bir açılan pencere göstermek için, `open` işlevi çağırın ve açılır pencerenin görüntüleneceği Haritayı geçirin.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Azure haritalar açılan menüsü](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> Bir sembol, kabarcık, çizgi veya Çokgen katmanıyla aynı şeyi yapmak için, katmanı bir işaretleyici yerine haritalar olay koduna geçirin.

**Ek Kaynaklar**

-   [Açılır pencere ekleme](./map-add-popup.md)
-   [Medya Içeriğiyle açılan pencere](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Şekillerdeki açılanlar](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Çoklu PIN ile açılan pencereyi yeniden kullanma](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Açılır Sınıf](/javascript/api/azure-maps-control/atlas.popup)
-   [Açılan seçenekler](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>İğne Kümelemesi

Haritadaki çok sayıda veri noktasını görselleştirirken, eşleme bozulur ve kullanımı zor olur. Nokta verisi Kümelemesi, bu kullanıcı deneyimini geliştirmek ve ayrıca performansı geliştirmek için kullanılabilir. Kümeleme noktası verileri, birbirine yakın olan nokta verisini birleştirme ve bunları haritada tek bir kümelenmiş veri noktası olarak temsil etme işlemidir. Kullanıcı haritada yakınlaştırdıktan sonra, kümeler kendi bireysel veri noktalarıyla kesilir.

Aşağıdaki örneklerde, eski haftadan gelen deprem verilerinin coğrafi JSON akışı yüklenir ve haritaya eklenir. Kümeler, içerdikleri noktaların sayısına bağlı olarak ölçekli ve renkli daireler olarak işlenir.

> [!NOTE]
> İğne Kümelemesi için kullanılan birkaç farklı algoritma vardır. Bing Haritalar basit bir kılavuz tabanlı işlev kullanır, Azure Maps daha gelişmiş ve görsel açıdan çekici bir nokta tabanlı kümeleme yöntemleri kullanır.

**Önce: Bing Haritalar**

Bing Haritalar 'da geojson verileri GeoJSON modülü kullanılarak yüklenebilir. Pushpin 'ler kümeleme modülüne yükleyerek ve içerdiği kümeleme katmanını kullanarak kümelenebilir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Haritalar Kümelemesi](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da veriler bir veri kaynağı tarafından eklenir ve yönetilir. Katmanlar veri kaynaklarına bağlanır ve verileri bunlarda işler. `DataSource`Azure Maps 'taki Sınıf çeşitli kümeleme seçenekleri sağlar.

* `cluster` – Veri kaynağını küme noktası verilerine bildirir. 
* `clusterRadius` -Bit cinsinden noktaları birlikte kümelemek için yarıçap.
* `clusterMaxZoom` -Kümelemenin gerçekleştiği en yüksek yakınlaştırma düzeyi. Bundan daha fazlasını yakınlaştırırsanız, tüm noktaları semboller olarak işlenir.
* `clusterProperties` -Her küme içindeki tüm noktalara karşı ifadeler kullanılarak hesaplanan ve her küme noktasının özelliklerine eklenen özel özellikleri tanımlar.

Kümeleme etkinleştirildiğinde veri kaynağı, işleme için katmanlara kümelenmiş ve kümelenmemiş veri noktaları gönderir. Veri kaynağı yüzlerce binlerce veri noktası kümelemesine sahiptir. Kümelenmiş bir veri noktası üzerinde aşağıdaki özelliklere sahiptir:

| Özellik adı               | Tür    | Description                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Özelliğin bir kümeyi temsil ettiğini belirtir.     |
| `cluster_id`                | string  | Kümeyle `DataSource` `getClusterExpansionZoom` , `getClusterChildren` , ve işlevleriyle KULLANıLABILECEK benzersiz bir kimlik `getClusterLeaves` . |
| `point_count`               | sayı  | Kümenin içerdiği noktaların sayısı.     |
| `point_count_abbreviated`   | string  | Long ise değeri abbreviates bir dize `point_count` . (örneğin, 4.000 4K olur) |

`DataSource`Sınıfı, kullanarak bir kümeyle ilgili ek bilgilere erişmek için aşağıdaki yardımcı işleve sahiptir `cluster_id` .

| İşlev       | Dönüş türü        | Description     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Sonraki yakınlaştırma düzeyinde verilen kümenin alt öğelerini alır. Bu alt öğeler, şekillerin ve alt kümelerin bir birleşimi olabilir. Alt kümeler, küme özellikleriyle eşleşen özelliklerle özellik olacaktır. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Kümenin genişlemekte veya parçalanmasına başlayacağı yakınlaştırma düzeyini hesaplar.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Kümedeki tüm noktaları alır. `limit`' İ, noktaların bir alt kümesini döndürecek şekilde ayarlayın ve bu `offset` noktaları noktaları üzerinden kullanın.    |

Haritada kümelenmiş verileri işlerken genellikle iki veya daha fazla katman kullanmak en kolay yoldur. Aşağıdaki örnek üç katman kullanır, küme boyutunu temel alan ölçeklendirilmiş renkli daireler çizmek için bir kabarcık katmanı, küme boyutunu metin olarak işlemek için bir sembol katmanı ve kümelenmemiş noktaları işlemek için ikinci bir sembol katmanı. [Küme noktası veri](./clustering-point-data-web-sdk.md) belgelerinde vurgulanan Azure haritalar 'da kümelenmiş verileri işlemenin birçok farklı yolu vardır.

GeoJSON verileri, sınıfındaki işlevi kullanılarak Azure Maps 'ta doğrudan içeri aktarılabilir `importDataFromUrl` `DataSource` .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure haritalar Kümelemesi](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**Ek Kaynaklar**

-   [Sembol katmanı ekleme](./map-add-pin.md)
-   [Kabarcık katmanı ekleme](./map-add-bubble-layer.md)
-   [Küme noktası verileri](./clustering-point-data-web-sdk.md)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Isı haritası ekleme

Nokta yoğunluğu haritaları olarak da bilinen ısı haritaları, bir dizi renk kullanan verilerin yoğunluğunu temsil etmek için kullanılan bir veri görselleştirmesi türüdür. Genellikle bir haritada "etkin noktalar" verilerini göstermek için kullanılır ve büyük nokta veri kümelerini işlemek için harika bir yoldur.

Aşağıdaki örneklerde, USG 'lerden geçen aya ait tüm deprem coğrafi JSON akışı yüklenir ve bunları bir ısı haritası olarak işler.

**Önce: Bing Haritalar**

Bing Haritalar 'da, ısı haritası oluşturmak için, ısı haritası modülüne yükleyin. Benzer şekilde, GeoJSON modülü de GeoJSON verileri için destek eklemek üzere yüklenir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Haritalar ısı haritasını](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Sonrasında: Azure Maps**

Azure Maps ' de GeoJSON verilerini bir veri kaynağına yükleyin ve veri kaynağını bir ısı haritası katmanına bağlayın. GeoJSON verileri, sınıfındaki işlevi kullanılarak Azure Maps 'ta doğrudan içeri aktarılabilir `importDataFromUrl` `DataSource` .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure haritalar ısı haritasını](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**Ek Kaynaklar**

-   [Isı haritası katmanı ekleme](./map-add-heat-map-layer.md)
-   [Isı haritası katman sınıfı](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Isı haritası katman seçenekleri](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Veri temelli stil ifadeleri kullanma](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Döşeme katmanını kaplama

Döşeme katmanları, haritalar döşeme sistemiyle hizalamış, daha küçük döşeli görüntülerle bölünmüş büyük görüntülerin yer kaplamasını sağlar. Bu, büyük görüntüleri veya çok büyük veri kümelerini kaplayan yaygın bir yoldur.

Aşağıdaki örneklerde, bir X, Y, zoom döşeme adlandırma şeması kullanan İowa çevresel Mesonet of Iowa çevresel bir hava durumu radar kutucuk katmanı yer kaplar.

**Önce: Bing Haritalar**

Bing Haritalar 'da, döşeme katmanları sınıfı kullanılarak oluşturulabilir `Microsoft.Maps.TileLayer` .

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Bing Haritalar ağırlıklı ısı haritasını](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, bir döşeme katmanı, diğer tüm katmanla aynı şekilde haritaya eklenebilir. X, y, zoom yertutucuları olan biçimli bir URL; `{x}`, `{y}` `{z}` sırasıyla katmana nerede erişacaklarını bildirmek için kullanılır. Azure haritalar kutucuk katmanları da destekler `{quadkey}` `{bbox-epsg-3857}` ve `{subdomain}` yer tutucular.

> [!TIP]
> Azure haritalar katmanları, temel harita katmanları dahil olmak üzere diğer katmanların altına kolayca işlenebilirler. Genellikle, daha kolay okunabilmeleri için harita etiketlerinin altında döşeme katmanlarını işlemek tercih edilir. `map.layers.add`İşlevi, aşağıdaki yeni katmanı eklemek için ikinci bir KATMANıN kimliği olan ikinci bir parametreyi alır. Harita etiketlerinin altına bir kutucuk katmanı eklemek için aşağıdaki kod kullanılabilir:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Azure haritalar ağırlıklı ısı haritasını](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> Kutucuk istekleri `transformRequest` harita seçeneği kullanılarak yakalanabilir. Bu, istenirse istekleri değiştirmenize veya üst bilgileri eklemenize olanak tanır.

**Ek Kaynaklar**

-   [Kutucuk katmanları ekleme](./map-add-tile-layer.md)
-   [Döşeme katmanı sınıfı](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Döşeme katmanı seçenekleri](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Trafik verilerini gösterme

Trafik verileri hem Bing hem de Azure Maps 'ta yer alabilir.

**Önce: Bing Haritalar**

Bing Haritalar 'da trafik verileri, trafik modülünü kullanarak haritada yer alabilir.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Bing Haritalar trafiği](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Sonrasında: Azure Maps**

Azure haritalar, trafiği görüntülemek için çeşitli farklı seçenekler sağlar. Yol kapanışları ve kazalardan dolayı gibi trafik olayları haritada simgeler olarak görüntülenebilir. Trafik akışı, renk kodlu yollar, haritada bulunabilir ve renkler, normal beklenen gecikmeye veya mutlak gecikmeye göre, postalanan hız sınırına göre temel olacak şekilde değiştirilebilir. Azure haritalar 'daki olay verileri her dakikada bir güncelleştirilir ve akış verileri her 2 dakikada bir güncelleştirilir.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Azure haritalar trafiği](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Azure haritalar 'daki trafik simgelerinden birine tıklarsanız, açılan pencerede ek bilgiler görüntülenir.

![Azure haritalar trafik açılan kutusu](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**Ek Kaynaklar**

-   [Haritada trafiği göster](./map-show-traffic.md)
-   [Trafik kaplama seçenekleri](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Trafik denetimi](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Zemin kaplama ekleme

Hem Bing hem de Azure Maps harita üzerinde coğrafi olarak başvurulan resimlerin üzerine yerleşimi destekler ve Haritada kaydırma ve yakınlaştırma sırasında ölçeği yakınlaştırıp ölçeklendirirler. Bing Haritalar 'da Bunlar, Azure haritalarının görüntü katmanları olarak adlandırıldıkları sırada yukarı biçimlendirmeler olarak bilinir. Bunlar, kat planları oluşturmak, eski haritaları yerleştirmek veya bir DRA 'dan Imagery için harika.

**Önce: Bing Haritalar**

Bing Haritalar 'da bir zemin kaplama oluştururken görüntünün URL 'sini ve haritada bağlamak için bir sınırlayıcı kutuyu belirtmeniz gerekir. Bu örnek, haritadaki 1922 ' [dan yeni Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 'in bir harita görüntüsünü yerdekiler.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Bu kodun bir tarayıcıda çalıştırılması, aşağıdaki görüntü gibi görünen bir eşlem görüntüler:

![Bing Haritalar zemin kaplaması](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, coğrafi olarak başvurulan görüntüler sınıfı kullanılarak kaplama olabilir `atlas.layer.ImageLayer` . Bu sınıf, görüntünün dört köşesi için bir görüntünün URL 'sini ve bir koordinat kümesini gerektirir. Görüntünün aynı etki alanında barındırılması ya da CORs 'nin etkin olması gerekir.

> [!TIP]
> Görüntünün her köşesi için yalnızca Kuzey, Güney, Doğu, Batı ve döndürme bilgilerine sahipseniz, statik [Atlas. Layer. ımagelayer. Getkoordinatör Tesfromedges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) işlevini kullanabilirsiniz.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure haritalar zemin kaplaması](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**Ek Kaynaklar**

-   [Resme katman ekleme](./map-add-image-layer.md)
-   [Görüntü katmanı sınıfı](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>KML verilerini haritaya ekleme

Hem Azure hem de Bing Haritalar haritadaki KML, KMZ, GeoRSS, GeoJSON ve Well-Known Text (WKT) verilerini içeri aktarabilir ve işleyebilir. Azure haritalar Ayrıca GPX, GML, uzamsal CSV dosyalarını, Web-Mapping Hizmetleri (WMS), Web-Mapping kutucuk Hizmetleri (WMTS) ve web özelliği hizmetlerini (WFS) destekler.

**Önce: Bing Haritalar**

Bu kodun bir tarayıcıda çalıştırılması, aşağıdaki görüntü gibi görünen bir eşlem görüntüler:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Haritalar KML](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da GeoJSON, Web SDK 'sında kullanılan ana veri biçimidir, ek uzamsal veri biçimleri de [uzamsal GÇ modülü](/javascript/api/azure-maps-spatial-io/)kullanılarak kolayca tümleştirilebilir. Bu modülde hem okuma hem de yazma uzamsal verileri için işlevler bulunur ve bu uzamsal veri biçimlerinden herhangi birinden verileri kolayca işleyebilmeniz gereken basit bir veri katmanı da vardır. Uzamsal bir veri dosyasındaki verileri okumak için, bir URL 'ye veya ham verileri işleve dize veya blob olarak geçirin `atlas.io.read` . Bu, dosyadan daha sonra haritaya eklenebilen tüm ayrıştırılmış verileri döndürür. Daha çok daha fazla stil bilgisi içerdiği için KML, en çok uzamsal veri biçiminden biraz daha karmaşıktır. `SpatialDataLayer`Sınıfı, bu stillerin büyük bölümünü oluşturmayı destekler, ancak özellik verileri yüklenmeden önce simge görüntülerinin haritaya yüklenmesi gerekir ve arka kaplamalar haritaya ayrı olarak eklenir. Bir URL aracılığıyla veri yüklerken, CORs 'yi etkinleştirmiş bir uç noktada barındırılmalıdır veya bir proxy hizmeti okuma işlevine seçenek olarak geçirilmelidir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure haritalar KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**Ek Kaynaklar**

-   [Atlas. IO. Read işlevi](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Çizim araçları ekleme

Hem Bing hem de Azure Maps, kullanıcının fare veya başka bir giriş cihazını kullanarak haritada şekil çizme ve düzenleme yeteneğini ekleyen bir modül sağlar. Her ikisi de Pushpin, çizgi ve çokgenler çizmeyi destekler. Azure haritalar, daireler ve dörtgenler çizilmesine yönelik seçenekler de sağlar.

**Önce: Bing Haritalar**

Bing Haritalar 'da `DrawingTools` Modül işlevi kullanılarak yüklenir `Microsoft.Maps.loadModule` . Yüklendikten sonra, DrawingTools sınıfının bir örneği oluşturulabilir ve `showDrawingManager` işlev, haritaya bir araç çubuğu ekle olarak adlandırılır.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Bing Haritalar çizim araçları](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, JavaScript ve CSS dosyalarının uygulamada başvurulması gerekir. Eşleme yüklendikten sonra, sınıfının bir örneği `DrawingManager` oluşturulup `DrawingToolbar` eklenmiş bir örnek olabilir.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Azure haritalar çizim araçları](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> Azure haritalar katmanlarında çizim araçları, kullanıcıların şekil çizebilmesine yönelik birden çok yol sağlar. Örneğin, bir çokgen çizerken Kullanıcı her noktayı eklemek için tıklayabilir veya sol fare düğmesini aşağı basılı tutup bir yol çizmek için fareyi sürükleyebilirsiniz. Bu seçeneği kullanılarak değiştirilebilir `interactionType` `DrawingManager` .

**Ek Kaynaklar**

-   [Belgeler](./set-drawing-options.md)
-   [Kod örnekleri](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>Ek kaynaklar

[Açık kaynaklı Azure Maps web SDK modüllerine](open-source-projects.md#open-web-sdk-modules)göz atın. Bu modüller, daha fazla işlevsellik sağlar ve tamamen özelleştirilebilir.

Diğer Bing Haritalar özelliklerini geçirme ile ilgili kod örneklerini gözden geçirin:

**Veri görselleştirmeleri**

> [!div class="nextstepaction"]
> [Dağılım katmanı](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Veri atma](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Hizmetler**

> [!div class="nextstepaction"]
> [Azure haritalar Hizmetleri modülünü kullanma](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [İlgi noktası arama](./map-search-location.md)

> [!div class="nextstepaction"]
> [Bir koordinatdan bilgi edinme (ters coğrafi kod)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A'dan B'ye yönleri gösterme](./map-route.md)

> [!div class="nextstepaction"]
> [JQuery Kullanıcı arabirimi ile otomatik öneri ara](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Azure Haritalar Web SDK 'Sı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Harita denetimini kullanma](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Hizmetler modülünü kullanma](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Çizim araçları modülünü kullanma](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure Maps web SDK hizmeti API başvuru belgeleri](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizlenecek kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Bing Haritalar 'dan Azure Maps 'a geçme hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Web hizmetini geçirme](migrate-from-bing-maps-web-services.md)