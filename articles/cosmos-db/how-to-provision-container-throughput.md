---
title: Azure Cosmos DB SQL API 'de kapsayıcı performansı sağlama
description: Azure portal, CLı, PowerShell ve çeşitli diğer SDK 'Ları kullanarak Azure Cosmos DB SQL API 'sindeki kapsayıcı düzeyinde aktarım hızı sağlamayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100108"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Azure Cosmos kapsayıcısında standart (el ile) aktarım hızı sağlama-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB SQL API 'sindeki bir kapsayıcıda standart (el ile) işleme sağlama açıklanır. Tek bir kapsayıcıda üretilen iş sağlama veya [bir veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md) ve veritabanı içindeki kapsayıcılar arasında paylaşma sağlayabilirsiniz. Azure portal, Azure CLı veya Azure Cosmos DB SDK 'Ları kullanarak bir kapsayıcıda aktarım hızı sağlayabilirsiniz.

Farklı bir API kullanıyorsanız, üretilen işi sağlamak için [MongoDB Için API](how-to-provision-throughput-mongodb.md), [Cassandra API](how-to-provision-throughput-cassandra.md)ve [Gremlin API](how-to-provision-throughput-gremlin.md) makalelerini inceleyin.

## <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni kapsayıcı**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir kapsayıcı KIMLIĞI girin.
   * Bir bölüm anahtarı değeri girin (örneğin, `/ItemID` ).
   * Sağlamak istediğiniz bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Yeni koleksiyonla vurgulanan Veri Gezgini ekran görüntüsü":::

## <a name="azure-cli-or-powershell"></a>Azure CLı veya PowerShell

Adanmış aktarım hızı içeren bir kapsayıcı oluşturmak için bkz.

* [Azure CLI kullanarak kapsayıcı oluşturma](manage-with-cli.md#create-a-container)
* [PowerShell kullanarak kapsayıcı oluşturma](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra ve MongoDB API hariç tüm Cosmos DB API 'Leri için üretilen iş sağlamak üzere SQL API için Cosmos SDK 'larını kullanın.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK'sı

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de üretilen iş sağlama hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Bir veritabanında standart (el ile) işleme sağlama](how-to-provision-database-throughput.md)
* [Veritabanında otomatik ölçeklendirme üretilen işi sağlama](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
