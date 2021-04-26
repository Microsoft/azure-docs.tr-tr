---
title: Azure Cosmos DB Gremlin API sorguları için istek birimi (RU) ücreti bulma
description: Azure Cosmos kapsayıcısına göre yürütülen Gremlin sorguları için istek birimi (RU) ücreti bulmayı öğrenin. RU ücreti bulmak için Azure portal, .NET, Java sürücülerini kullanabilirsiniz.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201353"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API 'de yürütülen işlemler için istek birimi ücreti bulma
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir.

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB tarafından normalleştirilir ve İstek Birimi (veya kısa RU) ile ifade edilir. İstek ücreti, tüm veritabanı işlemleriniz tarafından tüketilen istek birimleridir. , Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayan bir performans para birimi olarak RUs 'yi düşünebilirsiniz. Azure Cosmos kapsayıcınızla etkileşim kurmak için hangi API'yi kullanırsanız kullanın maliyetler her zaman RU cinsinden ölçülür. Veritabanı işleminin bir yazma, işaret okuma veya sorgu olup olmadığı, maliyetler her zaman RUs cinsinden ölçülür. Daha fazla bilgi için bkz. [İstek birimleri ve BT 'nin önemli konuları](request-units.md) makalesi.

Bu makalede, Azure Cosmos DB Gremlin API 'sindeki bir kapsayıcıya karşı yürütülen herhangi bir işlem için [istek birimi](request-units.md) (ru) tüketimini bulabileceğiniz farklı yollar sunulmaktadır. Farklı bir API kullanıyorsanız, RU/s ücretine ulaşmak için bkz. [MongoDB Için API](find-request-unit-charge-mongodb.md), [CASSANDRA API](find-request-unit-charge-cassandra.md), [SQL API](find-request-unit-charge.md)ve [tablo API'si](find-request-unit-charge-table.md) makaleleri.

Gremlin API tarafından döndürülen üstbilgiler, şu anda Gremlin .NET ve Java SDK 'Sı tarafından ortaya çıkacak olan özel durum öznitelikleriyle eşlenir. İstek ücreti, anahtar altında kullanılabilir `x-ms-request-charge` . Gremlin API 'yi kullandığınızda, bir Azure Cosmos kapsayıcısına karşı bir işlemin RU tüketimini bulmak için birden çok seçeneğiniz vardır.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-graph-gremlin-console.md#create-a-database-account) ve verilerle birlikte akışı yapın veya zaten veri içeren mevcut bir hesabı seçin.

1. **Veri Gezgini** bölmesine gidin ve üzerinde çalışmak istediğiniz kapsayıcıyı seçin.

1. Geçerli bir sorgu girin ve ardından **Gremlin sorgusunu Yürüt**' ü seçin.

1. Yürüttüğünüz istek için gerçek istek ücreti göstermek üzere **sorgu istatistikleri** ' ni seçin.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Azure portal Gremlin sorgu isteği ücreti almak için ekran görüntüsü":::

## <a name="use-the-net-sdk-driver"></a>.NET SDK sürücüsünü kullanma

[Gremlin.NET SDK 'sını](https://www.nuget.org/packages/Gremlin.Net/)kullandığınızda, durum öznitelikleri `StatusAttributes` nesnenin özelliği altında kullanılabilir `ResultSet<>` :

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Cosmos DB Gremlin API hesabı kullanarak .NET Framework veya çekirdek uygulama oluşturma](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Java SDK sürücüsünü kullanma

[Gremlin Java SDK 'sını](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)kullandığınızda, `statusAttributes()` nesne üzerinde yöntemini çağırarak durum özniteliklerini alabilirsiniz `ResultSet` :

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Java SDK 'sını kullanarak Azure Cosmos DB grafik veritabanı oluşturma](create-graph-java.md).

## <a name="next-steps"></a>Sonraki adımlar

RU tüketiminizi en iyi duruma getirme hakkında bilgi edinmek için şu makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)