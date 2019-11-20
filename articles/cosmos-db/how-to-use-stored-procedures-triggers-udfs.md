---
title: Azure Cosmos DB SDK 'Ları kullanarak saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri çağırma
description: Azure Cosmos DB SDK 'Ları kullanarak saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri nasıl kaydedeceğinizi ve çağıracağınızı öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: mjbrown
ms.openlocfilehash: 3cc144c1b8748710f0500b6ca2a418cd8bf5a2b7
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104839"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri kaydetme ve kullanma

Azure Cosmos DB içindeki SQL API 'SI, saklı yordamları, Tetikleyicileri ve JavaScript 'te yazılmış Kullanıcı tanımlı işlevleri (UDF 'ler) kaydetmeyi ve çağırmayı destekler. Saklı yordamları kaydettirmek ve çağırmak için SQL API [.net](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node. js](sql-api-sdk-node.md)veya [Python](sql-api-sdk-python.md) SDK 'larını kullanabilirsiniz. Bir veya daha fazla saklı yordam, tetikleyici ve Kullanıcı tanımlı işlevleri tanımladıktan sonra, Veri Gezgini kullanarak bunları [Azure Portal](https://portal.azure.com/) yükleyebilir ve görüntüleyebilirsiniz.

## <a id="stored-procedures"></a>Saklı yordamları çalıştırma

Saklı yordamlar JavaScript kullanılarak yazılır. Azure Cosmos kapsayıcısı içinde öğeleri oluşturabilir, güncelleştirebilir, okuyabilir, sorgulayabilir ve silebilir. Azure Cosmos DB saklı yordamları yazma hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB makalesinde saklı yordamlar yazma](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) .

Aşağıdaki örneklerde, Azure Cosmos DB SDK 'Ları kullanılarak saklı yordamın nasıl kaydedileceği ve çağrılacağını gösterilmektedir. Bu saklı yordamın kaynağı olarak `spCreateToDoItem.js`kaydedildiği Için [bir belge oluşturma](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) bölümüne bakın.

> [!NOTE]
> Bölümlenmiş kapsayıcılar için, saklı bir yordam yürütürken, istek seçeneklerinde bir bölüm anahtarı değeri belirtilmelidir. Saklı yordamlar her zaman bir bölüm anahtarına göre kapsamlandırılır. Farklı bir bölüm anahtarı değerine sahip öğeler, saklı yordama görünür olmayacaktır. Bu ayrıca tetikleyicilere de uygulanır.

### <a name="stored-procedures---net-sdk-v2"></a>Saklı yordamlar-.NET SDK v2

Aşağıdaki örnek, bir saklı yordamın .NET SDK v2 kullanılarak nasıl kaydedileceği gösterilmektedir:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Aşağıdaki kod, bir saklı yordamın .NET SDK v2 kullanılarak nasıl çağrılacağını göstermektedir:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
```

### <a name="stored-procedures---net-sdk-v3"></a>Saklı yordamlar-.NET SDK V3

Aşağıdaki örnek .NET SDK V3 kullanarak bir saklı yordamın nasıl kaydedileceği gösterilmektedir:

```csharp
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("database", "container").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = "spCreateToDoItem",
    Body = File.ReadAllText(@"..\js\spCreateToDoItem.js")
});
```

Aşağıdaki kod, .NET SDK V3 kullanarak bir saklı yordamın nasıl çağrılacağını göstermektedir:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), newItem);
```

### <a name="stored-procedures---java-sdk"></a>Saklı yordamlar-Java SDK

Aşağıdaki örnek, bir saklı yordamın Java SDK kullanarak nasıl kaydedileceği gösterilmektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Aşağıdaki kod, bir saklı yordamın Java SDK kullanarak nasıl çağrılacağını göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Saklı yordamlar-JavaScript SDK 'Sı

Aşağıdaki örnek, bir saklı yordamın JavaScript SDK kullanılarak nasıl kaydedileceği gösterilmektedir

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Aşağıdaki kod, JavaScript SDK kullanarak bir saklı yordamın nasıl çağrılacağını göstermektedir:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Saklı yordamlar-Python SDK

Aşağıdaki örnek, bir saklı yordamın Python SDK kullanılarak nasıl kaydedileceği gösterilmektedir

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

Aşağıdaki kod, bir saklı yordamın Python SDK kullanılarak nasıl çağrılacağını gösterir

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Ön Tetikleyiciler çalıştırma

Aşağıdaki örneklerde, Azure Cosmos DB SDK 'larını kullanarak bir ön tetikleyiciyi kaydetme ve çağırma gösterilmektedir. Bu ön tetikleyicinin kaynağı olarak `trgPreValidateToDoItemTimestamp.js`kaydedildiği için, [ön tetikleme örneğine](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) bakın.

Yürütme sırasında, ön Tetikleyiciler requestoptions nesnesine `PreTriggerInclude` geçirilir ve ardından bir liste nesnesi içinde tetikleyicinin adını geçirerek.

> [!NOTE]
> Tetikleyicinin adı bir liste olarak geçirilse de, işlem başına yalnızca bir tetikleyici yürütüleyebilirsiniz.

### <a name="pre-triggers---net-sdk-v2"></a>Ön Tetikleyiciler-.NET SDK v2

Aşağıdaki kod .NET SDK v2 kullanarak bir ön tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Aşağıdaki kod .NET SDK v2 kullanarak bir ön tetikleyicinin nasıl çağrılacağını göstermektedir:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Ön Tetikleyiciler-.NET SDK V3

Aşağıdaki kod .NET SDK V3 kullanarak bir ön tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

Aşağıdaki kod .NET SDK V3 kullanarak bir ön tetikleyicinin nasıl çağrılacağını göstermektedir:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Ön Tetikleyiciler-Java SDK

Aşağıdaki kod, Java SDK kullanarak bir ön tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Aşağıdaki kod, Java SDK kullanarak bir ön tetikleyicinin nasıl çağrılacağını göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Ön Tetikleyiciler-JavaScript SDK 'Sı

Aşağıdaki kod, JavaScript SDK kullanarak bir ön tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Aşağıdaki kod, JavaScript SDK kullanarak bir ön tetikleyicinin nasıl çağrılacağını göstermektedir:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Ön Tetikleyiciler-Python SDK

Aşağıdaki kod, Python SDK kullanarak bir ön tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Aşağıdaki kod, Python SDK kullanarak bir ön tetikleyicinin nasıl çağrılacağını göstermektedir:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Tetikleyicileri çalıştırma

Aşağıdaki örneklerde, Azure Cosmos DB SDK 'Ları kullanılarak tetikleyiciden nasıl kaydedileceği gösterilmektedir. Bu tetikleyicinin kaynak olarak `trgPostUpdateMetadata.js`kaydedildiği, [tetikleyici sonrası örneğine](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) bakın.

### <a name="post-triggers---net-sdk-v2"></a>Tetikleyiciler sonrası-.NET SDK v2

Aşağıdaki kod .NET SDK v2 kullanarak bir tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Aşağıdaki kod .NET SDK v2 kullanarak tetikleyiciden nasıl çağrılacağını göstermektedir:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Tetikleyiciler sonrası-.NET SDK V3

Aşağıdaki kod .NET SDK V3 kullanarak bir tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

Aşağıdaki kod .NET SDK V3 kullanarak bir tetikleyicisinin nasıl çağrılacağını göstermektedir:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Tetikleyiciler sonrası-Java SDK

Aşağıdaki kod, Java SDK 'sını kullanarak bir tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Aşağıdaki kod, Java SDK kullanarak bir tetikleyicisinin nasıl çağrılacağını göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Tetikleyiciler-son-JavaScript SDK

Aşağıdaki kod, JavaScript SDK kullanarak bir tetikleyicisinin nasıl kaydedileceği gösterilmektedir:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Aşağıdaki kod, JavaScript SDK kullanarak bir tetikleyicisinin nasıl çağrılacağını göstermektedir:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Tetikleyiciler-Python SDK

Aşağıdaki kod, Python SDK kullanarak bir tetikleyiciyi nasıl kaydedeceğinizi göstermektedir:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Aşağıdaki kod, Python SDK kullanarak bir tetikleyicisinin nasıl çağrılacağını göstermektedir:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Kullanıcı tanımlı işlevlerle çalışma

Aşağıdaki örneklerde, Azure Cosmos DB SDK 'Ları kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir. Bu tetikleyicinin kaynağı olarak `udfTax.js`kaydedildiği için bu [Kullanıcı tanımlı işlev örneğine](how-to-write-stored-procedures-triggers-udfs.md#udfs) başvurun.

### <a name="user-defined-functions---net-sdk-v2"></a>Kullanıcı tanımlı işlevler-.NET SDK v2

Aşağıdaki kod, .NET SDK v2 kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Aşağıdaki kod, .NET SDK v2 kullanılarak Kullanıcı tanımlı bir işlevin nasıl çağrılacağını göstermektedir:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Kullanıcı tanımlı işlevler-.NET SDK V3

Aşağıdaki kod, .NET SDK V3 kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

Aşağıdaki kod, .NET SDK V3 kullanılarak Kullanıcı tanımlı bir işlevin nasıl çağrılacağını göstermektedir:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Kullanıcı tanımlı işlevler-Java SDK

Aşağıdaki kod, Java SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Aşağıdaki kod, Java SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl çağrılacağını göstermektedir:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Kullanıcı tanımlı işlevler-JavaScript SDK

Aşağıdaki kod, JavaScript SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Aşağıdaki kod, JavaScript SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl çağrılacağını göstermektedir:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Kullanıcı tanımlı işlevler-Python SDK

Aşağıdaki kod, Python SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl kaydedileceği gösterilmektedir:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Aşağıdaki kod, Python SDK kullanılarak Kullanıcı tanımlı bir işlevin nasıl çağrılacağını göstermektedir:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yazma veya kullanma hakkında daha fazla bilgi edinin:

- [Azure Cosmos DB içinde Azure Cosmos DB saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevlerle çalışma](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB 'de JavaScript Dil tümleşik sorgu API 'SI ile çalışma](javascript-query-api.md)
- [Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri Azure Cosmos DB yazma](how-to-write-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB içinde JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazma](how-to-write-javascript-query-api.md)
