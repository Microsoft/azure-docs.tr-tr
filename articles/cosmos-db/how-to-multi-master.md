---
title: Azure Cosmos DB içinde çok bölgeli yazmaları yapılandırma
description: Azure Cosmos DB 'de farklı SDK 'Ları kullanarak uygulamalarınız için çok bölgeli yazma işlemlerini nasıl yapılandıracağınızı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 08d50b18605fd833e6b0efca987338d0ca1eef8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102488520"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB kullanan uygulamalarınızda çok bölgeli yazmaları yapılandırma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Birden fazla yazma bölgesi etkinken bir hesap oluşturulduktan sonra, Azure Cosmos DB içinde çok bölgeli yazmaları etkinleştirmek üzere Cosmos istemcisinin ConnectionPolicy öğesinde uygulamanızda iki değişiklik yapmanız gerekir. ConnectionPolicy içinde UseMultipleWriteLocations öğesini true olarak ayarlayın ve uygulamanın ApplicationRegion 'a dağıtıldığı bölgenin adını geçirin. Bu işlem, PreferredLocations özelliğini geçirilen konumdan coğrafi yakınlık temelinde doldurur. Yeni bir bölge daha sonra hesaba eklenirse, uygulamanın güncellenmesi veya yeniden dağıtılması gerekmez, daha yakın bölgeyi otomatik olarak algılar ve bölgesel bir olay oluşması gerekir.

> [!Note]
> Başlangıçta tek bir yazma bölgesi ile yapılandırılan Cosmos hesapları, sıfır saati olan birden fazla yazma bölgesine yapılandırılabilir. Daha fazla bilgi edinmek için bkz. [birden çok yazma bölgelerini yapılandırma](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="azure-portal"></a><a id="portal"></a> Azure portal

Azure portal çok bölgeli yazmaları etkinleştirmek için aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure Cosmos hesabınıza gidin ve menüden **verileri küresel olarak Çoğalt** bölmesini açın.

1. **Çok bölgeli yazma** seçeneği altında **Etkinleştir**' i seçin. Otomatik olarak, mevcut bölgeleri okuma ve yazma bölgelerine ekler.

1. Haritadaki simgeleri seçerek veya **bölge Ekle** düğmesini seçerek ek bölgeler ekleyebilirsiniz. Eklediğiniz tüm bölgelerde hem okuma hem de yazma işlemleri etkinleştirilir.

1. Bölge listesini güncelleştirdikten sonra değişiklikleri uygulamak için **Kaydet** ' i seçin.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Azure portal kullanarak çok bölgeli yazmaları etkinleştirme ekran görüntüsü" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Uygulamanızda çok bölgeli yazmaları etkinleştirmek için `UseMultipleWriteLocations` olarak ayarlayın `true` . Ayrıca, `SetCurrentLocation` uygulamanın dağıtıldığı bölgeye ve Azure Cosmos DB nerede çoğaltıldığına ayarlanır:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Uygulamanızda çok bölgeli yazmaları etkinleştirmek için, `ApplicationRegion` uygulamanın dağıtıldığı bölgeye ve Cosmos DB nerede çoğaltıldığına ayarlayın:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

İsteğe bağlı olarak, `CosmosClientBuilder` `WithApplicationRegion` aynı sonucu elde etmek için ve kullanabilirsiniz:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java v4 SDK 'Sı

Uygulamanızda çok bölgeli yazma işlemlerini etkinleştirmek için `.multipleWriteRegionsEnabled(true)` ve `.preferredRegions(preferredRegions)` istemci Oluşturucu 'da ve `preferredRegions` `List` uygulamanın dağıtıldığı bölge olan ve Cosmos DB nerede çoğaltılacağı bir öğe olan istemci Oluşturucusu 'nda öğesini çağırın:

# <a name="async"></a>[Eş](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) zaman uyumsuz API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Eşitle](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) eşitleme API 'si

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Zaman uyumsuz Java v2 SDK

Java v2 SDK 'Sı Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)'yi kullandı. Uygulamanızda çok bölgeli yazmaları etkinleştirmek için, ayarlayın `policy.setUsingMultipleWriteLocations(true)` ve `policy.setPreferredLocations` uygulamanın dağıtıldığı bölgeye ve Cosmos DB nerede çoğaltıldığından ayarlayın:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript ve TypeScript SDK 'Ları

Uygulamanızda çok bölgeli yazmaları etkinleştirmek için `connectionPolicy.UseMultipleWriteLocations` olarak ayarlayın `true` . Ayrıca, `connectionPolicy.PreferredLocations` uygulamanın dağıtıldığı bölgeye ve Cosmos DB nerede çoğaltıldığına ayarlanır:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK 'Sı

Uygulamanızda çok bölgeli yazmaları etkinleştirmek için `connection_policy.UseMultipleWriteLocations` olarak ayarlayın `true` . Ayrıca, `connection_policy.PreferredLocations` uygulamanın dağıtıldığı bölgeye ve Cosmos DB nerede çoğaltıldığına ayarlanır.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makaleleri okuyun:

* [Azure Cosmos DB tutarlılığı yönetmek için oturum belirteçlerini kullanın](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB çakışma türleri ve çözüm ilkeleri](conflict-resolution-policies.md)
* [Azure Cosmos DB yüksek kullanılabilirlik](high-availability.md)
* [Azure Cosmos DB'deki tutarlılık düzeyleri](consistency-levels.md)
* [Azure Cosmos DB doğru tutarlılık düzeyini seçin](./consistency-levels.md)
* [Azure Cosmos DB tutarlılık, kullanılabilirlik ve performans avantajları](./consistency-levels.md)
* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans avantajları](./consistency-levels.md)
* [Sağlanan aktarım hızını küresel olarak ölçeklendirme](./request-units.md)
* [Genel dağıtım: devlet](global-dist-under-the-hood.md)