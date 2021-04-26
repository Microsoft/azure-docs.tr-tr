---
title: Azure Cosmos DB tarihlerle çalışma
description: Azure Cosmos DB 'de DataTime nesnelerini depolamayı, dizinlemeyi ve sorgulamayı öğrenin
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359532"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure Cosmos DB tarihlerle çalışma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, yerel bir [JSON](https://www.json.org) veri modeli aracılığıyla şema esnekliği ve zengin dizin oluşturma sağlar. Veritabanları, kapsayıcılar, belgeler ve saklı yordamlar dahil tüm Azure Cosmos DB kaynakları, JSON belgeleri olarak modellenir ve depolanır. Taşınabilir olması için, JSON (ve Azure Cosmos DB) yalnızca küçük bir temel türler kümesini destekler: dize, sayı, Boolean, dizi, nesne ve null. Ancak, JSON esnektir ve geliştiricilerin ve çerçevelerin bu temel öğeleri kullanarak daha karmaşık türleri göstermesini ve bunları nesne veya dizi olarak oluşturma olanağı sağlar.

Temel türlerin yanı sıra, birçok uygulamanın tarih ve zaman damgalarını temsil etmesi için tarih saat türüne ihtiyacı vardır. Bu makalede, geliştiricilerin .NET SDK kullanarak Azure Cosmos DB tarihleri nasıl depolayabileceği, alabileceği ve Sorgulayabileceğiniz açıklanır.

## <a name="storing-datetimes"></a>Tarih zamanlarını depolama

Azure Cosmos DB dize, sayı, Boolean, null, dizi, nesne gibi JSON türlerini destekler. Bir tarih saat türünü doğrudan desteklemez. Şu anda Azure Cosmos DB tarihlerin yerelleştirmesini desteklemez. Bu nedenle, DateTimes 'ı dizeler olarak depolamanız gerekir. Azure Cosmos DB DateTime dizeleri için önerilen biçim `yyyy-MM-ddTHH:mm:ss.fffffffZ` ıso 8601 UTC standardını izler. Tüm tarihleri UTC olarak Azure Cosmos DB depolamanız önerilir. Tarih dizelerini bu biçime dönüştürmek, zaman sözcüıgrafik tarihleri sıralamasına izin verir. UTC dışı tarihler depolanıyorsa, mantığın istemci tarafında işlenmesi gerekir. Yerel bir tarih/saati UTC 'ye dönüştürmek için, fark JSON içinde bir özellik olarak bilinmeli/depolanmalıdır ve istemci UTC Tarih saat değerini hesaplamak için de sapmayı kullanabilir.

Filtre olarak DateTime dizeleri olan Aralık sorguları yalnızca DateTime dizelerinin UTC ve aynı uzunlukta olması durumunda desteklenir. Azure Cosmos DB, [GetCurrentDateTime](sql-query-getcurrentdatetime.md) System IşLEVI geçerli UTC Tarih ve saati ISO 8601 dize değerini şu biçimde döndürür: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Çoğu uygulama aşağıdaki nedenlerle tarih saat için varsayılan dize gösterimini kullanabilir:

* Dizeler karşılaştırılabilir ve tarih saat değerlerinin göreli sıralaması dizelere dönüştürüldüğünde korunur.
* Bu yaklaşım, JSON dönüştürmesi için özel kod veya öznitelik gerektirmez.
* JSON 'da depolanan tarihler insanlar tarafından okunabilir.
* Bu yaklaşım, hızlı sorgu performansı için Azure Cosmos DB dizininden yararlanabilir.

Örneğin, aşağıdaki kod parçacığı `Order` Iki DateTime özelliği içeren bir nesnesi `ShipDate` ve `OrderDate` .NET SDK kullanarak bir belge olarak depolar:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Bu belge Azure Cosmos DB ' de şu şekilde depolanır:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Alternatif olarak, 1 Ocak 1970 ' den itibaren geçen saniye sayısını temsil eden bir sayı olarak DateTimes 'ı UNIX zaman damgaları olarak saklayabilirsiniz. Azure Cosmos DB iç zaman damgası ( `_ts` ) özelliği bu yaklaşımı izler. [Unixdatetimeconverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) sınıfını, DateTimeS 'ı sayı olarak seri hale getirmek için kullanabilirsiniz.

## <a name="querying-datetimes-in-linq"></a>LINQ 'te DateTimes sorgulama

SQL .NET SDK, Azure Cosmos DB ' de depolanan verileri LINQ üzerinden otomatik olarak destekler. Örneğin, aşağıdaki kod parçacığında son üç gün içinde gönderilen siparişlere filtre uygulayan bir LINQ sorgusu gösterilmektedir:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Aşağıdaki SQL bildirimine çevrilmiş ve Azure Cosmos DB yürütüldü:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Azure Cosmos DB SQL sorgu dili ve LINQ sağlayıcısı hakkında daha fazla bilgi için bkz. [LINQ 'da Cosmos DB sorgulama](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Aralık sorguları için DateTimes dizini oluşturma

Sorgular DateTime değerleriyle ortaktır. Bu sorguları verimli bir şekilde yürütmek için sorgunun filtresindeki herhangi bir özelliklerde tanımlanmış bir dizininiz olmalıdır.

[Azure Cosmos DB Dizin oluşturma ilkelerinde](index-policy.md)dizin oluşturma ilkelerini yapılandırma hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="next-steps"></a>Sonraki Adımlar

* [GitHub 'Da kod örneklerini](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) indirme ve çalıştırma
* [SQL sorguları](sql-query-getting-started.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Dizin oluşturma ilkeleri](index-policy.md) hakkında daha fazla bilgi edinin