---
title: Azure Cosmos DB Cassandra API bir sorgu için istek birimi (RU) ücreti bulma
description: Azure Cosmos kapsayıcısına göre yürütülen Cassandra sorguları için istek birimi (RU) ücreti bulmayı öğrenin. RU ücretine ulaşmak için Azure portal, .NET ve Java sürücülerini kullanabilirsiniz.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b504702ad0c74ae9728c0a8b34fa94df26184f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200519"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB ' de yürütülen işlemler için istek birimi ücreti bulun Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir.

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB tarafından normalleştirilir ve İstek Birimi (veya kısa RU) ile ifade edilir. İstek ücreti, tüm veritabanı işlemleriniz tarafından tüketilen istek birimleridir. , Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayan bir performans para birimi olarak RUs 'yi düşünebilirsiniz. Azure Cosmos kapsayıcınızla etkileşim kurmak için hangi API'yi kullanırsanız kullanın maliyetler her zaman RU cinsinden ölçülür. Veritabanı işleminin bir yazma, işaret okuma veya sorgu olup olmadığı, maliyetler her zaman RUs cinsinden ölçülür. Daha fazla bilgi için bkz. [İstek birimleri ve BT 'nin önemli konuları](request-units.md) makalesi.

Bu makalede, Azure Cosmos DB Cassandra API bir kapsayıcıya karşı yürütülen herhangi bir işlem için [istek birimi](request-units.md) (ru) tüketimini bulabileceğiniz farklı yollar sunulmaktadır. Farklı bir API kullanıyorsanız, RU/s ücreti bulmak için bkz. [MongoDB Için API](find-request-unit-charge-mongodb.md), [SQL API](find-request-unit-charge.md), [gremlin API](find-request-unit-charge-gremlin.md)ve [tablo API'si](find-request-unit-charge-table.md) makaleleri.

Azure Cosmos DB Cassandra API karşı işlemler gerçekleştirdiğinizde RU ücreti, gelen yükünde adlı bir alan olarak döndürülür `RequestCharge` . RU ücretini almak için çeşitli seçenekleriniz vardır.

## <a name="use-the-net-sdk"></a>.NET SDK’yı kullanma

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)'yı kullandığınızda, `Info` bir nesnenin özelliği altında gelen yükü alabilirsiniz `RowSet` :

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Daha fazla bilgi için bkz. [hızlı başlangıç: .NET SDK ve Azure Cosmos DB kullanarak Cassandra uygulaması oluşturma](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Java SDK 'sını kullanma

[Java SDK 'sını](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)kullandığınızda, `getExecutionInfo()` yöntemi bir nesne üzerinde çağırarak gelen yükü alabilirsiniz `ResultSet` :

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Java SDK 'sını kullanarak Cassandra uygulaması derleme ve Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Sonraki adımlar

RU tüketiminizi en iyi duruma getirme hakkında bilgi edinmek için şu makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)