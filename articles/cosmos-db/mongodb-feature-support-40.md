---
title: 4,0 Server sürümü desteklenen özellikler ve sözdizimi MongoDB için Azure Cosmos DB API 'SI
description: MongoDB 4,0 Server sürümü desteklenen özellikleri ve söz dizimi için Azure Cosmos DB API 'SI hakkında bilgi edinin. Veritabanı komutları, sorgu dil desteği, veri türleri, toplama işlem hattı komutları ve desteklenen işleçler hakkında bilgi edinin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 74e08cce381d173e0c5e1458ae99167a0c8c697f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504519"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>MongoDB için API Azure Cosmos DB (4,0 sunucu sürümü): desteklenen özellikler ve sözdizimi
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Açık kaynaklı MongoDB istemci [sürücülerinden](https://docs.mongodb.org/ecosystem/drivers)herhangi birini kullanarak mongodb için Azure Cosmos DB API 'si ile iletişim kurabilirsiniz. MongoDB için Azure Cosmos DB’nin API’si, MongoDB [kablo protokolüne](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) bağlı kalarak mevcut istemci sürücülerinin kullanımını etkinleştirir.

MongoDB için Azure Cosmos DB API 'sini kullanarak, kullandığınız MongoDB avantajlarından yararlanarak, Cosmos DB sağladığı tüm kurumsal yetenekler sayesinde, [genel dağıtım](distribute-data-globally.md), [Otomatik](partitioning-overview.md)parçalar, kullanılabilirlik ve gecikme garantisi, Rest, yedeklemeler ve çok daha fazlası için de kullanabilirsiniz.

## <a name="protocol-support"></a>Protokol desteği

Desteklenen işleçler ve tüm sınırlamalar veya özel durumlar aşağıda listelenmiştir. Bu protokolleri anlayan tüm istemci sürücüleri MongoDB için Azure Cosmos DB’nin API’sine bağlanabilmesi gerekir. MongoDB hesapları için Azure Cosmos DB API 'SI kullanılırken, hesapların 3.6 + sürümleri biçiminde bulunur, `*.mongo.cosmos.azure.com` ancak hesaplarının 3,2 sürümü bitiş noktasına sahiptir `*.documents.azure.com` .

> [!NOTE]
> Bu makale yalnızca desteklenen sunucu komutlarını listeler ve istemci tarafı sarmalayıcı işlevlerini dışlar. Ve gibi istemci tarafı sarmalayıcı işlevleri `deleteMany()` `updateMany()` `delete()` ve sunucu komutlarını dahili olarak kullanır `update()` . Desteklenen sunucu komutlarını kullanan işlevler, MongoDB için Azure Cosmos DB API 'siyle uyumludur.

## <a name="query-language-support"></a>Sorgu dili desteği

MongoDB için Azure Cosmos DB API 'SI, MongoDB sorgu dili yapıları için kapsamlı destek sağlar. Aşağıda, şu anda desteklenen işlemler, işleçler, aşamalar, komutlar ve seçeneklerin ayrıntılı listesini bulabilirsiniz.

## <a name="database-commands"></a>Veritabanı komutları

MongoDB için Azure Cosmos DB API 'SI aşağıdaki veritabanı komutlarını destekler:

### <a name="query-and-write-operation-commands"></a>Sorgulama ve yazma işlemi komutları

| Komut | Desteklenir |
|---------|---------|
| [akışları Değiştir](mongodb-change-streams.md) | Yes |
| delete | Yes |
| Eval | Hayır |
| find | Yes |
| findAndModify | Yes |
| getLastError | Yes |
| getMore | Yes |
| getPrevError | Hayır |
| insert | Yes |
| parallelCollectionScan | Hayır |
| resetError | Hayır |
| update | Yes |

### <a name="transaction-commands"></a>İşlem komutları

| Komut | Desteklenir |
|---------|---------|
| abortTransaction | Yes |
| commitTransaction | Yes |

### <a name="authentication-commands"></a>Kimlik doğrulama komutları

| Komut | Desteklenir |
|---------|---------|
| authenticate | Yes |
| getnonce | Yes |
| logout | Yes |

### <a name="administration-commands"></a>Yönetim komutları

| Komut | Desteklenir |
|---------|---------|
| Clonecollectionascamış | Hayır |
| collMod | Hayır |
| connectionStatus | Hayır |
| Converttocamış | Hayır |
| copydb | Hayır |
| oluşturmaya | Yes |
| createIndexes | Yes |
| currentOp | Yes |
| drop | Yes |
| dropDatabase | Yes |
| dropIndexes | Yes |
| filemd5 | Yes |
| killCursors | Yes |
| killOp | Hayır |
| listCollections | Yes |
| listDatabases | Yes |
| listIndexes | Yes |
| reIndex | Yes |
| renameCollection | Hayır |

### <a name="diagnostics-commands"></a>Tanılama komutları

| Komut | Desteklenir |
|---------|---------|
| buildInfo | Yes |
| collStats | Yes |
| connPoolStats | Hayır |
| connectionStatus | Hayır |
| dataSize | Hayır |
| dbHash | Hayır |
| dbStats | Yes |
| açıklamak | Yes |
| özellikler | Hayır |
| hostInfo | Yes |
| listDatabases | Yes |
| listCommands | Hayır |
| profil | Hayır |
| serverStatus | Hayır |
| top | Hayır |
| whatsmyuri | Yes |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Toplama işlem hattı

### <a name="aggregation-commands"></a>Toplama komutları

| Komut | Desteklenir |
|---------|---------|
| aggregate | Yes |
| count | Yes |
| distinct | Yes |
| mapReduce | Hayır |

### <a name="aggregation-stages"></a>Toplama aşamaları

| Komut | Desteklenir |
|---------|---------|
| $addFields | Yes |
| $bucket | Hayır |
| $bucketAuto | Hayır |
| $changeStream | Yes |
| $collStats | Hayır |
| $count | Yes |
| $currentOp | Hayır |
| $facet | Yes |
| $geoNear | Yes |
| $graphLookup | Yes |
| $group | Yes |
| $indexStats | Hayır |
| $limit | Yes |
| $listLocalSessions | Hayır |
| $listSessions | Hayır |
| $lookup | Kısmi |
| $match | Yes |
| $out | Yes |
| $project | Yes |
| $redact | Yes |
| $replaceRoot | Yes |
| $replaceWith | Hayır |
| $sample | Yes |
| $skip | Yes |
| $sort | Yes |
| $sortByCount | Yes |
| $unwind | Yes |

> [!NOTE]
> `$lookup` , sunucu sürümü 3,6 ' de tanıtılan [bağıntısız alt sorgular](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) özelliğini henüz desteklememektedir. `let is not supported` `$lookup` İşlecini ve alanları ile kullanmayı denerseniz, içeren bir iletiyle hata alırsınız `let` `pipeline` .

### <a name="boolean-expressions"></a>Mantıksal ifadeler

| Komut | Desteklenir |
|---------|---------|
| $and | Yes |
| $not | Yes |
| $or | Yes |

### <a name="conversion-expressions"></a>Dönüştürme ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $convert | Yes |
| $toBool | Yes |
| $toDate | Yes |
| $toDecimal | Yes |
| $toDouble | Yes |
| $toInt | Yes |
| $toLong | Yes |
| $toObjectId | Yes |
| $toString | Yes |

### <a name="set-expressions"></a>Küme ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $setEquals | Yes |
| $setIntersection | Yes |
| $setUnion | Yes |
| $setDifference | Yes |
| $setIsSubset | Yes |
| $anyElementTrue | Yes |
| $allElementsTrue | Yes |

### <a name="comparison-expressions"></a>Karşılaştırma ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $cmp | Yes |
| $eq | Yes | 
| $gt | Yes | 
| $gte | Yes | 
| $lt | Yes |
| $lte | Yes | 
| $ne | Yes | 
| $in | Yes | 
| $nin | Yes | 

### <a name="arithmetic-expressions"></a>Aritmetik ifadeler

| Komut | Desteklenir |
|---------|---------|
| $abs | Yes |
| $add | Yes |
| $ceil | Yes |
| $divide | Yes |
| $exp | Yes |
| $floor | Yes |
| $ln | Yes |
| $log | Yes |
| $log10 | Yes |
| $mod | Yes |
| $multiply | Yes |
| $pow | Yes |
| $sqrt | Yes |
| $subtract | Yes |
| $trunc | Yes |

### <a name="string-expressions"></a>Dize ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $concat | Yes |
| $indexOfBytes | Yes |
| $indexOfCP | Yes |
| $ltrim | Yes |
| $rtrim | Yes |
| $trim | Yes |
| $split | Yes |
| $strLenBytes | Yes |
| $strLenCP | Yes |
| $strcasecmp | Yes |
| $substr | Yes |
| $substrBytes | Yes |
| $substrCP | Yes |
| $toLower | Yes |
| $toUpper | Yes |

### <a name="text-search-operator"></a>Metin arama işleci

| Komut | Desteklenir |
|---------|---------|
| $meta | Hayır |

### <a name="array-expressions"></a>Dizi ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $arrayElemAt | Yes |
| $arrayToObject | Yes |
| $concatArrays | Yes |
| $filter | Yes |
| $indexOfArray | Yes |
| $isArray | Yes |
| $objectToArray | Yes |
| $range | Yes |
| $reverseArray | Yes |
| $reduce | Yes |
| $size | Yes |
| $slice | Yes |
| $zip | Yes |
| $in | Yes |

### <a name="variable-operators"></a>Değişken işleçleri

| Komut | Desteklenir |
|---------|---------|
| $map | Yes |
| $let | Yes |

### <a name="system-variables"></a>Sistem değişkenleri

| Komut | Desteklenir |
|---------|---------|
| $ $CURRENT | Yes |
| $ $DESCEND | Yes |
| $ $KEEP | Yes |
| $ $PRUNE | Yes |
| $ $REMOVE | Yes |
| $ $ROOT | Yes |

### <a name="literal-operator"></a>Sabit değer operatörü

| Komut | Desteklenir |
|---------|---------|
| $literal | Yes |

### <a name="date-expressions"></a>Tarih ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $dayOfYear | Yes |
| $dayOfMonth | Yes |
| $dayOfWeek | Yes |
| $year | Yes |
| $month | Yes | 
| $week | Yes |
| $hour | Yes |
| $minute | Yes | 
| $second | Yes |
| $millisecond | Yes | 
| $dateToString | Yes |
| $isoDayOfWeek | Yes |
| $isoWeek | Yes |
| $dateFromParts | Hayır | 
| $dateToParts | Hayır |
| $dateFromString | Hayır |
| $isoWeekYear | Yes |

### <a name="conditional-expressions"></a>Koşullu ifadeler

| Komut | Desteklenir |
|---------|---------|
| $cond | Yes |
| $ifNull | Yes |
| $switch | Yes |

### <a name="data-type-operator"></a>Veri türü işleci

| Komut | Desteklenir |
|---------|---------|
| $type | Yes |

### <a name="accumulator-expressions"></a>Biriktiricidir ifadeleri

| Komut | Desteklenir |
|---------|---------|
| $sum | Yes |
| $avg | Yes |
| $first | Yes |
| $last | Yes |
| $max | Yes |
| $min | Yes |
| $push | Yes |
| $addToSet | Yes |
| $stdDevPop | Yes |
| $stdDevSamp | Yes |

### <a name="merge-operator"></a>Merge işleci

| Komut | Desteklenir |
|---------|---------|
| $mergeObjects | Yes |

## <a name="data-types"></a>Veri türleri

MongoDB için Azure Cosmos DB API 'SI MongoDB BSON biçiminde kodlanmış belgeleri destekler. 4,0 API sürümü, performansı artırmak ve maliyetleri azaltmak için bu biçimin iç kullanımını geliştirir. 4,0 avantajı çalıştıran bir uç nokta aracılığıyla yazılan veya güncellenen belgeler.
 
Bir [yükseltme senaryosunda](mongodb-version-upgrade.md), sürüm 4,0 ' e yükseltmeden önce yazılan belgeler, 4,0 uç noktası aracılığıyla bir yazma işlemi aracılığıyla güncelleştirilene kadar gelişmiş performansa karşı bir avantaja sahip olmayacaktır.

| Komut | Desteklenir |
|---------|---------|
| Çift | Evet |
| Dize | Yes |
| Nesne | Yes |
| Dizi | Yes |
| İkili veriler | Yes | 
| ObjectId | Yes |
| Boole | Yes |
| Tarih | Yes |
| Null | Yes |
| 32-bit tamsayı (int) | Yes |
| Timestamp | Yes |
| 64 bit tamsayı (uzun) | Yes |
| MinKey | Yes |
| MaxKey | Yes |
| Decimal128 | Yes | 
| Normal ifade | Yes |
| JavaScript | Yes |
| JavaScript (kapsama sahip)| Yes |
| Tanımlayan | Yes |

## <a name="indexes-and-index-properties"></a>Dizinler ve Dizin Özellikleri

### <a name="indexes"></a>Dizinler

| Komut | Desteklenir |
|---------|---------|
| Tek alan dizini | Yes |
| Bileşik Dizin | Yes |
| Çok tuşlu Dizin | Yes |
| Metin dizini | Hayır |
| 2dsphere | Yes |
| 2B Dizin | Hayır |
| Karma Dizin | Yes |

### <a name="index-properties"></a>Dizin Özellikleri

| Komut | Desteklenir |
|---------|---------|
| TTL | Yes |
| Benzersiz | Yes |
| Kısmi | Hayır |
| Büyük/küçük harf duyarsız | Hayır |
| Seyrek | Hayır |
| Arka Plan | Yes |

## <a name="operators"></a>İşleçler

### <a name="logical-operators"></a>Mantıksal işleçler

| Komut | Desteklenir |
|---------|---------|
| $or | Yes |
| $and | Yes |
| $not | Yes |
| $nor | Yes | 

### <a name="element-operators"></a>Öğe işleçleri

| Komut | Desteklenir |
|---------|---------|
| $exists | Yes |
| $type | Yes |

### <a name="evaluation-query-operators"></a>Değerlendirme sorgu işleçleri

| Komut | Desteklenir |
|---------|---------|
| $expr | Hayır |
| $jsonSchema | Hayır |
| $mod | Yes |
| $regex | Yes |
| $text | Hayır (desteklenmiyor. Bunun yerine $regex kullanın.)| 
| $where | Hayır | 

$Regex sorgularda, sola sabitlenmiş ifadeler Dizin aramasına izin verir. Ancak 'i' değiştiricisini (büyük/küçük harf duyarlığı) ve 'm' değiştiricisini (çok satırlılık) kullanmak, tüm ifadelerde koleksiyon taramasına neden olur.

'$' veya '|' karakterinin dahil edilmesinin gerektiği durumlarda iki (veya daha fazla) normal ifade sorgusu oluşturmak en iyisidir. Örneğin, aşağıdaki özgün sorgu verildiğinde `find({x:{$regex: /^abc$/})` , şu şekilde değiştirilmesi gerekir:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

İlk kısım aramayı ^abc ile başlayan belgeler ile sınırlamak için dizini kullanır, ikinci kısım ise tam girişler ile eşleşir. Çubuk işleci '|' "veya" işlevini görür - `find({x:{$regex: /^abc |^def/})` sorgusu 'x' alanının değerlerinin "abc" ile veya "def" ile başladığı belgelerle eşleşir. Dizinden yararlanmak için sorgunun $or işleci ile birleştirilen iki farklı sorguya bölünmesi gerekir: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Dizi işleçleri

| Komut | Desteklenir | 
|---------|---------|
| $all | Yes | 
| $elemMatch | Yes | 
| $size | Yes | 

### <a name="comment-operator"></a>Açıklama işleci

| Komut | Desteklenir | 
|---------|---------|
| $comment | Yes | 

### <a name="projection-operators"></a>Projeksiyon işleçleri

| Komut | Desteklenir |
|---------|---------|
| $elemMatch | Yes |
| $meta | Hayır |
| $slice | Yes |

### <a name="update-operators"></a>Güncelleştirme işleçleri

#### <a name="field-update-operators"></a>Alan güncelleştirme işleçleri

| Komut | Desteklenir |
|---------|---------|
| $inc | Yes |
| $mul | Yes |
| $rename | Yes |
| $setOnInsert | Yes |
| $set | Yes |
| $unset | Yes |
| $min | Yes |
| $max | Yes |
| $currentDate | Yes |

#### <a name="array-update-operators"></a>Dizi güncelleştirme işleçleri

| Komut | Desteklenir |
|---------|---------|
| $ | Yes |
| $[]| Yes |
| $[<identifier>]| Yes |
| $addToSet | Yes |
| $pop | Yes |
| $pullAll | Yes |
| $pull | Yes |
| $push | Yes |
| $pushAll | Yes |

#### <a name="update-modifiers"></a>Güncelleştirme değiştiricileri

| Komut | Desteklenir |
|---------|---------|
| $each | Yes |
| $slice | Yes |
| $sort | Yes |
| $position | Yes |

#### <a name="bitwise-update-operator"></a>Bit düzeyinde güncelleştirme işleci

| Komut | Desteklenir |
|---------|---------|
| $bit | Yes | 
| $bitsAllSet | Hayır |
| $bitsAnySet | Hayır |
| $bitsAllClear | Hayır |
| $bitsAnyClear | Hayır |

### <a name="geospatial-operators"></a>Jeo-uzamsal işleçler

Operatör | Desteklenir | 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near | Yes |
$nearSphere | Yes |
$geometry | Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | Hayır |
$centerSphere | Hayır |
$box | Hayır |
$polygon | Hayır |

## <a name="sort-operations"></a>Sıralama işlemleri

`findOneAndUpdate`İşlem kullanılırken, tek bir alanda sıralama işlemleri desteklenir, ancak birden çok alanda sıralama işlemleri desteklenmez.

## <a name="unique-indexes"></a>Benzersiz dizinler

[Benzersiz dizinler](mongodb-indexing.md#unique-indexes) , belirli bir alanın bir koleksiyondaki tüm belgeler genelinde yinelenen değerlere sahip olmamasını sağlar, bu da benzersizlik 'in varsayılan "_id" anahtarında korunma biçimine benzer. Kısıtlama parametresiyle komutu kullanarak Azure Cosmos DB benzersiz dizinler oluşturabilirsiniz `createIndex` `unique` :

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Bileşik dizinler

[Bileşik dizinler](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) , en fazla sekiz alan için alan grupları için dizin oluşturmanın bir yolunu sağlar. Bu tür bir dizin, yerel MongoDB bileşik dizinlerinden farklıdır. Azure Cosmos DB, bileşik dizinler birden çok alana uygulanan sıralama işlemleri için kullanılır. Bileşik dizin oluşturmak için, parametre olarak birden fazla özellik belirtmeniz gerekir:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
    "createdCollectionAutomatically" : false, 
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB, tüm GridFS uyumlu Mongo sürücüleri aracılığıyla GridFS 'yi destekler.

## <a name="replication"></a>Çoğaltma

Azure Cosmos DB, en düşük katmanlarda otomatik, yerel çoğaltmayı destekler. Bu mantık, düşük gecikme süresi ve küresel çoğaltma elde etmek için genişletilir. Cosmos DB el ile çoğaltma komutlarını desteklemez.

## <a name="retryable-writes"></a>Yeniden denenebilir yazmaları

Cosmos DB yeniden denenebilir yazmaları henüz desteklemiyor. İstemci sürücülerinin bağlantı dizesine ' Retryyazmaları = false ' URL parametresini eklemesi gerekir. Daha fazla URL parametresi, bir ' & ' ile önek ekleyerek eklenebilir. 

## <a name="sharding"></a>Parçalama

Azure Cosmos DB, otomatik, sunucu tarafı parçalamasını destekler. Parça oluşturma, yerleştirme ve dengelemeyi otomatik olarak yönetir. Azure Cosmos DB, el ile parçalı komutları desteklemez, bu, addShard, balancerStart, moveChunk gibi komutları çağırmak zorunda olmadığınız anlamına gelir. Yalnızca kapsayıcıları oluştururken veya verileri sorgularken parça anahtarını belirtmeniz gerekir.

## <a name="sessions"></a>Oturumlar

Azure Cosmos DB, sunucu tarafı oturumları komutlarını henüz desteklemiyor.

## <a name="time-to-live-ttl"></a>Etkin kalma süresi (TTL)

Azure Cosmos DB, belgenin zaman damgasına göre yaşam süresi (TTL) desteği sağlar. TTL, [Azure Portal](https://portal.azure.com)giderek koleksiyonlar için etkinleştirilebilir.

## <a name="transactions"></a>İşlemler

Çoklu belge işlemleri, parçalı olmayan bir koleksiyon içinde desteklenir. Çoklu belge işlemleri koleksiyonlar veya parçalı koleksiyonlar arasında desteklenmez. İşlemler için zaman aşımı sabit 5 saniyedir.

## <a name="user-and-role-management"></a>Kullanıcı ve rol yönetimi

Azure Cosmos DB henüz kullanıcıları ve rolleri desteklememektedir. Ancak, Azure rol tabanlı erişim denetimi (Azure RBAC) ve [Azure Portal](https://portal.azure.com) (bağlantı dizesi sayfası) aracılığıyla elde edilebilir salt okuma ve salt okuma parolalarını/anahtarlarını destekler Cosmos DB.

## <a name="write-concern"></a>Yazma Sorunu

Bazı uygulamalar, yazma işlemi sırasında gereken yanıt sayısını belirten bir [yazma kaygısını](https://docs.mongodb.com/manual/reference/write-concern/)kullanır. Cosmos DB’nin arka planda çoğaltma işleme şekli nedeniyle varsayılan olarak tüm yazma işlemleri otomatik olarak çekirdektir. İstemci kodu tarafından belirtilen herhangi bir yazma sorunu yok sayılır. Daha fazla bilgi için bkz. [Kullanılabilirlik ve performansı en üst düzeye çıkarmak için tutarlılık düzeylerini kullanma](consistency-levels.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
