---
title: Azure Cosmos DB 'da MongoDB belge başına TTL özelliği
description: Bir süre sonra sistemden otomatik olarak temizlemek üzere MongoDB için Azure Cosmos DB API 'sini kullanarak belgeler için yaşam süresi ayarlamayı öğrenin.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: javascript
ms.topic: how-to
ms.date: 12/26/2018
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f044e03fa3f8aece677e3918b2add8190041c7ac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589525"
---
# <a name="expire-data-with-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'siyle verilerde süre sonu
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Yaşam süresi (TTL) işlevi, veritabanının verilerin süresini otomatik olarak sonlandırmasını sağlar. MongoDB için Azure Cosmos DB API 'SI, Cosmos DB 'ın çekirdek TTL özelliklerini kullanır. İki mod desteklenir: koleksiyonun tamamı için varsayılan TTL değeri ayarlama ve her belge için ayrı bir TTL değeri ayarlama. Cosmos DB MongoDB için API 'sindeki TTL dizinlerini ve belge başına TTL değerlerini yöneten mantık, [Cosmos DB ile aynıdır](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>TTL dizinleri
TTL'yi bir koleksiyonun tamamında etkinleştirmek için bir ["TTL dizini" (yaşam süresi dizini)](../cosmos-db/mongodb-indexing.md) oluşturulması gerekir. TTL dizini, _ts alanında "expireAfterSeconds" değerini içeren bir dizindir.

Örnek:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Yukarıdaki örnekte verilen komut, TTL işlevine sahip bir dizin oluşturur. Dizin oluşturulduktan sonra veritabanı koleksiyon içinde bulunan ve son 10 saniyede değiştirilmemiş olan belgeleri otomatik olarak siler. 

> [!NOTE]
> **_ts**, Cosmos DB’ye özel bir alandır ve MongoDB istemcilerinden erişilemez. Belgenin son değiştirme tarihinin zaman damgasını içeren ayrılmış (sistem) bir özelliktir.
>
    
C# örneği de aşağıda belirtilmiştir: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Bir belge için yaşam süresi değeri belirleme 
Belge başına TTL değerleri de desteklenir. Belgelerde "ttl" (küçük harfle) kök düzey özellik olması ve bulunduğu koleksiyon için yukarıda anlatılan TTL dizini oluşturulmalıdır. Bir belge için ayarlanan TTL değerleri, koleksiyonunun TTL değerini geçersiz kılacaktır.

TTL değeri bir int32 olmalıdır. Alternatif olarak int32 aralığına sığan bir int64 değeri veya int32 aralığına sığan ve ondalık basamağı olmayan bir çift değer de kullanılabilir. Bu kurallara uymayan TTL özelliklerine izin verilir ancak bu değerler anlamlı bir belge TTL değeri olarak işlenmez.

Belge için TTL değeri isteğe bağlıdır; TTL değeri bulunmayan belgeler de aynı koleksiyona eklenebilir.  Bu durumda koleksiyonun TTL değeri kullanılacaktır. 

Aşağıdaki belgeler geçerli TTL değerlerine sahiptir. Belgeler eklendikten sonra, belge TTL değerleri koleksiyonun TTL değerlerini geçersiz kılar. Bu nedenle belgeler 20 saniye sonra kaldırılır.   

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

Aşağıdaki belgeler geçersiz TTL değerlerine sahiptir. Bu belgeler eklenir ancak belgenin TTL değeri kullanılmaz. Bu nedenle belgeler koleksiyonun TTL değeri olan 10 saniye sonra kaldırılır. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Cosmos DB verileri yaşam süresi ile otomatik olarak sona erer](../cosmos-db/time-to-live.md)
* [Cosmos veritabanınızı dizin oluşturma Azure Cosmos DB MongoDB için API 'SI ile yapılandırılmış](../cosmos-db/mongodb-indexing.md)
