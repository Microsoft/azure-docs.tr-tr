---
title: Azure haritalar 'da genişletilmiş coğrafi JSON geometrileri | Microsoft Docs
description: Azure haritalar 'da coğrafi JSON geometrileri genişletmeyi öğrenin
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881994"
---
# <a name="extended-geojson-geometries"></a>Genişletilmiş GeoJSON geometrileri

Azure Maps, coğrafi özellikler içinde/içinde arama yapmak için güçlü API 'lerin bir listesini sağlar.
Bu API 'Ler coğrafi özellikleri temsil etmek için [geojson spec][1] üzerinde standartlaştırın (örneğin: durum sınırları, rotalar).  

[Geojson özelliği][1] yalnızca aşağıdaki geometrileri destekler:

* GeometryCollection
* LineString
* MultiLineString
* Noktalı
* MultiPolygon
* Seçeneğinin
* Gen

Bazı Azure Maps API 'Leri (örneğin: [Geometri Içinde ara](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) [geojson belirtiminin][1]parçası olmayan "Circle" gibi geometriler kabul edin.

Bu makalede, Azure eşlemelerinin belirli geometrileri göstermek için [geojson belirtimini][1] nasıl genişlettiği hakkında ayrıntılı bir açıklama sunulmaktadır.

## <a name="circle"></a>Daire

Geometri, [geojson belirtimi][1]tarafından desteklenmiyor. `Circle` Bir daireyi temsil `GeoJSON Point Feature` etmek için bir nesnesi kullanıyoruz.

Nesnesini kullanarak temsil edilen bir `Circle` geometri şunları içermelidir: `GeoJSON Feature`

- Orta

    Dairenin merkezi bir `GeoJSON Point` nesne kullanılarak temsil edilir.

- Yarıçap

    Dairenin `radius` özellikleri kullanılarak `GeoJSON Feature`temsil edilir. Yarıçap değeri _Ölçü_ cinsindendir ve türünde `double`olmalıdır.

- SubType

    Daire geometrisi `subType` özelliği de içermelidir. Bu özellik, `GeoJSON Feature`özelliklerinin bir parçası olmalıdır ve değeri _daire_ olmalıdır

#### <a name="example"></a>Örnek

Burada ortalanan bir daireyi nasıl temsil edeceksiniz (Latitude: 47,639754, Boylam:-122,126986) bir yarıçapı, bir bir `GeoJSON Feature` nesne kullanarak bir Radius ile 100 eşittir:

```json            
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

## <a name="rectangle"></a>Dikdörtgen

Geometri, [geojson belirtimi][1]tarafından desteklenmiyor. `Rectangle` Bir dikdörtgeni temsil `GeoJSON Polygon Feature` etmek için bir nesnesi kullanıyoruz. Dikdörtgen uzantısı öncelikle web SDK 'nın çizim araçları modülü tarafından kullanılır.

Nesnesini kullanarak temsil edilen bir `Rectangle` geometri şunları içermelidir: `GeoJSON Polygon Feature`

- Elerinden

    Dikdörtgenin köşeleri `GeoJSON Polygon` nesnenin koordinatları kullanılarak temsil edilir. Her köşede biri olan beş koordinat ve çokgen halkasını kapatmak için 1 ile aynı olan beşinci bir koordinat olmalıdır. Bu koordinatların, geliştirici tarafından istendiği şekilde hizalanıp döndürülmeyeceğini varsayacaktır.

- SubType

    Dikdörtgen geometrisi `subType` özelliği de içermelidir. Bu özellik, `GeoJSON Feature`özelliklerinin bir parçası olmalıdır ve değeri _dikdörtgen_ olmalıdır

### <a name="example"></a>Örnek

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar 'da coğrafi JSON verileri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Geofence GeoJSON biçimi](geofence-geojson.md)

Azure haritalar ve konum zekası uygulamalarıyla ilişkili ortak teknik terimlerin sözlüğünü gözden geçirin:

> [!div class="nextstepaction"]
> [Azure haritalar sözlüğü](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
