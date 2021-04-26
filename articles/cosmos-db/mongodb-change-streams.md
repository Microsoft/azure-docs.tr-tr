---
title: MongoDB için Azure Cosmos DB API 'sindeki akışları değiştirme
description: Verilerinize yapılan değişiklikleri almak için MongoDB için akış değiştirme n Azure Cosmos DB API 'sini nasıl kullanacağınızı öğrenin.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 941fe8929b75fdebf187186ca7078b0ae1dd261c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658534"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sindeki akışları değiştirme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB, MongoDB için API 'sinde bulunan [akış](change-feed.md) desteğini değiştirme, akışları değiştirme API 'si kullanılarak kullanılabilir. Uygulamalarınız, değişiklik akışları API 'sini kullanarak koleksiyonda veya tek bir parçadaki öğelerde yapılan değişiklikleri alabilir. Daha sonra sonuçlara göre daha fazla işlem yapabilirsiniz. Koleksiyondaki öğelerde yapılan değişiklikler, değişiklik zamanının sırasına göre yakalanır ve sıralama düzeni parça anahtarı başına garanti edilir.

> [!NOTE]
> Değişiklik akışlarını kullanmak için, sunucu sürümü 3,6 veya üzeri olan MongoDB hesabı için Azure Cosmos DB API 'sini oluşturun. Değişiklik akışı örneklerini önceki bir sürüme karşı çalıştırırsanız, *tanınmayan işlem hattı aşama adı* ' nı görebilirsiniz: $changeStream hata.

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, koleksiyondaki tüm öğelerde değişiklik akışlarının nasıl alınacağını gösterir. Bu örnek, öğeleri yerleştirildiğinde, güncelleştirilirken veya değiştirildiğinde izlemek için bir imleç oluşturur. `$match` `$project` `fullDocument` Değişiklik akışlarını almak için aşama, aşama ve seçenek gereklidir. Değişiklik akışlarını kullanarak silme işlemleri için izleme şu anda desteklenmiyor. Geçici bir çözüm olarak, silinmekte olan öğelere bir yumuşak işaret ekleyebilirsiniz. Örneğin, "Deleted" adlı öğeye bir öznitelik ekleyebilirsiniz. Öğeyi silmek istediğinizde, ' Deleted ' olarak ayarlayabilir `true` ve öğe üzerinde BIR TTL ayarlayabilirsiniz. "Deleted" `true` güncelleştirmesi bir güncelleştirme olduğundan, bu değişiklik akış değişikliğini görünür hale görünecektir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnek, tüm örnek için bkz. Java 'da akış işlevinin nasıl kullanılacağını gösterir, bu [GitHub deposu](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java)'na bakın. Bu örnek ayrıca, `resumeAfter` son okunan tüm değişiklikleri aramak için yönteminin nasıl kullanılacağını gösterir. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Tek parça içindeki değişiklikler

Aşağıdaki örnek, tek bir parça içindeki öğelerde yapılan değişikliklerin nasıl alınacağını gösterir. Bu örnek, "a" ve parça anahtar değeri "1" değerine eşit olan öğelerin konumunu alır. Farklı parçalardan paralel olarak değişiklik okuyan farklı istemcilerin olması mümkündür.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Geçerli sınırlamalar

Değişiklik akışları kullanılırken aşağıdaki sınırlamalar geçerlidir:

* `operationType`Ve `updateDescription` özellikleri çıkış belgesinde henüz desteklenmiyor.
* `insert`, `update` Ve `replace` işlemler türleri şu anda destekleniyor. Ancak, silme işlemi veya diğer olaylar henüz desteklenmemektedir.

Bu sınırlamalar nedeniyle, önceki örneklerde gösterildiği gibi $match Stage, $project Stage ve fullDocument seçenekleri gereklidir.

Azure Cosmos DB SQL API 'sindeki değişiklik akışının aksine, değişiklik akışlarını kullanmak için ayrı bir [değişiklik akışı Işlemci kitaplığı](change-feed-processor.md) veya bir kiralama kapsayıcısı gereksinimi yoktur. Şu anda değişiklik akışlarını işlemek için [Azure işlevleri tetikleyicilerinin](change-feed-functions.md) desteği yoktur.

## <a name="error-handling"></a>Hata işleme

Değişiklik akışları kullanılırken aşağıdaki hata kodları ve iletileri desteklenir:

* **Http hata kodu 16500** -değişiklik akışı kısıtlandığından boş bir sayfa döndürülür.

* **NamespaceNotFound (OperationType geçersiz kıl)** -değişiklik akışını varolmayan koleksiyonda çalıştırırsanız veya koleksiyon bırakıldıysanız bir `NamespaceNotFound` hata döndürülür. Özelliği, `operationType` Çıkış belgesinde hata yerine döndürülmediğinden `operationType Invalidate` `NamespaceNotFound` hata döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

* [MongoDB için Azure Cosmos DB API 'sindeki verilerin süresi dolduğunda otomatik olarak yaşam süresi kullanma](mongodb-time-to-live.md)
* [MongoDB için Azure Cosmos DB API 'sinde dizin oluşturma](mongodb-indexing.md)
