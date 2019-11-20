---
title: C#Arama sonuçları sayfalandırmasına ilişkin öğretici
titleSuffix: Azure Cognitive Search
description: Bu öğreticide arama sonuçlarının sayfalama gösterilmektedir. İlk, sonraki, önceki, son ve numaralandırılmış düğmelere göre disk belleğine sahip mevcut bir oteller projesi üzerinde oluşturulur. İkinci bir disk belleği sistemi, dikey bir kaydırma çubuğunu alt sınırına taşıyarak tetiklenen, sonsuz kaydırma kullanır.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 04f8229a86fbd8fbd5404997926412e760e74973
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113756"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C#Öğretici: arama sonuçları sayfalandırma-Azure Bilişsel Arama

Sayfa numaralarına ve ikincinin sonsuz kaydırmasını temel alan iki farklı disk belleği sistemi uygulamayı öğrenin. Her iki sayfalama sistemi de yaygın olarak kullanılır ve sağ taraftaki seçim, sonuçlarla istediğiniz kullanıcı deneyimine bağlıdır. Bu öğretici, sayfalama sistemlerini [ C# öğreticide oluşturulan projede oluşturur: ilk uygulamanızı oluşturma-Azure bilişsel arama](tutorial-csharp-create-first-app.md) öğreticisi.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Uygulamanızı numaralandırılmış sayfalama ile genişletin
> * Sınırsız kaydırma ile uygulamanızı genişletme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

[Öğreticiye sahipsiniz: ilk uygulamanızı oluşturun-Azure bilişsel arama projenizi çalışır duruma ayarlayın. C# ](tutorial-csharp-create-first-app.md) Bu proje kendi sürümünüz olabilir ya da GitHub: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples)' dan yükleyebilirsiniz.

## <a name="extend-your-app-with-numbered-paging"></a>Uygulamanızı numaralandırılmış sayfalama ile genişletin

Numaralandırılmış sayfalama, ana internet arama motorları ve diğer birçok arama Web sitesinin tercih ettiği disk belleği sistemidir. Numaralandırılmış sayfalama genellikle gerçek sayfa numaralarının bir aralığına ek olarak "ileri" ve "Previous" seçeneğini içerir. Ayrıca "ilk sayfa" ve "son sayfa" seçeneği de kullanılabilir olabilir. Bu seçenekler, sayfa tabanlı sonuçlarla gezinerek bir kullanıcı denetimi sunar.

İlk, önceki, sonraki ve son seçeneklerini içeren bir sistem, 1 ' den başlamamayan sayfa numaralarıyla birlikte ekleyeceğiz, ancak örneğin, Kullanıcı sayfa 10 ' a bakıyorsam, belki sayfa numarası 8 ' de görünür , 9, 10, 11 ve 12 görüntülenir).

Sistem, genel bir değişkende görünür sayfa numaralarının belirtilmesine izin verecek kadar esnek olacaktır.

Sistem, en sol ve en sağdaki sayfa numarası düğmelerini özel olarak değerlendirir ve bu, görüntülendikleri sayfa numaralarının aralığının değiştirilmesini tetikler. Örneğin, 8, 9, 10, 11 ve 12 numaralı sayfa numaraları görüntüleniyorsa ve Kullanıcı 8 ' e tıkladığında sayfa numaraları aralığı 6, 7, 8, 9 ve 10 ' a değişir. Ayrıca, 12 ' nin seçildikleri takdirde sağa doğru bir kaydırma vardır.

### <a name="add-paging-fields-to-the-model"></a>Modele disk belleği alanları ekleme

Temel arama sayfası çözümünün açık olmasını sağlayabilirsiniz.

1. SearchData.cs model dosyasını açın.

2. Önce bazı genel değişkenler ekleyin. MVC 'de, genel değişkenler kendi statik sınıfında bildirilmiştir. **Resultsperpage** sayfa başına sonuç sayısını ayarlar. **Maxpagerange** görünümdeki görünür sayfa numaralarının sayısını belirler. **Pagerangedelta** , sayfa aralığının en sol veya sağ sayfa numarası seçildiğinde kaç sayfa sola veya sağa kaydırılacağı belirler. Genellikle bu ikinci sayı **Maxpagerange**'un yarısı etrafında. Aşağıdaki kodu ad alanına ekleyin.

    ```cs
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

3. Searchtext özelliğinden sonra, bir **Searchdata** sınıfına sayfalama özellikleri ekleyin.

    ```cs
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

1. İndex. cshtml dosyasını açın ve kapatma &lt;/Body&gt; etiketinin hemen öncesine aşağıdaki kodu ekleyin. Bu yeni kod, sayfalama seçeneklerinin bir tablosunu gösterir: ilk, önceki, 1, 2, 3, 4, 5, ileri, son.

    ```cs
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Öğeleri düzgünce hizalamak için bir HTML tablosu kullanıyoruz. Ancak, her biri, denetleyiciyi daha önce eklediğimiz **sayfalama** özelliğine göre farklı girdilerle oluşturulan **Yeni** bir modelle çağıran @Html.ActionLink deyimlerden gelir.

    İlk ve son sayfa seçenekleri "First" ve "Last" gibi dizeler göndermez, bunun yerine doğru sayfa numaralarını gönderir.

2. Oteller. CSS dosyasındaki HTML stilleri listesine bazı disk belleği sınıfları ekleyin. **Pageselected** sınıfı, sayfa numaraları listesindeki kullanıcının şu anda görüntüledikleri sayfayı (kalın sayıyı girerek) belirlemek için kullanılır.

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

1. HomeController.cs dosyasını açın ve **sayfa** eylemini ekleyin. Bu eylem, seçili sayfa seçeneklerinin herhangi birine yanıt verir.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
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
    > **TempData** çağrıları geçici depolamada bir değer ( **nesne**) depolar, ancak bu depolama _yalnızca_ tek bir çağrı için devam ediyor. Geçici verilerde bir şeyi depoluyoruz, bu, bir denetleyici eyleminin bir sonraki çağrısıyla kullanıma sunulacaktır, ancak bundan sonra çağrıdan kesinlikle çağrı yapılır! Bu kısa süre içinde, arama metni ve sayfalama özelliklerini geçici depolama alanına ve her bir **sayfa**çağrısıyla geri depolarız.

2. **Dizin (model)** eyleminin geçici değişkenleri depolaması için güncelleştirilmesi ve en soldaki sayfa parametresini **Runqueryasync** çağrısına eklemesi gerekir.

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

3. **Runqueryasync** yönteminin önemli ölçüde güncelleştirilmesi gerekir. **Skip** ayarından başlayarak, sonuçların yalnızca bir sayfa değerini Istemek Için **SearchParameters** sınıfının **Skip**, **top**ve **includetotalresultcount** alanlarını kullanırız. Görünümümüzü için sayfalama değişkenlerini de hesapladık. Tüm yöntemi aşağıdaki kodla değiştirin.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Son olarak, görünümde küçük bir değişiklik yapmanız gerekir. **ResultsList. Results. Count** değişkeni şimdi bir sayfada döndürülen sonuç sayısını (bizim örneğimizde 3), toplam sayı değil de içerecektir. **Includetotalresultcount** değerini true olarak belirlediğimiz Için, **resultsList. Count** değişkeni artık Toplam sonuç sayısını içerir. Bu nedenle, sonuçların sayısının görünümde nerede görüntülendiğini bulun ve aşağıdaki kodla değiştirin.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Bu toplamın Azure Bilişsel Arama tarafından hesaplanması gerektiğinden **ıncludetotalresultcount** değeri true olarak ayarlanarak, genellikle bir performans okuması vardır. Karmaşık veri kümeleriyle döndürülen değerin bir _yaklaşık_olduğunu belirten bir uyarı vardır. Otel verilerimizde doğru olacaktır.

### <a name="compile-and-run-the-app"></a>Uygulamayı derleyin ve çalıştırın

Şimdi **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın).

1. Çok sayıda sonuç sağlayacak bir metin arayın (örneğin, "WiFi"). Sonuçlar arasında düzgün şekilde sayfa eklenebilir mi?

    !["Havuz" sonuçları aracılığıyla numaralandırılmış sayfalama](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. En sağ ve daha sonraki, en son sayfa numaralarına tıklamakta çalışın. Sayfa numaralarını açık olan sayfayı ortalamak için uygun şekilde ayarlayın mi?

3. "First" ve "Last" seçenekleri faydalıdır mi? Bazı popüler web aramaları bu seçenekleri kullanır ve diğerleri değildir.

4. Sonuçların son sayfasına gidin. Son sayfa, en az **Resultsperpage** sonuçları içerebilen tek sayfasıdır.

    ![Son "WiFi" sayfası inceleniyor](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. "Town" yazın ve ara ' ya tıklayın. En az bir sayfa sonucu varsa, sayfalama seçeneği gösterilmez.

    !["Town" aranıyor](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Şimdi bu projeyi kapatın ve bu sayfalama biçimine bir alternatif deneyelim.

## <a name="extend-your-app-with-infinite-scrolling"></a>Sınırsız kaydırma ile uygulamanızı genişletme

Kullanıcı dikey kaydırma çubuğunu görüntülenmekte olan sonuçların en sonuna kaydırdığında sonsuz kaydırma tetiklenir. Bu olayda, bir sonraki sonuç sayfası için sunucuya çağrı yapılır. Daha fazla sonuç yoksa, hiçbir şey döndürülmez ve dikey kaydırma çubuğu değişmez. Daha fazla sonuç varsa, bunlar geçerli sayfaya eklenir ve kaydırma çubuğu, daha fazla sonuç olduğunu göstermek için değişir.

Buradaki önemli nokta, görüntülenen sayfanın değiştirilmemekte olduğu, ancak yeni sonuçlara eklendiği yerdir. Bir Kullanıcı her zaman aramanın ilk sonuçlarına geri gidebilirler.

Sonsuz kaydırma uygulamak için, sayfa numarası kaydırma öğelerinden herhangi biri eklenmeden önce projeyle başlayalım. Bu nedenle, gerekirse, GitHub: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples)' dan temel arama sayfasının başka bir kopyasını oluşturun.

### <a name="add-paging-fields-to-the-model"></a>Modele disk belleği alanları ekleme

1. İlk olarak, **Searchdata** sınıfına (SearchData.cs model dosyasında) bir **sayfalama** özelliği ekleyin.

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Bu değişken, bir sonraki sonuç sayfası gönderilmesi gerekiyorsa "ileri" yi tutan bir dizedir veya bir aramanın ilk sayfası için null olmalıdır.

2. Aynı dosyada ve ad alanı içinde, bir özelliği olan bir genel değişken sınıfı ekleyin. MVC 'de, genel değişkenler kendi statik sınıfında bildirilmiştir. **Resultsperpage** sayfa başına sonuç sayısını ayarlar. 

    ```cs
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

2. Bölümünü aşağıdaki kodla değiştirin. Yeni **&lt;div&gt;** bölümü, kaydırılabilir olması gereken alanı etrafında bulunur ve hem bir **overflow-y** özniteliği hem de "kaydırılan ()" adlı bir **OnScroll** işlevine çağrı ekler.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Doğrudan döngünün altında, &lt;/div&gt; etiketinden sonra, **kaydırılan** işlevi ekleyin.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
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

    Yukarıdaki betikteki **if** ifadesinde, kullanıcının dikey kaydırma çubuğunun alt kısmına kaydırılıp kaydırılamayacağını görmek için sınamalar yapılır. Varsa, **daha sonra**adlı bir eyleme **giriş** denetleyicisine yönelik bir çağrı yapılır. Denetleyici tarafından başka bir bilgi gerekmez, bu, sonraki veri sayfasını döndürür. Bu veriler daha sonra Özgün sayfa olarak aynı HTML stilleri kullanılarak biçimlendirilir. Hiçbir sonuç döndürülmezse hiçbir şey eklenmez ve işlemler oldukları gibi kalır.

### <a name="handle-the-next-action"></a>Sonraki eylemi işle

Denetleyiciye gönderilmesi gereken üç eylem vardır: uygulamanın ilk çalıştırılması ( **Dizin ()** , dizin ( **model)** ve ardından **İleri (model**) ile daha fazla sonuç için çağrı yapan, Kullanıcı tarafından yapılan ilk arama.

1. Ana denetleyici dosyasını açın ve özgün öğreticiden **Runqueryasync** yöntemini silin.

2. **Dizin (model)** eylemini aşağıdaki kodla değiştirin. Artık null olduğunda **disk belleği** alanını işler veya "ileri" olarak ayarlanır ve Azure bilişsel arama çağrısını işler.

    ```cs
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
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

    Numaralandırılmış sayfalama yöntemine benzer şekilde, yalnızca ihtiyacımız olan verileri istemek için **Atla** ve **üst** arama ayarlarını kullanırız.

3. Giriş denetleyicisine bir **sonraki** eylemi ekleyin. Bir listeyi nasıl döndürdüğünü, her bir otelin listeye iki öğe eklemesini ve bir otel adı ve otel açıklaması olduğunu aklınızda edin. Bu biçim, **kaydırılan** işlevin görünümdeki döndürülen verilerin kullanımıyla eşleşecek şekilde ayarlanır.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. **&lt;dize&gt;listede** bir sözdizimi hatası alıyorsanız, bu durumda denetleyici dosyasının baş bir yanındaki **using** yönergesini ekleyin.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Projenizi derleyin ve çalıştırın

Şimdi **hata ayıklama olmadan Başlat** ' ı seçin (veya F5 tuşuna basın).

1. Çok sayıda sonuç sağlayacak bir terim ("havuz" gibi) girin ve dikey kaydırma çubuğunu test edin. Yeni bir sonuç sayfası tetikleyecektir mi?

    !["Havuz" sonuçları aracılığıyla sonsuz kaydırma](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > İlk sayfada bir kaydırma çubuğunun göründüğünden emin olmak için sonuçların ilk sayfası, görüntülendikleri alanın yüksekliğini biraz daha aşmalıdır. Bizim örneğimizde **. Box1** , en fazla 30 piksel boyutunda, **. box2** , 100 piksel yüksekliğinde _ve_ 24 piksellik alt kenar boşluğuyla bulunur. Böylece her giriş 154 piksel kullanır. Üç giriş 3 x 154 = 462 piksel sürer. Dikey kaydırma çubuğunun göründüğünden emin olmak için, görüntüleme alanına yönelik bir yükseklik, 462 pikselden küçük, hatta 461 çalışıyor olmalıdır. Bu sorun yalnızca ilk sayfada, bir kaydırma çubuğu gösterildiğinizden emin olduktan sonra gerçekleşir. Güncelleştirilecek satır: **&lt;div ID = "myDiv" Style = "width: 800px; height: 450px; overflow-y: kaydır;" OnScroll = "kaydırılan ()"&gt;** .

2. Sonuçların sonuna kadar aşağı doğru kaydırın. Tüm bilgilerin artık tek bir görünüm sayfasında nasıl olduğunu fark edin. Herhangi bir sunucu çağrısı tetiklemeden, her şey için en üste doğru kaydırma yapabilirsiniz.

Daha gelişmiş sonsuz kaydırma sistemleri, yeni bir sonuç sayfası yüklemesini tetiklemek için fare tekerleğini veya benzer başka bir mekanizmayı kullanabilir. Bu öğreticilerde daha fazla sınırsız kaydırma gerçekleşmeyecek, ancak ek fare tıklamalarını önlediği ve diğer seçenekleri daha fazla araştırmak isteyebileceğiniz için belirli bir düğmesi vardır.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Numaralandırılmış sayfalama, sonuçların sırasının biraz rasgele olduğu, daha sonraki sayfalarda kullanıcılarınıza ilgi çekici bir şey olabileceği gibi, arama için de iyidir.
* Sonuçların sırası özellikle önemli olduğunda sonsuz kaydırma iyidir. Örneğin, sonuçlar hedef şehrin merkezinden uzaklığına göre sıralanır.
* Numaralandırılmış sayfalama, daha iyi bir gezinmede izin verir. Örneğin, bir Kullanıcı ilginç bir sonucun 6. sayfada olduğunu anımsayacak, sonsuz kaydırmadaki böyle kolay bir başvuru mevcut değildir.
* Sonsuz kaydırmanın kolay bir şekilde, üzerine tıklamasını sağlamak için Fussy sayfa numaraları olmadan yukarı ve aşağı kaydırın.
* Sonsuz kaydırmanın temel bir özelliği, sonuçların var olan bir sayfaya eklenmeme ve bu sayfanın değişmemelidir.
* Geçici depolama yalnızca tek bir çağrı için devam ettirir ve ek çağrılar için sıfırlanması gerekir.


## <a name="next-steps"></a>Sonraki adımlar

Sayfalama, internet aramalarında temel bir araçlardır. Sayfalama iyi ele alınarak, bir sonraki adım, daha sonra, tür ön aramalar ekleyerek kullanıcı deneyimini daha da geliştirmaktır.

> [!div class="nextstepaction"]
> [C#Öğretici: otomatik tamamlama ve öneriler ekleme-Azure Bilişsel Arama](tutorial-csharp-type-ahead-and-suggestions.md)
