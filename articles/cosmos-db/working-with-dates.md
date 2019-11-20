---
title: Azure Cosmos DB'de tarihlerle çalışma
description: Azure Cosmos DB'de tarihlerle çalışma hakkında bilgi edinin.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 9676642e96d437965fef041930b8223241cadeaa
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349033"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure cosmos DB'de tarihlerle çalışma
Azure Cosmos DB, şema esnekliği ve zengin dizin oluşturma yerel teslim [JSON](https://www.json.org) veri modeli. Veritabanları, kapsayıcılar, belgeler ve saklı yordamlar da dahil olmak üzere tüm Azure Cosmos DB kaynaklarını modellenir ve JSON belgeleri olarak depolanır. Taşınabilir olması için, JSON (ve Azure Cosmos DB) yalnızca küçük bir temel türler kümesini destekler: Dize, sayı, Boolean, dizi, nesne ve null. Ancak, JSON esnektir ve geliştiriciler ve çerçeveleri kullanarak bu temelleri ve nesne veya dizi oluşturmayı daha karmaşık türleri temsil etmek izin verin. 

Temel türlerin yanı sıra, birçok uygulamanın tarih ve zaman damgalarını temsil etmesi için tarih saat türüne ihtiyacı vardır. Bu makalede nasıl geliştiriciler depolamak, alabilir ve .NET SDK kullanarak Azure Cosmos DB tarihleri sorgu açıklanır.

## <a name="storing-datetimes"></a>Tarih/saat depolama

Azure Cosmos DB dize, sayı, Boolean, null, dizi, nesne gibi JSON türlerini destekler. Bir tarih saat türünü doğrudan desteklemez. Şu anda Azure Cosmos DB tarihlerin yerelleştirmesini desteklemez. Bu nedenle, DateTimes 'ı dizeler olarak depolamanız gerekir. Azure Cosmos DB `YYYY-MM-DDThh:mm:ss.sssZ` DateTime dizeleri için önerilen biçim ISO 8601 UTC standardını izler. Tüm tarihleri UTC olarak Azure Cosmos DB depolamanız önerilir. Tarih dizelerini bu biçime dönüştürmek, zaman sözcüıgrafik tarihleri sıralamasına izin verir. UTC dışı tarihler depolanıyorsa, mantığın istemci tarafında işlenmesi gerekir. Yerel bir tarih/saati UTC 'ye dönüştürmek için, fark JSON içinde bir özellik olarak bilinmeli/depolanmalıdır ve istemci UTC Tarih saat değerini hesaplamak için de sapmayı kullanabilir.

Çoğu uygulama, aşağıdaki nedenlerden dolayı DateTime için varsayılan dize gösterimi kullanabilirsiniz:

* Dizeleri karşılaştırma ve dizelere dönüştürülür, DateTime değerlerini göreli sıralamasını korunur. 
* Bu yaklaşım, herhangi bir özel kod veya öznitelikleri için JSON dönüştürme gerektirmez.
* JSON içinde depolanmış olarak tarihleri insan tarafından okunabilir.
* Bu yaklaşım, hızlı sorgu performansı için Azure Cosmos DB'nin dizini avantajlarından yararlanabilirsiniz.

Örneğin, aşağıdaki kod parçacığı depolayan bir `Order` içeren iki tarih/saat özellikleri - nesne `ShipDate` ve `OrderDate` .NET SDK'sını kullanarak bir belge olarak:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Bu belge, Azure Cosmos DB'de aşağıdaki şekilde depolanır:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternatif olarak, tarih/saat olarak Unix zaman damgası, diğer bir deyişle, 1 Ocak 1970'ten beri geçen saniyelerin sayısını gösteren bir sayı olarak depolayabilirsiniz. Azure Cosmos DB'nin iç zaman damgası (`_ts`) özelliği, bu yaklaşım izler. Kullanabileceğiniz [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) tarih/saat sayı olarak seri hale sınıf. 

## <a name="indexing-datetimes-for-range-queries"></a>Tarih saat aralık sorguları için dizin oluşturma
Aralık sorguları içeren DateTime değerleri yaygındır. Örneğin, dünden beri oluşturulan tüm siparişleri bulmak veya son beş dakika içinde tüm siparişlere bulmak gerekiyorsa, aralık sorguları gerçekleştirmek gerekir. Bu sorguları verimli bir şekilde yürütmek için aralığı dizeleri dizinini oluşturmak için koleksiyonunuzun yapılandırmanız gerekir.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Dizin oluşturma ilkeleri yapılandırma hakkında daha fazla bilgi [Azure Cosmos DB dizinleme ilkeleri](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>LINQ tarih saatleri sorgulama
LINQ ile Azure Cosmos DB'de depolanan verileri sorgulama, SQL .NET SDK'sını otomatik olarak destekler. Örneğin, aşağıdaki kod parçacığı bir LINQ Sorgu son üç günde sevk edilen bu filtreleri siparişleri gösterir.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Azure Cosmos DB SQL sorgu dili ve LINQ sağlayıcısı hakkında daha fazla bilgi [Cosmos DB'yi sorgulama](how-to-sql-query.md).

Bu makalede, depolamak, dizin ve Azure Cosmos DB'de tarih saatleri sorgu inceledik.

## <a name="next-steps"></a>Sonraki Adımlar
* İndirme ve çalıştırma [github'daki kod örnekleri](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Daha fazla bilgi edinin [SQL sorguları](how-to-sql-query.md)
* Daha fazla bilgi edinin [Azure Cosmos DB dizinleme ilkeleri](index-policy.md)
