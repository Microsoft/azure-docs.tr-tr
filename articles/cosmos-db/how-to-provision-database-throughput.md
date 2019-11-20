---
title: Azure Cosmos DB’de veritabanı aktarım hızını sağlama
description: Azure Cosmos DB’de aktarım hızını veritabanı düzeyinde sağlamayı öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 64a8bc9f4c9f5192dad22cb55cb4d2c4816d4fa5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575151"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB bir veritabanında üretilen iş sağlama

Bu makalede, Azure Cosmos DB bir veritabanında işleme sağlama açıklanmaktadır. Tek bir [kapsayıcıya](how-to-provision-container-throughput.md) aktarım hızı sağlayabilir veya aktarım hızını veritabanına sağlayıp içindeki kapsayıcılar arasında paylaştırabilirsiniz. Kapsayıcı düzeyinde ve veritabanı düzeyinde üretilen iş verimini ne zaman kullanacağınızı öğrenmek için bkz. [kapsayıcılar ve veritabanlarında üretilen iş sağlama Için kullanım örnekleri](set-throughput.md) makalesi. Azure portal veya Azure Cosmos DB SDK 'larını kullanarak veritabanı düzeyinde aktarım hızı sağlayabilirsiniz.

## <a name="provision-throughput-using-azure-portal"></a>Azure portalını kullanarak aktarım hızı sağlama

### <a id="portal-sql"></a>SQL (Core) API

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni veritabanı**' nı seçin. Aşağıdaki ayrıntıları sağlayın:

   * Bir veritabanı KIMLIĞI girin.
   * **Sağlama işleme**' yı seçin.
   * Bir üretilen iş girin (örneğin, 1000 ru).
   * **Tamam**’ı seçin.

    ![Yeni veritabanı iletişim kutusunun ekran görüntüsü](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLı veya PowerShell kullanarak verimlilik sağlama

Paylaşılan verimlilik içeren bir veritabanı oluşturmak için bkz.

* [Azure CLı kullanarak veritabanı oluşturma](manage-with-cli.md#create-a-database-with-shared-throughput)
* [PowerShell kullanarak veritabanı oluşturma](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK’sını kullanarak aktarım hızı sağlama

> [!Note]
> Tüm API 'Ler için üretilen iş sağlamak üzere SQL API için Cosmos SDK 'larını kullanabilirsiniz. İsteğe bağlı olarak, Cassandra API için aşağıdaki örneği de kullanabilirsiniz.

### <a id="dotnet-all"></a>Tüm API’ler

### <a name="net-v2-sdk"></a>.Net v2 SDK

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

### <a name="net-v3-sdk"></a>.Net v3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API’si
Benzer komut, tüm CQL uyumlu sürücüler aracılığıyla yürütülebilir. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de sağlanan aktarım hızı hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Küresel olarak sağlanan verimlilik ölçeği](scaling-throughput.md)
* [Kapsayıcılar ve veritabanları üzerinde üretilen iş sağlama](set-throughput.md)
* [Kapsayıcı için aktarım hızı sağlama](how-to-provision-container-throughput.md)
* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
