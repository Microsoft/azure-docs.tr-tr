---
title: MongoDB için Azure Cosmos DB API’sinde dizin oluşturmayı yönetme
description: Bu makalede, MongoDB için Azure Cosmos DB API 'SI kullanılarak Azure Cosmos DB Dizin oluşturma özelliklerine genel bir bakış sunulmaktadır
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/02/2021
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 8d19a5dadffdfa26ccb2d84e6dab278ad272c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658055"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API’sinde dizin oluşturmayı yönetme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB için Azure Cosmos DB API 'SI, Azure Cosmos DB temel dizin yönetimi olanaklarından yararlanır. Bu makalede, MongoDB için Azure Cosmos DB API 'sini kullanarak dizin ekleme konusu ele alınmaktadır. Ayrıca, tüm API 'lerde ilgili olan [Azure Cosmos DB Dizin oluşturma hakkında genel bir bakış](index-overview.md) da okuyabilirsiniz.

## <a name="indexing-for-mongodb-server-version-36-and-higher"></a>MongoDB sunucusu sürüm 3,6 ve üzeri için dizin oluşturma

Azure Cosmos DB MongoDB Server sürümü 3.6 + için API 'SI, alanı otomatik olarak dizine `_id` alarak bırakılamaz. Bu, parça anahtarı başına alanın benzersizlik düzeyini otomatik olarak zorlar `_id` . MongoDB için Azure Cosmos DB API 'sinde, parçalama ve dizin oluşturma ayrı kavramlardır. Parça anahtarınızı dizinlemek zorunda değilsiniz. Bununla birlikte, belgenizdeki diğer tüm özellikler gibi, bu özellik sorgularda ortak bir filtre ise parça anahtarının dizinlenmesini öneririz.

Ek alanları dizinlemek için MongoDB dizin yönetimi komutlarını uygularsınız. MongoDB 'de olduğu gibi, MongoDB için Azure Cosmos DB API 'SI yalnızca alanı otomatik olarak dizine ekler `_id` . Bu varsayılan dizinleme ilkesi, tüm alanları varsayılan olarak dizinleyen Azure Cosmos DB SQL API’sinden farklıdır.

Bir sorguya sıralama uygulamak için sıralama işleminde kullanılan alanlarda bir dizin oluşturmanız gerekir.

### <a name="editing-indexing-policy"></a>Dizin oluşturma ilkesini Düzenle

Azure portal içindeki Veri Gezgini dizin oluşturma ilkenizi düzenlemenizi öneririz.
. Veri Gezgini dizin oluşturma ilkesi düzenleyicisinden tek bir alan ve joker dizinler ekleyebilirsiniz:

:::image type="content" source="./media/mongodb-indexing/indexing-policy-editor.png" alt-text="Dizin oluşturma ilkesi Düzenleyicisi":::

> [!NOTE]
> Veri Gezgini dizin oluşturma ilkesi düzenleyicisini kullanarak Bileşik dizinler oluşturamazsınız.

## <a name="index-types"></a>Dizin türleri

### <a name="single-field"></a>Tek alan

Tek bir alanda dizinler oluşturabilirsiniz. Tek alan dizininin sıralama düzeni önemi değildir. Aşağıdaki komut, alanında bir dizin oluşturur `name` :

`db.coll.createIndex({name:1})`

Azure portal ' de aynı tek alan dizinini oluşturabilirsiniz `name` :

:::image type="content" source="./media/mongodb-indexing/add-index.png" alt-text="Dizin oluşturma ilkesi Düzenleyicisi 'nde ad dizini ekleme":::

Bir sorgu, kullanılabilir yerlerde birden çok tek alan dizini kullanır. Kapsayıcı başına en çok 500 tek alan dizini oluşturabilirsiniz.

### <a name="compound-indexes-mongodb-server-version-36"></a>Bileşik dizinler (MongoDB Server sürümü 3.6 +)

Azure Cosmos DB, MongoDB için API, sürüm 3,6 ve 4,0 kablo protokolünü kullanan hesaplara yönelik bileşik dizinleri destekler. Bileşik dizine en fazla sekiz alan ekleyebilirsiniz. MongoDB 'nin aksine, yalnızca sorgunuzun aynı anda birden çok alanda etkili bir şekilde sıralanması gerekiyorsa bileşik bir dizin oluşturmanız gerekir. Sıralama gerektirmeyen birden çok filtreye sahip sorgularda tek bir bileşik dizin yerine birden çok tek alan dizini oluşturun. 

> [!NOTE]
> İç içe özellikler veya diziler üzerinde Bileşik dizinler oluşturamazsınız.

Aşağıdaki komut, alanlarda bir bileşik dizin oluşturur `name` ve `age` :

`db.coll.createIndex({name:1,age:1})`

Aşağıdaki örnekte gösterildiği gibi, aynı anda birden çok alanda etkili bir şekilde sıralamak için bileşik dizinleri kullanabilirsiniz:

`db.coll.find().sort({name:1,age:1})`

Yukarıdaki bileşik dizini, tüm alanlarda ters sıralama düzeni ile bir sorguyu etkin bir şekilde sıralamak için de kullanabilirsiniz. Aşağıda bir örnek verilmiştir:

`db.coll.find().sort({name:-1,age:-1})`

Ancak, Birleşik dizindeki yolların sırası sorguyla tam olarak eşleşmelidir. Ek bir bileşik dizin gerektirecek bir sorgu örneği aşağıda verilmiştir:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Bileşik dizinler yalnızca sonuçları sıralayan sorgularda kullanılır. Sıralanması gerekmeyen birden çok filtresi olan sorgularda, tek alan dizinleri oluşturun.

### <a name="multikey-indexes"></a>Çok tuşlu dizinler

Azure Cosmos DB diziler içinde depolanan içeriğe dizin eklemek için çok tuşlu dizinler oluşturur. Bir alanı bir dizi değeriyle dizinleyiniz, Azure Cosmos DB dizideki her öğeyi otomatik olarak dizine ekler.

### <a name="geospatial-indexes"></a>Jeo-uzamsal dizinler

Birçok Jeo-uzamsal işleç, Jeo-uzamsal dizinlerden faydalanır. Şu anda, MongoDB için Azure Cosmos DB API 'leri destekler `2dsphere` . API henüz `2d` dizinleri desteklemez.

Alanda Jeo-uzamsal dizin oluşturma örneği aşağıda verilmiştir `location` :

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Metin dizinleri

MongoDB için Azure Cosmos DB API 'SI Şu anda metin dizinlerini desteklememektedir. Dizelerde metin arama sorguları için, Azure Cosmos DB ile [Azure bilişsel arama](../search/search-howto-index-cosmosdb.md) tümleştirmesini kullanmanız gerekir. 

## <a name="wildcard-indexes"></a>Joker karakter dizinleri

Bilinmeyen alanlara karşı sorguları desteklemek için joker dizinleri kullanabilirsiniz. Aileler hakkında verileri tutan bir koleksiyonunuz olduğunu düşünelim.

Bu koleksiyondaki örnek bir belgenin parçası aşağıda verilmiştir:

```json
"children": [
   {
     "firstName": "Henriette Thaulow",
     "grade": "5"
   }
]
```

İşte, bu kez daha az farklı bir özellik kümesine sahip bir örnek `children` :

```json
"children": [
    {
     "familyName": "Merriam",
     "givenName": "Jesse",
     "pets": [
         { "givenName": "Goofy" },
         { "givenName": "Shadow" }
         ]
   },
   {
     "familyName": "Merriam",
     "givenName": "John",
   }
]
```

Bu koleksiyonda, belgeler birçok farklı özelliğe sahip olabilir. Dizideki tüm verilerin dizinini `children` oluşturmak istiyorsanız iki seçeneğiniz vardır: her bir özellik için ayrı dizinler oluşturun veya tüm dizi için tek bir joker dizin oluşturun `children` .

### <a name="create-a-wildcard-index"></a>Joker karakter oluşturma

Aşağıdaki komut, içindeki herhangi bir özelliklerde bir joker dizin oluşturur `children` :

`db.coll.createIndex({"children.$**" : 1})`

**MongoDB 'Nin aksine, joker karakter dizinleri sorgu koşullarına göre birden çok alanı destekleyebilir**. Her özellik için ayrı bir dizin oluşturmak yerine bir tek joker karakter dizini kullanırsanız sorgu performansında bir farklılık olmayacaktır.

Joker karakter sözdizimini kullanarak aşağıdaki dizin türlerini oluşturabilirsiniz:

* Tek alan
* Jeo-uzamsal

### <a name="indexing-all-properties"></a>Tüm özellikleri dizine alma

Tüm alanlarda joker karakter oluşturmak için aşağıdaki adımları uygulayın:

`db.coll.createIndex( { "$**" : 1 } )`

Ayrıca, Azure portal Veri Gezgini kullanarak joker dizinler oluşturabilirsiniz:

:::image type="content" source="./media/mongodb-indexing/add-wildcard-index.png" alt-text="Dizin oluşturma ilkesi Düzenleyicisi 'nde joker karakter dizini ekleme":::

> [!NOTE]
> Geliştirmeye yeni başladıysanız, tüm alanlarda bir joker karakter dizini ile başlamasını **kesinlikle** öneririz. Bu, geliştirmeyi kolaylaştırabilir ve sorguları iyileştirmeyi kolaylaştırabilir.

Birçok alan içeren belgeler, yazma ve güncelleştirme işlemleri için yüksek bir Istek birimi (RU) ücretine sahip olabilir. Bu nedenle, yazma ağır bir iş yükünüz varsa, joker dizinleri kullanmanın aksine yolları tek tek dizine almalısınız.

### <a name="limitations"></a>Sınırlamalar

Joker dizinler aşağıdaki dizin türlerinden veya özelliklerden hiçbirini desteklemez:

* Bileşik
* TTL
* Benzersiz

**MongoDB 'Nin aksine**, mongodb için Azure Cosmos DB API 'sindeki farklı Joker **dizinleri kullanamazsınız:**

* Belirli birden çok alanın dahil edildiği bir joker dizin oluşturma

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection " :
          {
             "children.givenName" : 1,
             "children.grade" : 1
          }
      }
  )
  ```

* Belirli birden çok alanın hariç tutulduğu bir joker dizin oluşturma

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection" :
          {
             "children.givenName" : 0,
             "children.grade" : 0
          }
      }
  )
  ```

Alternatif olarak birden çok joker dizin oluşturabilirsiniz.

## <a name="index-properties"></a>Dizin Özellikleri

Aşağıdaki işlemler, hat Protokolü sürüm 4,0 ' a hizmet veren hesaplar ve önceki sürümlere hizmet veren hesaplar için ortaktır. [Desteklenen dizinler ve dizinli Özellikler](mongodb-feature-support-40.md#indexes-and-index-properties)hakkında daha fazla bilgi edinebilirsiniz.

### <a name="unique-indexes"></a>Benzersiz dizinler

[Benzersiz dizinler](unique-keys.md) , iki veya daha fazla belgenin dizinli alanlar için aynı değeri içermediğini zorlarken faydalıdır.

> [!IMPORTANT]
> Benzersiz dizinler yalnızca koleksiyon boş olduğunda (belge içermiyorsa) oluşturulabilir.

Aşağıdaki komut, alanında benzersiz bir dizin oluşturur `student_id` :

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

Parçalı koleksiyonlar için, benzersiz bir dizin oluşturmak üzere parça (bölüm) anahtarını sağlamanız gerekir. Diğer bir deyişle, parçalı bir koleksiyondaki tüm benzersiz dizinler, alanlardan birinin bölüm anahtarı olduğu bileşik dizinlerdir.

Aşağıdaki komutlar, ```coll``` alanlar üzerinde benzersiz bir dizin içeren parçalı bir koleksiyon (parça anahtarı ```university``` ) oluşturur `student_id` `university` :

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 3,
    "numIndexesAfter" : 4
}
```

Önceki örnekte, ```"university":1``` yan tümcesini atlayarak aşağıdaki iletiyle bir hata döndürülür:

*{University: 1,0} parça anahtar düzeniyle {student_id: 1,0} üzerinde benzersiz dizin oluşturulamıyor*

### <a name="ttl-indexes"></a>TTL dizinleri

Belirli bir koleksiyonda belge kullanım süresini etkinleştirmek için [yaşam süresi (TTL) dizini](../cosmos-db/time-to-live.md)oluşturmanız gerekir. TTL dizini, bir `_ts` değeri olan alan üzerindeki bir dizindir `expireAfterSeconds` .

Örnek:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Yukarıdaki komut, ```db.coll``` koleksiyonda son 10 saniye içinde değiştirilmeyen tüm belgeleri siler.

> [!NOTE]
> **_Ts** alanı Azure Cosmos DB özeldir ve MongoDB istemcilerinden erişilemez. Belgenin son değiştirildiği zaman damgasını içeren bir ayrılmış (sistem) özelliğidir.

## <a name="track-index-progress"></a>Dizin ilerlemesini izleme

MongoDB için Azure Cosmos DB 3.6 + sürümü, `currentOp()` veritabanı örneğindeki dizin ilerlemesini izleme komutunu destekler. Bu komut, bir veritabanı örneği üzerinde devam eden işlemler hakkında bilgi içeren bir belge döndürür. `currentOp`Yerel MongoDB 'de tüm devam eden işlemleri izlemek için komutunu kullanın. MongoDB için Azure Cosmos DB API 'sinde bu komut yalnızca dizin işleminin izlenmesini destekler.

İşte `currentOp` Dizin ilerlemesini izlemek için komutunun nasıl kullanılacağını gösteren bazı örnekler:

* Bir koleksiyon için Dizin ilerlemesini al:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Bir veritabanındaki tüm koleksiyonlar için Dizin ilerlemesini al:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Azure Cosmos hesabındaki tüm veritabanları ve koleksiyonlar için Dizin ilerlemesini alın:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Dizin ilerleme çıkışı örnekleri

Dizin ilerleme durumu ayrıntıları geçerli dizin işleminin ilerleme yüzdesini gösterir. Dizin ilerleme durumunun farklı aşamaları için çıkış belgesi biçimini gösteren bir örnek aşağıda verilmiştir:

* "Foo" koleksiyonundaki bir dizin işlemi ve yüzde 60 olan "çubuk" veritabanı aşağıdaki çıktı belgesine sahip olacaktır. `Inprog[0].progress.total`Alan, hedef tamamlanma yüzdesi olarak 100 gösterir.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

* Bir dizin işlemi bir "foo" koleksiyonu ve "çubuk" veritabanı üzerinde yeni başlamışsa, çıkış belgesi ölçülebilir bir düzeye ulaşıncaya kadar yüzde 0 ilerleme durumu gösterebilir.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

* Devam eden dizin işlemi tamamlandığında, çıktı belgesinde boş `inprog` işlemler görüntülenir.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Arka plan dizini güncelleştirmeleri

**Arka** plan dizini özelliği için belirtilen değerden bağımsız olarak, dizin güncelleştirmeleri her zaman arka planda yapılır. Dizin güncelleştirmeleri, diğer veritabanı işlemlerinden daha düşük bir öncelikte Istek birimlerini (ru) tükettiğinden, dizin değişiklikleri yazma, güncelleştirme veya silme işlemlerine hiçbir kesinti oluşmasına neden olmaz.

Yeni Dizin eklenirken kullanılabilirliği okuma etkisi yoktur. Sorgular, Dizin dönüştürme işlemi tamamlandıktan sonra yalnızca yeni dizinleri kullanacaktır. Dizin dönüştürmesi sırasında, sorgu altyapısı var olan dizinleri kullanmaya devam eder, bu nedenle dizinleme değişikliğini başlatmadan önce gözlemlediğiniz şekilde, dizin oluşturma dönüştürmesi sırasında benzer okuma performansını gözlemleyeceksiniz. Yeni dizinler eklenirken tamamlanmamış veya tutarsız sorgu sonuçlarının de riski yoktur.

Dizinler kaldırılırken ve hemen çalışan sorgular, bırakılan dizinlerde filtreleri varsa, Dizin dönüştürmesi bitene kadar sonuçlar tutarsız ve tamamlanmamış olabilir. Dizinleri kaldırırsanız sorgu altyapısı, bu yeni kaldırılan dizinlerde sorgular filtreladığında tutarlı veya tamamlanmamış sonuçlar sağlamaz. Çoğu geliştirici dizinleri bırakamaz ve sonra bu durum, uygulamada, bu durumun olası bir durumdur.

> [!NOTE]
> [Dizin ilerlemesini izleyebilirsiniz](#track-index-progress).

## <a name="reindex-command"></a>Yeniden dizinle komutu

`reIndex`Komut, bir koleksiyondaki tüm dizinleri yeniden oluşturacak. Çoğu durumda bu gerekli değildir. Ancak, bazı nadir durumlarda, komutu çalıştırdıktan sonra sorgu performansı iyileştirebilecek `reIndex` .

`reIndex`Aşağıdaki sözdizimini kullanarak komutu çalıştırabilirsiniz:

`db.runCommand({ reIndex: <collection> })`

Komutun çalıştırılması gerektiğini denetlemek için aşağıdaki sözdizimini kullanabilirsiniz `reIndex` :

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

Örnek çıktı:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

`reIndex`Gerekirse, **requiresreındex** doğru olacaktır. `reIndex`Gerekli değilse, bu özellik atlanır.

## <a name="migrate-collections-with-indexes"></a>Koleksiyonları dizinlerle geçirme

Şu anda, koleksiyon belge içermiyorsa yalnızca benzersiz dizinler oluşturabilirsiniz. Popüler MongoDB geçiş araçları, verileri içeri aktardıktan sonra benzersiz dizinler oluşturmaya çalışır. Bu sorunu aşmak için, geçiş aracının denemeye izin vermek yerine, ilgili koleksiyonları ve benzersiz dizinleri el ile oluşturabilirsiniz. ( ```mongorestore``` Komut satırındaki bayrağını kullanarak için bu davranışı elde edebilirsiniz `--noIndexRestore` .)

## <a name="indexing-for-mongodb-version-32"></a>MongoDB sürüm 3,2 için dizin oluşturma

Kullanılabilir dizin oluşturma özellikleri ve varsayılanlar, MongoDB kablo protokolünün 3,2 sürümü ile uyumlu olan Azure Cosmos hesapları için farklıdır. [Hesabınızın sürümünü denetleyebilir](mongodb-feature-support-36.md#protocol-support) ve [sürüm 3,6 ' e yükseltebilirsiniz](mongodb-version-upgrade.md).

Sürüm 3,2 kullanıyorsanız, bu bölümde 3.6 + sürümleriyle ilgili önemli farklılıklar özetlenmektedir.

### <a name="dropping-default-indexes-version-32"></a>Varsayılan dizinler bırakılıyor (sürüm 3,2)

MongoDB için Azure Cosmos DB API 'sinin 3.6 + sürümlerinden farklı olarak sürüm 3,2, varsayılan olarak her özelliği dizinler. Bir koleksiyon () için bu varsayılan dizinleri bırakmak üzere aşağıdaki komutu kullanabilirsiniz ```coll``` :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Varsayılan dizinleri bırakdıktan sonra, 3.6 + ' da olduğu gibi daha fazla dizin ekleyebilirsiniz.

### <a name="compound-indexes-version-32"></a>Bileşik dizinler (sürüm 3,2)

Bileşik dizinler bir belgenin birden çok alanına başvurular içerir. Bileşik dizin oluşturmak istiyorsanız, [3,6 veya 4,0 sürümüne yükseltin](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Joker karakter dizinleri (sürüm 3,2)

Joker karakter dizini oluşturmak istiyorsanız, [4,0 veya 3,6 sürümüne yükseltin](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB’de dizin oluşturma](../cosmos-db/index-policy.md)
* [Azure Cosmos DB verileri yaşam süresi ile otomatik olarak sona erer](../cosmos-db/time-to-live.md)
* Bölümlendirme ve dizin oluşturma arasındaki ilişki hakkında bilgi edinmek için bkz. [Azure Cosmos kapsayıcısını sorgulama](how-to-query-container.md) .
