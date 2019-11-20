---
title: MongoDB için Azure Cosmos DB API 'sinde dizin oluşturma
description: MongoDB için Azure Cosmos DB API 'SI ile dizin oluşturma özelliklerine genel bir bakış sunar.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753385"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sini kullanarak dizin oluşturma

MongoDB için Azure Cosmos DB API 'SI Cosmos DB otomatik dizin yönetimi yeteneklerini kullanır. Sonuç olarak, kullanıcıların Cosmos DB varsayılan dizin oluşturma ilkelerine erişimi vardır. Bu nedenle, Kullanıcı tarafından tanımlanmış bir dizin yoksa veya hiçbir dizin bırakılmazsa, bir koleksiyona yerleştirildiğinde tüm alanlar varsayılan olarak otomatik olarak dizine alınır. Çoğu senaryoda, hesap üzerinde ayarlanmış varsayılan dizinleme ilkesinin kullanılması önerilir.

## <a name="indexing-for-version-36"></a>Sürüm 3,6 için dizin oluşturma

Kablo Protokolü sürüm 3,6 ' a hizmet veren hesaplar, önceki sürümlerin sağladığı ilkeden farklı bir varsayılan dizin oluşturma ilkesi sağlar. Varsayılan olarak, yalnızca _ID alanı dizinlenir. Ek alanlara dizin eklemek için, kullanıcının MongoDB Dizin Yönetimi komutlarını uygulaması gerekir. Bir sorguya sıralama uygulamak için şu anda sıralama işleminde kullanılan alanlarda bir dizin oluşturulmalıdır.

### <a name="dropping-the-default-indexes-36"></a>Varsayılan dizinleri bırakma (3,6)

Kablo protokol 3,6 sürümüne hizmet veren hesaplar için, yalnızca varsayılan dizin, bırakılamaz.

### <a name="creating-a-compound-index-36"></a>Bileşik dizin oluşturma (3,6)

3,6 kablo protokolünü kullanan hesaplar için doğru Bileşik dizinler desteklenir. Aşağıdaki komut, ' a ' ve ' b ' alanlarında bir bileşik dizin oluşturacak: `db.coll.createIndex({a:1,b:1})`

Bileşik dizinler aynı anda birden çok alanda etkili bir şekilde sıralamak için kullanılabilir: `db.coll.find().sort({a:1,b:1})`

## <a name="indexing-for-version-32"></a>Sürüm 3,2 için dizin oluşturma

### <a name="dropping-the-default-indexes-32"></a>Varsayılan dizinleri bırakma (3,2)

Bir koleksiyon ```coll``` için varsayılan dizinleri bırakmak üzere aşağıdaki komut kullanılabilir:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Bileşik dizin oluşturma (3,2)

Bileşik dizinler bir belgenin birden çok alanına başvurular içerir. Mantıksal olarak, alan başına birden fazla tek dizin oluşturmaya eşdeğerdir. Cosmos DB dizinleme teknikleri tarafından sağlanan iyileştirmelerden yararlanmak için tek (benzersiz olmayan) bir bileşik dizin yerine birden fazla tek dizin oluşturulması önerilir.

## <a name="common-indexing-operations"></a>Ortak dizin oluşturma Işlemleri

Aşağıdaki işlemler, hat Protokolü sürüm 3,6 ' i sunan hesaplar ve eski hat Protokolü sürümlerine hizmet veren hesaplar için ortaktır. 

## <a name="creating-unique-indexes"></a>Benzersiz dizinler oluşturma

[Benzersiz dizinler](unique-keys.md), iki veya daha fazla belgeyi dizini oluşturulmuş alanlar için aynı değeri içermemeye zorlarken yararlıdır.

>[!Important]
> Şu anda benzersiz dizinler yalnızca koleksiyon boş olduğunda (hiçbir belge içermediğinde) oluşturulabilir.

Aşağıdaki komut, "student_id" alanında benzersiz bir dizin oluşturur:

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Parçalı koleksiyonlar için MongoDB davranışına göre benzersiz bir dizin oluşturmak, parça (bölüm) anahtarının sağlanmasını gerektirir. Diğer bir deyişle, parçalı bir koleksiyondaki tüm benzersiz dizinler, alanlardan birinin bölüm anahtarı olduğu bileşik dizinlerdir.

Aşağıdaki komutlar student_id ve university alanlarında benzersiz bir dizin ile ```coll``` parçalı koleksiyonu (parça anahtarı ```university```) oluşturur:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Yukarıdaki örnekte ```"university":1``` yan tümcesi çıkarılsaydı aşağıdaki iletiyle bir hata döndürülürdü:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL dizinleri

Belirli bir koleksiyonda belge geçerlilik süresini etkinleştirmek için bir ["TTL dizini" (yaşam süresi dizini)](../cosmos-db/time-to-live.md) oluşturulması gerekir. TTL dizini, _ts alanında "expireAfterSeconds" değerini içeren bir dizindir.

Örnek:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Yukarıdaki komut, ```db.coll``` koleksiyonunda son 10 saniye içinde değiştirilmemiş tüm belgelerin silinmesine neden olur.

> [!NOTE]
> **_ts**, Cosmos DB’ye özel bir alandır ve MongoDB istemcilerinden erişilemez. Belgenin son değiştirme tarihinin zaman damgasını içeren ayrılmış (sistem) bir özelliktir.

## <a name="migrating-collections-with-indexes"></a>Koleksiyonları dizinlerle geçirme

Şu anda benzersiz dizinler yalnızca koleksiyon hiçbir belge içermediğinde oluşturulabilir. Popüler MongoDB geçiş araçları, verileri içeri aktardıktan sonra benzersiz dizin oluşturmaya çalışır. Bu sorunu aşmak için, kullanıcıların geçiş aracına izin vermek yerine karşılık gelen koleksiyonları ve benzersiz dizinleri el ile oluşturması önerilir (```mongorestore``` için bu davranış, komut satırındaki `--noIndexRestore` bayrağı kullanılarak elde edilir).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB 'de dizin oluşturma](../cosmos-db/index-policy.md)
* [Azure Cosmos DB verileri yaşam süresi ile otomatik olarak sona erer](../cosmos-db/time-to-live.md)
