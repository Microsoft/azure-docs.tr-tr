---
title: Arama sonuçları sayfalamaya yönelik C# öğreticisi
titleSuffix: Azure Cognitive Search
description: İlk, sonraki, önceki, son ve numaralandırılmış düğmeleri eklemek üzere mevcut bir oteller projesi üzerinde oluşturma, arama sonuçlarına sayfalandırma ve gezinti düğmeleri ekleyin. İkinci bir disk belleği sistemi, dikey bir kaydırma çubuğunu alt sınırına taşıyarak tetiklenen, sonsuz kaydırma kullanır.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785878"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak arama sonuçlarına sayfalama ekleme

Sayfa numaralarına ve ikincinin sonsuz kaydırmasını temel alan iki farklı disk belleği sistemi uygulamayı öğrenin. Her iki sayfalama sistemi de yaygın olarak kullanılır ve sağ taraftaki seçim, sonuçlarla istediğiniz kullanıcı deneyimine bağlıdır. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Uygulamanızı numaralandırılmış sayfalama ile genişletin
> * Sınırsız kaydırma ile uygulamanızı genişletme

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir sayfalama sisteminin, [ilk arama uygulamanızı oluşturma](tutorial-csharp-create-first-app.md) öğreticisinde açıklanan önceden oluşturulmuş bir projeyle yer paylaşımlarını oluşturur.

Bu öğreticide geliştirdiğiniz kodun tamamlanmış sürümleri aşağıdaki projelerde bulunabilir:

* [2A-ekleme-sayfalama (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2B-Ekle-sonsuz-kaydırma (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Önkoşullar

* [1-temel arama sayfası (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) projesi. Bu proje, önceki öğreticiden ya da GitHub 'dan bir kopyadan oluşturulmuş kendi sürümünüz olabilir.

Bu öğretici, [Azure.Search.Documstalar (sürüm 11)](https://www.nuget.org/packages/Azure.Search.Documents/) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için bkz. [Microsoft. Azure. Search (sürüm 10) kod örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="extend-your-app-with-numbered-paging"></a>Uygulamanızı numaralandırılmış sayfalama ile genişletin

Numaralandırılmış sayfalama, ana ticari Web arama motorları ve diğer birçok arama Web sitesi için tercih edilen disk belleği sistemidir. Numaralandırılmış sayfalama genellikle gerçek sayfa numaralarının bir aralığına ek olarak "ileri" ve "Previous" seçeneğini içerir. Ayrıca "ilk sayfa" ve "son sayfa" seçeneği de kullanılabilir olabilir. Bu seçenekler, sayfa tabanlı sonuçlarla gezinerek bir kullanıcı denetimi sunar.

Bu öğreticide, ilk, önceki, sonraki ve son seçeneklerini içeren ve 1 ' den başlayan, ancak kullanıcının açık olduğu geçerli sayfayı kapsayan bir sistem ekleyeceksiniz (örneğin, Kullanıcı sayfa 10 ' a bakıyor, belki de 8, 9, 10, 11 ve 12 görüntülenir).

Sistem, genel bir değişkende görünür sayfa numaralarının belirtilmesine izin verecek kadar esnek olacaktır.

Sistem, en sol ve en sağdaki sayfa numarası düğmelerini özel olarak değerlendirir ve bu, görüntülendikleri sayfa numaralarının aralığının değiştirilmesini tetikler. Örneğin, 8, 9, 10, 11 ve 12 numaralı sayfa numaraları görüntüleniyorsa ve Kullanıcı 8 ' e tıkladığında sayfa numaraları aralığı 6, 7, 8, 9 ve 10 ' a değişir. Ayrıca, 12 ' nin seçildikleri takdirde sağa doğru bir kaydırma vardır.

### <a name="add-paging-fields-to-the-model"></a>Modele disk belleği alanları ekleme

Temel arama sayfası çözümünün açık olmasını sağlayabilirsiniz.

1. SearchData. cs model dosyasını açın.

1. Sayfalandırmayı desteklemek için genel değişkenler ekleyin. MVC 'de, genel değişkenler kendi statik sınıfında bildirilmiştir. **Resultsperpage** sayfa başına sonuç sayısını ayarlar. **Maxpagerange** görünümdeki görünür sayfa numaralarının sayısını belirler. **Pagerangedelta** , en sol veya en sağdaki sayfa numarası seçildiğinde kaç sayfanın sola veya sağa kaydırılacağı belirler. Genellikle bu ikinci sayı **Maxpagerange**'un yarısı etrafında. Aşağıdaki kodu ad alanına ekleyin.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Bu projeyi dizüstü bilgisayar gibi daha küçük bir ekran üzerinde çalıştırıyorsanız, daha sonra **Resultsperpage** 'i 2 olarak değiştirmeyi düşünün.

1. Searchtext özelliğinden sonra **Searchdata** sınıfına sayfalama özellikleri ekleyin. 

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Görünüme bir sayfalama seçenekleri tablosu ekleyin

1. İndex. cshtml dosyasını açın ve kapanış/Body etiketinden hemen önce aşağıdaki kodu ekleyin &lt; &gt; . Bu yeni kod, sayfalama seçeneklerinin bir tablosunu gösterir: ilk, önceki, 1, 2, 3, 4, 5, ileri, son.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    Öğeleri düzgünce hizalamak için bir HTML tablosu kullanıyoruz. Ancak, @Html.ActionLink her biri, denetleyiciyi daha önce eklediğimiz **sayfalama** özelliğine göre farklı girdilerle oluşturulan **Yeni** bir modelle çağıran deyimlerden gelir.

    İlk ve son sayfa seçenekleri "First" ve "Last" gibi dizeler göndermez, bunun yerine doğru sayfa numaralarını gönderir.

1. Oteller. CSS dosyasındaki HTML stilleri listesine sayfalama sınıfları ekleyin. **Pageselected** sınıfı, sayfa numaraları listesindeki geçerli sayfayı (sayfa numarasına bir kalın biçim uygulayarak) tanımlamak için kullanılır.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Denetleyiciye bir sayfa eylemi ekleyin

1. HomeController. cs dosyasını açın ve **Pageasync** eylemini ekleyin. Bu eylem, seçili sayfa seçeneklerinin herhangi birine yanıt verir.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    **Runqueryasync** yöntemi artık, bir bit içinde olacak üçüncü parametre nedeniyle bir sözdizimi hatası gösterir.

    > [!Note]
    > **TempData** çağrısı, geçici depolamada bir değer ( **nesne**), ancak bu depolama _yalnızca_ tek bir çağrı için devam ediyor. Geçici verilerde bir şeyi depoluyoruz, bu, bir denetleyici eyleminin bir sonraki çağrısıyla kullanılabilir, ancak bundan sonra çağrı tarafından bir daha alınır. Bu kısa süre içinde, arama metni ve sayfalama özelliklerini geçici depolamaya geri depolarız ve her **Pageasync** çağrısı.

1. Geçici değişkenleri depolamak için **Dizin (model)** eylemini güncelleştirin ve **Runqueryasync** çağrısına en soldaki sayfa parametresini ekleyin.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Önceki derste tanıtılan **Runqueryasync** yönteminin, söz dizimi hatasını çözmek için değiştirilmesi gerekir. **Skip** ayarından başlayarak yalnızca bir sayfa değeri Istemek Için [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) sınıfının **Skip**, **size** ve **includetotalcount** alanlarını kullanırız. Görünümümüzü için sayfalama değişkenlerini de hesapladık. Tüm yöntemi aşağıdaki kodla değiştirin.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        return View("Index", model);
    }
    ```

1. Son olarak, görünümde küçük bir değişiklik yapın. **Resultlist. Results. TotalCount** değişkeni artık toplam sayı değil, bir sayfada (bizim örneğimizde 3) döndürülen sonuç sayısını içerecektir. **IncludeTotalCount** değerini true olarak belirlediğimiz Için **Resultlist. TotalCount** değişkeni artık Toplam sonuç sayısını içermektedir. Bu nedenle, sonuçların sayısının görünümde nerede görüntülendiğini bulun ve aşağıdaki kodla değiştirin.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > Bu toplamın Azure Bilişsel Arama tarafından hesaplanması gerektiğinden, **IncludeTotalCount** değeri true olarak ayarlanırken küçük bir performans okuması vardır. Karmaşık veri kümeleri ile döndürülen değerin bir _yaklaşık_ olduğunu belirten bir uyarı vardır. Otel arama yapı küçük olduğundan doğru olacaktır.

### <a name="compile-and-run-the-app"></a>Uygulamayı derleyin ve çalıştırın

Şimdi **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın).

1. Çok sayıda sonuç döndüren bir dizeyi arayın (örneğin, "WiFi"). Sonuçlar arasında düzgün şekilde sayfa eklenebilir mi?

    !["Havuz" sonuçları aracılığıyla numaralandırılmış sayfalama](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. En sağ ve daha sonraki, en son sayfa numaralarına tıklamakta çalışın. Sayfa numaralarını açık olan sayfayı ortalamak için uygun şekilde ayarlayın mi?

1. "First" ve "Last" seçenekleri faydalıdır mi? Bazı ticari arama motorları bu seçenekleri kullanır ve diğerleri değildir.

1. Sonuçların son sayfasına gidin. Son sayfa, en az **Resultsperpage** sonuçları içerebilen tek sayfasıdır.

    ![Son "WiFi" sayfası inceleniyor](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. "Town" yazın ve ara ' ya tıklayın. Sonuçlar bir sayfadan daha azsa hiçbir sayfalama seçeneği gösterilmez.

    !["Town" aranıyor](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Bu projeyi kaydedin ve alternatif bir sayfalama formu için sonraki bölüme geçin.

## <a name="extend-your-app-with-infinite-scrolling"></a>Sınırsız kaydırma ile uygulamanızı genişletme

Kullanıcı dikey kaydırma çubuğunu görüntülenmekte olan sonuçların en sonuna kaydırdığında sonsuz kaydırma tetiklenir. Bu olayda, bir sonraki sonuç sayfası için arama hizmetine bir çağrı yapılır. Daha fazla sonuç yoksa, hiçbir şey döndürülmez ve dikey kaydırma çubuğu değişmez. Daha fazla sonuç varsa, bunlar geçerli sayfaya eklenir ve kaydırma çubuğu, daha fazla sonuç olduğunu göstermek için değişir.

Önemli bir nokta, geçerli sayfanın değiştirilmemesinin yanı, ek sonuçları göstermek için uzatılmasındır. Bir Kullanıcı her zaman aramanın ilk sonuçlarına geri gidebilirler.

Sonsuz kaydırma uygulamak için, sayfa numarası kaydırma öğelerinden herhangi biri eklenmeden önce projeyle başlayalım. GitHub 'da, bu [Firstazuresearchapp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) çözümüdür.

### <a name="add-paging-fields-to-the-model"></a>Modele disk belleği alanları ekleme

1. İlk olarak, **searchdata** sınıfına (searchdata. cs model dosyasında) bir **sayfalama** özelliği ekleyin.

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Bu değişken, bir sonraki sonuç sayfası gönderilmesi gerekiyorsa "ileri" yi tutan bir dizedir veya bir aramanın ilk sayfası için null olmalıdır.

1. Aynı dosyada ve ad alanı içinde, bir özelliği olan bir genel değişken sınıfı ekleyin. MVC 'de, genel değişkenler kendi statik sınıfında bildirilmiştir. **Resultsperpage** sayfa başına sonuç sayısını ayarlar. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Görünüme dikey kaydırma çubuğu ekleme

1. Sonuçları görüntüleyen index. cshtml dosyasının bölümünü bulun ( **@if (model! = null)** ile başlar).

1. Bölümünü aşağıdaki kodla değiştirin. Yeni **&lt; div &gt;** bölümü, kaydırılabilir olması gereken alanı etrafında bulunur ve hem bir **overflow-y** özniteliği hem de "kaydırılabilir ()" adlı bir **OnScroll** işlevine çağrı ekler.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Doğrudan döngünün altında, &lt; /div etiketinden sonra, &gt; **kaydırılan** işlevi ekleyin.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    Yukarıdaki betikteki **if** ifadesinde, kullanıcının dikey kaydırma çubuğunun alt kısmına kaydırılıp kaydırılmayacağını test eder. Varsa, bir **giriş** denetleyicisine bir çağrı **NextAsync** adlı bir eyleme yapılır. Denetleyici tarafından başka bir bilgi gerekmez, bu, sonraki veri sayfasını döndürür. Bu veriler daha sonra Özgün sayfa olarak aynı HTML stilleri kullanılarak biçimlendirilir. Hiçbir sonuç döndürülmezse hiçbir şey eklenmez ve işlemler oldukları gibi kalır.

### <a name="handle-the-next-action"></a>Sonraki eylemi işle

Denetleyiciye gönderilmesi gereken üç eylem vardır: uygulamanın ilk çalıştırılması ( **Dizin ()**, dizin ( **model)** ve ardından **İleri (model**) ile daha fazla sonuç için çağrı yapan, Kullanıcı tarafından yapılan ilk arama.

1. Ana denetleyici dosyasını açın ve özgün öğreticiden **Runqueryasync** yöntemini silin.

1. **Dizin (model)** eylemini aşağıdaki kodla değiştirin. Artık null olduğunda **disk belleği** alanını işler veya "ileri" olarak ayarlanır ve Azure bilişsel arama çağrısını işler.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Numaralandırılmış sayfalama yöntemine benzer şekilde, yalnızca ihtiyacımız olan verileri istemek için **Atla** ve **Boyutlandır** arama ayarlarını kullanırız.

1. **NextAsync** eylemini ana denetleyiciye ekleyin. Bir listeyi nasıl döndürdüğünü, her bir otelin listeye iki öğe eklediğine dikkat edin: otel adı ve otel açıklaması. Bu biçim, **kaydırılan** işlevin görünümdeki döndürülen verilerin kullanımıyla eşleşecek şekilde ayarlanır.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. **Liste &lt; &gt; dizesinde** sözdizimi hatası alırsanız, aşağıdaki **using** yönergesini denetleyici dosyasının baş üzerine ekleyin.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Projenizi derleyin ve çalıştırın

Şimdi **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın).

1. Çok sayıda sonuç sağlayacak bir terim ("havuz" gibi) girin ve dikey kaydırma çubuğunu test edin. Yeni bir sonuç sayfası tetikleyecektir mi?

    !["Havuz" sonuçları aracılığıyla sonsuz kaydırma](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > İlk sayfada bir kaydırma çubuğunun göründüğünden emin olmak için sonuçların ilk sayfası, görüntülendikleri alanın yüksekliğini biraz daha aşmalıdır. Bizim örneğimizde **. Box1** , en fazla 30 piksel boyutunda, **. box2** , 100 piksel yüksekliğinde _ve_ 24 piksellik alt kenar boşluğuyla bulunur. Böylece her giriş 154 piksel kullanır. Üç giriş 3 x 154 = 462 piksel sürer. Dikey kaydırma çubuğunun göründüğünden emin olmak için, görüntüleme alanına yönelik bir yükseklik, 462 pikselden küçük, hatta 461 çalışıyor olmalıdır. Bu sorun yalnızca ilk sayfada, bir kaydırma çubuğu gösterildiğinizden emin olduktan sonra gerçekleşir. Güncelleştirilecek satır: **&lt; div ID = "myDiv" Style = "width: 800px; height: 450px; overflow-y: kaydır;" OnScroll = "kaydırılan ()" &gt;**.

1. Sonuçların sonuna kadar aşağı doğru kaydırın. Tüm bilgilerin artık tek bir görünüm sayfasında nasıl olduğunu fark edin. Herhangi bir sunucu çağrısı tetiklemeden, her şey için en üste doğru kaydırma yapabilirsiniz.

Daha gelişmiş sonsuz kaydırma sistemleri, yeni bir sonuç sayfası yüklemesini tetiklemek için fare tekerleğini veya benzer başka bir mekanizmayı kullanabilir. Bu öğreticilerde daha fazla sınırsız kaydırma gerçekleşmeyecek, ancak ek fare tıklamalarını önlediği ve diğer seçenekleri daha fazla araştırmak isteyebileceğiniz için belirli bir düğmesi vardır.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Numaralandırılmış sayfalama, sonuçların sırasının biraz rasgele olduğu, diğer bir deyişle kullanıcılarınıza ilgi çekici bir şey olabileceği gibi, arama için yararlıdır.
* Sonsuz kaydırma, sonuçların sırası özellikle önemli olduğunda faydalıdır. Örneğin, sonuçlar hedef şehrin merkezinden uzaklığına göre sıralanır.
* Numaralandırılmış sayfalama daha iyi gezinilmesine olanak sağlar. Örneğin, bir Kullanıcı ilginç bir sonucun 6. sayfada olduğunu anımsayacak, sonsuz kaydırmadaki böyle kolay bir başvuru mevcut değildir.
* Sonsuz kaydırma kolay bir kolaylık sağlar, üzerine tıklamak için sayfa numarası olmadan yukarı ve aşağı kaydırın.
* Sonsuz kaydırmanın temel bir özelliği, sonuçların var olan bir sayfaya eklenmeme ve bu sayfanın değişmemelidir.
* Geçici depolama yalnızca tek bir çağrı için devam ettirir ve ek çağrılar için sıfırlanması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Sayfalama, bir arama deneyimi için temel bir deneyimdir. Bir sonraki adım, sayfalama iyi ele alınarak, daha önce yapılacak aramalar ekleyerek kullanıcı deneyimini daha da geliştirmaktır.

> [!div class="nextstepaction"]
> [C# öğreticisi: otomatik tamamlama ve öneriler ekleme-Azure Bilişsel Arama](tutorial-csharp-type-ahead-and-suggestions.md)