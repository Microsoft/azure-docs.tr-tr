---
title: SQL sorgu performansı & yürütme ölçümleri alın
description: SQL sorgu yürütme ölçümlerini almayı ve Azure Cosmos DB isteklerinin SQL sorgu performansını profil oluşturmayı öğrenin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097405"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>.NET SDK kullanarak SQL sorgu yürütme ölçümlerini alın ve sorgu performansını çözümleyin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB üzerinde SQL sorgu performansının nasıl profilini yapılacağı sunulmaktadır. Bu profil oluşturma `QueryMetrics` , .NET SDK 'dan alınan kullanılarak yapılabilir ve burada ayrıntılı olarak verilmiştir. [Queryölçümler](/dotnet/api/microsoft.azure.documents.querymetrics) , arka uç sorgu yürütmesi hakkında bilgi içeren türü kesin belirlenmiş bir nesnedir. Bu ölçümler, [sorgu performansını ayarlama](./sql-api-query-metrics.md) makalesinde daha ayrıntılı olarak belgelenmiştir.

## <a name="set-the-feedoptions-parameter"></a>Feedomeseçenekleri parametresini ayarlama

[Documentclient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) için tüm aşırı yüklemeler isteğe bağlı bir [feedoçenparametresi](/dotnet/api/microsoft.azure.documents.client.feedoptions) alır. Bu seçenek sorgu yürütmenin belirlenme ve parametreli hale getirilmiş olmasına olanak sağlar. 

SQL sorgu yürütme ölçümlerini toplamak için, [Feedoseçenekleri](/dotnet/api/microsoft.azure.documents.client.feedoptions) Içindeki [populatequeryölçümler](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) parametresini olarak ayarlamanız gerekir `true` . `PopulateQueryMetrics`True olarak ayarlamak, bunu, ilgili değerini içerecek şekilde yapar `FeedResponse` `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery () ile sorgu ölçümleri alın
Aşağıdaki kod örneği, [Asdocumentquery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) yöntemi kullanılırken ölçümlerin nasıl alınacağını gösterir:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Queryölçümlerini toplama

Önceki bölümde, [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) yöntemine yönelik birden çok çağrı olduğunu fark edersiniz. Her çağrı, `FeedResponse` `QueryMetrics` sorgunun her devamı için bir olan sözlüğü olan bir nesne döndürdü. Aşağıdaki örnek, LINQ kullanarak bunların nasıl toplanacağını göstermektedir `QueryMetrics` :

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Sorgu ölçümlerini bölüm KIMLIĞINE göre gruplandırma

`QueryMetrics`Bölüm kimliğine göre gruplandırabilirsiniz. Bölüm KIMLIĞINE göre gruplandırma, belirli bir bölümün diğer kullanıcılarla karşılaştırıldığında performans sorunlarına neden olup olmadığını görmenizi sağlar. Aşağıdaki örnek, LINQ ile nasıl gruplandıralınacağını gösterir `QueryMetrics` :

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>DocumentQuery üzerinde LINQ

Ayrıca, `FeedResponse` yöntemini kullanarak BIR LINQ sorgusundan edinebilirsiniz `AsDocumentQuery()` :

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Pahalı sorgular

Yüksek aktarım hızı kullanan pahalı sorguları veya sorguları araştırmak için her sorgu tarafından tüketilen istek birimlerini yakalayabilirsiniz. İstek ücretine, içindeki [requestücretözelliğini](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) kullanarak ulaşabilirsiniz `FeedResponse` . Azure portal ve farklı SDK 'Ları kullanarak istek ücreti alma hakkında daha fazla bilgi edinmek için bkz. [istek birimi ücreti bulma](find-request-unit-charge.md) makalesi.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Sorgu yürütme süresini al

İstemci tarafı bir sorguyu yürütmek için gereken süreyi hesaplarken, `ExecuteNextAsync` kod tabanlarınızın diğer parçalarını değil, yalnızca yöntemi çağırma süresini eklediğinizden emin olun. Bu çağrılar, aşağıdaki örnekte gösterildiği gibi sorgu yürütmenin ne kadar sürdüğünü hesaplamak için size yardımcı olur:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Sorgu tarama (genellikle yavaş ve pahalı)

Tarama sorgusu, sonuç kümesi döndürülmeden önce çok sayıda belge yüklendiği için dizin tarafından hizmet edilmemiş bir sorguyu ifade eder.

Tarama sorgusunun bir örneği aşağıda verilmiştir:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Bu sorgunun filtresi, dizinden sunulmayan ÜSTTEKI sistem işlevini kullanıyor. Bu sorguyu büyük bir koleksiyonda çalıştırmak ilk devamlılık için aşağıdaki sorgu ölçümlerini oluşturdu:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Sorgu ölçümleri çıktısından aşağıdaki değerleri aklınızda edin:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Bu sorgu, 399.998.938 baytlık toplam 60.951 belge yükledi. Bu kadar bayt yükleme, yüksek maliyetli veya istek birimi ücretine neden olur. Ayrıca, harcanan toplam süre özelliği ile birlikte boş olan sorguyu yürütmek uzun zaman alır:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Sorgunun yürütülmesi için 4,5 saniye geçen anlamına gelir (Bu yalnızca bir devamlılık oldu).

Bu örnek sorguyu iyileştirmek için, filtrede üst öğesinin kullanımını önleyin. Bunun yerine, belgeler oluşturulduğunda veya güncelleştirilirken `c.description` değerler tüm büyük harfli karakterlere eklenmelidir. Sorgu bundan sonra olur: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Bu sorgu artık dizinden sunulabilecek.

Sorgu performansını ayarlama hakkında daha fazla bilgi edinmek için bkz. [sorgu performansını ayarlama](./sql-api-query-metrics.md) makalesi.

## <a name="references"></a><a id="References"></a>Başvurular

- [Azure Cosmos DB SQL belirtimi](./sql-query-getting-started.md)
- [ANSı SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu performansını ayarlama](sql-api-query-metrics.md)
- [Dizinlemeye genel bakış](index-overview.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)