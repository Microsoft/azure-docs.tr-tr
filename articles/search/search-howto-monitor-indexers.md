---
title: Dizin Oluşturucu durumunu ve sonuçlarını izleme
titleSuffix: Azure Cognitive Search
description: REST API veya .NET SDK kullanarak Azure portal Azure Bilişsel Arama dizin oluşturucularının durumunu, ilerlemesini ve sonuçlarını izleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ed79831559802efdf7e3813f48b74c2fd3c63e87
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581862"
---
# <a name="monitor-indexer-status-and-results-in-azure-cognitive-search"></a>Azure Bilişsel Arama Dizin Oluşturucu durumunu ve sonuçlarını izleme

Dizin Oluşturucu işlemeyi Azure portal veya program aracılığıyla REST çağrıları veya bir Azure SDK aracılığıyla izleyebilirsiniz. Dizin oluşturucunun kendisi hakkındaki durumunun yanı sıra, başlangıç ve bitiş zamanlarını ve belirli bir çalıştırıldaki ayrıntılı hata ve uyarıları gözden geçirebilirsiniz.

## <a name="monitor-using-azure-portal"></a>Azure portal kullanarak izleme

Arama hizmeti genel bakış sayfanızda tüm dizin oluşturucularınızın geçerli durumunu görebilirsiniz. Portal sayfaları birkaç dakikada bir yenilenir, bu nedenle yeni bir Dizin Oluşturucu çalıştırmasının kanıtı 'nı hemen görmezsiniz.

   ![Dizin oluşturucular listesi](media/search-monitor-indexers/indexers-list.png "Dizin oluşturucular listesi")

| Durum | Açıklama |
|--------|-------------|
| **Sürüyor** | Etkin yürütmeyi gösterir. Portal kısmi bilgileri rapor eder. Dizin oluşturma ilerledikçe, **başarılı olan belge** değeri büyüme yanıtı ' nı izleyebilirsiniz. Büyük hacimler veri işleyen Dizin oluşturucular çalıştırmak uzun sürebilir. Örneğin, milyonlarca kaynak belgeyi işleyen Dizin oluşturucular 24 saat boyunca çalıştırılabilir ve hemen hemen yeniden başlatılabilir. Yüksek hacimli dizin oluşturucularının durumu her zaman portalda **devam** edebilir. Bir Dizin Oluşturucu çalışırken bile devam eden ilerleme ve önceki çalıştırmalar hakkında ayrıntılar bulunur. |
| **Başarılı** | Çalıştırmanın başarılı olduğunu gösterir. Hata sayısı, dizin oluşturucunun **en fazla başarısız öğe** ayarından daha küçükse, tek tek belgelerde hata olsa bile Dizin Oluşturucu çalıştırması başarılı olabilir. |
| **Başarısız** | Hata sayısı, başarısız olan **en fazla öğe** sayısını aştı ve dizin oluşturma durdu. |
| **Sıfırla** | Dizin oluşturucunun iç değişiklik izleme durumu sıfırlandı. Dizin Oluşturucu tam olarak çalışır, yalnızca yeni zaman damgalarına sahip olanları değil, tüm belgeleri yenilemez. |

Dizin oluşturucunun geçerli ve son çalıştırmaları hakkında daha fazla ayrıntı görmek için listedeki bir dizin oluşturucuya tıklayabilirsiniz.

   ![Dizin Oluşturucu Özeti ve yürütme geçmişi](media/search-monitor-indexers/indexer-summary.png "Dizin Oluşturucu Özeti ve yürütme geçmişi")

**Dizin Oluşturucu Özet** grafiğinde, en son çalıştırmalarından işlenen belgelerin sayısını görüntüleyen bir grafik görüntülenir.

**Yürütme ayrıntıları** listesinde en son yürütme sonuçlarının 50 ' i gösterilir. Bu çalıştıra ilişkin ayrıntıları görmek için listede bir yürütme sonucuna tıklayın. Bu, başlangıç ve bitiş zamanlarını ve oluşan hata ve uyarıları içerir.

   ![Dizin Oluşturucu yürütme ayrıntıları](media/search-monitor-indexers/indexer-execution.png "Dizin Oluşturucu yürütme ayrıntıları")

Çalıştırma sırasında belgeye özgü sorunlar varsa, bunlar hatalar ve uyarılar alanlarında listelenecektir.

   ![Dizin Oluşturucu ayrıntıları hatalarla](media/search-monitor-indexers/indexer-execution-error.png "Dizin Oluşturucu ayrıntıları hatalarla")

Uyarılar bazı Dizin oluşturucular türleriyle ortaktır ve her zaman bir sorun göstermez. Örneğin bilişsel hizmetler kullanan Dizin oluşturucular, görüntü veya PDF dosyaları işlemek için herhangi bir metin içermiyorsa uyarıları rapor edebilir. 

Dizin Oluşturucu hatalarını ve uyarılarını araştırma hakkında daha fazla bilgi için bkz. [Azure bilişsel arama 'da ortak Dizin Oluşturucu sorunlarını giderme](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Dizin Oluşturucu durumunu al (REST API) kullanarak izleme

Dizin Oluşturucu [durumunu Al komutunu](/rest/api/searchservice/get-indexer-status)kullanarak bir dizin oluşturucunun durum ve yürütme geçmişini alabilirsiniz:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Yanıt genel Dizin Oluşturucu durumunu, son (veya sürmekte olan) Dizin Oluşturucu çağrısını ve son Dizin Oluşturucu etkinleştirmeleri geçmişini içerir.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Yürütme geçmişi, geriye doğru kronolojik sıraya (en son ilk) göre sıralanan en güncel 50 çalıştırmaya kadar içerir.

İki farklı durum değeri olduğunu aklınızda edin. En üst düzey durum, dizin oluşturucunun kendisi içindir. Öğesinin Dizin Oluşturucu durumu, dizin oluşturucunun doğru şekilde ayarlandığı ve çalıştırılabileceği, ancak şu anda çalıştığı **anlamına gelir.**

Dizin oluşturucunun her çalışmasının Ayrıca, belirli yürütmenin devam eden (**çalışıyor**) veya **başarılı**, **geçişli bir hata** veya **kalıcı bir hata** durumuyla tamamlanmış olup olmadığını belirten kendi durumu vardır. 

Bir Dizin Oluşturucu değişiklik izleme durumunu yenilemek üzere sıfırlandığında, **sıfırlama** durumuyla ayrı bir yürütme geçmişi girişi eklenir.

Durum kodları ve Dizin Oluşturucu izleme verileri hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu durumunu Al](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>.NET kullanarak izleme

Aşağıdaki C# örneği, Azure Bilişsel Arama .NET SDK 'sını kullanarak bir dizin oluşturucunun durumu ve konsolunda en son (veya devam eden) sonuçları hakkında bilgi yazar.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Konsolundaki çıktı şuna benzer şekilde görünür:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

İki farklı durum değeri olduğunu aklınızda edin. Üst düzey durum, dizin oluşturucunun durumunun kendisidir. Öğesinin Dizin Oluşturucu durumu, dizin oluşturucunun doğru şekilde ayarlandığı ve yürütme için kullanılabilir olduğu, ancak şu anda yürütülmekte olduğu **anlamına gelir.**

Dizin oluşturucunun her çalışmasının Ayrıca, belirli yürütmenin devam eden (**çalışıyor**) veya **başarılı** veya **geçişli** bir durum ile zaten tamamlanmış olup olmadığı için kendi durumu vardır. 

Bir Dizin Oluşturucu değişiklik izleme durumunu yenilemek üzere sıfırlandığında, **sıfırlama** durumuyla ayrı bir geçmiş girişi eklenir.

## <a name="next-steps"></a>Sonraki adımlar

Durum kodları ve Dizin Oluşturucu izleme bilgileri hakkında daha fazla ayrıntı için aşağıdaki API başvurusuna başvurun:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)