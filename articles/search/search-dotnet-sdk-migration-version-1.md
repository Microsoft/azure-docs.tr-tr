---
title: Azure Search .NET SDK sürüm 1,1 ' e yükseltin
titleSuffix: Azure Cognitive Search
description: Kodu eski API sürümlerinden Azure Search .NET SDK sürüm 1,1 ' e geçirin. Nelerin yeni olduğunu ve hangi kod değişikliklerinin gerekli olduğunu öğrenin.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792392"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Azure Search .NET SDK sürüm 1,1 ' e yükseltin

[Azure Search .NET SDK 'sının](https://aka.ms/search-sdk)sürüm 1.0.2-Preview veya daha eski bir sürümünü kullanıyorsanız, bu makale uygulamanızı 1,1 sürümünü kullanacak şekilde yükseltmenize yardımcı olur.

Örnek içeren SDK hakkında daha genel bir anlatım için bkz. [.NET uygulamasından Azure Search kullanma](search-howto-dotnet-sdk.md).

> [!NOTE]
> Sürüm 1,1 ' e yükselttikten sonra veya 1,1 ile 2,0-Önizleme arasında zaten bir sürüm kullanıyorsanız, sürüm 3 ' e yükseltmeniz gerekir. Yönergeler için bkz. [.NET SDK sürüm 3 Azure Search yükseltme](search-dotnet-sdk-migration.md) .
>

İlk olarak, NuGet Paket Yöneticisi konsolunu kullanarak veya proje başvurularınız ' a sağ tıklayıp "NuGet Paketlerini Yönet..." seçeneğini belirleyerek NuGet başvurunuz `Microsoft.Azure.Search` güncelleştirin. Visual Studio 'da.

NuGet yeni paketleri ve bağımlılıklarını indirdikten sonra projenizi yeniden derleyin.

Daha önce 1.0.0-Preview, 1.0.1-Preview veya 1.0.2-Preview sürümünü kullanıyorsanız, derleme başarılı olur ve başlamaya hazırsınız!

Daha önce 0.13.0-Preview veya daha eski sürümü kullanıyorsanız, aşağıdaki gibi derleme hataları görmeniz gerekir:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Sonraki adım derleme hatalarını tek tek düzeltemedi. Çoğu, SDK 'da yeniden adlandırılmış bazı sınıf ve yöntem adlarının değiştirilmesini gerektirir. [Sürüm 1,1 ' deki son değişikliklerin listesi](#ListOfChangesV1) , bu ad değişikliklerinin bir listesini içerir.

Belgelerinizi modellemek için özel sınıflar kullanıyorsanız ve bu sınıfların null yapılamayan temel türlerin özellikleri (örneğin, `int` veya `bool` C#) varsa, bu SDK 'nın, bilmeniz gereken 1,1 sürümünde bir hata düzeltildi. Daha fazla bilgi için bkz. [sürüm 1,1 ' de hata düzeltmeleri](#BugFixesV1) .

Son olarak, herhangi bir yapı hatasını düzelttikten sonra, isterseniz yeni işlevlerden yararlanmak için uygulamanızda değişiklikler yapabilirsiniz.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Sürüm 1,1 ' daki son değişikliklerin listesi
Aşağıdaki liste, değişikliğin uygulama kodunuzu etkileyeceğine göre sıralanır.

### <a name="indexbatch-and-indexaction-changes"></a>Indexbatch ve ındexaction değişiklikleri
`IndexBatch.Create` `IndexBatch.New` olarak yeniden adlandırıldı ve artık bir `params` bağımsız değişkenine sahip değildir. Farklı eylem türlerini (birleştirme, silme, vb.) karıştıraş toplu işler için `IndexBatch.New` kullanabilirsiniz. Ayrıca, tüm eylemlerin aynı olduğu toplu işlemler oluşturmak için yeni statik yöntemler vardır: `Delete`, `Merge`, `MergeOrUpload`ve `Upload`.

`IndexAction` artık ortak oluşturuculara sahip değil ve özellikleri artık sabit. Farklı amaçlara yönelik eylemler oluşturmak için yeni statik yöntemleri kullanmanız gerekir: `Delete`, `Merge`, `MergeOrUpload`ve `Upload`. `IndexAction.Create` kaldırıldı. Yalnızca bir belge alan aşırı yüklemeyi kullandıysanız, bunun yerine `Upload` kullandığınızdan emin olun.

#### <a name="example"></a>Örnek
Kodunuz şuna benziyorsa:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

İsterseniz bunu daha da kolaylaştırmaya devam edebilirsiniz:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Indexbatchexception değişiklikleri
`IndexBatchException.IndexResponse` özelliği `IndexingResults`olarak yeniden adlandırıldı ve türü artık `IList<IndexingResult>`.

#### <a name="example"></a>Örnek
Kodunuz şuna benziyorsa:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>İşlem yöntemi değişiklikleri
Azure Search .NET SDK içindeki her işlem, zaman uyumlu ve zaman uyumsuz çağıranlar için bir dizi yöntem aşırı yüklemesi olarak sunulur. Bu yöntem aşırı yüklemelerinin imzaları ve düzenleme sürümü 1,1 sürümünde değiştirilmiştir.

Örneğin, SDK 'nın eski sürümlerindeki "Dizin Istatistiklerini al" işlemi şu imzaları kullanıma sunuldu:

`IIndexOperations` içinde:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

`IndexOperationsExtensions` içinde:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Sürüm 1,1 ' de aynı işleme yönelik yöntem imzaları şuna benzer:

`IIndexesOperations` içinde:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

`IndexesOperationsExtensions` içinde:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Sürüm 1,1 ' den başlayarak, .NET SDK Azure Search işlem yöntemlerini farklı şekilde düzenler:

* İsteğe bağlı parametreler artık ek yöntem aşırı yüklemeleri yerine varsayılan parametreler olarak modellenir. Bu, bazen önemli ölçüde önemli ölçüde yöntem aşırı yükleme sayısını azaltır.
* Uzantı yöntemleri artık, çağıranın çok sayıda HTTP ayrıntılarını gizler. Örneğin, SDK 'nın daha eski sürümleri, genellikle bir hata belirten herhangi bir durum kodu için işlem yöntemlerinin `CloudException` oluşturduğundan, genellikle denetlemeniz gerekmeyen bir HTTP durum kodu ile bir yanıt nesnesi döndürdü. Yeni uzantı yöntemleri yalnızca model nesnelerini döndürmekte ve bunları kodunuzda sarmalamak zorunda kalmadan yapmanız gereken bir sorun yaşıyor.
* Buna karşılık, çekirdek arabirimler artık ihtiyacınız olduğunda HTTP düzeyinde daha fazla denetim sağlayan yöntemleri kullanıma sunar. Artık isteklere dahil edilecek özel HTTP üstbilgilerini geçirebilir ve yeni `AzureOperationResponse<T>` dönüş türü, işlem için `HttpRequestMessage` ve `HttpResponseMessage` doğrudan erişmenizi sağlar. `AzureOperationResponse`, `Microsoft.Rest.Azure` ad alanında tanımlanmıştır ve `Hyak.Common.OperationResponse`yerini alır.

### <a name="scoringparameters-changes"></a>Değişiklikleri ScoringParameters
Bir arama sorgusunda Puanlama profillerine parametre sağlamayı kolaylaştırmak için en son SDK 'ya `ScoringParameter` adlı yeni bir sınıf eklenmiştir. Daha önce `SearchParameters` sınıfının `ScoringProfiles` özelliği `IList<string>`olarak yazılmış. Artık `IList<ScoringParameter>`olarak yazılmış.

#### <a name="example"></a>Örnek
Kodunuz şuna benziyorsa:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Model sınıfı değişiklikleri
[İşlem yöntemi değişikliklerinde](#OperationMethodChanges)açıklanan imza değişiklikleri nedeniyle `Microsoft.Azure.Search.Models` ad alanındaki birçok sınıf yeniden adlandırıldı veya kaldırılmıştır. Örnek:

* `IndexDefinitionResponse`, `AzureOperationResponse<Index>` tarafından değiştirildi
* `DocumentSearchResponse`, `DocumentSearchResult` olarak yeniden adlandırıldı
* `IndexResult`, `IndexingResult` olarak yeniden adlandırıldı
* `Documents.Count()` artık `DocumentCountResponse` yerine belge sayısı ile bir `long` döndürüyor
* `IndexGetStatisticsResponse`, `IndexGetStatisticsResult` olarak yeniden adlandırıldı
* `IndexListResponse`, `IndexListResult` olarak yeniden adlandırıldı

Özetlemek gerekirse, yalnızca bir model nesnesini kaydırmak için varolan `OperationResponse`türetilmiş sınıflar kaldırılmıştır. Kalan sınıfların son eki `Response` `Result`olarak değiştirildi.

#### <a name="example"></a>Örnek
Kodunuz şuna benziyorsa:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Yanıt sınıfları ve IEnumerable
Kodunuzu etkileyebilecek ek bir değişiklik, koleksiyonları tutan yanıt sınıflarının artık `IEnumerable<T>`uygulamalarıdır. Bunun yerine, doğrudan koleksiyon özelliğine erişebilirsiniz. Örneğin, kodunuz şuna benziyorsa:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Web uygulamaları için özel durum
Doğrudan tarayıcıya arama sonuçları göndermek için `DocumentSearchResponse` seri hale getirilen bir Web uygulamanız varsa, kodunuzu değiştirmeniz gerekir, aksi takdirde sonuçlar doğru serileştirilmeyecektir. Örneğin, kodunuz şuna benziyorsa:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Arama sonucu işlemesini onarmak için arama yanıtının `.Results` özelliğini alarak bunu değiştirebilirsiniz:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Kodunuzda bu tür durumları aramanız gerekecektir; `JsonResult.Data` `object`türünde olduğundan **derleyici sizi uyarmaz** .

### <a name="cloudexception-changes"></a>CloudException değişiklikleri
`CloudException` sınıfı, `Hyak.Common` ad alanından `Microsoft.Rest.Azure` ad alanına taşındı. Ayrıca, `Error` özelliği `Body`olarak yeniden adlandırıldı.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient ve Searchındexclient değişiklikleri
`Credentials` özelliğinin türü, `SearchCredentials` olan `ServiceClientCredentials`temel sınıfına değişti. `SearchIndexClient` veya `SearchServiceClient``SearchCredentials` erişmeniz gerekiyorsa, lütfen yeni `SearchCredentials` özelliğini kullanın.

SDK 'nın daha eski sürümlerinde, `SearchServiceClient` ve `SearchIndexClient` bir `HttpClient` parametresi geçen oluşturuculara sahipti. Bunlar, bir `HttpClientHandler` ve `DelegatingHandler` nesneleri dizisi alan oluşturucularla değiştirilmiştir. Bu, gerekirse HTTP isteklerini önceden işlemek için özel işleyiciler yüklemeyi kolaylaştırır.

Son olarak, bir `Uri` ve `SearchCredentials` geçen oluşturucular değişmiştir. Örneğin, aşağıdakine benzer bir kodunuz varsa:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Ayrıca, kimlik bilgileri parametresinin türünün `ServiceClientCredentials`olarak değiştirildiğini unutmayın. `SearchCredentials` `ServiceClientCredentials`türetildiği için kodunuzun etkileri düşüktür.

### <a name="passing-a-request-id"></a>İstek KIMLIĞI geçirme
SDK 'nın eski sürümlerinde, `SearchServiceClient` veya `SearchIndexClient` bir istek KIMLIĞI ayarlayabilir ve bu, REST API her isteğine dahil edilebilir. Bu, desteğe başvurmanız gerekirse arama hizmetinizdeki sorunları gidermeye yarar. Ancak, tüm işlemler için aynı KIMLIĞI kullanmak yerine her işlem için benzersiz bir istek KIMLIĞI ayarlamak daha yararlıdır. Bu nedenle, `SearchServiceClient` ve `SearchIndexClient` `SetClientRequestId` yöntemleri kaldırılmıştır. Bunun yerine, isteğe bağlı `SearchRequestOptions` parametresi aracılığıyla her bir işlem yöntemine bir istek KIMLIĞI geçirebilirsiniz.

> [!NOTE]
> SDK 'nın gelecek bir sürümünde, diğer Azure SDK 'Ları tarafından kullanılan yaklaşımla tutarlı olan istemci nesnelerinde bir istek KIMLIĞINI genel olarak ayarlamaya yönelik yeni bir mekanizma ekleyeceğiz.
> 
> 

### <a name="example"></a>Örnek
Aşağıdakine benzer bir kodunuz varsa:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Herhangi bir derleme hatasını çözebilmeniz için bunu bu şekilde değiştirebilirsiniz:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Arabirim adı değişiklikleri
İşlem grubu arabirim adlarının hepsi karşılık gelen özellik adlarıyla tutarlı olacak şekilde değişti:

* `ISearchServiceClient.Indexes` türü `IIndexOperations` `IIndexesOperations`olarak yeniden adlandırıldı.
* `ISearchServiceClient.Indexers` türü `IIndexerOperations` `IIndexersOperations`olarak yeniden adlandırıldı.
* `ISearchServiceClient.DataSources` türü `IDataSourceOperations` `IDataSourcesOperations`olarak yeniden adlandırıldı.
* `ISearchIndexClient.Documents` türü `IDocumentOperations` `IDocumentsOperations`olarak yeniden adlandırıldı.

Bu arabirimlerin test amaçları doğrultusunda bu arabirimlerin bir listesini oluşturmadığınız müddetçe bu değişiklik, kodunuzun etkilenmemesi için olası bir olasılıktır.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Sürüm 1,1 ' de hata düzeltmeleri
Azure Search .NET SDK 'sının özel model sınıflarının serileştirilmesi ile ilgili eski sürümlerinde bir hata oluştu. Null olamayan bir değer türünün özelliği ile özel bir model sınıfı oluşturduysanız hata oluşabilir.

### <a name="steps-to-reproduce"></a>Yeniden oluşturma adımları
Null olamayan değer türünde bir özelliği olan özel bir model sınıfı oluşturun. Örneğin, `int?`yerine `int` türünde bir ortak `UnitCount` özelliği ekleyin.

Bir belgeyi bu türün varsayılan değeriyle dizinederseniz (örneğin, `int`için 0), alan Azure Search null olacaktır. Bu belgeyi daha sonra arıyorsanız `Search` çağrısı, `null` `int`dönüştüremediğinden `JsonSerializationException` şikayetler oluşturur.

Ayrıca, istenen değer yerine null dizine yazıldığı için filtreler beklendiği gibi çalışmayabilir.

### <a name="fix-details"></a>Ayrıntıları giderme
Bu sorunu SDK sürüm 1,1 ' de düzelttik. Şimdi şöyle bir model sınıfınız varsa:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

`IntValue` 0 olarak ayarlarsanız, bu değer artık kabloda 0 olarak doğru şekilde serileştirilir ve dizinde 0 olarak depolanır. Yuvarlak Daire ayrıca beklendiği gibi da çalışmaktadır.

Bu yaklaşımla farkında olmak için olası bir sorun var: null yapılamayan bir özellik ile model türü kullanırsanız, dizininizdeki hiçbir belgenin karşılık gelen alan için null değer **içermediğinden emin olmanız** gerekir. SDK ne de Azure Search REST API bunu zorunlu etmenize yardımcı olur.

Bu yalnızca kuramsal bir sorun değildir: Var olan `Edm.Int32` türünde bir dizine yeni bir alan eklediğiniz bir senaryoyu düşünün. Dizin tanımını güncelleştirdikten sonra, tüm belgelerin bu yeni alan için boş bir değeri olur (bunun nedeni, Azure Search'te tüm türlerin boş değer atanabilir olmasıdır). Ardından bu alan için boş değer atanamayan bir `int` özelliğiyle bir model sınıfı kullanırsanız belgeleri almaya çalışırken bunun gibi bir `JsonSerializationException` alırsınız:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Bu nedenle, en iyi uygulama olarak model sınıflarınızda null yapılabilir türler kullanmanızı öneririz.

Bu hata ve düzeltmeyle ilgili daha fazla bilgi için lütfen [GitHub 'da Bu soruna](https://github.com/Azure/azure-sdk-for-net/issues/1063)bakın.

