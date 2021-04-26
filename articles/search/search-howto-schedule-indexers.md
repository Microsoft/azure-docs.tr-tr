---
title: Dizin Oluşturucu yürütmeyi zamanla
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama dizin oluşturucularının içeriği düzenli aralıklarla veya belirli zamanlarda dizine almak için zamanlayın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097985"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama Dizinleyicileri zamanlama

Dizin Oluşturucu normalde, oluşturulduktan hemen sonra bir kez çalışır. Daha sonra, Azure portal, [Dizin Oluşturucu (REST)](/rest/api/searchservice/run-indexer)veya BIR Azure SDK kullanarak isteğe bağlı olarak yeniden çalıştırabilirsiniz. Alternatif olarak, bir dizin Oluşturucuyu bir zamanlamaya göre çalışacak şekilde de yapılandırabilirsiniz. Dizin Oluşturucu çizelgelemenin yararlı olduğu bazı durumlar şunlardır:

* Kaynak veriler zamanla değişecektir ve arama dizin oluşturucunun farkı otomatik olarak işlemesini istiyorsunuz.

* Kaynak veriler çok büyükse ve Dizin Oluşturucu işlemesini zaman içinde yaymak istiyorsunuz. Dizin Oluşturucu işleri, normal veri kaynakları için en fazla 24 saat çalışma zamanına ve becerileri ile Dizin oluşturucular için 2 saate tabidir. Dizin oluşturma en fazla Aralık içinde tamamlanamaz, her 2 saatte bir çalışan bir zamanlama yapılandırabilirsiniz. Dizin oluşturucular, dizin oluşturmanın son sonlandırdığı yeri işaret eden bir iç yüksek su işareti tarafından kararlaştırılmış şekilde otomatik olarak kapatılabileceği yerleri otomatik olarak alabilir. Yinelenen 2 saat zamanlamasında bir dizin oluşturucunun çalıştırılması, tek bir iş için izin verilen aralığın ötesinde çok büyük bir veri kümesini (birçok milyonlarca belge) işlemesini sağlar. Büyük veri birimlerinin dizinini oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da büyük veri kümelerini dizin bilişsel arama](search-howto-large-index.md).

* Bir arama dizini birden fazla veri kaynağından doldurulacak ve dizin oluşturucularının çakışmaları azaltmak için farklı zamanlarda çalıştırılmasını istiyorsunuz.

Görsel olarak, bir zamanlama aşağıdaki gibi görünebilir: 1 Ocak 'tan başlayıp 50 dakikada bir çalışıyor.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Zamanlayıcı, Azure Bilişsel Arama 'ın yerleşik bir özelliğidir. Dış zamanlayıcılar desteği yoktur.

## <a name="schedule-property"></a>Zamanlama özelliği

Zamanlama, Dizin Oluşturucu tanımının bir parçasıdır. **Schedule** özelliği atlanırsa, Dizin Oluşturucu, oluşturulduktan hemen sonra bir kez çalışır. Bir **zamanlama** özelliği eklerseniz, iki bölüm belirtirsiniz.

| Özellik | Açıklama |
|----------|-------------|
|**Aralık** | istenir Art arda iki Dizin Oluşturucu yürütme başlangıcı arasındaki zaman miktarı. İzin verilen en küçük Aralık 5 dakikadır ve en uzun süre 1440 dakikadır (24 saat). XSD "dayTimeDuration" değeri ( [ıso 8601 Duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Bunun için olan model: `P(nD)(T(nH)(nM))` . <br/><br/>Örnekler: her `PT15M` `PT2H` 2 saat için 15 dakikada bir.|
| **Başlangıç saati (UTC)** | seçim Zamanlanan yürütmelerin ne zaman başlaması gerektiğini gösterir. Atlanırsa, geçerli UTC saati kullanılır. Bu süre geçmişte olabilir, bu durumda ilk yürütme, dizin oluşturucunun özgün **başlangıçzamanından** bu yana sürekli olarak çalışıp çalışmadığını olarak zamanlanır.<br/><br/>Örnekler: `2021-01-01T00:00:00Z` 1 ocak 10:28 ' de başlayan gece yarısından itibaren `2021-01-05T22:28:00Z` p.m. 5 Ocak 'ta.|

## <a name="scheduling-behavior"></a>Zamanlama davranışı

Bir dizin oluşturucunun tek seferde yalnızca bir yürütmesi çalıştırılabilir. Bir Dizin Oluşturucu bir sonraki yürütmesi zamanlandığında zaten çalışıyorsa, bu yürütme zamanlanan bir sonraki saate kadar ertelenir.

Daha somut hale getirmek için bir örnek ele alalım. Bir Dizin Oluşturucu zamanlamasını saat **aralığı** ve 1 Haziran 2019 ' in 8:00:00: UTC ' de **Başlangıç saati** ile yapılandırdığımızda varsayın. Bir Dizin Oluşturucu çalıştırıldığında bir saatten uzun sürme durumunda bu durum oluşabilir:

* İlk Dizin Oluşturucu yürütmesi 1 Haziran 2019 ' de 8:00: UTC 'de başlar. Bu yürütmenin 20 dakika (veya 1 saatten kısa bir süre) aldığını varsayın.
* İkinci yürütme 1 Haziran 2019 9:00, UTC 'de veya sonrasında başlar. Bu yürütmenin 70 dakika boyunca (bir saatten fazla) aldığını ve 10:10: UTC tarihine kadar tamamlanmadığını varsayın.
* Üçüncü yürütme 10:00: UTC olarak başlayacak şekilde zamanlanır, ancak bu süre içinde önceki yürütme çalışmaya devam etmektedir. Bu zamanlanmış yürütme daha sonra atlanır. Dizin oluşturucunun sonraki yürütülmesi 11:00. UTC tarihine kadar başlamaz.

> [!NOTE]
> Bir Dizin Oluşturucu belirli bir zamanlamaya göre ayarlandıysa, ancak her seferinde aynı belgede sürekli olarak başarısız olursa, Dizin Oluşturucu bir daha az sıklıkta yeniden ilerleme yapana kadar daha az sıklıkta (24 saatte bir en fazla zaman aralığına kadar) çalışmaya başlayacaktır. Temel alınan sorunun ne olduğuna inanıyorsanız, Dizin oluşturucuyu el ile çalıştırabilir ve dizin oluşturma işlemi başarılı olursa Dizin Oluşturucu düzenli zamanlamaya geri döner.

## <a name="schedule-using-rest"></a>REST kullanmayı zamanla

Dizin oluşturucuyu oluştururken veya güncelleştirirken **Schedule** özelliğini belirtin.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>.NET kullanarak zamanla

Aşağıdaki C# örneği, önceden tanımlanmış bir veri kaynağı ve dizini kullanarak bir Azure SQL veritabanı Dizin Oluşturucu oluşturur ve zamanlamasını her gün bir kez çalışacak şekilde ayarlar, şimdi başlatılıyor:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Zamanlama, [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)kullanarak bir Dizin Oluşturucu oluşturduğunuzda veya güncelleştirdiğinizde [ındexingschedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) sınıfı kullanılarak tanımlanır. **Indexingschedule** Oluşturucusu, **TimeSpan** nesnesi kullanılarak belirtilen bir **Interval** parametresi gerektiriyor. Daha önce belirtildiği gibi, izin verilen en küçük Aralık değeri 5 dakikadır ve en büyük değer 24 saattir. **DateTimeOffset** nesnesi olarak belirtilen ikinci **StartTime** parametresi isteğe bağlıdır.

## <a name="next-steps"></a>Sonraki adımlar

Bir zamanlamaya göre çalışan Dizin oluşturucular için, arama hizmetinden durumu alarak işlemleri izleyebilir veya tanılama günlüğünü etkinleştirerek ayrıntılı bilgiler edinebilirsiniz.

* [Arama Dizin Oluşturucu durumunu izleme](search-howto-monitor-indexers.md)
* [Günlük verilerini toplayın ve çözümleyin](search-monitor-logs.md)