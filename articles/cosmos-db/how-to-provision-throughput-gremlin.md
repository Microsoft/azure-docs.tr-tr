---
title: Azure Cosmos DB Gremlin API kaynaklarında üretilen iş sağlama
description: Azure Cosmos DB Gremlin API kaynaklarında kapsayıcı, veritabanı ve otomatik ölçeklendirme işleme sağlamayı öğrenin. Azure portal, CLı, PowerShell ve diğer çeşitli SDK 'Ları kullanacaksınız.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086168"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Azure Cosmos DB Gremlin API kaynaklarında veritabanı, kapsayıcı veya otomatik ölçeklendirme işleme sağlama
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Bu makalede, Azure Cosmos DB Gremlin API 'de üretilen iş sağlama açıklanmaktadır. Bir kapsayıcıda veya veritabanında standart (el ile) veya otomatik ölçeklendirme üretilen işi sağlayabilir ve veritabanı içindeki kapsayıcılar arasında paylaşabilirsiniz. Azure portal, Azure CLı veya Azure Cosmos DB SDK 'Ları kullanarak üretilen iş sağlayabilirsiniz.

Farklı bir API kullanıyorsanız, üretilen işi sağlamak için bkz. [SQL API](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [MongoDB makalelerine yönelik API](how-to-provision-throughput-mongodb.md) .

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure portal

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni grafik**' i seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin. Veritabanı düzeyinde aktarım hızı sağlamak istiyorsanız **Veritabanı aktarım hızı sağla** seçeneğini belirleyin.
   * Bir grafik KIMLIĞI girin.
   * Bir bölüm anahtarı değeri girin (örneğin, `/ItemID` ).
   * Sağlamak istediğiniz bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Veritabanı düzeyinde aktarım hızı ile yeni bir grafik oluştururken Veri Gezgini ekran görüntüsü":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra ve MongoDB API hariç tüm Azure Cosmos DB API 'Leri için üretilen iş sağlamak üzere SQL API için Cosmos SDK 'larını kullanın.

### <a name="provision-container-level-throughput"></a>Kapsayıcı düzeyinde üretilen iş sağlama

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

### <a name="provision-database-level-throughput"></a>Veritabanı düzeyinde üretilen iş sağlama

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager şablonlar, tüm Azure Cosmos DB API 'Leri için veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için [Azure Cosmos DB için bkz. Azure Resource Manager şablonları](templates-samples-gremlin.md) .

## <a name="azure-cli"></a>Azure CLI’si

Azure CLı, tüm Azure Cosmos DB API 'Leri için bir veritabanı veya kapsayıcı düzeyinde bir kaynak üzerinde otomatik ölçeklendirme üretilen işi sağlamak üzere kullanılabilir. Örnekler için bkz. [Azure CLI örnekleri Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, tüm Azure Cosmos DB API 'Leri için bir veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için bkz. [Azure Cosmos DB için Azure PowerShell örnekleri](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de üretilen iş sağlama hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)