---
title: Azure Cosmos DB 'de SQL sorgusu yürütme
description: Azure Cosmos DB 'de SQL sorgu yürütme hakkında bilgi edinin
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: c42732df1bcfa8649c89899febc364bb1f5f9b5a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999924"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>SQL sorgu yürütmeyi Azure Cosmos DB

HTTP/HTTPS istekleri yapabilen herhangi bir dil Cosmos DB REST API çağırabilir. Cosmos DB ayrıca .NET, Node. js, JavaScript ve Python programlama dilleri için programlama kitaplıkları sunar. REST API ve kitaplıkları, SQL aracılığıyla sorgulama desteği ve .NET SDK, [LINQ sorgulama](sql-query-linq-to-sql.md)'yı da destekler.

Aşağıdaki örneklerde bir sorgu oluşturma ve Cosmos veritabanı hesabına göre gönderme işlemlerinin nasıl yapılacağı gösterilmektedir.

## <a id="REST-API"></a>REST API

Cosmos DB, HTTP üzerinden açık bir RESTful programlama modeli sunar. Kaynak modeli, bir Azure aboneliğinin sağlamasını sağlayan bir veritabanı hesabı altındaki bir kaynak kümesinden oluşur. Veritabanı hesabı, her biri birden çok *kapsayıcı*içerebilen bir *veritabanı*kümesinden oluşur. Bu, *öğeleri*, UDF 'leri ve diğer kaynak türlerini içerir. Her Cosmos DB kaynak, mantıksal ve kararlı bir URI kullanılarak adreslenedir. Bir dizi kaynağa *akış*denir. 

Bu kaynaklarla temel etkileşim modeli,, `GET`, ve `DELETE`standart yorumlarıyla birlikte `PUT`http `POST`fiilleri üzerinden yapılır. Yeni `POST` bir kaynak oluşturmak, saklı yordamı yürütmek veya Cosmos DB bir sorgu vermek için kullanın. Sorgu her zaman salt okunur yan etkileri olan işlemlerdir.

Aşağıdaki örneklerde örnek öğeler için `POST` bir SQL API sorgusu gösterilmektedir. Sorgunun JSON `name` özelliğinde basit bir filtresi vardır. Ve Content-Type: `application/query+json` Headers işlemin bir sorgu olduğunu gösterir. `x-ms-documentdb-isquery` Cosmos DB `mysqlapicosmosdb.documents.azure.com:443` hesabınız için URI ile değiştirin.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Sonuçlar şunlardır:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Sonraki, daha karmaşık sorgu bir birleşimden birden çok sonuç döndürüyor:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Sonuçlar şunlardır: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Sorgunun sonuçları tek bir sayfaya sığamayacak olursa REST API `x-ms-continuation-token` yanıt üst bilgisi aracılığıyla bir devamlılık belirteci döndürür. İstemciler, üst bilgiyi izleyen sonuçlara dahil ederek sonuçları sayfadan alabilir. Ayrıca, `x-ms-max-item-count` sayı üst bilgisinden sayfa başına sonuç sayısını denetleyebilirsiniz.

Sorgunun COUNT gibi bir toplama işlevi varsa, sorgu sayfası yalnızca bir sonuç sayfası üzerinde kısmen toplanmış bir değer döndürebilir. İstemciler nihai sonuçları oluşturmak için bu sonuçlara ikinci düzey bir toplama işlemi gerçekleştirmelidir. Örneğin, toplam sayısı döndürmek için ayrı sayfalarda döndürülen sayların toplamı.

Sorgular için veri tutarlılığı ilkesini yönetmek üzere `x-ms-consistency-level` üst bilgisini tüm REST API isteklerinde olarak kullanın. Oturum tutarlılığı Ayrıca sorgu isteğindeki en son `x-ms-session-token` tanımlama bilgisi üst bilgisini yankılandırmasını gerektirir. Sorgulanan kapsayıcının dizin oluşturma ilkesini tutarlılığını sorgu sonuçlarını da etkileyebilir. Kapsayıcılar için varsayılan dizin oluşturma ilkesi ayarları ile, Dizin her zaman öğe içeriğiyle geçerli olur ve sorgu sonuçları, veriler için seçilen tutarlılık ile eşleşir. Daha fazla bilgi için bkz. [Azure Cosmos DB tutarlılık düzeyleri] [tutarlılık-düzeyler].

Kapsayıcıda yapılandırılan Dizin oluşturma ilkesi belirtilen sorguyu destekleyemiyorum, Azure Cosmos DB sunucusu 400 "bozuk Istek" döndürür. Bu hata iletisi, dizin oluşturma işleminden açıkça dışlanan yolların bulunduğu sorgular için döndürür. Bir dizin kullanılamadığında sorgunun `x-ms-documentdb-query-enable-scan` tarama yapmasına izin vermek için üst bilgiyi belirtebilirsiniz.

`x-ms-documentdb-populatequerymetrics` Üstbilgiyi olarak`true`ayarlayarak sorgu yürütme hakkında ayrıntılı ölçümler edinebilirsiniz. Daha fazla bilgi için [Azure Cosmos DB için SQL sorgu ölçümleri](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C#(.NET SDK)

LINQ hem SQL .NET SDK'yı destekleyen sorgulama. Aşağıdaki örnek, önceki filtre sorgusunun .NET ile nasıl gerçekleştirileceğini göstermektedir:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Aşağıdaki örnek, her öğe içinde eşitlik için iki özelliği karşılaştırır ve anonim tahminleri kullanır.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Sonraki örnekte, LINQ `SelectMany`aracılığıyla ifade edilen birleşimler gösterilmektedir.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET istemcisi, önceki örnekte gösterildiği gibi `foreach` bloklardaki sorgu sonuçlarının tüm sayfalarında otomatik olarak yinelenir. [REST API](#REST-API) bölümünde tanıtılan sorgu seçenekleri, .NET SDK 'da, `FeedOptions` `CreateDocumentQuery` yöntemindeki ve `FeedResponse` sınıfları kullanılarak da kullanılabilir. `MaxItemCount` Ayarını kullanarak sayfa sayısını kontrol edebilirsiniz.

Ayrıca, `IDocumentQueryable` `IQueryable` nesnesini kullanarak ve sonra `ResponseContinuationToken` değerleri okuyarak ve sonra yeniden `RequestContinuationToken` `FeedOptions`geçirerek, sayfalama denetimini açıkça denetleyebilirsiniz. Sorgu, yapılandırılmış `EnableScanInQuery` dizin oluşturma ilkesi tarafından desteklenmiyorsa taramayı etkinleştir olarak ayarlayabilirsiniz. Bölümlenmiş kapsayıcılar için, sorguyu tek bir `PartitionKey` bölüme karşı çalıştırmak için kullanabilirsiniz, ancak Azure Cosmos db sorgu metinden otomatik olarak ayıklayabilir. Birden çok bölüme karşı sorgu çalıştırmak için'ikullanabilirsiniz.`EnableCrossPartitionQuery`

Sorgularla daha fazla .NET örneği için bkz. GitHub 'da [.net örneklerine Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3) .

## <a id="JavaScript-server-side-API"></a>JavaScript sunucu tarafı API'si

Azure Cosmos DB, saklı yordamları ve Tetikleyicileri kullanarak doğrudan kapsayıcılarda [JavaScript tabanlı uygulama mantığını yürütmeye](stored-procedures-triggers-udfs.md) yönelik bir programlama modeli sağlar. Kapsayıcı düzeyinde kayıtlı JavaScript mantığı, daha sonra çevresel ACID işlemlerine kaydırılmış olan belirtilen kapsayıcının öğeleri üzerinde veritabanı işlemleri verebilir.

Aşağıdaki örnek, saklı yordamların ve tetikleyicilerin `queryDocuments` içinden sorgu yapmak için JavaScript sunucu API 'sinde nasıl kullanılacağını gösterir:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB giriş](introduction.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Tutarlılık düzeylerini Azure Cosmos DB](consistency-levels.md)
