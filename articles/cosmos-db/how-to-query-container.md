---
title: Azure Cosmos DB'de kapsayıcıları sorgulama
description: Azure Cosmos DB'de kapsayıcıları sorgulamayı öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092959"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısını sorgulama

Bu makalede, Azure Cosmos DB bir kapsayıcının (koleksiyon, grafik veya tablo) nasıl sorgulanacağı açıklanmaktadır.

## <a name="in-partition-query"></a>Bölüm içi sorgu

Kapsayıcılardan veri sorguladığınızda, sorguda bir bölüm anahtarı filtresi belirtilmişse Azure Cosmos DB sorguyu otomatik olarak işler. Sorguyu, filtrede belirtilen bölüm anahtarı değerlerine karşılık gelen bölümlere yönlendirir. Örneğin, aşağıdaki sorgu bölüm anahtarı değerine `DeviceId` `XMS-0001`karşılık gelen tüm belgeleri tutan bölüme yönlendirilir.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Bölümler arası sorgu

Aşağıdaki sorgu, bölüm anahtarı (`DeviceId`) üzerinde bir filtreye sahip değildir ve bölümün dizininde çalıştığı tüm bölümlere göre belirlenir. Bölümler arasında bir sorgu çalıştırmak için, true `EnableCrossPartitionQuery` (veya `x-ms-documentdb-query-enablecrosspartition`  REST API) olarak ayarlayın.

Enable, Spartitionquery özelliği bir Boole değeri kabul eder. True olarak ayarlandığında ve sorgunuzun bölüm anahtarı yoksa, Azure Cosmos DB. Fanı, tüm bölümlere tek sorgular vererek yapılır. Sorgu sonuçlarını okumak için, istemci uygulamaların, FeedResponse sonuçlarını kullanması ve ContinuationToken özelliğini denetlemesi gerekir. Tüm sonuçları okumak için, ContinuationToken null olana kadar verilerde yinelenmesine devam edin. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB, SQL kullanarak toplam işlev sayısı, MIN, MAX ve Ort. kapsayıcıları destekler. SDK 'nın 1.12.0 ve sonraki bir sürümünden başlayarak kapsayıcılar üzerinde toplama işlevleri. Sorgular tek bir toplama işleci içermelidir ve projeksiyonde tek bir değer içermelidir.

## <a name="parallel-cross-partition-query"></a>Paralel bölümler arası sorgu

Azure Cosmos DB SDK 'Ları 1.9.0 ve üzeri paralel sorgu yürütme seçeneklerini destekler. Paralel bölümler arası sorgular, düşük gecikme süreli bölümler arası sorgular yürütmenize olanak tanır. Örneğin, aşağıdaki sorgu bölümler arasında paralel çalıştırılacak şekilde yapılandırılmıştır.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Aşağıdaki parametreleri ayarlayarak paralel sorgu yürütme işlemini yönetebilirsiniz:

- **Maxdegreeofparalellik**: Kapsayıcının bölümlerine en fazla eşzamanlı ağ bağlantısı sayısını ayarlar. Bu özelliği-1 olarak ayarlarsanız, SDK paralellik derecesini yönetir.  `MaxDegreeOfParallelism`Belirtilmemişseveyavarsayılandeğerolan0olarakayarlandıysa,kapsayıcınınbölümlerinetek bir ağ bağlantısı vardır.

- **Maxbuffereditemcount**: Sorgu gecikmesini, istemci tarafı bellek kullanımına karşı Trat. Bu seçenek atlanırsa veya-1 ' e ayarlanırsa, SDK paralel sorgu yürütme sırasında arabelleğe alınan öğe sayısını yönetir.

Koleksiyonun aynı durumuyla, paralel bir sorgu sonuçları seri yürütme ile aynı sırada döndürür. Sıralama işleçlerini (ORDER BY, TOP) içeren bir çapraz bölümlü sorgu gerçekleştirirken, Azure Cosmos DB SDK sorguyu bölümler arasında paralel olarak verir. Genel olarak sıralanmış sonuçlar oluşturmak için istemci tarafında kısmen sıralanmış sonuçları birleştirir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB bölümlendirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
- [Azure Cosmos DB'de yapay bölümleme anahtarları](synthetic-partition-keys.md)
