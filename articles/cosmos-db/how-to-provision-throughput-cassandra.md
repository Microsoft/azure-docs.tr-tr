---
title: Azure Cosmos DB Cassandra API kaynaklarında üretilen iş sağlama
description: Azure Cosmos DB Cassandra API kaynaklarında kapsayıcı, veritabanı ve otomatik ölçeklendirme işleme sağlamayı öğrenin. Azure portal, CLı, PowerShell ve diğer çeşitli SDK 'Ları kullanacaksınız.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086287"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Azure Cosmos DB Cassandra API kaynaklarında veritabanı, kapsayıcı veya otomatik ölçeklendirme üretilen işi sağlama
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede, Azure Cosmos DB Cassandra API aktarım hızını sağlama açıklanmaktadır. Bir kapsayıcıda veya veritabanında standart (el ile) veya otomatik ölçeklendirme üretilen işi sağlayabilir ve veritabanı içindeki kapsayıcılar arasında paylaşabilirsiniz. Azure portal, Azure CLı veya Azure Cosmos DB SDK 'Ları kullanarak üretilen iş sağlayabilirsiniz.

Farklı bir API kullanıyorsanız, üretilen işi sağlamak için bkz. [SQL API](how-to-provision-container-throughput.md), [MongoDB için API](how-to-provision-throughput-mongodb.md), [Gremlin API](how-to-provision-throughput-gremlin.md) makaleleri.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure portal

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni tablo**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir anahtar alanı mi yoksa mevcut bir anahtar alanı mı kullandığınızı belirtin. Anahtar alanı düzeyinde aktarım hızı sağlamak istiyorsanız **Veritabanı aktarım hızı sağla** seçeneğini belirleyin.
   * CQL komutu içinde tablo KIMLIĞINI girin.
   * Birincil anahtar değeri girin (örneğin, `/userrID` ).
   * Sağlamak istediğiniz bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Veritabanı düzeyinde aktarım hızı ile yeni bir koleksiyon oluştururken Veri Gezgini ekran görüntüsü":::

> [!Note]
> Cassandra API ile yapılandırılmış bir Azure Cosmos hesabındaki kapsayıcıda aktarım hızı sağlıyorsanız, `/myPrimaryKey` bölüm anahtarı yolu için kullanın.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Cassandra tablosu için üretilen iş sağlama

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Benzer komutlar, CQL uyumlu herhangi bir sürücü aracılığıyla verilebilir.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Cassandra tablosunun aktarım hızını değiştirme veya değiştirme

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Benzer komut, tüm CQL uyumlu sürücüler aracılığıyla yürütülebilir.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager şablonlar, tüm Azure Cosmos DB API 'Leri için veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için [Azure Cosmos DB için bkz. Azure Resource Manager şablonları](templates-samples-cassandra.md) .

## <a name="azure-cli"></a>Azure CLI’si

Azure CLı, tüm Azure Cosmos DB API 'Leri için bir veritabanı veya kapsayıcı düzeyinde bir kaynak üzerinde otomatik ölçeklendirme üretilen işi sağlamak üzere kullanılabilir. Örnekler için bkz. [Azure CLI örnekleri Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, tüm Azure Cosmos DB API 'Leri için bir veritabanında veya kapsayıcı düzeyindeki kaynaklarda otomatik ölçeklendirme üretilen işi sağlamak için kullanılabilir. Örnekler için bkz. [Azure Cosmos DB için Azure PowerShell örnekleri](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de üretilen iş sağlama hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)