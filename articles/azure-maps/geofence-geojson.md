---
title: Azure haritalar 'da bölge bölge coğrafi JSON verisi biçimi | Microsoft Docs
description: Azure haritalar 'da bölge bölge coğrafi JSON veri biçimi hakkında bilgi edinin
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5946180c161a38a30f44e235ce0b626fd70a5400
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735135"
---
# <a name="geofencing-geojson-data"></a>Bölge sınırlaması coğrafi JSON verileri

Azure haritalar [bölge](/rest/api/maps/spatial/getgeofence) sınırlaması ve [son bölge](/rest/api/maps/spatial/postgeofence) API 'leri, belirtilen bölge veya bir sınırlama kümesine göre bir koordinat yakınlığını almanızı sağlar. Bu makalede, Azure haritalar GET ve POST API 'sinde kullanılabilen bölge verilerinin nasıl hazırlanacağı anlatılmaktadır.

Bölge ölçütü veya bölge `Feature` kümesi verileri, [rfc7946](https://tools.ietf.org/html/rfc7946)içinde tanımlanan `GeoJSON` biçimdeki nesne ve `FeatureCollection` nesne tarafından temsil edilir. Bunlara ek olarak:

* Geojson nesne türü bir `Feature` nesne `FeatureCollection` veya nesne olabilir.
* `Point`Geometri nesne türü `MultiPoint` ,`Polygon` `GeometryCollection`, ,,,,veolabilir.`MultiLineString` `LineString` `MultiPolygon`
* Tüm özellik özellikleri, geofence `geometryId`'yi tanımlamak için kullanılan bir içermelidir.
* `Point`Özelliği ,`MultiPoint` ,,`radius` ,, özelliklerinde içermesi gerekir.`MultiLineString` `LineString` `radius`değer ölçü cinsinden ölçülür, `radius` değer 1 ile 10000 arasında değişir.
* `polygon` Ve`multipolygon` geometri türündeki özellik bir Radius özelliğine sahip değil.
* `validityTime`, Kullanıcı bölge verileri için süresi dolmuş süreyi ve geçerlilik süresi dönemini ayarlamaya olanak sağlayan isteğe bağlı bir özelliktir. Belirtilmemişse, verilerin süresi süresiz olur ve her zaman geçerlidir.
* , `expiredTime` Bölge sınırlaması verilerinin son kullanma tarihi ve saati. İstekteki değeri `userTime` bu değerden daha sonra ise, karşılık gelen bölge verileri, süre dolmayan veriler olarak değerlendirilir ve sorgulanmaz. Bu durumda, bu bölge verilerinin geometryıd 'si, bölge sınırı yanıtı içinde `expiredGeofenceGeometryId` diziye dahil edilir.
* , `validityPeriod` Geofence 'ın geçerlilik zaman döneminin bir listesidir. İstekteki değeri `userTime` geçerlilik süresinin dışında kalırsa, ilgili bölge verileri geçersiz olarak kabul edilir ve sorgulanmaz. Bu bölge verilerinin geometryıd 'si, bölge sınırı Response içindeki `invalidPeriodGeofenceGeometryId` diziye dahil edilmiştir. Aşağıdaki tabloda validityPeriod öğesinin özellikleri gösterilmektedir.

| Name | Type | Gerekli  | Açıklama |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Geçerlilik zaman döneminin başlangıç tarihi saati. |
| endTime   | Datetime  | true |  Geçerlilik zaman döneminin bitiş tarihi. |
| Numaralarına | dize | false |   Dönemin yinelenme türü. Değer `Daily` `Weekly` ,,`Yearly`veya olabilir. `Monthly` Varsayılan değer `Daily`.|
| yalnızca BusinessDay | Boole değeri | false |  Verilerin yalnızca iş günleri için geçerli olup olmadığını belirtin. Varsayılan değer `false`.|


* Tüm koordinat değerleri içinde `WGS84`tanımlanmış [Boylam, Enlem] olarak gösterilir.
* `MultiPoint` ,`MultiLineString`,, Veya`GeometryCollection`içeren her özellik için, özellikler tüm öğelere uygulanır. `MultiPolygon` Örneğin: İçindeki `MultiPoint` tüm noktaları, birden çok daire bölge oluşturmak için aynı yarıçapı kullanır.
* Nokta-daire senaryosunda, bir daire geometrisi, `Point` [geojson geometrileri genişletilerek](https://docs.microsoft.com/azure/azure-maps/extend-geojson)ayrıntılı Properties ile bir geometri nesnesi kullanılarak gösterilebilir.      

Aşağıda, bir orta nokta ve yarıçap `GeoJSON` kullanan bir daire bölge alanı geometrisi olarak temsil edilen bölge için örnek bir istek gövdesi verilmiştir. Bölge verilerinin geçerli dönemi 2018-10-22, 00 ila 17:00, hafta sonu hariç her gün yinelenir. `expiredTime``userTime` istekte`2019-01-01`daha sonra olması durumunda bu bölge verilerinin zaman aşımına geçeceğini belirtir.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
