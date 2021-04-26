---
title: Bölge sınırı için geojson veri biçimi | Microsoft Azure haritaları
description: Azure haritalar bölge verileri hakkında bilgi edinin. Bir bölge alanına göre koordinatların konumunu alırken bölge alma ve bölge oluşturma API 'Lerini nasıl kullanacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897203"
---
# <a name="geofencing-geojson-data"></a>Bölge sınırlaması coğrafi JSON verileri

Azure haritalar [bölge](/rest/api/maps/spatial/getgeofence) sınırlaması ve [son bölge](/rest/api/maps/spatial/postgeofence) API 'leri, belirtilen bölge veya bir sınırlama kümesine göre bir koordinat yakınlığını almanızı sağlar. Bu makalede, Azure haritalar GET ve POST API 'sinde kullanılabilen bölge verilerinin nasıl hazırlanacağı anlatılmaktadır.

Bölge ölçütü veya bölge kümesi verileri `Feature` `FeatureCollection` `GeoJSON` , [rfc7946](https://tools.ietf.org/html/rfc7946)içinde tanımlanan biçimdeki nesne ve nesne tarafından temsil edilir. Bunlara ek olarak:

* GeoJSON nesne türü bir `Feature` nesne veya `FeatureCollection` nesne olabilir.
* Geometri nesne türü,,,,,, `Point` `MultiPoint` ve olabilir `LineString` `MultiLineString` `Polygon` `MultiPolygon` `GeometryCollection` .
* Tüm özellik özellikleri `geometryId` , geofence 'yi tanımlamak için kullanılan bir içermelidir.
* Özelliği,,,,, `Point` `MultiPoint` `LineString` `MultiLineString` özelliklerinde içermesi gerekir `radius` . `radius` değer ölçü cinsinden ölçülür, `radius` değer 1 ile 10000 arasında değişir.
* `polygon`Ve `multipolygon` geometri türündeki özellik bir Radius özelliğine sahip değil.
* `validityTime` , Kullanıcı bölge verileri için süresi dolmuş süreyi ve geçerlilik süresi dönemini ayarlamaya olanak sağlayan isteğe bağlı bir özelliktir. Belirtilmemişse, verilerin süresi süresiz olur ve her zaman geçerlidir.
* , `expiredTime` Bölge sınırlaması verilerinin son kullanma tarihi ve saati. `userTime`İstekteki değeri bu değerden daha sonra ise, karşılık gelen bölge verileri, süre dolmayan veriler olarak değerlendirilir ve sorgulanmaz. Bu durumda, bu bölge verilerinin geometryıd 'si, `expiredGeofenceGeometryId` Bölge sınırı yanıtı içinde diziye dahil edilir.
* , `validityPeriod` Geofence 'ın geçerlilik zaman döneminin bir listesidir. `userTime`İstekteki değeri geçerlilik süresinin dışında kalırsa, ilgili bölge verileri geçersiz olarak kabul edilir ve sorgulanmaz. Bu bölge verilerinin geometryıd 'si, `invalidPeriodGeofenceGeometryId` Bölge sınırı Response içindeki diziye dahil edilmiştir. Aşağıdaki tabloda validityPeriod öğesinin özellikleri gösterilmektedir.

| Ad | Tür | Gerekli  | Açıklama |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Tarih saat  | true | Geçerlilik zaman döneminin başlangıç tarihi saati. |
| endTime   | Tarih saat  | true |  Geçerlilik zaman döneminin bitiş tarihi. |
| Numaralarına | string | yanlış |   Dönemin yinelenme türü. Değer,, `Daily` veya olabilir `Weekly` `Monthly` `Yearly` . Varsayılan değer `Daily` olarak belirlenmiştir.|
| yalnızca BusinessDay | Boole | yanlış |  Verilerin yalnızca iş günleri için geçerli olup olmadığını belirtin. Varsayılan değer `false` olarak belirlenmiştir.|


* Tüm koordinat değerleri içinde tanımlanmış [Boylam, Enlem] olarak gösterilir `WGS84` .
* ,,, Veya içeren her özellik için, `MultiPoint` `MultiLineString` `MultiPolygon` `GeometryCollection` Özellikler tüm öğelere uygulanır. Örneğin: içindeki tüm noktaları, `MultiPoint` birden çok daire bölge oluşturmak için aynı yarıçapı kullanır.
* Nokta-daire senaryosunda, bir daire geometrisi, `Point` [geojson geometrileri genişletilerek](./extend-geojson.md)ayrıntılı Properties ile bir geometri nesnesi kullanılarak gösterilebilir.      

Aşağıda, bir `GeoJSON` orta nokta ve yarıçap kullanan bir daire bölge alanı geometrisi olarak temsil edilen bölge için örnek bir istek gövdesi verilmiştir. Bölge verilerinin geçerli dönemi 2018-10-22, 00 ila 17:00, hafta sonu hariç her gün yinelenir. `expiredTime` istekte daha sonra olması durumunda bu bölge verilerinin zaman aşımına geçeceğini belirtir `userTime` `2019-01-01` .  

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