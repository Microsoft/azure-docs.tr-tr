---
title: Azure Cosmos DB bir SQL sorgusu için istek birimi (RU) ücreti bulma
description: Azure Cosmos kapsayıcısına göre yürütülen SQL sorguları için istek birimi (RU) ücreti bulmayı öğrenin. RU ücretine ulaşmak için Azure portal, .NET, Java, Python ve Node.js dillerini kullanabilirsiniz.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201291"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API 'sinde yürütülen işlemler için istek birimi ücreti bulma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir.

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB tarafından normalleştirilir ve İstek Birimi (veya kısa RU) ile ifade edilir. İstek ücreti, tüm veritabanı işlemleriniz tarafından tüketilen istek birimleridir. , Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayan bir performans para birimi olarak RUs 'yi düşünebilirsiniz. Azure Cosmos kapsayıcınızla etkileşim kurmak için hangi API'yi kullanırsanız kullanın maliyetler her zaman RU cinsinden ölçülür. Veritabanı işleminin bir yazma, işaret okuma veya sorgu olup olmadığı, maliyetler her zaman RUs cinsinden ölçülür. Daha fazla bilgi için bkz. [İstek birimleri ve ilgili konular](request-units.md) makalesi.

Bu makalede, Azure Cosmos DB SQL API 'sindeki bir kapsayıcıya karşı yürütülen herhangi bir işlem için [istek birimi](request-units.md) (ru) tüketimini bulabileceğiniz farklı yollar sunulmaktadır. Farklı bir API kullanıyorsanız, RU/s ücretine ulaşmak için bkz. [MongoDB Için API](find-request-unit-charge-mongodb.md), [Cassandra API](find-request-unit-charge-cassandra.md), [gremlin API](find-request-unit-charge-gremlin.md)ve [tablo API'si](find-request-unit-charge-table.md) makaleleri.

Şu anda, bu tüketimi yalnızca Azure portal kullanarak veya SDK 'lardan biri aracılığıyla Azure Cosmos DB geri gönderilen yanıtı inceleyerek ölçebilirsiniz. SQL API 'sini kullanıyorsanız, bir Azure Cosmos kapsayıcısına karşı bir işlemin RU tüketimini bulmak için birden çok seçeneğiniz vardır.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account) ve verilerle birlikte akışı yapın veya zaten veri içeren mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesine gidin ve üzerinde çalışmak istediğiniz kapsayıcıyı seçin.

1. **Yeni SQL Sorgusu**'nu seçin.

1. Geçerli bir sorgu girin ve sonra **sorguyu Yürüt**' ü seçin.

1. Yürüttüğünüz istek için gerçek istek ücreti göstermek üzere **sorgu istatistikleri** ' ni seçin.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Azure portal bir SQL sorgu isteği ücreti ekran görüntüsü":::

## <a name="use-the-net-sdk"></a>.NET SDK’yı kullanma

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 'den döndürülen nesneler bir özelliği kullanıma sunar `RequestCharge` :

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 'den döndürülen nesneler bir özelliği kullanıma sunar `RequestCharge` :

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Cosmos DB ' de BIR SQL API hesabı kullanarak .NET Web uygulaması oluşturma](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Java SDK 'sını kullanma

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 'dan döndürülen nesneler bir yöntemi kullanıma sunar `getRequestCharge()` :

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Cosmos db SQL API hesabı kullanarak Java uygulaması oluşturma](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Node.js SDK 'sını kullanma

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 'dan döndürülen nesneler, `headers` temel alınan http API 'si tarafından döndürülen tüm üst bilgileri eşleyen bir alt nesne sunar. İstek ücreti şu anahtar altında kullanılabilir `x-ms-request-charge` :

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Cosmos db SQL API hesabı kullanarak Node.js uygulama oluşturma](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Python SDK'yı kullanma

`CosmosClient` [Python SDK](https://pypi.org/project/azure-cosmos/) 'daki nesne, `last_response_headers` yürütülen son işlem için temeldeki HTTP API 'si tarafından döndürülen tüm üstbilgileri eşleyen bir sözlük sunar. İstek ücreti şu anahtar altında kullanılabilir `x-ms-request-charge` :

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Cosmos db SQL API hesabı kullanarak bir Python uygulaması oluşturma](create-sql-api-python.md). 

## <a name="next-steps"></a>Sonraki adımlar

RU tüketiminizi en iyi duruma getirme hakkında bilgi edinmek için şu makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)
* [Sağlanan aktarım hızını küresel olarak ölçeklendirme](./request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Bir kapsayıcı için sağlama işleme](how-to-provision-container-throughput.md)
* [Azure Cosmos DB ölçümlerle izleme ve hata ayıklama](use-metrics.md)
