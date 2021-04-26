---
title: Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos) kullanmak için uygulamanızı geçirin
description: Mevcut Java uygulamanızı Azure Cosmos DB, çekirdek (SQL) API 'SI için daha yeni Java SDK 4,0 (com. Azure. Cosmos paketi) ile birlikte kullanarak nasıl yükselteceğinizi öğrenin.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658546"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Uygulamanızı Java SDK 'Sı v4 Azure Cosmos DB kullanmak için geçirin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Bu SDK hakkında daha fazla bilgi için lütfen Azure Cosmos DB Java SDK v4 [sürüm notları](sql-api-sdk-java-v4.md), [maven deposu](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4 [Performans ipuçları](performance-tips-java-sdk-v4-sql.md)ve Azure Cosmos DB Java SDK v4 [sorun giderme kılavuzunu](troubleshoot-java-sdk-v4-sql.md)görüntüleyin.
>

Bu makalede, daha eski bir Java SDK 'sını kullanan mevcut Java uygulamanızın çekirdek (SQL) API 'SI için daha yeni Azure Cosmos DB Java SDK 4,0 Azure Cosmos DB nasıl yükseltileceği açıklanır. Azure Cosmos DB Java SDK v4 pakete karşılık gelir `com.azure.cosmos` . Uygulamanızı aşağıdaki Azure Cosmos DB Java SDK 'larından herhangi birinden geçiriyorsanız, bu belgedeki yönergeleri kullanabilirsiniz: 

* Java SDK 'Sı 2. x. x Eşitle
* Zaman uyumsuz Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Java SDK 'sının ve paket eşlemelerini Azure Cosmos DB

Aşağıdaki tabloda farklı Azure Cosmos DB Java SDK 'Ları, paket adı ve sürüm bilgileri listelenmektedir:

| Java SDK| Yayın Tarihi | Paketlenmiş API 'Ler   | Maven jar  | Java paket adı  |API Başvurusu   | Sürüm Notları  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Zaman uyumsuz 2. x. x  | Haziran 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Sürüm Notları](sql-api-sdk-async-java.md) |
| 2. x. x eşitlemesini Eşitle     | Eyl 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Sürüm Notları](sql-api-sdk-java.md)  |
| 3. x. x    | Temmuz 2019    | Async (reaktör)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | Haziran 2020   | Async (reaktör)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>SDK düzeyinde uygulama değişiklikleri

Aşağıda, farklı SDK 'lar arasındaki önemli uygulama farklılıkları verilmiştir:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava, Azure Cosmos DB Java SDK sürümü 3. x ve 4,0 ' de reaktör ile değiştirilmiştir

Zaman uyumsuz programlama veya reaktif programlama hakkında bilginiz yoksa, zaman uyumsuz programlama ve proje Reaktörine giriş için [reaktör deseninin kılavuzuna](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) bakın. Bu kılavuz, daha önce Java SDK 'Sı 2. x. x veya Azure Cosmos DB Java SDK 3. x. x eşitleme API 'sini kullanıyorsanız Azure Cosmos DB yararlı olabilir.

Azure Cosmos DB zaman uyumsuz Java SDK 'Sı 2. x. x kullandıysanız ve 4,0 SDK 'sına geçirmeyi planlıyorsanız, RxJava kodunu reaktör kullanmak üzere dönüştürmeye yönelik rehberlik için [reaktör vs Rxjava kılavuzuna](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) bakın.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4, hem Async hem de Sync API 'Lerinde doğrudan bağlantı moduna sahiptir

Java SDK 'Sı 2. x. x Azure Cosmos DB eşitleme kullanıyorsanız, hem Async hem de Sync API 'Leri için, TCP 'ye (HTTP 'nin aksine) bağlı olan doğrudan bağlantı modunun Azure Cosmos DB Java SDK 4,0 ' de uygulandığını unutmayın.

## <a name="api-level-changes"></a>API düzeyi değişiklikleri

Aşağıda, Java SDK 4. x. x Azure Cosmos DB önceki SDK 'lara kıyasla (Java SDK 3. x. x, zaman uyumsuz Java SDK 2. x. x ve Eşitle Java SDK 'sı 2. x. x) API düzeyi değişiklikler verilmiştir:

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB Java SDK adlandırma kuralları":::

* Java SDK 3. x. x ve 4,0 Azure Cosmos DB istemci kaynaklarına olarak başvurur `Cosmos<resourceName>` . Örneğin,, `CosmosClient` , `CosmosDatabase` `CosmosContainer` . 2. x. x sürümünde, Java SDK 'larının Azure Cosmos DB Tekdüzen adlandırma düzeni yoktur.

* Azure Cosmos DB Java SDK 3. x. x ve 4,0 hem Sync hem de Async API 'Leri sunmaktadır.

  * **Java SDK 4,0** : sınıf adı daha sonra eklenmediği takdirde tüm SıNıFLAR eşitleme API 'sine aittir `Async` `Cosmos` .

  * **Java SDK 3. x. x**: sınıf adı daha sonra eklenmediği takdirde tüm sınıflar zaman uyumsuz API 'ye aittir `Async` `Cosmos` .

  * **Async Java SDK 2. x. x**: sınıf adları eşitleme Java SDK 'sı 2. x. x ile benzerdir, ancak ad *zaman uyumsuz* olarak başlar.

### <a name="hierarchical-api-structure"></a>Hiyerarşik API yapısı

Azure Cosmos DB Java SDK 4,0 ve 3. x. x, aşağıdaki 4,0 SDK kod parçacığında gösterildiği gibi, iç içe geçmiş bir biçimde istemcileri, veritabanlarını ve kapsayıcıları düzenleyen hiyerarşik bir API yapısını tanıtmaktadır:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Azure Cosmos DB Java SDK 'sının 2. x. x sürümünde, kaynak ve belge üzerindeki tüm işlemler istemci örneği aracılığıyla gerçekleştirilir.

### <a name="representing-documents"></a>Belgeleri temsil etme

Azure Cosmos DB Java SDK 4,0 ' de, özel POJO `JsonNodes` 'lar ve Azure Cosmos DB belgeleri okuyup yazmak için iki seçenek vardır.

Java SDK 3. x. x Azure Cosmos DB, `CosmosItemProperties` nesnesi ortak API tarafından gösterilir ve bir belge temsili olarak sunulur. Bu sınıf artık 4,0 sürümünde herkese açık değildir.

### <a name="imports"></a>İşlemlerinin

* Azure Cosmos DB Java SDK 4,0 paketleri şununla başlar `com.azure.cosmos`
* Azure Cosmos DB Java SDK 3. x. x paketleri şununla başlar `com.azure.data.cosmos`
* Azure Cosmos DB Java SDK 2. x. x eşitleme API 'SI paketleri şununla başlar `com.microsoft.azure.documentdb`

* Azure Cosmos DB Java SDK 4,0, iç içe bir pakete birkaç sınıfı koyar `com.azure.cosmos.models` . Bu paketlerin bazıları şunlardır:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Yukarıdaki tüm paketler için zaman uyumsuz API analoglarından
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... benzerlerini.

### <a name="accessors"></a>C

Azure Cosmos DB Java SDK 4,0 `get` `set` , örnek üyelerine erişim yöntemleri sunar. Örneğin, `CosmosContainer` örneği `container.getId()` ve `container.setId()` yöntemleri vardır.

Bu, akıcı bir arabirim sunan Azure Cosmos DB Java SDK 3. x. x ' ten farklıdır. Örneğin, `CosmosSyncContainer` `container.id()` değeri almak veya ayarlamak için aşırı yüklenmiş bir örnek vardır `id` .

## <a name="code-snippet-comparisons"></a>Kod parçacığı karşılaştırmaları

### <a name="create-resources"></a>Kaynak oluşturma

Aşağıdaki kod parçacığı, kaynakların 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'Leri arasında nasıl oluşturulduğuna ilişkin farkları gösterir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Öğe işlemleri

Aşağıdaki kod parçacığı 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'Leri arasında öğe işlemlerinin gerçekleştirilme farklarını gösterir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Dizinleme

Aşağıdaki kod parçacığı, dizin oluşturma işleminin 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'Leri arasında nasıl oluşturulduğuna ilişkin farkları göstermektedir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Saklı yordamlar

Aşağıdaki kod parçacığı, saklı yordamların 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'Leri arasında nasıl oluşturulduğuna ilişkin farkları gösterir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Akışı değiştirme

Aşağıdaki kod parçacığı, 4,0 ve 3. x. x zaman uyumsuz API 'Ler arasında değişiklik akışı işlemlerinin yürütülme farklarını gösterir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

* Bu özellik Java SDK v2 eşitlemesi itibariyle desteklenmez. 
---

### <a name="container-level-time-to-livettl"></a>Kapsayıcı düzeyi yaşam süresi (TTL)

Aşağıdaki kod parçacığı, 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'lerini kullanarak kapsayıcıdaki veriler için yaşam süresi oluşturma farklılıklarını gösterir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Öğe düzeyi yaşam süresi (TTL)

Aşağıdaki kod parçacığı, 4,0, 3. x. x zaman uyumsuz API 'Ler ve 2. x. x eşitleme API 'lerini kullanarak bir öğe için yaşam süresi oluşturma ile ilgili farkları göstermektedir:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4,0 zaman uyumsuz API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x zaman uyumsuz API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2. x. x eşitleme API 'SI](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Sonraki adımlar

* V4 SDK 'sını kullanarak Azure Cosmos DB SQL API verilerini yönetmek için [bir Java uygulaması oluşturma](create-sql-api-java.md)
* [Reaktör tabanlı Java SDK 'ları](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) hakkında bilgi edinin
* [Reaktör vs Rxjava kılavuzuyla](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) zaman uyumsuz kodu yeniden aktör Için rxjava Async kodunu dönüştürme hakkında bilgi edinin
