---
title: Azure Cosmos DB'de tutarlılığı yönetmeyi öğrenin
description: Azure Cosmos DB'de tutarlılığı yönetmeyi öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: bc5554e2d56987e969894ba57052d548e1499938
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093315"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB'deki tutarlılık düzeylerini yönetme

Bu makalede, Azure Cosmos DB ' de tutarlılık düzeylerinin nasıl yönetileceği açıklanmaktadır. Varsayılan tutarlılık düzeyini yapılandırmayı, varsayılan tutarlılığı geçersiz kılmayı, oturum belirteçlerini el ile yönetmeyi ve olasılık açısından iyi sınırlı Stalet (PBS) ölçüsünü anladığınızı öğrenirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini yapılandırma

[Varsayılan tutarlılık düzeyi](consistency-levels.md) , istemcilerin varsayılan olarak kullandığı tutarlılık düzeyidir. İstemciler, her zaman geçersiz kılabilir.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Bu örnek, Doğu ABD ve Batı ABD bölgelerinde birden fazla yazma bölgesi etkin olan yeni bir Azure Cosmos hesabı oluşturur. Varsayılan tutarlılık düzeyi, *oturum* tutarlılığı olarak ayarlanır.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure portal

Varsayılan tutarlılık düzeyini görüntülemek veya değiştirmek için Azure portal oturum açın. Azure Cosmos hesabınızı bulun ve **varsayılan tutarlılık** bölmesini açın. Yeni varsayılan olarak istediğiniz tutarlılık düzeyini seçin ve ardından **Kaydet**' i seçin. Azure portal Ayrıca, müzik notlarıyla farklı tutarlılık seviyelerinin bir görselleştirmesi sağlar. 

![Azure portal tutarlılık menüsü](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini geçersiz kılma

İstemciler, hizmet tarafından belirlenen varsayılan tutarlılık düzeyini geçersiz kılabilir. Tutarlılık düzeyi istek başına ayarlanabilir ve bu, hesap düzeyinde ayarlanan varsayılan tutarlılık düzeyini geçersiz kılar.

### <a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK’sı

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

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK’sı

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK’sı

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Oturum belirteçlerini kullanma

Azure Cosmos DB ' deki tutarlılık düzeylerinden biri *oturum* tutarlılığı. Bu, varsayılan olarak Cosmos hesaplarına uygulanan varsayılan düzeydir. *Oturum* tutarlılığı ile çalışırken, istemci, ayarlanan tutarlılık düzeyinin korunmasını sağlamak için her bir okuma/sorgu isteğiyle dahili olarak bir oturum belirteci kullanır.

Oturum belirteçlerini el ile yönetmek için, yanıttan oturum belirtecini alın ve istek başına bunları ayarlayın. Oturum belirteçlerini el ile yönetmeniz gerekmiyorsa, bu örnekleri kullanmanız gerekmez. SDK oturum belirteçlerini otomatik olarak izler. Oturum belirtecini el ile ayarlamazsanız, SDK varsayılan olarak en son oturum belirtecini kullanır.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK’sı

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK’sı

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK’sı

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

Ne kadar nihai tutarlılık? Ortalama durumda, sürüm geçmişi ve saatine göre, eskime sınırları sunarız. Olasılığa göre [**sınırlı stalet (PBS)** ](https://pbs.cs.berkeley.edu/) ölçümü, eskime olasılığını ölçmenizi ve bunu bir ölçüm olarak gösterir. PBS ölçüsünü görüntülemek için Azure portal Azure Cosmos hesabınıza gidin. **Ölçümler** bölmesini açın ve **tutarlılık** sekmesini seçin. **İş yükünüze göre sağlam tutarlı okumaların olasılığı adlı grafiğe bakın (bkz. PBS)** .

![Azure portal PBS grafiği](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Sonraki adımlar

Veri çakışmalarını yönetme veya Azure Cosmos DB bir sonraki anahtar kavramına geçme hakkında daha fazla bilgi edinin. Aşağıdaki makalelere bakın:

* [Azure Cosmos DB 'deki tutarlılık düzeyleri](consistency-levels.md)
* [Bölgeler arasındaki çakışmaları yönetme](how-to-manage-conflicts.md)
* [Bölümleme ve veri dağıtımı](partition-data.md)
* [Modern dağıtılmış veritabanı sistemleri tasarımında tutarlılık avantajları](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Yüksek kullanılabilirlik](high-availability.md)
* [SLA Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
