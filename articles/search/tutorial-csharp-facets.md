---
title: C#gezinmeye yardımcı olmak için modelleri kullanma öğreticisi
titleSuffix: Azure Cognitive Search
description: Bu öğretici, model gezintisi eklemek için "arama sonuçları sayfalandırma-Azure Bilişsel Arama" projesinde oluşturulur. Bir aramanın kolayca daraltabilmesini sağlamak için modellerinin nasıl kullanılabileceğini öğrenin.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9f0d716e9077b2d9702f26b1afe92d9e4faf4a77
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794062"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C#Öğretici: Azure Bilişsel Arama Yardım için modelleri kullanma

Kullanıcılara, aramasına odaklanmak için kullanılacak bir bağlantı kümesi sağlayarak gezinmeye yardımcı olması için modeller kullanılır. Modeller, verilerin öznitelikleridir (kategori gibi, örnek verilerimizde bulunan bir otelin belirli bir özelliği).

Bu öğretici [ C# öğreticide oluşturulan sayfalama projesi üzerinde oluşturulmuştur: arama sonuçları sayfalandırma-Azure bilişsel arama](tutorial-csharp-paging.md) öğreticisi.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Model özelliklerini _ıscetable_ olarak ayarlama
> * Uygulamanıza model gezintisi ekleyin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

[Öğreticiye sahip olmak: arama sonuçları sayfalandırma-Azure bilişsel arama projesi çalışıyor ve çalışıyor. C# ](tutorial-csharp-paging.md) Bu proje kendi sürümünüz olabilir ya da GitHub: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples)' dan yükleyebilirsiniz.

## <a name="set-model-properties-as-isfacetable"></a>Model özelliklerini ıscetable olarak ayarlama

Model özelliğinin bir model aramasında bulunması için **ıbıı tablosuyla**etiketlenmesi gerekir.

1. **Otel** sınıfını inceleyin. **Kategori** ve **Etiketler**, örneğin, **ıbıı tablosu**olarak etiketlenebilir, ancak **hoteladı** ve **Açıklama** değildir. 

    ```cs
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
    ```

2. Bu öğreticinin bir parçası olarak herhangi bir etiket değiştirilmeyecektir, bu nedenle hotel.cs dosyasını değiştirilmemiş olarak kapatın.

    > [!Note]
    > Aramada istenen bir alan uygun şekilde etiketlenmemişse, bir model araması bir hata oluşturur.


## <a name="add-facet-navigation-to-your-app"></a>Uygulamanıza model gezintisi ekleyin

Bu örnekte, kullanıcının sonuçların solunda gösterilen bağlantı listelerinden bir otel veya bir değişiklik kategorisi seçmesini etkinleştireceğiz. Kullanıcı bazı arama metinleri girerek başlar, sonra bir kategori seçerek aramanın sonuçlarını daraltabilir ve bir değişiklik seçerek sonuçları daraltabilir, ya da ilk değişikliği seçebilir (Order önemli değildir).

Görünüme model listelerini geçirmek için denetleyiciye ihtiyacımız var. Arama ilerledikçe kullanıcı seçimlerini sürdürmemiz ve yine de verileri koruma mekanizması olarak geçici depolama kullanıyoruz.

!["Havuz" aramasını daraltmak için model gezintisi kullanma](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>SearchData modeline filtre dizeleri ekleme

1. SearchData.cs dosyasını açın ve, model filtresi dizelerini tutmak için **Searchdata** sınıfına dize özellikleri ekleyin.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Model eylem yöntemini ekleyin

Ana denetleyicinin, yeni bir eylem, **model**ve var olan **Dizin** ve **sayfa** eylemlerine yönelik güncelleştirmelerin yanı sıra **runqueryasync** yöntemine yönelik güncelleştirmeler olması gerekir.

1. Giriş denetleyicisi dosyasını açın ve **using** Ifadesini ekleyerek **liste&lt;dize&gt;** yapısını etkinleştirin.

    ```cs
    using System.Collections.Generic;
    ```

2. **Index (SearchData model)** eylem yöntemini değiştirin.

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **Sayfa (SearchData model)** eylem yöntemini değiştirin.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

4. Kullanıcı bir model bağlantısına tıkladığında etkinleştirilecek bir **model (SearchData model)** eylem yöntemi ekleyin. Model, bir kategori arama filtresi veya bir düzeltme arama filtresi içerir. Belki de **sayfa** eyleminden sonra ekleyebilirsiniz.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
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
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Arama filtresini ayarlama

Bir Kullanıcı belirli bir modeli seçtiğinde, örneğin, **çare ve Spa** kategorisine tıkladığınızda yalnızca bu kategori olarak belirtilen oteller sonuçlarda döndürülmelidir. Bu şekilde bir aramayı daraltmak için bir _filtre_ayarlaması gerekiyor.

1. **Runqueryasync** yöntemini aşağıdaki kodla değiştirin. Birincil olarak, bir kategori filtre dizesi ve bir düzeltme filtre dizesi alır ve **SearchParameters**'ın **filtre** parametresini ayarlar.

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

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    Döndürülecek öğe **seçme** listesine **Kategori** ve **Etiketler** özelliklerini ekledik. Bu ek, model gezintisinin çalışması için bir gereklilik değildir ancak doğru filtrelemediğimiz doğrulamak için bu bilgileri kullanırız.

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

2. Görünüm için çıktıyı, sol taraftaki model listelerini ve sağdaki sonuçları doğru şekilde hizalamak için bir tabloya düzenledik. İndex. cshtml dosyasını açın. HTML &lt;gövdesi&gt; etiketlerinin tüm içeriğini aşağıdaki kodla değiştirin.

    ```cs
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
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
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
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
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. **Çare ve Spa** kategorisine tıklayın. Tüm sonuçların bu kategoride olduğunu doğrulayın.

    ![Aramayı "çare ve Spa" olarak daraltma](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. **Kıtağa hızlı** bir şekilde değişiklik yapmak için tıklatın. Seçili değişiklik için tüm sonuçların hala "çare ve Spa" kategorisinde olduğunu doğrulayın.

    ![Aramayı "kıental Breakfast" olarak daraltma](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Başka bir kategori seçip, bir değişiklik yapmayı deneyin ve daraltma sonuçlarını görüntüleyin. Ardından, tek bir değişiklik ve sonra bir kategori olmak üzere başka bir çözüm deneyin.

    >[!Note]
    > Bir model listesinde (kategori gibi) bir seçim yapıldığında, kategori listesi içinde önceki seçimleri geçersiz kılar.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Her bir özelliğin, model gezintisine dahil olmaları durumunda **ıbıı tablosu**olarak işaretlenmesi zorunludur.
* Model gezintisi, bir aramayı daraltma yoluyla kolay ve sezgisel bir kullanıcı sağlar.
* Model gezintisi en iyi bölümlere ayrılır (otel kategorileri, otel, fiyat aralıkları, derecelendirme aralıkları, vb.), her bölüm uygun bir başlığa sahip.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide sonuçları sıralama konusuna baktık. Bu noktaya kadar, sonuçlar yalnızca veritabanında bulundukları sırada sıralanır.

> [!div class="nextstepaction"]
> [C#Öğretici: sonuçları sıralama-Azure Bilişsel Arama](tutorial-csharp-orders.md)
