---
title: 1. x Işlevleri için Azure Cosmos DB bağlamaları
description: Azure Işlevleri 1. x içinde Azure Cosmos DB Tetikleyicileri ve bağlamaları nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002128"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Işlevleri 1. x için Azure Cosmos DB bağlamaları

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Işlevleri çalışma zamanının sürümünü seçin: "]
> * [Sürüm 1](functions-bindings-cosmosdb.md)
> * [Sürüm 2](functions-bindings-cosmosdb-v2.md)

Bu makalede, Azure Işlevlerinde [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. Azure Işlevleri Azure Cosmos DB için tetikleyici, giriş ve çıkış bağlamalarını destekler.

> [!NOTE]
> Bu makale, Azure Işlevleri 1. x içindir. Bu bağlamaları 2. x ve üzeri Işlevlerde kullanma hakkında daha fazla bilgi için bkz. [Azure işlevleri için Azure Cosmos DB bağlamaları 2. x](functions-bindings-cosmosdb-v2.md).
>
>Bu bağlamanın adı ilk olarak DocumentDB olarak adlandırılmıştır. Işlev sürümü 1. x içinde yalnızca tetikleyici yeniden adlandırıldı Cosmos DB; Giriş bağlama, çıkış bağlama ve NuGet paketi DocumentDB adını korur.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB bağlamaları yalnızca SQL API ile kullanım için desteklenir. Diğer tüm Azure Cosmos DB API 'Lerinde, [Azure Cosmos DB MongoDB](../cosmos-db/mongodb-introduction.md), [Cassandra API](../cosmos-db/cassandra-introduction.md), [gremlin API](../cosmos-db/graph-introduction.md)ve [tablo API'SI](../cosmos-db/table-introduction.md)API 'si de dahil olmak üzere API 'niz için statik istemciyi kullanarak işlevinizden veritabanına erişmeniz gerekir.

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

2. x Işlevleri için Azure Cosmos DB bağlamaları, sürüm 1. x [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet paketinde verilmiştir. Bağlamaların kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub deposunda bulunur.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Tetikleyici

Azure Cosmos DB tetikleyicisi, bölümler arasında eklemeleri ve güncelleştirmeleri dinlemek için [Azure Cosmos DB değişiklik akışını](../cosmos-db/change-feed.md) kullanır. Değişiklik akışı, silme işlemleri değil, eklemeleri ve güncelleştirmeleri yayımlar.

## <a name="trigger---example"></a>Tetikleyici-örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, belirtilen veritabanında ve koleksiyonda ekleme veya güncelleştirme olduğunda çağrılan bir [C# işlevini](functions-dotnet-class-library.md) göstermektedir.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* bir Cosmos DB tetikleyici bağlamasını ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi Cosmos DB kayıtları değiştirildiğinde günlük iletilerini yazar.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# betik kodu aşağıda verilmiştir:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* bir Cosmos DB tetikleyici bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi Cosmos DB kayıtları değiştirildiğinde günlük iletilerini yazar.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Tetikleyici-öznitelikler

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [Cosmosdbtrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, veritabanı adını ve koleksiyon adını alır. Yapılandırabileceğiniz bu ayarlar ve diğer özellikler hakkında daha fazla bilgi için bkz. [tetikleyici-yapılandırma](#trigger---configuration). `CosmosDBTrigger`Bir yöntem imzasında bir öznitelik örneği aşağıda verilmiştir:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Tam bir örnek için bkz. [Trigger-C# örneği](#trigger).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

---

## <a name="trigger---configuration"></a>Tetikleyici-yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `CosmosDBTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `cosmosDBTrigger` . |
|**Görünüm** | yok | Olarak ayarlanmalıdır `in` . Bu parametre, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | Değişiklik içeren belge listesini temsil eden işlev kodunda kullanılan değişken adı. |
|**connectionStringSetting**|**ConnectionStringSetting** | İzlenmekte olan Azure Cosmos DB hesabına bağlanmak için kullanılan bağlantı dizesini içeren uygulama ayarının adı. |
|**Dosyasında**|**Dosyasında**  | İzlenen koleksiyonun Azure Cosmos DB veritabanının adı. |
|**Ma** |**CollectionName** | İzlenen koleksiyonun adı. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Seçim Kira toplamayı tutan hizmete bağlantı dizesini içeren uygulama ayarının adı. Ayarlanmayan `connectionStringSetting` değer kullanılır. Bu parametre, bağlama portalda oluşturulduğunda otomatik olarak ayarlanır. Kiralamalar koleksiyonunun bağlantı dizesinde yazma izinleri olmalıdır.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Seçim Kiraları depolamak için kullanılan koleksiyonu tutan veritabanının adı. Ayarlamadığınızda, `databaseName` ayarın değeri kullanılır. Bu parametre, bağlama portalda oluşturulduğunda otomatik olarak ayarlanır. |
|**leaseCollectionName** | **LeaseCollectionName** | Seçim Kiralamaları depolamak için kullanılan koleksiyonun adı. Ayarlanmayan değer `leases` kullanılır. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Seçim Olarak ayarlandığında `true` , kiralamalar koleksiyonu zaten mevcut olmadığında otomatik olarak oluşturulur. `false` varsayılan değerdir. |
|**Leasescollectionüretilen Iş**| **Leasescollectionüretilen Iş**| Seçim Kiralamalar koleksiyonu oluşturulduğunda atanacak Istek birimi miktarını tanımlar. Bu ayar yalnızca, `createLeaseCollectionIfNotExists` olarak ayarlandığında kullanılır `true` . Bu parametre, bağlama Portal kullanılarak oluşturulduğunda otomatik olarak ayarlanır.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Seçim Ayarlandığında, bu Işlev için kira koleksiyonunda oluşturulan kiralara bir ön ek ekler ve iki ayrı Azure Işlevinin farklı ön ekleri kullanarak aynı Kiralama koleksiyonunu paylaşmasına olanak tanır.
|**feedPollDelay**| **FeedPollDelay**| Seçim Ayarlandığında, tüm geçerli değişiklikler boşaltılır sonra, bir bölümü, akıştaki yeni değişiklikler için yokladığında gecikme arasındaki gecikmeyi milisaniye cinsinden tanımlar. Varsayılan değer 5000 ' dir (5 saniye).
|**Leaseacquireınterval**| **Leaseacquireınterval**| Seçim Ayarlandığında, bölümler bilinen konak örnekleri arasında eşit olarak dağıtılırsa bir görevi başlatma aralığını milisaniye cinsinden tanımlar. Varsayılan değer 13000 ' dir (13 saniye).
|**Leaseexpirationınterval**| **Leaseexpirationınterval**| Seçim Ayarlandığında, kiralamanın bir bölümü temsil eden bir kira üzerinde alındığı aralığı milisaniye cinsinden tanımlar. Kira bu Aralık dahilinde yenilenmezse, süresinin dolmasına ve bölümün sahipliğinin başka bir örneğe taşınmasına neden olur. Varsayılan değer 60000 ' dir (60 saniye).
|**leaseRenewInterval**| **LeaseRenewInterval**| Seçim Ayarlandığında, bir örnek tarafından şu anda tutulan bölümlerin tüm kiralamaları için yenileme aralığını milisaniye cinsinden tanımlar. Varsayılan değer 17000 ' dir (17 saniye).
|**Checkpointflik**| **Checkpointflik**| Seçim Ayarlandığında, kira kontrol noktaları arasındaki aralığı milisaniye cinsinden tanımlar. Varsayılan değer her bir Işlev çağrısından sonra olur.
|**Maxıtemsperınvocation**| **Maxıtemsperınvocation**| Seçim Ayarlandığında, Işlev çağrısı başına alınan en fazla öğe miktarını özelleştirir.
|**Startfromstarted**| **Startfromstarted**| Seçim Ayarlandığında, tetikleyicisine, geçerli saat yerine koleksiyonun geçmişinden başlayarak değişiklikleri okumaya başlamasını söyler. Bu, sonraki çalışmalarda olduğu gibi, yalnızca tetikleyici başlatıldığında çalışır ve kontrol noktaları zaten depolanır. Zaten oluşturulan kiralamalar varsa bunu olarak ayarlamanın `true` hiçbir etkisi olmaz.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Tetikleyici-kullanım

Tetikleyici, bölümler üzerinde _kiraları_ depolamak için kullanacağı ikinci bir koleksiyon gerektirir. Tetikleyicinin çalışması için izlenmekte olan koleksiyonun ve kiralamaları içeren koleksiyonun kullanılabilir olması gerekir.

>[!IMPORTANT]
> Birden çok işlev aynı koleksiyon için bir Cosmos DB tetikleyicisi kullanacak şekilde yapılandırıldıysa, işlevlerin her biri adanmış bir kira koleksiyonu kullanmalı veya `LeaseCollectionPrefix` her bir işlev için farklı bir işlev belirtmelidir. Aksi takdirde, işlevlerden yalnızca biri tetiklenecektir. Ön ek hakkında daha fazla bilgi için [yapılandırma bölümüne](#trigger---configuration)bakın.

Tetikleyici bir belgenin güncelleştirildiğini veya eklendiğini göstermez, yalnızca belgenin kendisini sağlar. Güncelleştirmeleri işlemeniz ve farklı şekilde eklemeniz gerekiyorsa, bunu ekleme veya güncelleştirme için zaman damgası alanları uygulayarak yapabilirsiniz.

## <a name="input"></a>Giriş

Azure Cosmos DB giriş bağlaması SQL API'sini kullanarak bir veya daha fazla Azure Cosmos DB belgesini getirir ve işlevin giriş parametresine iletir. Belge kimliği veya sorgu parametreleri, işlevi çağıran tetikleyiciye göre belirlenebilir.

## <a name="input---example"></a>Giriş-örnek

# <a name="c"></a>[C#](#tab/csharp)

Bu bölüm aşağıdaki örnekleri içerir:

* [Kuyruk tetikleyicisi, JSON 'dan KIMLIK arama](#queue-trigger-look-up-id-from-json-c)
* [HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara](#http-trigger-look-up-id-from-query-string-c)
* [HTTP tetikleyicisi, rota verilerinden KIMLIK arama](#http-trigger-look-up-id-from-route-data-c)
* [HTTP tetikleyicisi, SqlQuery kullanarak rota verilerinden KIMLIK arama](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP tetikleyicisi, SqlQuery kullanarak birden çok belge al](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP tetikleyicisi, DocumentClient kullanarak birden çok belge edinme](#http-trigger-get-multiple-docs-using-documentclient-c)

Örnekler basit bir `ToDoItem` türe başvurur:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Kuyruk tetikleyicisi, JSON 'dan KIMLIK arama

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir. İşlev, JSON nesnesi içeren bir kuyruk iletisi tarafından tetiklenir. Sıra tetikleyicisi, JSON `ToDoItemLookup` öğesini, aranacak kimliği içeren adlı bir nesne olarak ayrıştırır. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir. İşlev, bir sorgu dizesi kullanarak aranacak kimliği belirten bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP tetikleyicisi, rota verilerinden KIMLIK arama

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir. İşlev, aranacak KIMLIĞI belirtmek için rota verilerini kullanan bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Giriş örneklerini atla](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP tetikleyicisi, SqlQuery kullanarak rota verilerinden KIMLIK arama

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir. İşlev, aranacak KIMLIĞI belirtmek için rota verilerini kullanan bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Giriş örneklerini atla](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP tetikleyicisi, SqlQuery kullanarak birden çok belge al

Aşağıdaki örnek, belge listesini alan bir [C# işlevini](functions-dotnet-class-library.md) gösterir. İşlev bir HTTP isteği tarafından tetiklenir. Sorgu `SqlQuery` öznitelik özelliğinde belirtilir.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Giriş örneklerini atla](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP tetikleyicisi, DocumentClient kullanarak birden çok belge edinme (C#)

Aşağıdaki örnek, belge listesini alan bir [C# işlevini](functions-dotnet-class-library.md) gösterir. İşlev bir HTTP isteği tarafından tetiklenir. Kod, `DocumentClient` bir belge listesini okumak için Azure Cosmos DB bağlama tarafından sunulan bir örneği kullanır. `DocumentClient`Örnek, yazma işlemleri için de kullanılabilir.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Bu bölüm aşağıdaki örnekleri içerir:

* [Kuyruk tetikleyicisi, dizeden KIMLIK arama](#queue-trigger-look-up-id-from-string-c-script)
* [Kuyruk tetikleyicisi, SqlQuery kullanarak birden çok belge alma](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP tetikleyicisi, rota verilerinden KIMLIK arama](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP tetikleyicisi, SqlQuery kullanarak birden çok belge al](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP tetikleyicisi, DocumentClient kullanarak birden çok belge edinme](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP tetikleyicisi örnekleri basit bir `ToDoItem` türe başvurur:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Kuyruk tetikleyicisi, dizeden KIMLIK arama

Aşağıdaki örnek, bir *function.js* dosyasında bir Cosmos DB girişi bağlamasını ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi tek bir belgeyi okur ve belgenin metin değerini güncelleştirir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Kuyruk tetikleyicisi, SqlQuery kullanarak birden çok belge alma

Aşağıdaki örnek, bir *function.js* dosyasında bir Azure Cosmos DB girişi bağlamasını ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi, sorgu parametrelerini özelleştirmek için bir kuyruk tetikleyicisi kullanarak bir SQL sorgusu tarafından belirtilen birden çok belgeyi alır.

Sıra tetikleyicisi bir parametre sağlar `departmentId` . Bir kuyruk iletisi `{ "departmentId" : "Finance" }` , finans departmanı için tüm kayıtları döndürür.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# betik işlevi](functions-reference-csharp.md) gösterilmektedir. İşlev, bir sorgu dizesi kullanarak aranacak kimliği belirten bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

C# betik kodu aşağıda verilmiştir:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP tetikleyicisi, rota verilerinden KIMLIK arama

Aşağıdaki örnekte, tek bir belgeyi alan bir [C# betik işlevi](functions-reference-csharp.md) gösterilmektedir. İşlev, aranacak KIMLIĞI belirtmek için rota verilerini kullanan bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

C# betik kodu aşağıda verilmiştir:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP tetikleyicisi, SqlQuery kullanarak birden çok belge al

Aşağıdaki örnekte, belge listesini alan bir [C# betik işlevi](functions-reference-csharp.md) gösterilmektedir. İşlev bir HTTP isteği tarafından tetiklenir. Sorgu `SqlQuery` öznitelik özelliğinde belirtilir.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# betik kodu aşağıda verilmiştir:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP tetikleyicisi, DocumentClient kullanarak birden çok belge edinme

Aşağıdaki örnekte, belge listesini alan bir [C# betik işlevi](functions-reference-csharp.md) gösterilmektedir. İşlev bir HTTP isteği tarafından tetiklenir. Kod, `DocumentClient` bir belge listesini okumak için Azure Cosmos DB bağlama tarafından sunulan bir örneği kullanır. `DocumentClient`Örnek, yazma işlemleri için de kullanılabilir.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# betik kodu aşağıda verilmiştir:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Bu bölüm aşağıdaki örnekleri içerir:

* [Kuyruk tetikleyicisi, JSON 'dan KIMLIK arama](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP tetikleyicisi, rota verilerinden KIMLIK arama](#http-trigger-look-up-id-from-route-data-javascript)
* [Kuyruk tetikleyicisi, SqlQuery kullanarak birden çok belge alma](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Kuyruk tetikleyicisi, JSON 'dan KIMLIK arama

Aşağıdaki örnek, bir *function.js* dosyasında bir Cosmos DB girişi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi tek bir belgeyi okur ve belgenin metin değerini güncelleştirir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP tetikleyicisi, sorgu dizesinden KIMLIĞI ara

Aşağıdaki örnekte, tek bir belgeyi alan bir [JavaScript işlevi](functions-reference-node.md) gösterilmektedir. İşlev, bir sorgu dizesi kullanarak aranacak kimliği belirten bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP tetikleyicisi, rota verilerinden KIMLIK arama

Aşağıdaki örnekte, tek bir belgeyi alan bir [JavaScript işlevi](functions-reference-node.md) gösterilmektedir. İşlev, bir sorgu dizesi kullanarak aranacak kimliği belirten bir HTTP isteği tarafından tetiklenir. Bu KIMLIK, `ToDoItem` belirtilen veritabanından ve koleksiyondan bir belgeyi almak için kullanılır.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Kuyruk tetikleyicisi, SqlQuery kullanarak birden çok belge alma

Aşağıdaki örnek, bir *function.js* dosyasında bir Azure Cosmos DB girişi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi, sorgu parametrelerini özelleştirmek için bir kuyruk tetikleyicisi kullanarak bir SQL sorgusu tarafından belirtilen birden çok belgeyi alır.

Sıra tetikleyicisi bir parametre sağlar `departmentId` . Bir kuyruk iletisi `{ "departmentId" : "Finance" }` , finans departmanı için tüm kayıtları döndürür.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

---

## <a name="input---attributes"></a>Giriş öznitelikleri

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, veritabanı adını ve koleksiyon adını alır. Yapılandırabileceğiniz bu ayarlar ve diğer özellikler hakkında daha fazla bilgi için [aşağıdaki yapılandırma bölümüne](#input---configuration)bakın.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

---

## <a name="input---configuration"></a>Giriş-yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `DocumentDB` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle**     | yok | Olarak ayarlanmalıdır `documentdb` .        |
|**Görünüm**     | yok | Olarak ayarlanmalıdır `in` .         |
|**ada**     | yok | İşlevdeki belgeyi temsil eden bağlama parametresinin adı.  |
|**Dosyasında** |**Dosyasında** |Belgeyi içeren veritabanı.        |
|**Ma** |**CollectionName** | Belgeyi içeren koleksiyonun adı. |
|**id**    | **Numarasını** | Alınacak belgenin KIMLIĞI. Bu özellik [bağlama ifadelerin](./functions-bindings-expressions-patterns.md)kullanılmasını destekler. Hem **ID** hem de **SQLQuery** özelliklerini ayarlama. Bunlardan birini ayarlamazsanız, tüm koleksiyon alınır. |
|**sqlQuery**  |**SqlQuery**  | Birden çok belge almak için kullanılan bir SQL sorgusu Azure Cosmos DB. Özelliği, şu örnekte olduğu gibi çalışma zamanı bağlamalarını destekler: `SELECT * FROM c where c.departmentId = {departmentId}` . Hem **ID** hem de **SQLQuery** özelliklerini ayarlama. Bunlardan birini ayarlamazsanız, tüm koleksiyon alınır.|
|**bağlanma**     |**ConnectionStringSetting**|Azure Cosmos DB Bağlantı dizenizi içeren uygulama ayarının adı.        |
|**partitionKey**|**PartitionKey**|Arama için bölüm anahtarı değerini belirtir. Bağlama parametreleri içerebilir.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Giriş kullanımı

# <a name="c"></a>[C#](#tab/csharp)

İşlev başarıyla çıktığında, giriş belgesinde adlandırılmış giriş parametreleri aracılığıyla yapılan tüm değişiklikler otomatik olarak kalıcı yapılır.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

İşlev başarıyla çıktığında, giriş belgesinde adlandırılmış giriş parametreleri aracılığıyla yapılan tüm değişiklikler otomatik olarak kalıcı yapılır.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

İşlev çıkışı sırasında güncelleştirmeler otomatik olarak yapılmaz. Bunun yerine, `context.bindings.<documentName>In` `context.bindings.<documentName>Out` güncelleştirmeleri yapmak için ve kullanın. [Giriş örneğine](#input)bakın.

---

## <a name="output"></a>Çıktı

Azure Cosmos DB çıkış bağlaması, SQL API 'sini kullanarak bir Azure Cosmos DB veritabanına yeni bir belge yazmanıza olanak sağlar.

## <a name="output---example"></a>Çıkış-örnek

# <a name="c"></a>[C#](#tab/csharp)

Bu bölüm aşağıdaki örnekleri içerir:

* Kuyruk tetikleyicisi, bir belge yaz
* Kuyruk tetikleyicisi, kullanarak belgeleri yazma `IAsyncCollector`

Örnekler basit bir `ToDoItem` türe başvurur:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Kuyruk tetikleyicisi, bir belge yaz

Aşağıdaki örnek, kuyruk depolamadan ileti içinde belirtilen verileri kullanarak bir veritabanına belge ekleyen bir [C# işlevini](functions-dotnet-class-library.md) gösterir.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Kuyruk tetikleyicisi, ıasynccollector kullanarak belge yazma

Aşağıdaki örnek, bir veritabanı JSON iletisinde belirtilen verileri kullanarak bir veritabanına bir belge koleksiyonu ekleyen bir [C# işlevini](functions-dotnet-class-library.md) gösterir.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Bu bölüm aşağıdaki örnekleri içerir:

* Kuyruk tetikleyicisi, bir belge yaz
* Kuyruk tetikleyicisi, kullanarak belgeleri yazma `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Kuyruk tetikleyicisi, bir belge yaz

Aşağıdaki örnek, bir *function.js* dosyasında bir Azure Cosmos db çıktı bağlamayı ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlevi, JSON 'yi aşağıdaki biçimde alan bir sıra için bir kuyruk girişi bağlaması kullanır:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

İşlevi her kayıt için aşağıdaki biçimde Azure Cosmos DB belgeler oluşturur:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Kuyruk tetikleyicisi, ıasynccollector kullanarak belge yazma

Birden çok belge oluşturmak için, `ICollector<T>` `IAsyncCollector<T>` `T` desteklenen türlerden biri olan veya ' ye bağlayabilirsiniz.

Bu örnek bir basit tür anlamına gelir `ToDoItem` :

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Dosyada function.js:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# betik kodu aşağıda verilmiştir:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyasında bir Azure Cosmos DB çıkış bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi, JSON 'yi aşağıdaki biçimde alan bir sıra için bir kuyruk girişi bağlaması kullanır:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

İşlevi her kayıt için aşağıdaki biçimde Azure Cosmos DB belgeler oluşturur:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Çıkış-öznitelikler

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, veritabanı adını ve koleksiyon adını alır. Yapılandırabileceğiniz bu ayarlar ve diğer özellikler hakkında daha fazla bilgi için bkz. [çıkış-yapılandırma](#output---configuration). `DocumentDB`Bir yöntem imzasında bir öznitelik örneği aşağıda verilmiştir:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Tüm bir örnek için bkz. [output](#output).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

---

## <a name="output---configuration"></a>Çıkış-yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `DocumentDB` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle**     | yok | Olarak ayarlanmalıdır `documentdb` .        |
|**Görünüm**     | yok | Olarak ayarlanmalıdır `out` .         |
|**ada**     | yok | İşlevdeki belgeyi temsil eden bağlama parametresinin adı.  |
|**Dosyasında** | **Dosyasında**|Belgenin oluşturulduğu koleksiyonu içeren veritabanı.     |
|**Ma** |**CollectionName**  | Belgenin oluşturulduğu koleksiyonun adı. |
|**Createıfnotexists çağırmanız**  |**Createıfnotexists çağırmanız**    | Koleksiyonun mevcut olmadığında oluşturulup oluşturulmayacağını belirten bir Boole değeri. Varsayılan değer *false* 'dur çünkü yeni koleksiyonlar, maliyet etkilerine sahip olan ayrılmış aktarım hızı ile oluşturulmuştur. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |`CreateIfNotExists`True olduğunda, oluşturulan koleksiyon için bölüm anahtarı yolunu tanımlar.|
|**Collectionverimlilik**|**Collectionverimlilik**| `CreateIfNotExists`Doğru olduğunda, oluşturulan koleksiyonun [verimini](../cosmos-db/set-throughput.md) tanımlar.|
|**bağlanma**    |**ConnectionStringSetting** |Azure Cosmos DB Bağlantı dizenizi içeren uygulama ayarının adı.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Çıkış kullanımı

Varsayılan olarak, işlevinizdeki çıkış parametresine yazdığınızda, veritabanınızda bir belge oluşturulur. Bu belgede, belge KIMLIĞI olarak otomatik olarak oluşturulan bir GUID bulunur. Çıktı `id` parametresine GEÇIRILEN JSON nesnesindeki özelliği belirterek çıktı belgesinin belge kimliğini belirtebilirsiniz.

> [!Note]
> Mevcut bir belgenin KIMLIĞINI belirttiğinizde, yeni çıktı belgesiyle üzerine yazılır.

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama | Başvuru |
|---|---|
| Cosmos DB | [CosmosDB hata kodları](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Sonraki adımlar

* [Cosmos DB ile sunucusuz veritabanı hesaplama hakkında daha fazla bilgi edinin](../cosmos-db/serverless-computing-database.md)
* [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
