---
title: Android haritalar 'da harita olaylarını işleme | Microsoft Azure haritaları
description: Kullanıcılar eşlemelerle etkileşim kurarken hangi olayların tetiklediği hakkında bilgi edinin. Desteklenen tüm harita olaylarının listesini görüntüleyin. Olayları işlemek için Azure Maps Android SDK kullanma konusuna bakın.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ebe61e5956dc0f35794211a336eb7d884ee18d76
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608910"
---
# <a name="interact-with-the-map-android-sdk"></a>Eşlemele etkileşim kurma (Android SDK)

Bu makalede haritalar olayları yöneticisini nasıl kullanabileceğiniz gösterilmektedir.

## <a name="interact-with-the-map"></a>Harita ile etkileşim kurma

Map, tüm olaylarını özelliği aracılığıyla yönetir `events` . Aşağıdaki tabloda desteklenen tüm harita olayları listelenmektedir.

| Olay                  | Olay işleyicisi biçimi | Description |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Eşleme "boşta" durumuna girmeden önce oluşturulan son çerçeveden sonra harekete geçirilir:<ul><li>Devam eden bir kamera geçişi yok.</li><li>Şu anda istenen tüm kutucuklar yüklendi.</li><li>Tüm belirme/geçiş animasyonları tamamlandı.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Bir görünümden diğerine animasyon eklenmiş bir geçiş sırasında, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak tekrar tekrar tetiklenir. |
| `OnCameraMoveCanceled` | `()`                 | Kameraya bir taşıma isteği iptal edildiğinde harekete geçirilir. |
| `OnCameraMoveStarted`  | `(int reason)`       | Eşleme, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak bir görünümden diğerine geçiş yapmaya başlamadan hemen önce tetiklenir. `reason`Olay dinleyicisinin bağımsız değişkeni, kamera hareketinin nasıl başlatılmasının ayrıntılarını sağlayan bir tamsayı değeri döndürür. Aşağıdaki listede olası nedenler özetlenmektedir:<ul><li>1: hareket</li><li>2: Geliştirici animasyonu</li><li>3: API animasyonu</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Eşleme basıldığında ve haritada aynı noktada bırakıldığında harekete geçirilir. Bu olay işleyicisi, olayın diğer olay dinleyicilerine daha fazla harcanması veya geçirilmesi gerektiğini belirten bir Boole değeri döndürür. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Eşleme basıldığında harekete geçirilir ve bir özellikte aynı noktada serbest bırakılır. Bu olay işleyicisi, olayın diğer olay dinleyicilerine daha fazla harcanması veya geçirilmesi gerektiğini belirten bir Boole değeri döndürür. |
| `OnLayerAdded` | `(Layer layer)` | Haritaya bir katman eklendiğinde harekete geçirildi. |
| `OnLayerRemoved` | `(Layer layer)` | Eşlemden bir katman kaldırıldığında harekete geçirilir. |
| `OnLoaded` | `()` | Tüm gerekli kaynaklar indirildikten ve haritanın ilk görsel olarak işlenmesi tamamlandıktan hemen sonra harekete geçirilir. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Eşleme basıldığında harekete geçirilir, bir süre tutulur ve sonra haritada aynı noktada serbest bırakılır. Bu olay işleyicisi, olayın diğer olay dinleyicilerine daha fazla harcanması veya geçirilmesi gerektiğini belirten bir Boole değeri döndürür. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Eşleme basıldığında harekete geçirilir, bir süre tutulur ve sonra bir özellikte aynı noktada serbest bırakılır. Bu olay işleyicisi, olayın diğer olay dinleyicilerine daha fazla harcanması veya geçirilmesi gerektiğini belirten bir Boole değeri döndürür. |
| `OnReady`              | `(AzureMap map)`     | Eşleme başlangıçta yüklendiğinde veya uygulama yönü değiştiğinde ve gereken en az sayıda eşleme kaynağı yüklendiğinde ve eşleme, program aracılığıyla ile etkileşim için hazırlanmaya hazırsa tetiklenir. |
| `OnSourceAdded` | `(Source source)` | `DataSource`Eşlemeye bir veya `VectorTileSource` eklendiğinde tetiklenir. |
| `OnSourceRemoved` | `(Source source)` | `DataSource`Eşlemden bir veya `VectorTileSource` kaldırıldığında tetiklenir. |
| `OnStyleChange` | `()` | Haritanın stili yüklenirken veya değiştiğinde harekete geçirilir. |

Aşağıdaki kod,, `OnClick` `OnFeatureClick` ve olaylarının haritaya nasıl ekleneceğini gösterir `OnCameraMove` .

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Daha fazla bilgi için harita ve tetikleme olaylarıyla nasıl etkileşim kuracağınızı öğrenmek için [harita](how-to-use-android-map-control-library.md#navigating-the-map) belgelerine bakın.

## <a name="scope-feature-events-to-layer"></a>Katman özelliği olaylarını katmana kapsama

`OnFeatureClick`Veya `OnLongFeatureClick` olaylarını haritaya eklerken, bir katman örneği veya katman kimliği ikinci bir parametre olarak geçirilebilir. Bir katman geçirildiğinde, olay yalnızca bu katmanda olay gerçekleşirse harekete geçirilir. Katmanlara kapsamlı olaylar sembol, kabarcık, çizgi ve çokgen katmanları tarafından desteklenir.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

Tam kod örnekleri için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Haritada gezinme](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Baloncuk katmanı ekleme](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](how-to-add-shapes-to-android-map.md)
