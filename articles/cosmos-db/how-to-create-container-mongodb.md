---
title: MongoDB için Azure Cosmos DB API 'sinde kapsayıcı oluşturma
description: Azure portal, .NET, Java, Node.js ve diğer SDK 'Ları kullanarak MongoDB için Azure Cosmos DB API 'de kapsayıcı oluşturmayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101604"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sinde kapsayıcı oluşturma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu makalede, MongoDB için Azure Cosmos DB API 'sinde bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Azure portal, Azure CLı, PowerShell veya desteklenen SDK 'lar kullanılarak nasıl kapsayıcı oluşturulacağını gösterir. Bu makalede bir kapsayıcı oluşturma, Bölüm anahtarını belirtme ve üretilen iş sağlama işlemlerinin nasıl yapılacağı gösterilir.

Bu makalede, MongoDB için Azure Cosmos DB API 'sinde bir kapsayıcı oluşturmanın farklı yolları açıklanmaktadır. Farklı bir API kullanıyorsanız, kapsayıcıyı oluşturmak için [SQL API](how-to-create-container.md), [Cassandra API](how-to-create-container-cassandra.md), [gremlin API](how-to-create-container-gremlin.md)ve [tablo API'si](how-to-create-container-table.md) makalelerini inceleyin.

> [!NOTE]
> Kapsayıcılar oluştururken, aynı ada ancak büyük küçük harflere sahip iki kapsayıcı oluşturmadığınızdan emin olun. Bunun nedeni, Azure platformunun bazı bölümlerinin büyük/küçük harfe duyarlı olmaması ve bu nedenle, bu tür adlara sahip kapsayıcılar ve telemetri ve eylemlerin karışmasına ve çakışmasına neden olabilir.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Azure portalını kullanarak oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni kapsayıcı**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir kapsayıcı KIMLIĞI girin.
   * Parça anahtarı girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="MongoDB için Azure Cosmos DB API 'nin ekran görüntüsü, kapsayıcı ekleme iletişim kutusu":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>.NET SDK kullanarak oluşturma

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB tel protokolü, [Istek birimi](request-units.md)kavramını anlamıyor. Üzerinde üretilen işleme ile yeni bir koleksiyon oluşturmak için SQL API için Azure portal veya Cosmos DB SDK 'larını kullanın.

Bir koleksiyon oluştururken zaman aşımı özel durumuyla karşılaşırsanız, koleksiyonun başarıyla oluşturulduğunu doğrulamak için bir okuma işlemi yapın. Okuma işlemi, koleksiyon oluşturma işlemi başarılı olana kadar bir özel durum oluşturur. Oluşturma işlemi tarafından desteklenen durum kodlarının listesi için [Azure Cosmos DB Için http durum kodları](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) makalesine bakın.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLı kullanarak oluşturma

[Azure CLI Ile MongoDB API 'si için Azure Cosmos DB bir koleksiyon oluşturun](./scripts/cli/mongodb/create.md). Tüm Azure Cosmos DB API 'lerde tüm Azure CLı örneklerinin listelenmesi için bkz. [Azure Cosmos DB Için Azure CLI örnekleri](cli-samples.md).

## <a name="create-using-powershell"></a>PowerShell kullanarak oluşturma

[MongoDB API 'si Için PowerShell ile Azure Cosmos DB için bir koleksiyon oluşturun](./scripts/powershell/mongodb/create.md). Tüm Azure Cosmos DB API 'lerde tüm PowerShell örneklerinin listesi için bkz. [PowerShell örnekleri](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak kapsayıcı oluşturma

[Kaynak Yöneticisi şablonuyla MongoDB API 'si için Azure Cosmos DB bir koleksiyon oluşturun](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](./account-databases-containers-items.md)