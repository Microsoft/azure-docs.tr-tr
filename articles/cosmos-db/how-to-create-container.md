---
title: Azure Cosmos DB SQL API 'de kapsayıcı oluşturma
description: Azure portal, .NET, Java, Python, Node.js ve diğer SDK 'Ları kullanarak Azure Cosmos DB SQL API 'de kapsayıcı oluşturmayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006857"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API 'de kapsayıcı oluşturma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, SQL API Azure Cosmos DB bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Azure portal, Azure CLı, PowerShell veya desteklenen SDK 'Ları kullanarak nasıl kapsayıcı oluşturulacağını gösterir. Bu makalede bir kapsayıcı oluşturma, Bölüm anahtarını belirtme ve üretilen iş sağlama işlemlerinin nasıl yapılacağı gösterilir.

Bu makalede, SQL API Azure Cosmos DB bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Farklı bir API kullanıyorsanız, kapsayıcıyı oluşturmak için bkz. [MongoDB Için API](how-to-create-container-mongodb.md), [Cassandra API](how-to-create-container-cassandra.md), [gremlin API](how-to-create-container-gremlin.md)ve [tablo API'si](how-to-create-container-table.md) makaleleri.

> [!NOTE]
> Kapsayıcılar oluştururken, aynı ada ancak büyük küçük harflere sahip iki kapsayıcı oluşturmadığınızdan emin olun. Bunun nedeni, Azure platformunun bazı bölümlerinin büyük/küçük harfe duyarlı olmaması ve bu nedenle, bu tür adlara sahip kapsayıcılar ve telemetri ve eylemlerin karışmasına ve çakışmasına neden olabilir.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Azure portalını kullanarak kapsayıcı oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni kapsayıcı**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir kapsayıcı KIMLIĞI girin.
   * Bir bölüm anahtarı girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Yeni kapsayıcı vurgulanmış şekilde Veri Gezgini bölmesinin ekran görüntüsü":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Azure CLI kullanarak kapsayıcı oluşturma

[Azure CLI ile bir kapsayıcı oluşturun](manage-with-cli.md#create-a-container). Tüm Azure Cosmos DB API 'lerde tüm Azure CLı örneklerinin listelenmesi için bkz. [Azure Cosmos DB Için Azure CLI örnekleri](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>PowerShell kullanarak kapsayıcı oluşturma

[PowerShell ile bir kapsayıcı oluşturun](manage-with-powershell.md#create-container). Tüm Azure Cosmos DB API 'lerde tüm PowerShell örneklerinin listesi için bkz. [PowerShell örnekleri](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>.NET SDK'sını kullanarak kapsayıcı oluşturma

Bir koleksiyon oluştururken zaman aşımı özel durumuyla karşılaşırsanız, koleksiyonun başarıyla oluşturulduğunu doğrulamak için bir okuma işlemi yapın. Okuma işlemi, koleksiyon oluşturma işlemi başarılı olana kadar bir özel durum oluşturur. Oluşturma işlemi tarafından desteklenen durum kodlarının listesi için [Azure Cosmos DB Için http durum kodları](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) makalesine bakın.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](./account-databases-containers-items.md)