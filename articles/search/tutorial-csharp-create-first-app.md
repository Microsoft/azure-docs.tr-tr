---
title: İlk uygulamanızı oluşturmak için C# öğreticisi
titleSuffix: Azure Cognitive Search
description: İlk C# arama uygulamanızı adım adım oluşturmayı öğrenin. Öğretici, GitHub 'da çalışan bir uygulamaya yönelik bir indirme bağlantısı ve uygulamayı sıfırdan oluşturmak için tamamlanmış bir işlem sağlar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695133"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak ilk arama uygulamanızı oluşturma

Bu öğreticide, Azure Bilişsel Arama ve Visual Studio kullanarak arama dizininden sonuçları sorgulayan ve döndüren bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Geliştirme ortamı ayarlama
> * Model veri yapıları
> * Sorgu girişlerini toplamak ve sonuçları göstermek için bir Web sayfası oluşturun
> * Arama yöntemi tanımlama
> * Uygulamayı test etme

Ayrıca, bir arama çağrısının ne olduğunu de öğreneceksiniz. Geliştirileceği koddaki anahtar deyimler aşağıdaki birkaç satırda kapsüllenir.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Yalnızca bir çağrı dizini sorgular ve sonuçları döndürür.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="* Havuzu * aranıyor" border="true":::

## <a name="overview"></a>Genel Bakış

Bu öğretici, [veri alma hızlı](search-get-started-portal.md)başlangıcı aracılığıyla kendi arama hizmetinize hızlı bir şekilde oluşturabileceğiniz oteller-Sample-Index ' i kullanır. Dizin, her arama hizmetinde yerleşik bir veri kaynağı olarak bulunan kurgusal otel verileri içeriyor.

Bu öğreticideki ilk derste, sonraki derslerde sayfalama, modeller ve bir tür ön deneyim dahil olmak üzere geliştirecek olan temel bir sorgu yapısı ve arama sayfası oluşturulur.

Kodun tamamlanmış bir sürümü aşağıdaki projede bulunabilir:

* [1-temel-arama sayfası (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Bu öğretici, Azure.Search.Documstalar (sürüm 11) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için bkz. [Microsoft. Azure. Search (sürüm 10) kod örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Önkoşullar

* [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun.

* [Hızlı başlangıç: arama dizini oluşturma](search-get-started-portal.md)bölümündeki yönergeleri kullanarak oteller-Sample-Index oluşturun.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure Bilişsel Arama istemci kitaplığı (sürüm 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Projeyi GitHub 'dan yükleyip çalıştırın

Çalışan bir uygulamaya geçmek istiyorsanız, tamamlanmış kodu indirmek ve çalıştırmak için aşağıdaki adımları izleyin.

1. GitHub 'da örneği bulun: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. [Kök klasörde](https://github.com/Azure-Samples/azure-search-dotnet-samples), **kod**' u seçin, ardından **kopya** veya **ZIP** ' i yükleyerek projenin özel yerel kopyasını oluşturun.

1. Visual Studio 'yu kullanarak öğesine gidin ve temel arama sayfası ("1-temel-arama-sayfası") çözümünü açın ve **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın) ve programı oluşturun ve çalıştırın.

1. Bu bir oteller dizinidir, bu nedenle oteller aramak için kullanabileceğiniz bazı sözcükleri yazın (örneğin, "WiFi", "Görünüm", "çubuk", "Park") ve sonuçları inceleyin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="* WiFi * aranıyor" border="true":::

Bu proje sorunsuz bir şekilde çalışır ve Web uygulamanız çalışır. Daha karmaşık aramalar için gereken birçok önemli bileşen bu uygulamaya dahil edilmiştir ve bu sayede adım adım adım yeniden oluşturmanız iyi bir fikirdir. Aşağıdaki bölümlerde bu adımlar ele alınmaktadır.

## <a name="set-up-a-development-environment"></a>Geliştirme ortamı ayarlama

Bu projeyi sıfırdan oluşturmak ve bu nedenle Azure Bilişsel Arama kavramlarını göz önünde bulundurarak bir Visual Studio projesi ile başlayın.

1. Visual Studio 'da **Yeni**  >  **Proje**' yi ve ardından **Web uygulaması ASP.NET Core**' nı seçin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Bulut projesi oluşturma" border="true":::

1. Projeye "FirstSearchApp" gibi bir ad verin ve konumu ayarlayın. **Oluştur**’u seçin.

1. **Web uygulaması (Model-View-Controller)** proje şablonunu seçin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="MVC projesi oluşturma" border="true":::

1. İstemci kitaplığını yükler. **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **çözüm için NuGet Paketlerini Yönet...**' i seçin  , ardından "azure.search.documlar" için arama yapın. Lisans sözleşmelerini ve bağımlılıklarını kabul ederek **Azure.Search.Documtları** (sürüm 11 veya üzeri) yükler.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Azure kitaplıklarını eklemek için NuGet kullanma" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure Bilişsel Arama Başlat

Bu örnekte, genel kullanıma açık otel verileri kullanıyorsunuz. Bu veriler, yalnızca tanıtım verileri sağlamak amacıyla oluşturulan, 50 kurgusal otel adı ve açıklamalarının rastgele bir koleksiyonudur. Bu verilere erişmek için bir ad ve API anahtarı belirtin.

1. **appsettings.js** açın ve varsayılan satırları arama hizmeti URL 'si (biçimde) ile değiştirin ve `https://<service-name>.search.windows.net` arama hizmetinizin [yönetici veya sorgu API anahtarını](search-security-api-keys.md) değiştirin. Bir dizin oluşturmanız veya güncelleştirmeniz gerekmiyorsa, bu öğreticide sorgu anahtarını kullanabilirsiniz.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. Çözüm Gezgini, dosyayı seçin ve Özellikler ' de, **daha yeniyse**, **Çıkış Dizinine Kopyala** ayarını değiştirin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Uygulama ayarları çıkışa kopyalanıyor" border="true":::

## <a name="model-data-structures"></a>Model veri yapıları

Modeller (C# sınıfları) istemci (görünüm), sunucu (denetleyici) ve ayrıca MVC (model, görünüm, denetleyici) mimarisi kullanılarak Azure bulutu arasındaki verileri iletmek için kullanılır. Genellikle, bu modeller erişildiği verilerin yapısını yansıtır.

Bu adımda, arama dizininin veri yapılarını ve ayrıca görünüm/denetleyici iletişimlerinde kullanılan arama dizesini modelleyebilirsiniz. Oteller dizininde, her otel birçok odaya sahiptir ve her otel çok parçalı bir adrese sahiptir. Tamamen, bir otelin tam gösterimi hiyerarşik ve iç içe geçmiş bir veri yapısıdır. Her bileşeni oluşturmak için üç sınıfa ihtiyacınız olacak.

**Otel**, **Adres** ve **oda** sınıfları kümesi, Azure bilişsel arama 'ın önemli bir özelliği olan [*karmaşık türler*](search-howto-complex-data-types.md)olarak bilinir. Karmaşık türler, sınıfların ve alt sınıfların çok fazla düzeyi olabilir ve *basit türler* (yalnızca temel Üyeler içeren bir sınıf) kullanmaktan daha karmaşık veri yapılarının kullanılmasına olanak tanır.

1. Çözüm Gezgini, **modeller**  >    >  **Yeni öğe** Ekle ' ye sağ tıklayın.

1. **Sınıf** ' ı seçin ve öğeyi otel. cs olarak adlandırın. Otel. cs ' nin tüm içeriğini aşağıdaki kodla değiştirin. Sınıfın **Adres** ve **Oda** üyelerine, bu alanların kendileri için de modeller ihtiyaç duyduğuna dikkat edin.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. **Adres sınıfı için** bir model oluşturma işlemini yineleyin, bu dosya adresini. cs olarak adlandırın. İçeriği aşağıdaki kodla değiştirin.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Daha sonra, **Oda sınıfını oluşturmak** için aynı süreci izleyin. cs dosyasını adlandırarak.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Bu öğreticide oluşturacağınız son model **Searchdata** adlı bir sınıftır ve kullanıcının girişini (**searchtext**) ve aramanın çıkışını (**resultlist**) temsil eder. Çıktının türü kritik ve **SearchResults &lt; otel &gt;**'tir. bu tür, aramanın sonuçlarıyla tam olarak eşleşir ve bu başvuruyu görünümüne geçirmeniz gerekir. Varsayılan şablonu aşağıdaki kodla değiştirin.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Web sayfası oluşturma

Proje şablonları, **Görünümler** klasöründe bulunan çok sayıda istemci görünümleriyle gelir. Tam görünümler, kullanmakta olduğunuz çekirdek .NET sürümüne bağlıdır (Bu örnekte 3,1 kullanılır). Bu öğreticide, **Dizin. cshtml** 'yi bir arama sayfasının öğelerini içerecek şekilde değiştirirsiniz.

Index. cshtml içeriğini tamamen silin ve aşağıdaki adımlarda dosyayı yeniden derleyin.

1. Öğretici, görünümde iki küçük görüntü kullanır: bir Azure logosu ve bir arama Büyüteç simgesi (azure-logo.png ve search.png). GitHub projesinden görüntü genelinde projenizdeki **Wwwroot/görüntüler** klasörüne kopyalayın.

1. Index. cshtml 'nin ilk satırı, istemci (görünüm) ve sunucu (denetleyici) arasında verileri iletmek için kullanılan modele başvurmalıdır ve bu, daha önce oluşturulan **Searchdata** modeli olur. Bu satırı Index. cshtml dosyasına ekleyin.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Görünüm için bir başlık girmek standart bir uygulamadır, bu nedenle sonraki satırların olması gerekir:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Başlığı izleyerek, kısa bir süre içinde oluşturacağınız HTML stil sayfasına bir başvuru girin.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. Görünümün gövdesinde iki kullanım durumu ele alır. İlk olarak, herhangi bir arama metni girmeden önce, ilk kullanımda boş bir sayfa sağlamalıdır. İkinci olarak, yinelenen sorgular için arama metin kutusuna ek olarak sonuçların işlenmesi gerekir.

   Her iki durumu da işlemek için, görünüme girilen modelin null olup olmadığını denetlemeniz gerekir. Boş bir model, ilk kullanım durumunu gösterir (uygulamanın ilk çalışması). Aşağıdaki dizini Index. cshtml dosyasına ekleyin ve yorumları okuyun.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Stil sayfasını ekleyin. Visual Studio 'da **Dosya** >  **Yeni**  >  **dosyası**' nda **stil sayfası** ' nı ( **genel** vurgulanmış olarak) seçin.

   Varsayılan kodu aşağıdaki kodla değiştirin. Bu dosyaya daha ayrıntılı bir şekilde gitmeyecekiz, stiller standart HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Stil sayfası dosyasını, varsayılan site. css dosyasının yanı sıra **Wwwroot/CSS** klasörüne oteller. CSS olarak kaydedin.

Görünümümüzü tamamlar. Bu noktada, hem modeller hem de görünümler tamamlanır. Her şeyi birbirine bağlamak için yalnızca denetleyici bırakılır.

## <a name="define-methods"></a>Yöntemleri tanımlama

Bu adımda, **ana denetleyicinin** içeriğiyle değiştirin.

1. HomeController. cs dosyasını açın ve **using** deyimlerini aşağıdaki ile değiştirin.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Dizin yöntemleri ekleme

MVC uygulamasında, **Index ()** yöntemi herhangi bir denetleyici için varsayılan bir eylem yöntemidir. Dizin HTML sayfasını açar. Bu öğreticide, hiçbir parametre alan varsayılan yöntem, uygulama başlatma kullanım örneği: boş bir arama sayfası işleme için kullanılır.

Bu bölümde, yöntemi ikinci kullanım örneğini destekleyecek şekilde genişlettik: bir Kullanıcı Arama metnine girildiğinde sayfayı işleme. Bu durumu desteklemek için Dizin yöntemi bir modeli parametre olarak alacak şekilde genişletilir.

1. Varsayılan **Index ()** yönteminden sonra aşağıdaki yöntemi ekleyin.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Metodun **zaman uyumsuz** bildirimine ve **Runqueryasync** için **await** çağrısına dikkat edin. Bu anahtar sözcükler, zaman uyumsuz çağrılar yapmaktan kaçınır ve bu nedenle sunucudaki iş parçacıklarını engellemeyi önleyin.

    **Catch** bloğu varsayılan olarak oluşturulan hata modelini kullanır.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Hata işleme ve diğer varsayılan görünümleri ve yöntemleri göz önünde

Kullandığınız .NET Core sürümüne bağlı olarak, varsayılan olarak farklı bir varsayılan görünüm kümesi oluşturulur. .NET Core 3,1 için varsayılan görünümler Dizin, gizlilik ve hatadır. Uygulamayı çalıştırırken bu varsayılan sayfaları görüntüleyebilir ve bunların denetleyicide nasıl işlendiğini inceleyebilirsiniz.

Bu öğreticide daha sonra hata görünümünü test edersiniz.

GitHub örneğinde, kullanılmayan görünümler ve bunlarla ilişkili eylemler silinir.

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync yöntemini ekleyin

Azure Bilişsel Arama çağrısı **Runqueryasync** yöntemi içinde kapsüllenir.

1. İlk olarak, Azure hizmetini ayarlamak için bazı statik değişkenler ve bunları başlatmak için bir çağrı ekleyin.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Şimdi **Runqueryasync** yönteminin kendisini ekleyin.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    Bu yöntemde, önce Azure yapılandırmanızın başlatılmış olduğundan emin olun, sonra bazı arama seçeneklerini ayarlayın. **Seçim** seçeneği, sonuçlarda döndürülecek alanları belirtir ve bu nedenle **otel** sınıfındaki Özellik adlarıyla eşleşir. **Seç**' i atlarsanız, tüm gizli olmayan alanlar döndürülür, bu, yalnızca tüm olası alanların bir alt kümesiyle ilgileniyorsanız, verimsiz olabilir.

    Arama için zaman uyumsuz çağrı, isteği ( **searchtext** olarak modellenen) ve yanıtı ( **SearchResult** olarak modellenen) göre formüle göre ifade edin. Bu kodda hata ayıklaması yapıyorsanız, **model. resultList** öğesinin içeriğini incelemeniz gerekiyorsa, bir kesme noktası ayarlamak Için **SearchResult** sınıfı iyi bir adaydır. Bunun sezgisel olduğunu, daha fazla bilgi edinmek istediğiniz verileri sağlar.

### <a name="test-the-app"></a>Uygulamayı test etme

Şimdi uygulamanın doğru şekilde çalışıp çalışmadığını denetlim.

1. Hata   >  **ayıklamadan Başlat** ' ı seçin veya **F5** tuşuna basın. Uygulama beklendiği gibi çalışırsa, ilk dizin görünümünü almalısınız.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Uygulamayı açma" border="true":::

1. "Plaj" gibi bir sorgu dizesi girin (veya aklınızda gelen herhangi bir metin) ve isteği göndermek için arama simgesine tıklayın.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="* Plaj * aranıyor" border="true":::

1. "Beş yıldız" girmeyi deneyin. Bu sorgunun hiçbir sonuç döndürmediğine dikkat edin. Daha karmaşık bir arama "merkezlerini" için "beş yıldızlı" bir eş anlamlı olarak değerlendirilir ve bu sonuçları döndürür. [Eş anlamlılar](search-synonyms.md) desteği Azure bilişsel arama 'da kullanılabilir, ancak bu öğretici serisinin kapsamına alınmıyor.

1. Arama metni olarak "sık erişimli" girmeyi deneyin. Bu, içinde "otel" kelimesiyle _giriş döndürmez._ Aramamız yalnızca tüm kelimeleri bulur, ancak birkaç sonuç döndürülür.

1. Diğer sözcükleri deneyin: "havuz", "güneş ışığı", "Görünüm" ve herhangi bir şey. Azure Bilişsel Arama en basit, ancak yine de ikna edici bir düzeyde çalıştığını göreceksiniz.

## <a name="test-edge-conditions-and-errors"></a>Test Edge koşulları ve hataları

Hata işleme özelliklerimizin, her şeyin mükemmel şekilde çalıştığı durumlarda bile çalıştığını doğrulamak önemlidir. 

1. **Dizin** yönteminde, **TRY {** çağrısından sonra **Yeni özel durum oluştur ()** satırını girin. Bu özel durum, metinde arama yaptığınızda bir hataya zorlanır.

2. Uygulamayı çalıştırın, arama metni olarak "çubuk" yazın ve arama simgesine tıklayın. Özel durum hata görünümü ile sonuçlanmalıdır.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Bir hataya zorla" border="true":::

    > [!Important]
    > Hata sayfalarında iç hata numaralarını döndürmek için bir güvenlik riski olarak değerlendirilir. Uygulamanızın genel kullanıma yönelik olması amaçlanıyorsa, bir hata oluştuğunda neler Dönebileceğinize ilişkin güvenli ve en iyi uygulamalara yönelik bazı araştırma yapın.

3. Hata işlemenin gerektiği gibi çalıştığını karşıladığınızda, **Yeni özel durum ()** öğesini kaldırın.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Azure Bilişsel Arama çağrısı kısa bir işlemdir ve sonuçların yorumlanması kolaydır.
* Zaman uyumsuz çağrılar denetleyiciye az miktarda karmaşıklık ekler, ancak kaliteli uygulamalar geliştirmek istiyorsanız en iyi uygulamadır.
* Bu uygulama, **SearchOptions**'da ayarlananla tanımlanan, düz metin araması gerçekleştirdi. Ancak, bu bir sınıf, bir aramaya gelişmiş algoritmaların mümkündür ekleyen birçok üye ile doldurulabilirler. Bu uygulamayı önemli ölçüde daha güçlü hale getirmek için çok ek iş gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcı deneyimini geliştirmek için daha fazla özellik ekleyin, özellikle sayfalama (sayfa numaralarını veya sonsuz kaydırmayı kullanarak) ve otomatik tamamlama/öneriler ekleyin. Ayrıca daha karmaşık arama seçeneklerini de göz önünde bulundurun (örneğin, belirli bir noktanın belirtilen yarıçapı içindeki oteller üzerinde coğrafi aramalar ve arama sonuçları sıralaması).

Bu sonraki adımlar, kalan öğreticilerde giderilmiştir. Sayfalama ile başlayalım.

> [!div class="nextstepaction"]
> [C# öğreticisi: arama sonuçları sayfalandırma-Azure Bilişsel Arama](tutorial-csharp-paging.md)