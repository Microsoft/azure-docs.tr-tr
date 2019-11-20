---
title: C#ilk uygulamanızı oluşturma öğreticisi
titleSuffix: Azure Cognitive Search
description: İlk C# arama uygulamanızı nasıl oluşturacağınızı öğrenin adım adım. Öğretici, GitHub 'da çalışan bir uygulamanın bağlantısını ve uygulamayı sıfırdan oluşturmak için tüm işlemleri sağlar. Azure Bilişsel Arama 'nin temel bileşenleri hakkında bilgi edinin.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: fc3d9d52ca6eb3d35138323908c73eb0d09cf519
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112236"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C#Öğretici: ilk uygulamanızı oluşturma-Azure Bilişsel Arama

Azure Bilişsel Arama kullanarak bir dizinden arama sonuçlarını sorgulamak ve sunmak için bir Web arabirimi oluşturmayı öğrenin. Bu öğretici, bir arama sayfası oluşturmaya odaklanabilmeniz için mevcut, barındırılan bir dizinle başlar. Dizin, kurgusal otel verileri içeriyor. Temel bir sayfanız olduktan sonra, sayfalama, modeller ve bir tür ön deneyim dahil olmak üzere sonraki derslerde bunu geliştirebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Geliştirme ortamı kurma
> * Model veri yapıları
> * Web sayfası oluşturma
> * Yöntemleri tanımlama
> * Uygulamayı test etme

Ayrıca, bir arama çağrısının ne olduğunu de öğreneceksiniz. Geliştirileceği koddaki anahtar deyimler aşağıdaki birkaç satırda kapsüllenir.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Bu bir çağrı Azure verilerinin bir aramasını başlatır ve sonuçları döndürür.

!["Havuz" aranıyor](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

IDE olarak kullanmak için [Visual Studio 'Yu yükler](https://visualstudio.microsoft.com/) .

### <a name="install-and-run-the-project-from-github"></a>Projeyi GitHub 'dan yükleyip çalıştırın

1. GitHub 'da örneği bulun: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. **Kopyala veya indir** ' i seçin ve projenin özel yerel kopyasını yapın.
1. Visual Studio 'yu kullanarak öğesine gidin ve temel arama sayfasına yönelik çözümü açın ve **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın).
1. Bazı sözcükleri yazın (örneğin, "WiFi", "Görünüm", "çubuk", "Park") ve sonuçları inceleyin!

    !["WiFi" aranıyor](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Bu proje sorunsuz bir şekilde çalışacaktır ve Azure uygulaması çalışıyor. Daha karmaşık aramalar için gereken birçok önemli bileşen bu uygulamaya dahil edilmiştir ve bu sayede adım adım adım yeniden oluşturmanız iyi bir fikirdir.

Bu projeyi sıfırdan oluşturmak ve bu nedenle Azure Bilişsel Arama bileşenlerini aklınızda bulundurmaya yardımcı olmak için aşağıdaki adımları izleyin.

## <a name="set-up-a-development-environment"></a>Geliştirme ortamı ayarlama

1. Visual Studio 2017 veya sonraki sürümlerde, **Yeni/proje** ' yi **ASP.NET Core Web uygulaması**' nı seçin. Projeye "FirstAzureSearchApp" gibi bir ad verin.

    ![Bulut projesi oluşturma](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Bu proje türü için **Tamam** ' a tıkladıktan sonra bu proje için uygulanan ikinci bir seçenek kümesi sağlanacaktır. **Web uygulaması (Model-View-Controller)** seçeneğini belirleyin.

    ![MVC projesi oluşturma](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Ardından, **Araçlar** menüsünde, **NuGet Paket Yöneticisi** ' ni seçin ve ardından **çözüm için NuGet paketlerini yönetin...** . Yüklenmesi gereken bir paket var. **Araştır sekmesini seçin** ve arama kutusuna "Azure bilişsel arama" yazın. Listede (sürüm 9.0.1 veya üzeri) göründüğünde **Microsoft. Azure. Search** uygulamasını yükler. Yüklemeyi tamamlaması için birkaç ek iletişim kutusuna tıklamanız gerekir.

    ![Azure kitaplıklarını eklemek için NuGet kullanma](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Azure Bilişsel Arama Başlat

Bu örnekte, genel kullanıma açık otel verileri kullanıyoruz. Bu veriler, yalnızca tanıtım verileri sağlamak amacıyla oluşturulan, 50 kurgusal otel adı ve açıklamalarının rastgele bir koleksiyonudur. Bu verilere erişebilmek için bir ad ve anahtar belirtmeniz gerekir.

1. Yeni projenizde appSettings. json dosyasını açın ve varsayılan satırları aşağıdaki ad ve anahtarla değiştirin. Burada gösterilen API anahtarı bir anahtara örnek değildir, _yalnızca_ otel verilerine erişmeniz gereken anahtardır. AppSettings. JSON dosyanız şimdi şöyle görünmelidir.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Henüz bu dosyayla yapılmadınız, bu dosya için özellikleri seçin ve **Çıkış Dizinine Kopyala** ayarını **daha yeniyse kopyala**olarak değiştirin.

    ![Uygulama ayarları çıkışa kopyalanıyor](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Model veri yapıları

Modeller (C# sınıflar) istemci (görünüm), sunucu (denetleyici) ve ayrıca MVC (model, görünüm, denetleyici) mimarisi kullanılarak Azure bulutu arasındaki verileri iletmek için kullanılır. Genellikle, bu modeller erişildiği verilerin yapısını yansıtır. Ayrıca, görünüm/denetleyici iletişimlerini işlemek için bir modele ihtiyacımız var.

1. Çözüm Gezgini kullanarak projenizin **modeller** klasörünü açın ve burada bir varsayılan model göreceksiniz: **ErrorViewModel.cs**.

2. **Modeller** klasörüne sağ tıklayın ve ardından **Yeni öğe** **Ekle** ' yi seçin. Ardından, görüntülenen iletişim kutusunda ilk seçenek **sınıfını** **ASP.NET Core** ' yi seçin. . Cs dosyasını Hotel.cs olarak yeniden adlandırın ve **Ekle**' ye tıklayın. Hotel.cs öğesinin tüm içeriğini aşağıdaki kodla değiştirin. Sınıfın **Adres** ve **Oda** üyelerine, bu alanların kendileri için de modellere ihtiyaç duyuruz olduğunu fark edersiniz.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. **Adres** sınıfı için bir model oluşturma sürecini takip edin, örneğin, Address.cs adlı dosyayı adlandırın. İçeriği aşağıdaki kodla değiştirin.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. Yine de, Room.cs dosyasını adlandırarak **Oda** sınıfını oluşturmak için aynı süreci izleyin. Yine, içeriği aşağıdaki ile değiştirin.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. **Otel**, **Adres**ve **Oda** sınıfları kümesi, Azure 'da önemli bir özelliktir ve Azure bilişsel arama [_karmaşık türler_](search-howto-complex-data-types.md)olarak bilinirler. Karmaşık türler, sınıfların ve alt sınıfların çok fazla düzeyi olabilir ve _basit türler_ (yalnızca temel Üyeler içeren bir sınıf) kullanmaktan daha karmaşık veri yapılarının kullanılmasına olanak tanır. Bir modelin daha olması gerekir, bu nedenle yeni bir model sınıfı oluşturma işlemini yeniden deneyin. bu kez, SearchData.cs sınıfını çağırır ve varsayılan kodu aşağıdaki kodla değiştirin.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Bu sınıf kullanıcının girişini (**searchtext**) ve aramanın çıkışını (**resultlist**) içerir. Çıktının türü kritiktir, **Documentsearchresult&lt;otel&gt;** ve bu tür, aramanın sonuçlarıyla tam olarak eşleştiğinden, bu başvuruyu görünüme iletmemiz gerekir.



## <a name="create-a-web-page"></a>Web sayfası oluşturma

Oluşturduğunuz proje varsayılan olarak bir dizi istemci görünümü oluşturur. Tam görünümler, kullanmakta olduğunuz çekirdek .NET sürümüne bağlıdır (Bu örnekte 2,1 kullanıyoruz). Bunlar, projenin **Görünümler** klasöründe bulunur. Yalnızca Index. cshtml dosyasını değiştirmeniz gerekir ( **Görünümler/giriş** klasörü).

Index. cshtml içeriğini tamamen silin ve aşağıdaki adımlarda dosyayı yeniden derleyin.

1. Görünümde iki küçük resim kullanıyoruz. GitHub projesinden kendi kendinize veya görüntü genelinde kopyalayabilirsiniz: Azure-logo. png ve Search. png. Bu iki görüntü **Wwwroot/görüntüler** klasörüne yerleştirilmelidir.

2. Index. cshtml 'nin ilk satırı, istemci (görünüm) ve sunucu (denetleyici) arasında veri iletişim kurmak için kullanacağınız modele başvurmalıdır. Bu, oluşturduğumuz **Searchdata** modelidir. Bu satırı Index. cshtml dosyasına ekleyin.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Görünüm için bir başlık girmek standart bir uygulamadır, bu nedenle sonraki satırların olması gerekir:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Başlığı izleyerek, kısa bir süre içinde oluşturacağız HTML stil sayfasına bir başvuru girin.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Şimdi görünümün et 'e. Anımsanması gereken önemli şey, görünümün iki durum işlemesini sağlar. İlk olarak, uygulama ilk kez başlatıldığında ve Kullanıcı henüz herhangi bir arama metni girmediyse, ekranı işlemelidir. İkinci olarak, Kullanıcı tarafından yinelenen kullanım için arama metin kutusuna ek olarak sonuçların görüntülenmesini ele almalıdır. Bu iki durum için, görünüme girilen modelin null olup olmadığını denetliyoruz. Boş bir model, iki durumda (uygulamanın ilk kez çalıştırıldığımız) biz olduğunu gösterir. Aşağıdaki dizini Index. cshtml dosyasına ekleyin ve yorumları okuyun.

    ```cs
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
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Son olarak, stil sayfasını ekliyoruz. Visual Studio 'da, **Dosya** menüsünde **yeni/dosya** ' yı ve ardından **stil sayfası** ' nı ( **genel** vurgulanmış olarak) seçin. Varsayılan kodu aşağıdaki kodla değiştirin. Bu dosyaya daha ayrıntılı bir şekilde gitmeyecekiz, stiller standart HTML.

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

7. Stil sayfası dosyasını, varsayılan site. css dosyasının yanı sıra Wwwroot/CSS klasörüne oteller. CSS olarak kaydedin.

Görünümümüzü tamamlar. İyi bir ilerleme yapıyoruz. Modeller ve görünümler tamamlanır, her şeyi birbirine bağlamak için yalnızca denetleyici bırakılır.

## <a name="define-methods"></a>Yöntemleri tanımlama

Varsayılan olarak oluşturulan bir denetleyicinin (**giriş denetleyicisi**) içeriğini değiştirmemiz gerekiyor.

1. HomeController.cs dosyasını açın ve **using** deyimlerini aşağıdaki kodla değiştirin.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Dizin yöntemleri ekleme

Tek bir parametre (örneğin, uygulama ilk açıldığında) ve bir modeli parametre olarak (Kullanıcı arama metni girdiyse için) alan iki **Dizin** yöntemine ihtiyacımız var. Bu yöntemlerin ilki varsayılan olarak oluşturulur. 

1. Varsayılan **Index ()** yönteminden sonra aşağıdaki yöntemi ekleyin.

    ```cs
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

    Metodun **zaman uyumsuz** bildirimine ve **Runqueryasync**için **await** çağrısına dikkat edin. Bu anahtar sözcükler, çağrılarımızın zaman uyumsuz olmasını ve sunucudaki iş parçacıklarını engellemeyi önleyin.

    **Catch** bloğu, varsayılan olarak ABD için oluşturulan hata modelini kullanır.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Hata işleme ve diğer varsayılan görünümleri ve yöntemleri göz önünde

Kullandığınız .NET Core sürümüne bağlı olarak, varsayılan olarak farklı bir varsayılan görünüm kümesi oluşturulur. .NET Core 2,1 için varsayılan görünümler Dizin, hakkında, Iletişim, gizlilik ve hata ' dir. .NET Core 2,2 için, örneğin, varsayılan görünümler Dizin, gizlilik ve hatadır. Her iki durumda da, uygulamayı çalıştırırken bu varsayılan sayfaları görüntüleyebilir ve bunların denetleyicide nasıl işlendiğini inceleyebilirsiniz.

Bu öğreticide daha sonra hata görünümünü test edeceğiz.

GitHub örneğinde, kullanılmamış görünümleri ve bunlarla ilişkili eylemleri sildik.

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync yöntemini ekleyin

Azure Bilişsel Arama çağrısı **Runqueryasync** yöntemi içinde kapsüllenir.

1. İlk olarak, Azure hizmetini ayarlamak için bazı statik değişkenler ve bunları başlatmak için bir çağrı ekleyin.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Şimdi **Runqueryasync** yönteminin kendisini ekleyin.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Bu yöntemde, önce Azure yapılandırmanızın başlatılmış olduğundan emin olun, sonra bazı arama parametrelerini ayarlayın. **Select** parametresindeki alanların adları, **otel** sınıfındaki Özellik adlarıyla tamamen eşleşir. **Select** parametresini bırakmak mümkündür, bu durumda tüm özellikler döndürülür. Ancak, yalnızca verilerin bir alt kümesiyle ilgileniyorsanız, hiçbir **Select** parametresi ayarı verimsiz değildir. İlgilendiğiniz özellikleri belirterek, yalnızca bu özellikler döndürülür.

    Arama için zaman uyumsuz çağrı (**model. resultList = await _indexClient. Documents. SearchAsync&lt;otel&gt;(model. searchText, Parameters);** ), bu öğretici ve uygulamanın her şeyi sağlar. **Documentsearchresult** sınıfı ilginç bir fikirdir ve iyi bir fikir (uygulama çalışırken) burada bir kesme noktası ayarlamak ve **model. resultlist**içeriğini incelemek için bir hata ayıklayıcı kullanmaktır. Bunun sezgisel olduğunu, daha fazla bilgi edinmek istediğiniz verileri sağlar.

Şimdi gerçeği.

### <a name="test-the-app"></a>Uygulamayı test etme

Şimdi uygulamanın doğru şekilde çalıştığını kontrol edelim.

1. Hata ayıklama **/hata ayıklama olmadan Başlat** ' ı seçin veya F5 tuşuna basın. Doğru şekilde kodluysa ilk dizin görünümünü alırsınız.

     ![Uygulamayı açma](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. "Plaj" (veya aklınızda gelen herhangi bir metin) gibi bir metin girin ve arama simgesine tıklayın. Bazı sonuçlar almalısınız.

     !["Plaj" aranıyor](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. "Beş yıldız" girmeyi deneyin. Sonuçların nasıl alınacağını aklınızda yapın. Daha karmaşık bir arama "merkezlerini" için "beş yıldızlı" bir eş anlamlı olarak değerlendirilir ve bu sonuçları döndürür. Eş anlamlıların kullanımı Azure Bilişsel Arama 'da kullanılabilir, ancak ilk öğreticilerde kapsamayacak.
 
4. Arama metni olarak "sık erişimli" girmeyi deneyin. Bu, içinde "otel" kelimesiyle _giriş döndürmez._ Aramamız yalnızca tüm kelimeleri bulur, ancak birkaç sonuç döndürülür.

5. Diğer sözcükleri deneyin: "havuz", "güneş ışığı", "Görünüm" ve herhangi bir şey. Azure Bilişsel Arama en basit, ancak yine de ikna edici bir düzeyde çalıştığını göreceksiniz.

## <a name="test-edge-conditions-and-errors"></a>Test Edge koşulları ve hataları

Hata işleme özelliklerimizin, her şeyin mükemmel şekilde çalıştığı durumlarda bile çalıştığını doğrulamak önemlidir. 

1. **Dizin** yönteminde, **TRY {** çağrısından sonra **Yeni özel durum oluştur ()** satırını girin. Bu özel durum, metinde arama yaptığımız zaman bir hataya zorlanır.

2. Uygulamayı çalıştırın, arama metni olarak "çubuk" yazın ve arama simgesine tıklayın. Özel durum hata görünümü ile sonuçlanmalıdır.

     ![Bir hataya zorla](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Hata sayfalarında iç hata numaralarını döndürmek için bir güvenlik riski olarak değerlendirilir. Uygulamanızın genel kullanıma yönelik olması amaçlanıyorsa, bir hata oluştuğunda neler Dönebileceğinize ilişkin güvenli ve en iyi uygulamalara yönelik bazı araştırma yapın.

3. Hata işlemenin gerektiği gibi çalıştığını karşıladığınızda, **Yeni özel durum ()** öğesini kaldırın.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Azure Bilişsel Arama çağrısı kısa bir işlemdir ve sonuçların yorumlanması kolaydır.
* Zaman uyumsuz çağrılar denetleyiciye az miktarda karmaşıklık ekler, ancak kaliteli uygulamalar geliştirmek istiyorsanız en iyi uygulamadır.
* Bu uygulama, **SearchParameters**içinde ayarlananla tanımlanan, düz metin araması gerçekleştirdi. Ancak, bu bir sınıf, bir aramaya gelişmiş algoritmaların mümkündür ekleyen birçok üye ile doldurulabilirler. Bu uygulamayı önemli ölçüde daha güçlü hale getirmek için çok ek iş gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

Azure Bilişsel Arama kullanarak en iyi kullanıcı deneyimini sağlamak için daha fazla Özellik (sayfa numaralarını veya sonsuz kaydırmayı kullanarak) ve otomatik tamamlama/öneriler eklememiz gerekir. Ayrıca, daha karmaşık arama parametrelerini de göz önünde bulundurmanız gerekir (örneğin, belirli bir noktanın belirtilen yarıçapı içindeki oteller üzerinde coğrafi aramalar ve arama sonuçları sıralaması).

Bu sonraki adımlar bir dizi öğreticilerde giderilmiştir. Sayfalama ile başlayalım.

> [!div class="nextstepaction"]
> [C#Öğretici: arama sonuçları sayfalandırma-Azure Bilişsel Arama](tutorial-csharp-paging.md)


