---
title: Genişletilmiş GeoJSON geometrileri | Microsoft Azure haritaları
description: Azure haritalar 'ın coğrafi JSON belirtimini ek geometrik şekiller içerecek şekilde nasıl genişlettiğini öğrenin. Eşlemlerde kullanılmak üzere daireler ve dörtgenler ayarlandığı örnekleri görüntüleyin.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895792"
---
# <a name="extended-geojson-geometries"></a>Genişletilmiş GeoJSON geometrileri

Azure Maps, coğrafi özelliklerin içinde ve üzerinde arama yapmak için güçlü API 'lerin bir listesini sağlar. Bu API 'Ler, coğrafi özellikleri temsil eden standart [geojson][1] belirtimine uyar.  

[Geojson belirtimi][1] yalnızca aşağıdaki geometrileri destekler:

* GeometryCollection
* LineString
* MultiLineString
* Noktalı
* MultiPolygon
* Seçeneğinin
* Poligon

Bazı Azure Maps API 'Leri, [geojson belirtiminin][1]parçası olmayan geometrileri kabul eder. Örneğin, geometri API 'si [Içinde arama](/rest/api/maps/search/postsearchinsidegeometry) , daire ve çokgenler kabul eder.

Bu makalede, Azure eşlemelerinin belirli geometrileri göstermek için [geojson belirtimini][1] nasıl genişlettiği hakkında ayrıntılı bir açıklama sunulmaktadır.

## <a name="circle"></a>Daire

`Circle`Geometri, [geojson belirtimi][1]tarafından desteklenmiyor. Bir `GeoJSON Point Feature` daireyi temsil etmek için bir nesnesi kullanıyoruz.

`Circle`Nesnesini kullanarak temsil edilen bir `GeoJSON Feature` geometri  aşağıdaki koordinatları ve özellikleri içermelidir:

- Merkez

    Dairenin merkezi bir nesne kullanılarak temsil edilir `GeoJSON Point` .

- Radius

    Dairenin `radius` özellikleri kullanılarak temsil edilir `GeoJSON Feature` . Yarıçap değeri _Ölçü_ cinsindendir ve türünde olmalıdır `double` .

- SubType

    Daire geometrisi özelliği de içermelidir `subType` . Bu özellik, özelliklerinin bir parçası olmalıdır `GeoJSON Feature` ve değeri _daire_ olmalıdır

#### <a name="example"></a>Örnek

Bir nesneyi kullanarak bir daireyi nasıl temsil ettiğini aşağıda bulabilirsiniz `GeoJSON Feature` . Bu çemberi Enlem: 47,639754 ve Boylam:-122,126986 ' ye kadar daire ortalayalım ve bu değere bir Radius atamak için 100.

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

`Rectangle`Geometri, [geojson belirtimi][1]tarafından desteklenmiyor. Bir `GeoJSON Polygon Feature` dikdörtgeni temsil etmek için bir nesnesi kullanıyoruz. Dikdörtgen uzantısı öncelikle web SDK 'nın çizim araçları modülü tarafından kullanılır.

`Rectangle`Nesnesini kullanarak temsil edilen bir `GeoJSON Polygon Feature` geometri  aşağıdaki koordinatları ve özellikleri içermelidir:

- Elerinden

    Dikdörtgenin köşeleri nesnenin koordinatları kullanılarak temsil edilir `GeoJSON Polygon` . Her köşede birer tane olmak üzere beş koordinat olmalıdır. Ve çokgen halkasını kapatmak için ilk koordinat ile aynı olan beşinci bir koordinat. Bu koordinatların hizalandığından ve geliştiricinin istenen şekilde döndürebildiğinden emin olacak.

- SubType

    Dikdörtgen geometrisi özelliği de içermelidir `subType` . Bu özellik, özelliklerinin bir parçası olmalıdır `GeoJSON Feature` ve değeri _dikdörtgen_ olmalıdır

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
> [Azure Haritalar sözlüğü](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946