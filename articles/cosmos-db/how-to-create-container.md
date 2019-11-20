---
title: Azure Cosmos DB bir kapsayıcı oluşturma
description: Azure Cosmos DB bir kapsayıcı oluşturmayı öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 911f56cef58e5d9c161a3ba947850bcfff073200
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242401"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısı oluşturma

Bu makalede, Azure Cosmos kapsayıcısı (koleksiyon, tablo veya grafik) oluşturmanın farklı yolları açıklanmaktadır. Bu için Azure portal, Azure CLı veya desteklenen SDK 'lar kullanabilirsiniz. Bu makalede bir kapsayıcı oluşturma, Bölüm anahtarını belirtme ve üretilen iş sağlama işlemlerinin nasıl yapılacağı gösterilir.

## <a name="create-a-container-using-azure-portal"></a>Azure portal kullanarak kapsayıcı oluşturma

### <a id="portal-sql"></a>SQL APı 'SI

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni kapsayıcı**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir kapsayıcı KIMLIĞI girin.
   * Bir bölüm anahtarı girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam ' ı**seçin.

    ![Yeni kapsayıcı vurgulanmış şekilde Veri Gezgini bölmesinin ekran görüntüsü](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>MongoDB için Azure Cosmos DB API 'SI

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni kapsayıcı**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir kapsayıcı KIMLIĞI girin.
   * Parça anahtarı girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam ' ı**seçin.

    ![MongoDB için Azure Cosmos DB API 'nin ekran görüntüsü, kapsayıcı ekleme iletişim kutusu](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-cassandra-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni tablo**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir keyspace mi, yoksa var olanı mi kullandığınızı belirtin.
   * Tablo adı girin.
   * Özellikleri girin ve bir birincil anahtar belirtin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam ' ı**seçin.

    ![Cassandra API, tablo Ekle iletişim kutusunun ekran görüntüsü](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API için birincil anahtar, bölüm anahtarı olarak kullanılır.

### <a id="portal-gremlin"></a>Gremlin API 'SI

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-graph-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **yeni grafik**' i seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Yeni bir veritabanı mı yoksa var olan bir veritabanını mı kullandığınızı belirtin.
   * Bir grafik KIMLIĞI girin.
   * **Sınırsız** depolama kapasitesi seçin.
   * Köşeler için bir bölüm anahtarı girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam ' ı**seçin.

    ![Gremlin API ekran görüntüsü, Graf Ekle iletişim kutusu](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Tablo API'si

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-table-dotnet.md#create-a-database-account)veya var olan bir hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni tablo**' yı seçin. Ardından, aşağıdaki ayrıntıları sağlayın:

   * Tablo KIMLIĞI girin.
   * Sağlanacak bir üretilen iş (örneğin, 1000 ru) girin.
   * **Tamam ' ı**seçin.

    ![Tablo API'si, tablo Ekle iletişim kutusunun ekran görüntüsü](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Tablo API'si için, her yeni satır eklediğiniz her seferinde bölüm anahtarı belirtilir.

## Azure CLı kullanarak kapsayıcı oluşturma<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Aşağıdaki bağlantılar, Azure CLı kullanarak Azure Cosmos DB için kapsayıcı kaynakları oluşturmayı gösterir.

Tüm Azure Cosmos DB API 'lerde tüm Azure CLı örneklerinin listesi için bkz. [SQL API](cli-samples.md), [Cassandra API](cli-samples-cassandra.md), [MongoDB API](cli-samples-mongodb.md), [Gremlin API](cli-samples-gremlin.md)ve [tablo API'si](cli-samples-table.md)

* [Azure CLı ile kapsayıcı oluşturma](manage-with-cli.md#create-a-container)
* [Azure CLı ile MongoDB API 'SI için Azure Cosmos DB bir koleksiyon oluşturma](./scripts/cli/mongodb/create.md)
* [Azure CLı ile Cassandra tablosu oluşturma](./scripts/cli/cassandra/create.md)
* [Azure CLı ile Gremlin grafiği oluşturma](./scripts/cli/gremlin/create.md)
* [Azure CLı ile Tablo API'si tablosu oluşturma](./scripts/cli/table/create.md)

## PowerShell<a id="ps-sql"></a>@no__t kullanarak kapsayıcı oluşturma-1<a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Aşağıdaki bağlantılar, PowerShell kullanarak Azure Cosmos DB için kapsayıcı kaynaklarının nasıl oluşturulacağını gösterir.

Tüm Azure Cosmos DB API 'lerde tüm Azure CLı örneklerinin listesi için bkz. [SQL API](powershell-samples-sql.md), [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)ve [tablo API'si](powershell-samples-table.md)

* [PowerShell ile kapsayıcı oluşturma](manage-with-powershell.md#create-container)
* [PowerShell ile MongoDB API 'SI için Azure Cosmos DB bir koleksiyon oluşturma](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [PowerShell ile Cassandra tablosu oluşturma](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [PowerShell ile Gremlin grafiği oluşturma](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [PowerShell ile Tablo API'si tablosu oluşturma](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>.NET SDK kullanarak kapsayıcı oluşturma

### <a id="dotnet-sql-graph"></a>SQL API ve Gremlin API 'SI

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

### <a id="dotnet-mongodb"></a>MongoDB için Azure Cosmos DB API 'SI

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB tel protokolü, [Istek birimi](request-units.md)kavramını anlamıyor. Üzerinde üretilen işleme ile yeni bir koleksiyon oluşturmak için SQL API için Azure portal veya Cosmos DB SDK 'larını kullanın.

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB bölümleniyor](partitioning-overview.md)
* [Azure Cosmos DB istek birimleri](request-units.md)
* [Kapsayıcılar ve veritabanları üzerinde üretilen iş sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](account-overview.md)
