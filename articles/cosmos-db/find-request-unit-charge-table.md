---
title: Azure Cosmos DB Tablo API'si bir sorgu için istek birimi (RU) ücreti bulma
description: Azure Cosmos kapsayıcısına göre yürütülen Tablo API'si sorguları için istek birimi (RU) ücreti bulmayı öğrenin. RU ücretine ulaşmak için Azure portal, .NET, Java, Python ve Node.js dillerini kullanabilirsiniz.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201306"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB ' de yürütülen işlemler için istek birimi ücreti bulun Tablo API'si
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir.

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB tarafından normalleştirilir ve İstek Birimi (veya kısa RU) ile ifade edilir. İstek ücreti, tüm veritabanı işlemleriniz tarafından tüketilen istek birimleridir. , Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayan bir performans para birimi olarak RUs 'yi düşünebilirsiniz. Azure Cosmos kapsayıcınızla etkileşim kurmak için hangi API'yi kullanırsanız kullanın maliyetler her zaman RU cinsinden ölçülür. Veritabanı işleminin bir yazma, işaret okuma veya sorgu olup olmadığı, maliyetler her zaman RUs cinsinden ölçülür. Daha fazla bilgi için bkz. [İstek birimleri ve BT 'nin önemli konuları](request-units.md) makalesi.

Bu makalede, Azure Cosmos DB Tablo API'si bir kapsayıcıya karşı yürütülen herhangi bir işlem için [istek birimi](request-units.md) (ru) tüketimini bulabileceğiniz farklı yollar sunulmaktadır. Farklı bir API kullanıyorsanız, RU/s ücreti bulmak için bkz. [MongoDB Için API](find-request-unit-charge-mongodb.md), [Cassandra API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)ve [SQL API](find-request-unit-charge.md) makaleleri.

## <a name="use-the-net-sdk"></a>.NET SDK’yı kullanma

Şu anda, tablo işlemleri için RU ücreti döndüren tek SDK [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)olur. `TableResult`Nesnesi, `RequestCharge` Azure Cosmos db tablo API'si KARŞı kullandığınızda SDK tarafından doldurulan bir özelliği kullanıma sunar:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Daha fazla bilgi için bkz. [hızlı başlangıç: .NET SDK ve Azure Cosmos DB kullanarak tablo API'si uygulama oluşturma](create-table-dotnet.md).

## <a name="next-steps"></a>Sonraki adımlar

RU tüketiminizi en iyi duruma getirme hakkında bilgi edinmek için şu makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)