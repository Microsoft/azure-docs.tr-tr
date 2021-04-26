---
title: Azure Cosmos DB tutarlılığı yönetme
description: Azure portal, .NET SDK, Java SDK ve çeşitli diğer SDK 'Ları kullanarak Azure Cosmos DB tutarlılık düzeylerini yapılandırma ve yönetme hakkında bilgi edinin
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7cab67b49196a3d50ce5483282971bbb7b9ece1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483291"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB'deki tutarlılık düzeylerini yönetme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB ' de tutarlılık düzeylerinin nasıl yönetileceği açıklanmaktadır. Varsayılan tutarlılık düzeyini yapılandırmayı, varsayılan tutarlılığı geçersiz kılmayı, oturum belirteçlerini el ile yönetmeyi ve olasılık açısından iyi sınırlı Stalet (PBS) ölçüsünü anladığınızı öğrenirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini yapılandırma

[Varsayılan tutarlılık düzeyi](consistency-levels.md) , istemcilerin varsayılan olarak kullandığı tutarlılık düzeyidir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Varsayılan tutarlılık düzeyini görüntülemek veya değiştirmek için Azure portal oturum açın. Azure Cosmos hesabınızı bulun ve **varsayılan tutarlılık** bölmesini açın. Yeni varsayılan olarak istediğiniz tutarlılık düzeyini seçin ve ardından **Kaydet**' i seçin. Azure portal Ayrıca, müzik notlarıyla farklı tutarlılık seviyelerinin bir görselleştirmesi sağlar. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Azure portal tutarlılık menüsü":::

# <a name="cli"></a>[CLI](#tab/cli)

Oturum tutarlılığı olan bir Cosmos hesabı oluşturun ve ardından varsayılan tutarlılığı güncelleştirin.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Oturum tutarlılığı olan bir Cosmos hesabı oluşturun ve ardından varsayılan tutarlılığı güncelleştirin.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini geçersiz kılma

İstemciler, hizmet tarafından belirlenen varsayılan tutarlılık düzeyini geçersiz kılabilir. Tutarlılık düzeyi istek başına ayarlanabilir ve bu, hesap düzeyinde ayarlanan varsayılan tutarlılık düzeyini geçersiz kılar.

> [!TIP]
> Tutarlılık yalnızca istek düzeyinde **gevşek** olabilir. Daha zayıf bir tutarlılığa geçiş yapmak için Cosmos hesabının varsayılan tutarlılığını güncelleştirin.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java v4 SDK 'Sı

# <a name="async"></a>[Eş](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) zaman uyumsuz API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Eşitle](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) eşitleme API 'SI

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java v2 SDK 'Ları

# <a name="async"></a>[Eş](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Eşitle](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com. Microsoft. Azure:: Azure-DocumentDB)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK 'Sı

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Oturum belirteçlerini kullanma

Azure Cosmos DB ' deki tutarlılık düzeylerinden biri *oturum* tutarlılığı. Bu, varsayılan olarak Cosmos hesaplarına uygulanan varsayılan düzeydir. *Oturum* tutarlılığı ile çalışırken, istemci, ayarlanan tutarlılık düzeyinin korunmasını sağlamak için her bir okuma/sorgu isteğiyle dahili olarak bir oturum belirteci kullanır.

Oturum belirteçlerini el ile yönetmek için, yanıttan oturum belirtecini alın ve istek başına bunları ayarlayın. Oturum belirteçlerini el ile yönetmeniz gerekmiyorsa, bu örnekleri kullanmanız gerekmez. SDK oturum belirteçlerini otomatik olarak izler. Oturum belirtecini el ile ayarlamazsanız, SDK varsayılan olarak en son oturum belirtecini kullanır.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java v4 SDK 'Sı

# <a name="async"></a>[Eş](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) zaman uyumsuz API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Eşitle](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) eşitleme API 'SI

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java v2 SDK 'Ları

# <a name="async"></a>[Eş](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Eşitle](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com. Microsoft. Azure:: Azure-DocumentDB)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK 'Sı

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Olasılığa Dayalı Sınırlanmış Eskime Durumu (PBS) ölçümünü izleme

Ne kadar nihai tutarlılık? Ortalama durumda, sürüm geçmişi ve saatine göre, eskime sınırları sunarız. [**Olasılığa göre sınırlı stalet (PBS)**](https://pbs.cs.berkeley.edu/) ölçümü, eskime olasılığını ölçmenizi ve bunu bir ölçüm olarak gösterir. PBS ölçüsünü görüntülemek için Azure portal Azure Cosmos hesabınıza gidin. **Ölçümler** bölmesini açın ve **tutarlılık** sekmesini seçin. **İş yükünüze göre sağlam tutarlı okumaların olasılığı adlı grafiğe bakın (bkz. PBS)**.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Azure portal PBS grafiği":::

## <a name="next-steps"></a>Sonraki adımlar

Veri çakışmalarını yönetme veya Azure Cosmos DB bir sonraki anahtar kavramına geçme hakkında daha fazla bilgi edinin. Aşağıdaki makalelere bakın:

* [Azure Cosmos DB 'deki tutarlılık düzeyleri](consistency-levels.md)
* [Bölümleme ve veri dağıtımı](./partitioning-overview.md)
* [Bölgeler arasındaki çakışmaları yönetme](how-to-manage-conflicts.md)
* [Bölümleme ve veri dağıtımı](partitioning-overview.md)
* [Modern dağıtılmış veritabanı sistemleri tasarımında tutarlılık avantajları](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Yüksek kullanılabilirlik](high-availability.md)
* [SLA Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
