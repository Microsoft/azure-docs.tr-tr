---
title: MongoDB kaynakları için Azure Cosmos DB API üzerinde üretilen iş sağlama
description: MongoDB kaynakları için Azure Cosmos DB API 'de kapsayıcı, veritabanı ve otomatik ölçeklendirme işleme sağlamayı öğrenin. Azure portal, CLı, PowerShell ve diğer çeşitli SDK 'Ları kullanacaksınız.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086134"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>MongoDB kaynakları için Azure Cosmos DB API 'sinde veritabanı, kapsayıcı veya otomatik ölçeklendirme performansı sağlama
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu makalede, MongoDB için Azure Cosmos DB API 'sinde üretilen iş sağlama açıklanmaktadır. Bir kapsayıcıda veya veritabanında standart (el ile) veya otomatik ölçeklendirme üretilen işi sağlayabilir ve veritabanı içindeki kapsayıcılar arasında paylaşabilirsiniz. Azure portal, Azure CLı veya Azure Cosmos DB SDK 'Ları kullanarak üretilen iş sağlayabilirsiniz.

Farklı bir API kullanıyorsanız, işleme sağlamak için [SQL API](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) makaleleri bölümüne bakın.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure portal

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni koleksiyon**' u seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin. Veritabanı düzeyinde aktarım hızı sağlamak istiyorsanız **Veritabanı aktarım hızı sağla** seçeneğini belirleyin.
   * Bir koleksiyon KIMLIĞI girin.
   * Bir bölüm anahtarı değeri girin (örneğin, `/ItemID` ).
   * Sağlamak istediğiniz bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Veritabanı düzeyinde aktarım hızı ile yeni bir koleksiyon oluştururken Veri Gezgini ekran görüntüsü":::

> [!Note]
> MongoDB için Azure Cosmos DB API 'siyle yapılandırılmış bir Azure Cosmos hesabındaki kapsayıcıda aktarım hızı sağlıyorsanız, `/myShardKey` bölüm anahtarı yolu için kullanın.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager şablonlar, tüm Azure Cosmos DB API 'Leri için veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için [Azure Cosmos DB için bkz. Azure Resource Manager şablonları](templates-samples-mongodb.md) .

## <a name="azure-cli"></a>Azure CLI’si

Azure CLı, tüm Azure Cosmos DB API 'Leri için bir veritabanı veya kapsayıcı düzeyinde bir kaynak üzerinde otomatik ölçeklendirme üretilen işi sağlamak üzere kullanılabilir. Örnekler için bkz. [Azure CLI örnekleri Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, tüm Azure Cosmos DB API 'Leri için bir veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için bkz. [Azure Cosmos DB için Azure PowerShell örnekleri](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de üretilen iş sağlama hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)