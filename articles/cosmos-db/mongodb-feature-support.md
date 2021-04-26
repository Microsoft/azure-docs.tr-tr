---
title: MongoDB (3,2 sürüm) için API Azure Cosmos DB desteklenen özellikler ve sözdizimi
description: MongoDB (3,2 sürümü) tarafından desteklenen özellikler ve söz dizimi için Azure Cosmos DB API 'SI hakkında bilgi edinin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605758"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>MongoDB için Azure Cosmos DB’nin API’si (sürüm 3.2): desteklenen özellikler ve söz dizimi
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Açık kaynaklı MongoDB istemci [sürücülerinden](https://docs.mongodb.org/ecosystem/drivers)herhangi birini kullanarak mongodb için Azure Cosmos DB API 'si ile iletişim kurabilirsiniz. MongoDB için Azure Cosmos DB’nin API’si, MongoDB [kablo protokolüne](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) bağlı kalarak mevcut istemci sürücülerinin kullanımını etkinleştirir.

MongoDB için Azure Cosmos DB API 'sini kullanarak, kullandığınız MongoDB 'nin avantajlarından yararlanarak, Cosmos DB sağladığı tüm kurumsal yetenekler şunlardır: [küresel dağıtım](distribute-data-globally.md), [Otomatik](partitioning-overview.md)parçalama, kullanılabilirlik ve gecikme garantisi, her alanın otomatik dizin oluşturma, Rest, yedeklemeler ve çok daha fazlası.

> [!NOTE]
> MongoDB için Cosmos DB API 'sinin 3,2 sürümü, yaşam sonu (EOL) için geçerli bir plan içermiyor. Gelecek EOL için en düşük bildirim üç yıldır.

## <a name="protocol-support"></a>Protokol desteği

MongoDB için Azure Cosmos DB API 'sine yönelik tüm yeni hesaplar MongoDB sunucu sürümü **3,6** ile uyumludur. Bu makalede MongoDB sürüm 3.2 ele alınır. Desteklenen işleçler ve tüm sınırlamalar veya özel durumlar aşağıda listelenmiştir. Bu protokolleri anlayan tüm istemci sürücüleri MongoDB için Azure Cosmos DB’nin API’sine bağlanabilmesi gerekir. 

MongoDB için Azure Cosmos DB API 'SI, uygun hesaplar için sorunsuz bir yükseltme deneyimi sunmaktadır. [MongoDB sürüm yükseltme Kılavuzu](mongodb-version-upgrade.md)hakkında daha fazla bilgi edinin.

## <a name="query-language-support"></a>Sorgu dili desteği

MongoDB için Azure Cosmos DB API 'SI, MongoDB sorgu dili yapıları için kapsamlı destek sağlar. Aşağıda, şu anda desteklenen işlemler, işleçler, aşamalar, komutlar ve seçeneklerin ayrıntılı listesini bulabilirsiniz.

## <a name="database-commands"></a>Veritabanı komutları

MongoDB için Azure Cosmos DB API 'SI aşağıdaki veritabanı komutlarını destekler:

> [!NOTE]
> Bu makale yalnızca desteklenen sunucu komutlarını listeler ve istemci tarafı sarmalayıcı işlevlerini dışlar. Ve gibi istemci tarafı sarmalayıcı işlevleri `deleteMany()` `updateMany()` `delete()` ve sunucu komutlarını dahili olarak kullanır `update()` . Desteklenen sunucu komutlarını kullanan işlevler, MongoDB için Azure Cosmos DB API 'siyle uyumludur.

### <a name="query-and-write-operation-commands"></a>Sorgulama ve yazma işlemi komutları

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Kimlik doğrulama komutları

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Yönetim komutları

- dropDatabase
- listCollections
- drop
- oluşturmaya
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Tanılama komutları

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Toplama ardışık düzeni</a>

Cosmos DB, genel önizlemede MongoDB 3,2 için toplama işlem hattını destekler. Genel önizlemenin katılımı hakkında yönergeler için [Azure bloguna](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/) bakın.

### <a name="aggregation-commands"></a>Toplama komutları

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Toplama aşamaları

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Toplama ifadeleri

#### <a name="boolean-expressions"></a>Mantıksal ifadeler

- $and
- $or
- $not

#### <a name="set-expressions"></a>Küme ifadeleri

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Karşılaştırma ifadeleri

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetik ifadeler

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Dize ifadeleri

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Dizi ifadeleri

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Tarih ifadeleri

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Koşullu ifadeler

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Toplama biriktiricileri

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>İşleçler

Desteklenen işleçler, bunların kullanımıyla ilgili örneklerle birlikte aşağıda verilmiştir. Sorgularda kullanılan bu örnek belgeyi göz önünde bulundurun:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

| İşleç | Örnek |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Notlar

Normal ifade ($regex) sorgularında, soldan başlayan ifadeler dizin aramasına izin verir. Ancak 'i' değiştiricisini (büyük/küçük harf duyarlığı) ve 'm' değiştiricisini (çok satırlılık) kullanmak, tüm ifadelerde koleksiyon taramasına neden olur.
'$' veya '|' karakterinin dahil edilmesinin gerektiği durumlarda iki (veya daha fazla) normal ifade sorgusu oluşturmak en iyisidir.
Örneğin şu özgün sorgu: ```find({x:{$regex: /^abc$/})``` şu şekilde değiştirilmelidir: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
İlk kısım aramayı ^abc ile başlayan belgeler ile sınırlamak için dizini kullanır, ikinci kısım ise tam girişler ile eşleşir.
Çubuk işleci '|' "veya" işlevini görür - ```find({x:{$regex: /^abc|^def/})``` sorgusu 'x' alanının değerlerinin "abc" ile veya "def" ile başladığı belgelerle eşleşir. Dizinden yararlanmak için sorgunun $or işleci ile birleştirilen iki farklı sorguya bölünmesi gerekir: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Güncelleştirme işleçleri

#### <a name="field-update-operators"></a>Alan güncelleştirme işleçleri

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Dizi güncelleştirme işleçleri

- $addToSet
- $pop
- $pullAll
- $pull (Not: koşulu $pull desteklenmez)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bit düzeyinde güncelleştirme işleci

- $bit

### <a name="geospatial-operators"></a>Jeo-uzamsal işleçler

İşleç | Örnek | Desteklenir |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Yes |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Yes |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Yes |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |

## <a name="sort-operations"></a>Sıralama Işlemleri

`findOneAndUpdate`İşlem kullanılırken, tek bir alanda sıralama işlemleri desteklenir, ancak birden çok alanda sıralama işlemleri desteklenmez.

## <a name="other-operators"></a>Diğer işleçler

İşleç | Örnek | Notlar
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Desteklenmez. Bunun yerine $regex kullanın.

## <a name="unsupported-operators"></a>Desteklenmeyen işleçler

```$where``` ve ```$eval``` işleçleri, Azure Cosmos DB tarafından desteklenmiyor.

### <a name="methods"></a>Yöntemler

Aşağıdaki yöntemler desteklenir:

#### <a name="cursor-methods"></a>İmleç yöntemleri

Yöntem | Örnek | Notlar
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Sıralama anahtarı olmayan belgeler döndürülmez

## <a name="unique-indexes"></a>Benzersiz dizinler

Cosmos DB, varsayılan olarak veritabanına yazılan belgelerdeki her alanı dizine ekler. Benzersiz dizinler, belirli bir alanın bir koleksiyondaki tüm belgeler genelinde yinelenen değerlere sahip olmamasını sağlar, bu da benzersizlik 'in varsayılan anahtarda korunma biçimine benzer `_id` . ' Unique ' kısıtlaması dahil CreateIndex komutunu kullanarak Cosmos DB özel dizinler oluşturabilirsiniz.

Benzersiz dizinler, Azure Cosmos DB MongoDB için API kullanan tüm Cosmos hesaplarında kullanılabilir.

## <a name="time-to-live-ttl"></a>Etkin kalma süresi (TTL)

Cosmos DB, belgenin zaman damgasına göre yaşam süresi (TTL) desteği sağlar. TTL, [Azure Portal](https://portal.azure.com)giderek koleksiyonlar için etkinleştirilebilir.

## <a name="user-and-role-management"></a>Kullanıcı ve rol yönetimi

Cosmos DB henüz kullanıcıları ve rolleri desteklemez. Ancak, Azure rol tabanlı erişim denetimi (Azure RBAC) ve [Azure Portal](https://portal.azure.com) (bağlantı dizesi sayfası) aracılığıyla elde edilebilir salt okuma ve salt okuma parolalarını/anahtarlarını destekler Cosmos DB.

## <a name="replication"></a>Çoğaltma

Cosmos DB, en düşük katmanlarda otomatik, yerel çoğaltmayı destekler. Bu mantık, düşük gecikme süresi ve küresel çoğaltma elde etmek için genişletilir. Cosmos DB el ile çoğaltma komutlarını desteklemez.

## <a name="write-concern"></a>Yazma Sorunu

Bazı uygulamalar, yazma işlemi sırasında gereken yanıt sayısını belirten bir [yazma kaygısını](https://docs.mongodb.com/manual/reference/write-concern/) kullanır. Cosmos DB’nin arka planda çoğaltma işleme şekli nedeniyle varsayılan olarak tüm yazma işlemleri otomatik olarak çekirdektir. İstemci kodu tarafından belirtilen herhangi bir yazma sorunu yok sayılır. Daha fazla bilgi için bkz. [Kullanılabilirlik ve performansı en üst düzeye çıkarmak için tutarlılık düzeylerini kullanma](consistency-levels.md).

## <a name="sharding"></a>Parçalama

Azure Cosmos DB, otomatik, sunucu tarafı parçalamasını destekler. Parça oluşturma, yerleştirme ve dengelemeyi otomatik olarak yönetir. Azure Cosmos DB, el ile parçalı komutları desteklemez, bu da shardCollection, Addkıard, balancerStart, moveChunk vb. gibi komutları çağırmak zorunda olmadığınız anlamına gelir. Yalnızca kapsayıcıları oluştururken veya verileri sorgularken parça anahtarını belirtmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
