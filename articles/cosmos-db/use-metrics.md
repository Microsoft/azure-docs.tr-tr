---
title: Azure Cosmos DB ölçümlerle izleme ve hata ayıklama
description: Ortak sorunları ayıklamak ve veritabanını izlemek için Azure Cosmos DB ölçümleri kullanın.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: cf92d9e1a1f92c2dc3294b71e3e620166fd90680
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818715"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB ölçümlerle izleme ve hata ayıklama
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB işleme hızı, depolama, tutarlılık, kullanılabilirlik ve gecikme süresi ölçümleri sağlar. Azure portalı bu ölçümlerin bir toplu görünümünü sağlar. Azure Cosmos DB ölçümlerini Azure İzleyici API'sinden de görüntüleyebilirsiniz. Kapsayıcı adı gibi ölçümler için boyut değerleri büyük/küçük harfe duyarlıdır. Bu nedenle, bu boyut değerlerinde dize karşılaştırmaları yaparken büyük/küçük harfe duyarsız karşılaştırma kullanmanız gerekir. Azure izleyici 'de ölçümleri görüntüleme hakkında daha fazla bilgi edinmek için [Azure izleyici 'den ölçümleri alma](./monitor-cosmos-db.md) makalesine bakın.

Bu makale yaygın kullanım örneklerinde yol gösterir ve Azure Cosmos DB ölçümlerinin bu sorunlarda analiz ve hata ayıklama işlemleri için nasıl kullanılabileceğini açıklar. Ölçümler her beş dakikada bir toplanır ve yedi gün boyunca tutulur.

## <a name="view-metrics-from-azure-portal"></a>Azure portal ölçümleri görüntüleme

1. [Azure Portal](https://portal.azure.com/) oturum aç

1. **Ölçümler** bölmesini açın. Varsayılan olarak, ölçümler bölmesi Azure Cosmos hesabınızdaki tüm veritabanları için depolama, dizin, istek birimleri ölçümlerini gösterir. Her veritabanı, kapsayıcı veya bir bölge için bu ölçümleri filtreleyebilirsiniz. Ayrıca ölçümleri belirli bir zaman düzeyinde filtreleyebilirsiniz. Aktarım hızı, depolama, kullanılabilirlik, gecikme süresi ve tutarlılık ölçümleri hakkında daha fazla ayrıntı, ayrı sekmelerde sunulmaktadır. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure portal Cosmos DB performans ölçümleri":::

**Ölçümler** bölmesinde aşağıdaki ölçümler mevcuttur:

* **İşleme ölçümleri** -Bu ölçüm, kapsayıcı için sağlanan aktarım hızı veya depolama kapasitesi aşıldığı için tüketilen veya başarısız olan isteklerin (429 yanıt kodu) sayısını gösterir.

* **Depolama ölçümleri** -Bu ölçüm, veri ve Dizin kullanımının boyutunu gösterir.

* **Kullanılabilirlik ölçümleri** -Bu ölçüm, toplam istek üzerindeki başarılı isteklerin yüzdesini saat başına gösterir. Başarı oranı Azure Cosmos DB SLA 'Lar tarafından tanımlanır.

* **Gecikme ölçümleri** -Bu ölçüm, hesabınızın çalıştığı bölgedeki Azure Cosmos DB tarafından gözlenen okuma ve yazma gecikmesini gösterir. Coğrafi olarak çoğaltılan bir hesap için bölgeler genelinde gecikme süresini görselleştirebilirsiniz. Bu ölçüm, uçtan uca istek gecikmesini temsil etmez.

* **Tutarlılık ölçümleri** -Bu ölçüm, seçtiğiniz tutarlılık modeli için ne kadar tutarlılık olduğunu gösterir. Bu ölçüm, çok bölgeli hesaplar için seçtiğiniz bölgeler arasındaki çoğaltma gecikmesini de gösterir.

* **Sistem ölçümleri** -Bu ölçüm, birincil bölüm tarafından sunulan meta veri isteklerinin sayısını gösterir. Ayrıca, kısıtlanan istekleri belirlemesine de yardımcı olur.

Aşağıdaki bölümlerde Azure Cosmos DB ölçümlerini kullanabileceğiniz yaygın senaryolar açıklanmaktadır. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Kaç isteğin başarılı olduğunu veya hatalara neden olduğunu anlayın

Başlamak için [Azure Portal](https://portal.azure.com) gidin ve **ölçümler** dikey penceresine gidin. Dikey pencerede, * * istek sayısı ' nı 1 dakikalık bir grafik olarak aşmış olan istek sayısını bulun. Bu grafik, durum koduna göre bölünmüş toplam dakikalık isteği gösterir. HTTP durum kodları hakkında daha fazla bilgi için bkz. [Azure Cosmos DB Için http durum kodları](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

En yaygın hata durum kodu 429 ' dir (hız sınırlandırma/azaltma). Bu hata, Azure Cosmos DB isteklerinin sağlanan aktarım hızına göre daha fazla olduğu anlamına gelir. Bu sorunun en yaygın çözümü, belirtilen koleksiyon için [Rus ölçeğini ölçeklendirmektir](./set-throughput.md) .

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Dakika başına istek sayısı":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Bölümler arasında üretilen iş dağıtımını belirleme

Bölüm anahtarlarınızın iyi bir önemliliğine sahip olmak, ölçeklenebilir bir uygulama için gereklidir. Bölümlere göre ayrılmış herhangi bir bölümlenmiş kapsayıcının üretilen iş dağıtımını öğrenmek için [Azure Portal](https://portal.azure.com) **ölçümler dikey penceresine** gidin. **Aktarım hızı** sekmesinde, depolama dökümü **her bir fiziksel bölüm GRAFIĞININ en fazla ru/saniye** cinsinden gösterilir. Aşağıdaki grafik, en soldaki çarpıtılmış bölümde gösterildiği gibi verilerin yanlış dağıtımına ilişkin bir örnek gösterir.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Tek bölüm yoğun kullanımı görüntüleme":::

Düzensiz bir üretilen iş dağıtımı, kısıtlanmış isteklere yol açabilecek ve yeniden bölümleme gerektirebilecek *etkin* bölümlere neden olabilir. Azure Cosmos DB bölümlendirme hakkında daha fazla bilgi için bkz. [Azure Cosmos DB bölüm ve ölçek](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Bölümler arasında depolama dağılımını belirleme

Tüm ölçeklenebilir uygulamalar için bölümünüzün iyi bir önemliliğine sahip olmak önemlidir. Bölümlere göre ayrılmış herhangi bir bölümlenmiş kapsayıcının depolama dağılımını öğrenmek için [Azure Portal](https://portal.azure.com)ölçümler dikey penceresine gidin. Depolama alanı sekmesinde, depolama dökümü üst bölüm anahtarları tarafından tüketilen veri + dizin depolaması bölümünde gösterilir. Aşağıdaki grafik, en soldaki asimetrik bölümde gösterildiği gibi veri depolamanın kötü bir dağıtımını gösterir.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Kötü veri dağıtımı örneği":::

Grafikteki bölüme tıklayarak, hangi bölüm anahtarının dağıtımı eğriltir.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Bölüm anahtarı dağılımı eğriltme":::

Hangi bölüm anahtarının dağıtım içinde eğilmesine neden olduğunu tanımladıktan sonra, kapsayıcınızı daha fazla Dağıtılmış bölüm anahtarıyla yeniden bölümlemeniz gerekebilir. Azure Cosmos DB bölümlendirme hakkında daha fazla bilgi için bkz. [Azure Cosmos DB bölüm ve ölçek](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Veri boyutunu Dizin boyutuyla karşılaştırın

Azure Cosmos DB, tüketilen toplam depolama alanı, hem veri boyutu hem de dizin boyutunun birleşimidir. Genellikle, dizin boyutu veri boyutunun bir kesiri olur. Daha fazla bilgi edinmek için [Dizin boyutu](index-policy.md#index-size) makalesine bakın. [Azure Portal](https://portal.azure.com)ölçümler dikey penceresinde, depolama sekmesi verileri ve dizini temel alarak depolama tüketiminin dökümünü alır.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Dizin alanını korumak istiyorsanız, [Dizin oluşturma ilkesini](index-policy.md)ayarlayabilirsiniz.

## <a name="debug-why-queries-are-running-slow"></a>Sorguların neden yavaş çalıştığını hata ayıkla

SQL API SDK 'lerinde, Azure Cosmos DB sorgu yürütme istatistikleri sağlar.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*Queryölçümler* , sorgunun her bileşeninin yürütme için ne kadar süreceğine ilişkin ayrıntılar sağlar. Uzun süre çalışan sorgulara ilişkin en yaygın temel neden, sorgunun dizinlerden yararlanamaz anlamına gelir. Bu sorun, daha iyi bir filtre koşuluyla çözülebilir.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure portal belirtilen ölçümleri kullanarak sorunları nasıl izleyip ayıklayacağınız hakkında daha fazla öğrendiniz. Aşağıdaki makaleleri okuyarak veritabanı performansını artırma hakkında daha fazla bilgi edinmek isteyebilirsiniz:

* Azure izleyici 'de ölçümleri görüntüleme hakkında daha fazla bilgi edinmek için [Azure izleyici 'den ölçümleri alma](./monitor-cosmos-db.md) makalesine bakın. 
* [Azure Cosmos DB ile performans ve ölçek testi](performance-testing.md)
* [Azure Cosmos DB için performans ipuçları](performance-tips.md)