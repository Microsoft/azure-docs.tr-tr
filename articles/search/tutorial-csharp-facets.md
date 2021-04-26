---
title: Gezintiye yardımcı olmak için modelleri kullanma hakkında C# öğreticisi
titleSuffix: Azure Cognitive Search
description: Çok yönlü gezinme eklemek için "sayfalama sonuçları" ndan devam edin. Bir aramanın kolayca daraltabilmesini sağlamak için modellerinin nasıl kullanılabileceğini öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789811"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak çok yönlü gezinti ekleme

Modeller, sonuçları filtrelemeye yönelik bir dizi bağlantı sağlayarak kendinden yönlendirilmiş gezintiyi etkinleştirir. Bu öğreticide, çok yönlü bir gezinti yapısı sayfanın sol tarafına, Etiketler ve tıklatılabilir metinle sonuçları kırpacak şekilde yerleştirilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Model özelliklerini _ıscetable_ olarak ayarlama
> * Uygulamanıza model gezintisi ekleyin

## <a name="overview"></a>Genel Bakış

Modeller, arama dizininizdeki alanları temel alır. Model = [String] içeren bir sorgu isteği, alanı tarafından model olarak sunulur. `&facet=category&facet=amenities`Her biri bir ve işareti (&) karakteriyle ayrılmış gibi birden çok model eklemek yaygındır. Çok yönlü bir gezinti yapısının uygulanması için hem modelleri hem de filtreleri belirtmeniz gerekir. Filtre, sonuçları daraltmak için bir tıklama olayında kullanılır. Örneğin, "bütçe" seçeneğine tıkladığınızda sonuçları ilgili ölçütlere göre filtreler.

Bu öğreticide, [Arama sonuçlarına sayfalama Ekle](tutorial-csharp-paging.md) öğreticisinde oluşturulan sayfalama projesi genişletilir.

Bu öğreticideki kodun tamamlanmış bir sürümü aşağıdaki projede bulunabilir:

* [4-Add-model-gezinti (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Önkoşullar

* [2A-Add-sayfalama (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) çözümü. Bu proje, önceki öğreticiden ya da GitHub 'dan bir kopyadan oluşturulmuş kendi sürümünüz olabilir.

Bu öğretici, [Azure.Search.Documstalar (sürüm 11)](https://www.nuget.org/packages/Azure.Search.Documents/) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için bkz. [Microsoft. Azure. Search (sürüm 10) kod örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Model özelliklerini ıscetable olarak ayarlama

Model özelliğinin bir model aramasında bulunması için **ıbıı tablosuyla** etiketlenmesi gerekir.

1. **Otel** sınıfını inceleyin. **Kategori** ve **Etiketler**, örneğin, **ıbıı tablosu** olarak etiketlenebilir, ancak **hoteladı** ve **Açıklama** değildir. 

    ```cs
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
    ```

1. Bu öğreticinin bir parçası olarak herhangi bir etiket değiştirilmeyecektir, bu nedenle otel. cs dosyasını değiştirilmemiş olarak kapatın.

    > [!Note]
    > Aramada istenen bir alan uygun şekilde etiketlenmemişse, bir model araması bir hata oluşturur.

## <a name="add-facet-navigation-to-your-app"></a>Uygulamanıza model gezintisi ekleyin

Bu örnekte, kullanıcının sonuçların solunda gösterilen bağlantı listelerinden bir otel veya bir değişiklik kategorisi seçmesini etkinleştireceğiz. Kullanıcı bazı arama metinleri girerek başlar ve sonra bir kategori veya değişiklik seçerek aramanın sonuçlarını aşamalı olarak daraltın.

Bu, görünümün iş, görünüme model listelerini geçirme işleridir. Arama ilerledikçe kullanıcı seçimlerini sürdürmek için, durumu koruma mekanizması olarak geçici depolamayı kullanırız.

!["Havuz" aramasını daraltmak için model gezintisi kullanma](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>SearchData modeline filtre dizeleri ekleme

1. SearchData. cs dosyasını açın ve, model filtresi dizelerini tutmak için **searchdata** sınıfına dize özellikleri ekleyin.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Model eylem yöntemini ekleyin

Ana denetleyicinin mevcut **Dizin** ve **sayfa** eylemlerine ve **runqueryasync** yöntemine yönelik yeni bir eylem, **model** ve güncelleştirme olması gerekir.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. **Index (SearchData model)** eylem yöntemini değiştirin.

    ```cs
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
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. **Pageasync (SearchData model)** eylem yöntemini değiştirin.

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
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

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Kullanıcı bir model bağlantısına tıkladığında etkinleştirilecek bir çok **yönlü tasync (SearchData model)** eylem yöntemi ekleyin. Modelde bir kategori veya değişiklik arama filtresi yer alacak. **Pageasync** eyleminden sonra ekleyin.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>Arama filtresini ayarlama

Bir Kullanıcı belirli bir modeli seçtiğinde, örneğin, **çare ve Spa** kategorisine tıkladığınızda yalnızca bu kategori olarak belirtilen oteller sonuçlarda döndürülmelidir. Bu şekilde bir aramayı daraltmak için bir _filtre_ ayarlaması gerekiyor.

1. **Runqueryasync** yöntemini aşağıdaki kodla değiştirin. Birincil olarak, bir kategori filtre dizesi ve bir düzeltme filtre dizesi alır ve **SearchOptions**'ın **filtre** parametresini ayarlar.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

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

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    **Kategori** ve **Etiketler** özelliklerinin döndürülecek öğe **seçme** listesine eklendiğinden emin olun. Bu ek, model gezinmesinin çalışması için bir gereksinim değildir, ancak bu bilgileri filtrelerin doğru şekilde çalıştığını doğrulamak için kullanırız.

### <a name="add-lists-of-facet-links-to-the-view"></a>Görünüme model bağlantıları listesi ekleyin

Görünüm bazı önemli değişiklikler gerektirecektir. 

1. Oteller. css dosyasını (Wwwroot/CSS klasöründe) açarak başlatın ve aşağıdaki sınıfları ekleyin.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. Görünüm için çıktıyı bir tabloya düzenleyin, sol taraftaki model listelerini ve sağdaki sonuçları doğru şekilde hizalayın. İndex. cshtml dosyasını açın. HTML &lt; gövde etiketlerinin tüm içeriğini &gt; aşağıdaki kodla değiştirin.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
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
                                        <td class="tdPage">
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

                                    <td class="tdPage">
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

                                    <td class="tdPage">
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
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    **HTML. ActionLink** çağrısının kullanımına dikkat edin. Bu çağrı, Kullanıcı bir model bağlantısına tıkladığında denetleyiciyi geçerli filtre dizeleriyle iletişim kurar. 

### <a name="run-and-test-the-app"></a>Uygulamayı çalıştırma ve test etme

Kullanıcı için model gezinmesinin avantajı, aşağıdaki sırayla gösterebilmemiz için tek tıklamayla aramaları daraltabilecekleri bir üstünlüktür.

1. Uygulamayı çalıştırın, arama metni olarak "Havaalanı" yazın. Model listesinin sola doğru şekilde göründüğünden emin olun. Bu modeller, kendi metin verilerinde "Havaalanı" olan oteller için, ne sıklıkla gerçekleştikleri hakkında bir sayı ile uygulanır.

    !["Havaalanı" aramasını daraltmak için model gezintisi kullanma](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. **Çare ve Spa** kategorisine tıklayın. Tüm sonuçların bu kategoride olduğunu doğrulayın.

    ![Aramayı "çare ve Spa" olarak daraltma](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. **Kıtağa hızlı** bir şekilde değişiklik yapmak için tıklatın. Seçili değişiklik için tüm sonuçların hala "çare ve Spa" kategorisinde olduğunu doğrulayın.

    ![Aramayı "kıental Breakfast" olarak daraltma](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Başka bir kategori seçip, bir değişiklik yapmayı deneyin ve daraltma sonuçlarını görüntüleyin. Ardından, tek bir değişiklik ve sonra bir kategori olmak üzere başka bir çözüm deneyin. Sayfayı sıfırlamak için boş bir arama gönderin.

    >[!Note]
    > Bir model listesinde (kategori gibi) bir seçim yapıldığında, kategori listesi içinde önceki seçimleri geçersiz kılar.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Her bir çok yönlü tablo alanını, model gezintisine eklenmek üzere **ıscetable** özelliğiyle işaretlemek zorunludur.
* Sonuçlar, sonuçları azaltmak için filtrelerle birleştirilir.
* Her seçim, daha fazla dar sonuçları daha fazla daraltmak için, her bir seçimi temel alarak, modeller birikimlidir.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide sonuçları sıralama konusuna baktık. Bu noktaya kadar, sonuçlar yalnızca veritabanında bulundukları sırada sıralanır.

> [!div class="nextstepaction"]
> [C# öğreticisi: sonuçları sıralama-Azure Bilişsel Arama](tutorial-csharp-orders.md)
