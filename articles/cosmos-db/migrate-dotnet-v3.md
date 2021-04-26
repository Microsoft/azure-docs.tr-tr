---
title: Uygulamanızı .NET SDK 3,0 (com. Azure. Cosmos) Azure Cosmos DB kullanmak için geçirin
description: Mevcut .NET uygulamanızı, temel (SQL) API 'SI için v2 SDK 'sından daha yeni .NET SDK v3 (com. Azure. Cosmos paketi) sürümüne nasıl yükselteceğinizi öğrenin.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334030"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Uygulamanızı .NET SDK V3 Azure Cosmos DB kullanmak için geçirin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> .NET SDK V3 Azure Cosmos DB hakkında bilgi edinmek için [sürüm notlarına](sql-api-sdk-dotnet-standard.md), [.net GitHub DEPOSUNA](https://github.com/Azure/azure-cosmos-dotnet-v3), .NET SDK V3 [performans ipuçlarına](performance-tips-dotnet-sdk-v3-sql.md)ve [sorun giderme kılavuzuna](troubleshoot-dot-net-sdk.md)bakın.
>

Bu makalede, mevcut .NET uygulamanızı Core (SQL) API 'SI için .NET SDK V3 Azure Cosmos DB yeni sürüme yükseltmeyle ilgili bazı konular vurgulanmaktadır. Azure Cosmos DB .NET SDK V3, Microsoft. Azure. Cosmos ad alanına karşılık gelir. Uygulamanızı aşağıdaki Azure Cosmos DB .NET SDK 'larından herhangi birinden geçiriyorsanız, bu belgede belirtilen bilgileri kullanabilirsiniz:

* SQL API için Azure Cosmos DB .NET Framework SDK v2
* SQL API için Azure Cosmos DB .NET Core SDK v2

Bu makaledeki yönergeler Ayrıca, artık Azure Cosmos DB .NET SDK V3 for Core (SQL) API 'sinin bir parçası olan aşağıdaki dış kitaplıkları geçirmenize yardımcı olur:

* .NET değişiklik akışı işlemci kitaplığı 2,0
* .NET toplu yürütücü kitaplığı 1,1 veya üzeri

## <a name="whats-new-in-the-net-v3-sdk"></a>.NET v3 SDK 'daki yenilikler

V3 SDK, aşağıdakiler dahil olmak üzere birçok kullanılabilirlik ve performans geliştirmesi içerir:

* Sezgisel programlama modeli adlandırma
* .NET Standard 2,0 * *
* Akış API 'SI desteğiyle artan performans
* URI fabrikası gereksinimini değiştiren akıcı hiyerarşi
* Değişiklik akışı işlemci kitaplığı için yerleşik destek
* Toplu işlemler için yerleşik destek
* Daha kolay birim testi için Mockabale API 'Leri
* İşlem toplu işi ve Blazor desteği
* Takılabilir serileştiriciler
* Bölümlendirilmemiş ve otomatik ölçeklendirme kapsayıcılarını ölçeklendirme

* * SDK, mevcut Azure Cosmos DB .NET Framework ve .NET Core SDK 'larını tek bir .NET SDK 'Sı ile birleştiren 2,0 .NET Standard hedefler. .NET SDK 'yi, .NET Framework 4.6.1 + ve .NET Core 2.0 + uygulamalarınız dahil olmak üzere .NET Standard 2,0 ' i uygulayan herhangi bir platformda kullanabilirsiniz.

Ağ, yeniden deneme mantığı ve SDK 'nın daha düşük düzeyleri büyük ölçüde değişmeden kalır.

**Azure Cosmos DB .NET SDK V3, artık açık kaynak.** Tüm çekme isteklerini destekliyoruz ve GitHub 'daki sorunları günlüğe kaydetmek ve geri bildirim izlemek olacaktır [.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Müşteri deneyimini geliştiren tüm özellikleri alırken çalışacağız.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Neden .NET v3 SDK 'sına geçirilir?

Birçok kullanılabilirlik ve performans iyileştirmelerine ek olarak, en son SDK 'da yapılan yeni özellik yatırımları eski sürümlere eklenmeyecektir.

[2,0 SDK 'ları için desteği devre dışı bırakmaya](sql-api-sdk-dotnet.md)yönelik bir plan olmasa da SDK 'lar gelecekte daha yeni sürümlerle DEĞIŞTIRILIRKEN, SDK bakım moduna alınacaktır. En iyi geliştirme deneyimi için, her zaman SDK 'nın desteklenen en son sürümüyle başlamasını öneririz.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>V2 SDK 'dan v3 SDK 'ya büyük ad değişiklikleri

Çekirdek (SQL) API 'SI için API adlandırma kurallarına uyum sağlamak üzere .NET 3,0 SDK 'nın tamamında aşağıdaki ad değişiklikleri uygulandı:

* `DocumentClient` yeniden adlandırıldı `CosmosClient`
* `Collection` yeniden adlandırıldı `Container`
* `Document` yeniden adlandırıldı `Item`

Tüm kaynak nesneleri ek özelliklerle yeniden adlandırılır, bu, açıklık için kaynak adını içerir.

Bazı ana sınıf adı değişiklikleri aşağıda verilmiştir:

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>.NET v3 SDK 'da sınıflar değiştirilmiştir

Aşağıdaki sınıflar 3,0 SDK 'sında değiştirilmiştir:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documlar. UriFactory sınıfı, akıcı tasarımla değiştirilmiştir. Akıcı tasarım, URL 'Leri dahili olarak oluşturur ve tek `Container` bir nesnenin,, ve yerine geçirilmesini sağlar `DocumentClient` `DatabaseName` `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Öğe KIMLIĞI oluşturma değişiklikleri

Öğe KIMLIĞI artık .NET v3 SDK 'sında otomatik olarak doldurulamaz. Bu nedenle, öğe KIMLIĞI özel olarak oluşturulmuş bir KIMLIK içermelidir. Aşağıdaki örneği görüntüleyin:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Bağlantı modu için varsayılan davranış değiştirildi

SDK V3, artık ağ geçidi + HTTPS bağlantı modlarında varsayılan olarak, önceki v2 SDK 'sına kıyasla doğrudan + TCP bağlantı modlarını varsayılan olarak belirler. Bu değişiklik, gelişmiş performans ve ölçeklenebilirlik sağlar.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Feedos değişiklikleri (v 3.0 SDK 'da QueryRequestOptions)

`FeedOptions`SDK v2 'deki sınıfı artık `QueryRequestOptions` SDK V3 ve sınıfının içinde olarak yeniden adlandırıldı, bazı özellikler ad ve/veya varsayılan değerde değişiklik içeriyordu veya tamamen kaldırıldı.  

`FeedOptions.MaxDegreeOfParallelism` , olarak yeniden adlandırıldı `QueryRequestOptions.MaxConcurrency` ve varsayılan değer ile ilişkili davranış aynı kalır, paralel sorgu yürütme sırasında istemci tarafında çalıştırma işlemleri, hiçbir paralellik olmadan hizmet dışı olarak yürütülür.

`FeedOptions.EnableCrossPartitionQuery` kaldırılmıştır ve SDK 3,0 ' deki varsayılan davranış, özellikle özelliği etkinleştirmeye gerek olmadan çapraz bölüm sorgularının yürütüldüğünden olur.

`FeedOptions.PopulateQueryMetrics` , yanıtın tanılama özelliğinde mevcut olan sonuçlarla varsayılan olarak etkindir.

`FeedOptions.RequestContinuation` Artık sorgu yöntemlerine yükseltilir.

Aşağıdaki özellikler kaldırılmıştır:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>İstemci oluşturma

.NET SDK V3, `CosmosClientBuilder` SDK v2 URI fabrikası gereksinimini değiştiren bir akıcı sınıf sağlar.

Aşağıdaki örnek, güçlü bir Me `CosmosClientBuilder` düzeyi ve tercih edilen konumların bir listesi ile yeni bir oluşturur:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Doğrudan v3 SDK 'dan akış işlemcisi API 'Lerini değiştirme

V3 SDK, değişiklik akışı Işlemcisi API 'Leri için yerleşik desteğe sahiptir ve uygulamanızı oluşturmak için aynı SDK 'Yı kullanmanıza ve akış işlemcisi uygulamasını değiştirmenize olanak sağlar. Daha önce, ayrı bir değişiklik akışı işlemci kitaplığı kullanmanız gerekiyordu.

Daha fazla bilgi için bkz [. değişiklik akışı işlemci kitaplığından .net v3 SDK Azure Cosmos DB geçirme](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Doğrudan v3 SDK 'dan toplu yürütücü kitaplığını kullanma

V3 SDK, toplu yürütücü kitaplığı için yerleşik desteğe sahiptir ve uygulamanızı oluşturmak ve toplu işlemleri gerçekleştirmek için aynı SDK 'Yı kullanmanıza olanak sağlar. Daha önce, ayrı bir toplu yürütücü kitaplığı kullanmanız gerekiyordu.

Daha fazla bilgi için bkz [. .net v3 SDK Azure Cosmos DB toplu yürütücü kitaplığı 'ndan toplu desteğe geçiş](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>Kod parçacığı karşılaştırmaları

Aşağıdaki kod parçacığı, .NET v2 ve v3 SDK 'Ları arasında kaynakların oluşturulma farklarının farklarını gösterir:

## <a name="database-operations"></a>Veritabanı işlemleri

### <a name="create-a-database"></a>Veritabanı oluşturma

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>KIMLIĞE göre bir veritabanı oku

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Veritabanı silme

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Kapsayıcı işlemleri

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Kapsayıcı oluşturma (otomatik ölçeklendirme + zaman aşımı ile yaşam süresi)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Kapsayıcı özelliklerini okuma

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Kapsayıcı silme

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Öğe ve sorgu işlemleri

### <a name="create-an-item"></a>Öğe oluşturma

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Bir kapsayıcıdaki tüm öğeleri okuma

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Sorgu öğeleri

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Öğeyi silme

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Sonraki adımlar

* V3 SDK kullanarak Azure Cosmos DB SQL API verilerini yönetmek için [bir konsol uygulaması oluşturma](sql-api-get-started.md)
* [V3 SDK ile](sql-api-dotnet-v3sdk-samples.md) yapabilecekleriniz hakkında daha fazla bilgi edinin
