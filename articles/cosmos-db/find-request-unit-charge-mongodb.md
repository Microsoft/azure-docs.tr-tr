---
title: MongoDB işlemlerine yönelik Azure Cosmos DB API 'SI için istek birimi ücreti bulma
description: Azure Cosmos kapsayıcısına göre yürütülen MongoDB sorguları için istek birimi (RU) ücreti bulmayı öğrenin. Azure portal, MongoDB .NET, Java, Node.js sürücülerini kullanabilirsiniz.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801834"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sinde yürütülen işlemler için istek birimi ücreti bulma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir.

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB tarafından normalleştirilir ve İstek Birimi (veya kısa RU) ile ifade edilir. İstek ücreti, tüm veritabanı işlemleriniz tarafından tüketilen istek birimleridir. , Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlayan bir performans para birimi olarak RUs 'yi düşünebilirsiniz. Azure Cosmos kapsayıcınızla etkileşim kurmak için hangi API'yi kullanırsanız kullanın maliyetler her zaman RU cinsinden ölçülür. Veritabanı işleminin bir yazma, işaret okuma veya sorgu olup olmadığı, maliyetler her zaman RUs cinsinden ölçülür. Daha fazla bilgi için bkz. [İstek birimleri ve BT 'nin önemli konuları](request-units.md) makalesi.

Bu makalede, MongoDB için Azure Cosmos DB API 'sindeki bir kapsayıcıya karşı yürütülen herhangi bir işlem için [istek birimi](request-units.md) (ru) tüketimini bulabileceğiniz farklı yollar sunulmaktadır. Farklı bir API kullanıyorsanız, RU/s ücretine ulaşmak için bkz. [SQL API](find-request-unit-charge.md), [Cassandra API](find-request-unit-charge-cassandra.md), [gremlin API](find-request-unit-charge-gremlin.md)ve [tablo API'si](find-request-unit-charge-table.md) makaleleri.

RU ücreti adlı özel bir [veritabanı komutu](https://docs.mongodb.com/manual/reference/command/) tarafından sunulur `getLastRequestStatistics` . Komutu, yürütülen son işlemin adını, istek ücretine ve süresini içeren bir belge döndürür. MongoDB için Azure Cosmos DB API kullanıyorsanız, RU ücreti almak için birden çok seçeneğiniz vardır.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure Cosmos hesabı oluşturun](create-mongodb-dotnet.md#create-a-database-account) ve verilerle birlikte akışı yapın veya zaten veri içeren mevcut bir hesabı seçin.

1. **Veri Gezgini** bölmesine gidin ve üzerinde çalışmak istediğiniz kapsayıcıyı seçin.

1. Kapsayıcı adının yanındaki **...** öğesini seçin ve **Yeni sorgu**' yı seçin.

1. Geçerli bir sorgu girin ve sonra **sorguyu Yürüt**' ü seçin.

1. Yürüttüğünüz istek için gerçek istek ücreti göstermek üzere **sorgu istatistikleri** ' ni seçin. Bu sorgu Düzenleyicisi, yalnızca sorgu koşullarına yönelik istek birimi ücretlerini çalıştırmanızı ve görüntülemenizi sağlar. INSERT deyimleri gibi veri işleme komutları için bu düzenleyiciyi kullanamazsınız.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure portal MongoDB sorgu isteği ücreti ekran görüntüsü":::

1. Veri işleme komutlarına yönelik istek ücretleri almak için, `getLastRequestStatistics` komutu Mongo kabuğu, [Robo 3T](mongodb-robomongo.md), [MongoDB pusula](mongodb-compass.md)gibi bir kabuk tabanlı kullanıcı arabiriminden veya Shell komut dosyası ile vs Code uzantısıyla çalıştırın.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>MongoDB .NET sürücüsünü kullanma

[Resmi MongoDB .net sürücüsünü](https://docs.mongodb.com/ecosystem/drivers/csharp/)kullandığınızda, `RunCommand` yöntemini bir nesne üzerinde çağırarak komutları çalıştırabilirsiniz `IMongoDatabase` . Bu yöntem, soyut sınıfın bir uygulamasını gerektirir `Command<>` :

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Daha fazla bilgi için bkz. [hızlı başlangıç: MongoDB için Azure Cosmos DB API kullanarak .NET Web uygulaması oluşturma](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>MongoDB Java sürücüsünü kullanma


[Resmi MongoDB Java sürücüsünü](https://mongodb.github.io/mongo-java-driver/)kullandığınızda, `runCommand` metodu bir nesne üzerinde çağırarak komutları çalıştırabilirsiniz `MongoDatabase` :

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Daha fazla bilgi için bkz. [hızlı başlangıç: MongoDB için Azure Cosmos DB API ve Java SDK 'sı kullanarak Web uygulaması oluşturma](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js sürücüsünü kullanma

[Resmi MongoDB Node.js sürücüsünü](https://mongodb.github.io/node-mongodb-native/)kullandığınızda, `command` metodu bir nesne üzerinde çağırarak komutları çalıştırabilirsiniz `db` :

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Daha fazla bilgi için bkz. [hızlı başlangıç: mevcut bir MongoDB Node.js Web uygulamasını Azure Cosmos DB 'ye geçirme](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Sonraki adımlar

RU tüketiminizi en iyi duruma getirme hakkında bilgi edinmek için şu makalelere bakın:

* [Azure Cosmos DB'deki istek birimleri ve aktarım hızı](request-units.md)
* [Azure Cosmos DB’de sağlanan işlem hızını iyileştirme](optimize-cost-throughput.md)
* [Azure Cosmos DB'de sorgu maliyetini iyileştirme](./optimize-cost-reads-writes.md)