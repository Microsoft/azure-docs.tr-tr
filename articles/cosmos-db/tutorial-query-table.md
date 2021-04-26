---
title: Azure Cosmos DB’de tablo verilerini sorgulama
description: OData filtrelerini ve LINQ sorgularını kullanarak Azure Cosmos DB Tablo API'si hesabında depolanan verileri sorgulamayı öğrenin
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e184d85e3daee41f530334aa0034fc98f40a8766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099235"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Öğretici: Tablo API’sini kullanarak Azure Cosmos DB’yi sorgulama
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB [Table API’si](table-introduction.md), anahtar/değer (tablo) verilerine karşı OData ve [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) sorgularını destekler.  

Bu makale aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Tablo API’si ile verileri sorgulama

Bu makaledeki sorgularda aşağıdaki `People` tablosu kullanılmaktadır:

| PartitionKey | RowKey | E-posta | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

Tablo API’sini kullanarak nasıl sorgulama yapılacağı ile ilgili ayrıntılar için bkz. [Tabloları ve Varlıkları Sorgulama](/rest/api/storageservices/fileservices/querying-tables-and-entities).

Azure Cosmos DB’nin sunduğu üstün yetenekler hakkında daha fazla bilgi için bkz. [Azure Cosmos DB Tablo API’si](table-introduction.md) ve [.NET’te Tablo API’si ile geliştirme](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Önkoşullar

Bu sorguların çalışması için bir Azure Cosmos DB hesabınız ve kapsayıcıda varlık verileriniz olmalıdır. Bunlardan biri yok mu? Bir hesap oluşturmak ve veritabanınızı doldurmak için [beş dakikalık hızlı başlangıç](create-table-dotnet.md) veya [geliştirici öğreticisini](tutorial-develop-table-dotnet.md) tamamlayın.

## <a name="query-on-partitionkey-and-rowkey"></a>PartitionKey ve RowKey üzerinde sorgulama

PartitionKey ve RowKey özellikleri bir varlığın birincil anahtarını oluşturduğundan, varlığı belirlemek için aşağıdaki özel sözdizimini kullanabilirsiniz:

**Sorgu**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Sonuçlar**

| PartitionKey | RowKey | E-posta | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Alternatif olarak, aşağıdaki bölümde gösterildiği gibi, `$filter` seçeneğinin parçası olarak bu özellikleri belirtebilirsiniz. Anahtar özelliği adlarının ve sabit değerlerin büyük/küçük harfe duyarlı olduğunu unutmayın. PartitionKey ve RowKey özellikleri, Dize türündedir.

## <a name="query-by-using-an-odata-filter"></a>OData filtresi kullanarak sorgulama

Bir filtre dizesi oluştururken şu kuralları göz önünde bulundurun:

* Bir özelliği bir değerle karşılaştırmak için OData Protokol Belirtimi tarafından tanımlanan mantıksal işleçleri kullanın. Bir özelliği dinamik değerle karşılaştıramayacağınızı unutmayın. İfadenin bir tarafı sabit olmalıdır.
* Özellik adı, işleç ve sabit değeri, URL kodlamalı boşluklarla ayrılmalıdır. Boşluk, `%20` olarak URL kodlamalı olur.
* Filtre dizesinin tüm kısımları büyük/küçük harfe duyarlıdır.
* Filtrenin geçerli sonuçlar döndürmesi için sabit değer, özellikle aynı veri türünde olmalıdır. Desteklenen özellik türleri hakkında daha fazla bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama](/rest/api/storageservices/understanding-the-table-service-data-model).

Aşağıda, OData `$filter` kullanılarak PartitionKey ve Email özelliklerine göre nasıl filtreleme yapılacağını gösteren örnek bir sorgu verilmiştir.

**Sorgu**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Çeşitli veri türleri için filtre ifadeleri oluşturma hakkında daha fazla bilgi için bkz. [Tabloları ve Varlıkları Sorgulama](/rest/api/storageservices/querying-tables-and-entities).

**Sonuçlar**

| PartitionKey | RowKey | E-posta | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

Tarih saat özelliklerindeki sorgular, Azure Cosmos DB Tablo API'si yürütüldüğünde hiçbir veri döndürmez. Azure Tablo depolama, tarih değerlerini Tick ile birlikte depolarken, Azure Cosmos DB Tablo API'si  `_ts` özelliği kullanır. `_ts`Özelliği, bir OData filtresi olmayan ikinci bir ayrıntı düzeyi düzeyidir. Bu nedenle, zaman damgası özelliklerindeki sorgular Azure Cosmos DB tarafından engellenir. Geçici bir çözüm olarak, özel bir tarih saat veya uzun veri türü özelliği tanımlayabilir ve tarih değerini istemciden ayarlayabilirsiniz.

## <a name="query-by-using-linq"></a>LINQ kullanarak sorgulama 
İlgili OData sorgu ifadelerine çeviri yapan LINQ kullanarak da sorgulama yapabilirsiniz. Aşağıda, .NET SDK kullanılarak sorgu derlemeye ilişkin bir örnek verilmiştir:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Tablo API’sini kullanarak nasıl sorgulama yapıldığını öğrendiniz

Artık verilerinizi genel olarak nasıl dağıtacağınızı öğrenmek için sonraki öğreticiye ilerleyebilirsiniz.

> [!div class="nextstepaction"]
> [Verilerinizi genel olarak dağıtma](tutorial-global-distribution-table.md)
