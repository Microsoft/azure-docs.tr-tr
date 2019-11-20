---
title: Azure Cosmos DB içinde yaşam süresi yapılandırma ve yönetme hakkında bilgi edinin
description: Azure Cosmos DB içinde yaşam süresi yapılandırma ve yönetme hakkında bilgi edinin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: e36b894ec9c7433bc114145e0de5f519f73486f1
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815906"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Azure Cosmos DB yaşam süresi yapılandırma

Azure Cosmos DB, kapsayıcı düzeyinde yaşam süresi (TTL) yapılandırmayı seçebilir veya kapsayıcının ayarından sonra öğeyi bir öğe düzeyinde geçersiz kılabilirsiniz. Bir kapsayıcı için Azure portal veya dile özgü SDK 'Ları kullanarak TTL yapılandırabilirsiniz. Öğe düzeyi TTL geçersiz kılmaları SDK 'lar kullanılarak yapılandırılabilir.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Azure portal kullanarak bir kapsayıcıda yaşam süresi etkinleştirme

Zaman aşımı olmadan bir kapsayıcıda canlı kalma süresini etkinleştirmek için aşağıdaki adımları kullanın. TTL 'nin öğe düzeyinde geçersiz kılınmasına izin vermek için bunu etkinleştirin. TTL 'yi saniye için sıfır olmayan bir değer girerek de ayarlayabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

2. Yeni bir Azure Cosmos hesabı oluşturun veya var olan bir hesabı seçin.

3. **Veri Gezgini** bölmesini açın.

4. Var olan bir kapsayıcıyı seçin, genişletin ve aşağıdaki değerleri değiştirin:

   * **Ölçek & ayarları** penceresini açın.
   * Bulma **ayarı** altında, **yaşam süresi**' nin altında.
   * **Açık (varsayılan)** **seçeneğini belirleyin veya SEÇIN ve bir** TTL değeri ayarlayın
   * Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

   ![Azure portal yaşam süresi yapılandırma](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* DefaultTimeToLive null olduğunda canlı kalma süresi kapalıdır
* DefaultTimeToLive-1 olduğunda yaşam süresi ayarı açık olur (varsayılan değildir)
* DefaultTimeToLive, başka bir Int değeri (0 dışında) olduğunda, canlı ayarınız ayarı açık olur

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Azure CLı veya PowerShell kullanarak bir kapsayıcıda yaşam süresi sağlama

Bir kapsayıcıda TTL oluşturmak veya etkinleştirmek için bkz.,

* [Azure CLı kullanarak TTL ile kapsayıcı oluşturma](manage-with-cli.md#create-a-container-with-ttl)
* [PowerShell kullanarak TTL ile kapsayıcı oluşturma](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>SDK kullanarak bir kapsayıcıda yaşam süresi sağlama

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>SDK kullanarak bir kapsayıcıda yaşam süresi belirleme

Bir kapsayıcıda yaşam süresi ayarlamak için saniye cinsinden zaman aralığını belirten sıfır olmayan bir pozitif sayı sağlamalısınız. Yapılandırılmış TTL değerine bağlı olarak, `_ts` öğesinin son değiştirilme zaman damgasından sonra kapsayıcıdaki tüm öğeler silinir.

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK 'Sı

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Bir öğe üzerinde yaşam süresi ayarlama

Bir kapsayıcıda yaşam için varsayılan bir zaman ayarlamaya ek olarak, bir öğe için bir süre için canlı olarak ayarlayabilirsiniz. Öğe düzeyinde yaşam süresi ayarı, o kapsayıcıdaki öğenin varsayılan TTL değerini geçersiz kılar.

* Bir öğe üzerindeki TTL 'yi ayarlamak için, öğenin son değiştirilme @no__t zaman damgasından sonra öğenin süresinin dolacağını belirten sıfır olmayan pozitif bir sayı sağlamanız gerekir.-0.

* Öğenin bir TTL alanı yoksa, varsayılan olarak kapsayıcıya ayarlanan TTL öğe için geçerlidir.

* TTL kapsayıcı düzeyinde devre dışıysa, kapsayıcıda TTL yeniden etkinleştirilene kadar öğedeki TTL alanı yok sayılır.

### <a id="portal-set-ttl-item"></a>Azure portal

Bir öğe üzerinde yaşam süresini etkinleştirmek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

2. Yeni bir Azure Cosmos hesabı oluşturun veya var olan bir hesabı seçin.

3. **Veri Gezgini** bölmesini açın.

4. Var olan bir kapsayıcıyı seçin, genişletin ve aşağıdaki değerleri değiştirin:

   * **Ölçek & ayarları** penceresini açın.
   * Bulma **ayarı** altında, **yaşam süresi**' nin altında.
   * **Açık (varsayılan)** seçeneğini belirleyin veya Select seçeneğini **BELIRLEYIN ve bir** TTL değeri ayarlayın. 
   * Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

5. Daha sonra, yaşam süresi ayarlamak istediğiniz öğeye gidin, `ttl` özelliğini ekleyin ve **Güncelleştir**' i seçin. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>.NET SDK (any)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK 'Sı

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

## <a name="reset-time-to-live"></a>Zamanı canlı olarak Sıfırla

Öğe üzerinde bir yazma veya güncelleştirme işlemi gerçekleştirerek bir öğe üzerinde canlı kalma süresini sıfırlayabilirsiniz. Yazma veya güncelleştirme işlemi, `_ts` ' ı geçerli saate ayarlar ve öğe için TTL 'nin süresinin dolacağı şekilde yeniden başlaması gerekir. Bir öğenin TTL 'sini değiştirmek isterseniz, alanı başka bir alanı güncelleştirdiğinizde de güncelleştirebilirsiniz.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Canlı kalma süresini kapat

Bir öğe üzerinde yaşam süresi ayarlandıysa ve artık bu öğenin süresinin dolmasını istemiyorsanız, öğeyi alabilir, TTL alanını kaldırabilir ve sunucudaki öğeyi değiştirebilirsiniz. TTL alanı öğeden kaldırıldığında, kapsayıcıya atanan varsayılan TTL değeri öğeye uygulanır. Bir öğenin süresinin dolmasını ve kapsayıcıdan TTL değerini devralmasını engellemek için TTL değerini-1 olarak ayarlayın.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Yaşam süresini devre dışı bırak

Bir kapsayıcıda yaşam süresini devre dışı bırakmak ve arka plan işleminin süresi geçmiş öğeleri denetlemesini durdurmak için, kapsayıcıdaki `DefaultTimeToLive` özelliği silinmelidir. Bu özelliğin silinmesi,-1 ' e ayarlamalarından farklıdır. -1 olarak ayarladığınızda, kapsayıcıya eklenen yeni öğeler sonsuza kadar canlı olur, ancak bu değeri kapsayıcıdaki belirli öğelerde geçersiz kılabilirsiniz. TTL özelliğini kapsayıcıdan kaldırdığınızda, önceki varsayılan TTL değerini açıkça geçersiz kılmamış olsa bile öğelerin süresi dolmayacaktır.

### <a id="dotnet-disable-ttl"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>.NET SDK v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalede yaşam süresi hakkında daha fazla bilgi edinin:

* [Yaşam süresi](time-to-live.md)
