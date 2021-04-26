---
title: SQL API Azure Cosmos DB veritabanı üretimini sağlama
description: Azure portal, CLı, PowerShell ve çeşitli diğer SDK 'Ları kullanarak Azure Cosmos DB SQL API 'sindeki veritabanı düzeyinde üretilen iş sağlamayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342011"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Azure Cosmos DB-SQL API 'sinde bir veritabanında standart (el ile) aktarım hızı sağlama
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB SQL API 'sindeki bir veritabanında standart (el ile) işleme sağlama açıklanır. Tek bir [kapsayıcıya](how-to-provision-container-throughput.md) aktarım hızı sağlayabilir veya aktarım hızını veritabanına sağlayıp içindeki kapsayıcılar arasında paylaştırabilirsiniz. Kapsayıcı düzeyi ve veritabanı düzeyi işleme ne zaman kullanacağınızı öğrenmek için bkz. [kapsayıcılar ve veritabanlarında üretilen iş sağlama Için kullanım örnekleri](set-throughput.md) makalesi. Azure portal veya Azure Cosmos DB SDK 'larını kullanarak veritabanı düzeyinde aktarım hızı sağlayabilirsiniz.

Farklı bir API kullanıyorsanız, üretilen işi sağlamak için [MongoDB Için API](how-to-provision-throughput-mongodb.md), [Cassandra API](how-to-provision-throughput-cassandra.md)ve [Gremlin API](how-to-provision-throughput-gremlin.md) makalelerini inceleyin.

## <a name="provision-throughput-using-azure-portal"></a>Azure portalını kullanarak aktarım hızı sağlama

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya mevcut bir Azure Cosmos hesabı seçin.

1. **Veri Gezgini** bölmesini açın ve **Yeni veritabanı**' nı seçin. Aşağıdaki ayrıntıları sağlayın:

   * Bir veritabanı KIMLIĞI girin.
   * **Veritabanı Işleme sağlama** seçeneğini belirleyin.
   * Bir üretilen iş girin (örneğin, 1000 ru).
   * **Tamam**’ı seçin.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Yeni veritabanı iletişim kutusunun ekran görüntüsü":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLı veya PowerShell kullanarak verimlilik sağlama

Paylaşılan verimlilik içeren bir veritabanı oluşturmak için bkz.

* [Azure CLı kullanarak veritabanı oluşturma](manage-with-cli.md#create-a-database-with-shared-throughput)
* [PowerShell kullanarak veritabanı oluşturma](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK’sını kullanarak aktarım hızı sağlama

> [!Note]
> Tüm API 'Ler için üretilen iş sağlamak üzere SQL API 'SI için Azure Cosmos SDK 'larını kullanabilirsiniz. İsteğe bağlı olarak, Cassandra API için aşağıdaki örneği de kullanabilirsiniz.

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

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de sağlanan aktarım hızı hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Sağlanan aktarım hızını küresel olarak ölçeklendirme](./request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Bir kapsayıcı için standart (el ile) üretilen iş sağlama](how-to-provision-container-throughput.md)
* [Bir kapsayıcı için otomatik ölçeklendirme üretilen işi sağlama](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)