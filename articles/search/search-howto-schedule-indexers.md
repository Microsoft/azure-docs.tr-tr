---
title: Dizin Oluşturucu yürütmeyi zamanla
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama dizin oluşturucularının içeriği düzenli aralıklarla veya belirli zamanlarda dizine almak için zamanlayın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112953"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama Dizinleyicileri zamanlama

Dizin Oluşturucu normalde, oluşturulduktan hemen sonra bir kez çalışır. Portalı, REST API veya .NET SDK kullanarak isteğe bağlı olarak yeniden çalıştırabilirsiniz. Bir dizin Oluşturucuyu bir zamanlamaya göre düzenli aralıklarla çalışacak şekilde de yapılandırabilirsiniz.

Dizin Oluşturucu çizelgelemenin yararlı olduğu bazı durumlar:

* Kaynak veriler zamanla değişecektir ve Azure Bilişsel Arama dizin oluşturucularının değiştirilen verileri otomatik olarak işlemesini istiyorsunuz.
* Dizin birden çok veri kaynağından doldurulacak ve oluşturucuların çakışmaları azaltmak için farklı zamanlarda çalıştığından emin olmak istiyorsunuz.
* Kaynak veriler çok büyükse ve Dizin Oluşturucu işlemesini zaman içinde yaymak istiyorsunuz. Büyük hacimteki verileri dizinleme hakkında daha fazla bilgi için bkz. [Azure bilişsel arama büyük veri kümelerini Dizin](search-howto-large-index.md)oluşturma.

Zamanlayıcı, Azure Bilişsel Arama 'ın yerleşik bir özelliğidir. Arama dizin oluşturucularının denetlemek için bir dış Zamanlayıcı kullanamazsınız.

## <a name="define-schedule-properties"></a>Zamanlama özelliklerini tanımla

Dizin Oluşturucu zamanlamasının iki özelliği vardır:
* Zaman **aralığı**, zamanlanan Dizin Oluşturucu yürütmelerinin arasındaki süreyi tanımlar. İzin verilen en küçük Aralık 5 dakikadır ve en büyük değer 24 saattir.
* **Başlangıç saati (UTC)** , dizin oluşturucunun ilk kez çalıştırılması gereken zamanı gösterir.

İlk dizin oluşturucuyu oluştururken veya dizin oluşturucunun özelliklerini daha sonra güncelleştirerek bir zamanlama belirtebilirsiniz. Dizin Oluşturucu zamanlamaları, [Portal](#portal), [REST API](#restApi)veya [.NET SDK](#dotNetSdk)kullanılarak ayarlanabilir.

Bir dizin oluşturucunun tek seferde yalnızca bir yürütmesi çalıştırılabilir. Bir Dizin Oluşturucu bir sonraki yürütmesi zamanlandığında zaten çalışıyorsa, bu yürütme zamanlanan bir sonraki saate kadar ertelenir.

Daha somut hale getirmek için bir örnek ele alalım. Bir Dizin Oluşturucu zamanlamasını saat **aralığı** ve 1 Haziran 2019 ' in 8:00:00: UTC ' de **Başlangıç saati** ile yapılandırdığımızda varsayın. Bir Dizin Oluşturucu çalıştırıldığında bir saatten uzun sürme durumunda bu durum oluşabilir:

* İlk Dizin Oluşturucu yürütmesi 1 Haziran 2019 ' de 8:00: UTC 'de başlar. Bu yürütmenin 20 dakika (veya 1 saatten kısa bir süre) aldığını varsayın.
* İkinci yürütme 1 Haziran 2019 9:00, UTC 'de veya sonrasında başlar. Bu yürütmenin 70 dakika boyunca (bir saatten fazla) aldığını ve 10:10: UTC tarihine kadar tamamlanmadığını varsayın.
* Üçüncü yürütme 10:00: UTC olarak başlayacak şekilde zamanlanır, ancak bu süre içinde önceki yürütme çalışmaya devam etmektedir. Bu zamanlanmış yürütme daha sonra atlanır. Dizin oluşturucunun sonraki yürütülmesi 11:00. UTC tarihine kadar başlamaz.

> [!NOTE]
> Bir Dizin Oluşturucu belirli bir zamanlamaya göre ayarlanırsa ancak her çalıştırıldığında aynı belgede tekrar tekrar tekrar başarısız olursa, Dizin Oluşturucu bir süre önce devam edene kadar daha az sıklıkta (en fazla 24 saatte bir) çalışmaya başlar 'ndaki.  Dizin oluşturucunun belirli bir noktada takılmasına neden olan sorunu düzelttiğini düşünüyorsanız, dizin oluşturucunun isteğe bağlı olarak çalıştırılmasını gerçekleştirebilir ve başarıyla ilerleme yapıyorsa, Dizin Oluşturucu ayarlanan zamanlama aralığına yeniden döner.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Portalda zamanla

Portalda veri Içeri aktarma Sihirbazı, oluşturma zamanında bir dizin oluşturucunun zamanlamasını tanımlamanızı sağlar. Varsayılan zamanlama ayarı **her saat ' tir.** bu, dizin oluşturucunun oluşturulduktan sonra bir kez çalıştırıldığı ve sonrasında her saat sonra yeniden çalıştırıldığı anlamına gelir.

Dizin oluşturucunun yeniden otomatik olarak çalışmasını istemiyorsanız **veya günde bir** kez çalıştırmak Istemiyorsanız, zamanlama ayarını **bir kez** daha değiştirebilirsiniz. Farklı bir Aralık veya belirli bir başlangıç saati belirtmek istiyorsanız, bunu **özel** olarak ayarlayın.

Zamanlamayı **özel**olarak ayarladığınızda, alanlar, **aralığı** ve **başlangıç saatini (UTC)** belirtmenize izin verecek şekilde görünür. İzin verilen en kısa zaman aralığı 5 dakikadır ve en uzun süre 1440 dakikadır (24 saat).

   ![Veri alma Sihirbazı 'nda Dizin Oluşturucu zamanlamasını ayarlama](media/search-howto-schedule-indexers/schedule-import-data.png "Veri alma Sihirbazı 'nda Dizin Oluşturucu zamanlamasını ayarlama")

Bir Dizin Oluşturucu oluşturulduktan sonra, dizin oluşturucunun düzenleme panelini kullanarak zamanlama ayarlarını değiştirebilirsiniz. Zamanlama alanları, verileri Içeri aktarma Sihirbazı ile aynıdır.

   ![Dizin Oluşturucu düzenleme panelinde zamanlamayı ayarlama](media/search-howto-schedule-indexers/schedule-edit.png "Dizin Oluşturucu düzenleme panelinde zamanlamayı ayarlama")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>REST API 'Lerini kullanmayı zamanla

REST API kullanarak bir dizin oluşturucunun zamanlamasını tanımlayabilirsiniz. Bunu yapmak için, Dizin oluşturucuyu oluştururken veya güncelleştirirken **Schedule** özelliğini ekleyin. Aşağıdaki örnekte, var olan bir dizin oluşturucuyu güncelleştirmek için bir PUT isteği gösterilmektedir:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parametresi gereklidir. Aralık, arka arkaya iki Dizin Oluşturucu yürütmelerinin başlangıcı arasındaki süreyi ifade eder. İzin verilen en küçük Aralık 5 dakikadır; en uzun değer bir gündür. XSD "dayTimeDuration" değeri ( [ıso 8601 Duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Bunun için olan model: `P(nD)(T(nH)(nM))`. Örnekler: her 2 saat için `PT2H` 15 dakikada bir `PT15M`.

İsteğe bağlı **StartTime** , zamanlanan yürütmelerin ne zaman başlaması gerektiğini gösterir. Atlanırsa, geçerli UTC saati kullanılır. Bu süre geçmişte olabilir, bu durumda ilk yürütme, dizin oluşturucunun özgün **başlangıçzamanından**bu yana sürekli olarak çalışıp çalışmadığını olarak zamanlanır.

Ayrıca, Dizin oluşturucuyu Çalıştır çağrısını kullanarak istediğiniz zaman bir Dizin Oluşturucu çalıştırabilirsiniz. Dizin oluşturucular çalıştırma ve Dizin Oluşturucu zamanlamalarını ayarlama hakkında daha fazla bilgi için, REST API başvurusunda [Dizin oluşturucuyu çalıştırma](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Dizin](https://docs.microsoft.com/rest/api/searchservice/get-indexer)Oluşturucu ve [güncelleştirme dizin oluşturucuyu](https://docs.microsoft.com/rest/api/searchservice/update-indexer) inceleyin.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>.NET SDK kullanarak zamanlama

Bir dizin oluşturucunun zamanlamasını Azure Bilişsel Arama .NET SDK kullanarak tanımlayabilirsiniz. Bunu yapmak için, bir Dizin Oluşturucu oluştururken veya güncelleştirirken **Schedule** özelliğini ekleyin.

Aşağıdaki C# örnek, önceden tanımlanmış bir veri kaynağı ve dizini kullanarak bir dizin oluşturucu oluşturur ve zamanlamasını 30 dakika sonra her gün çalışacak şekilde ayarlar.

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
**Schedule** parametresi atlanırsa, Dizin Oluşturucu, oluşturulduktan sonra yalnızca bir kez çalışır.

**StartTime** parametresi geçmişteki bir zamana ayarlanabilir. Bu durumda, ilk yürütme, dizin oluşturucunun verilen **StartTime**bu yana sürekli olarak çalışıyor olması halinde zamanlanır.

Zamanlama, [ındexingschedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) sınıfı kullanılarak tanımlanır. **Indexingschedule** Oluşturucusu, **TimeSpan** nesnesi kullanılarak belirtilen bir **Interval** parametresi gerektiriyor. İzin verilen en küçük Aralık değeri 5 dakikadır ve en büyük değer 24 saattir. **DateTimeOffset** nesnesi olarak belirtilen ikinci **StartTime** parametresi isteğe bağlıdır.

.NET SDK, [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) sınıfını ve **IIndexersOperations** arabiriminden Yöntemler uygulayan [Indexers](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) özelliğini kullanarak Dizin Oluşturucu işlemlerini denetlemenizi sağlar. 

Bir dizin oluşturucuyu, [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)veya [Runwithhttpmessagesasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) yöntemlerinden birini kullanarak dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.

Dizin oluşturucular oluşturma, güncelleştirme ve çalıştırma hakkında daha fazla bilgi için bkz. [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
