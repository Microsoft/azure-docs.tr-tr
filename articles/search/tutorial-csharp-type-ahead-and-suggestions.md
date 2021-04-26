---
title: Otomatik tamamlama ve önerilerle ilgili C# öğreticisi
titleSuffix: Azure Cognitive Search
description: Açılır liste kullanarak kullanıcılardan arama terimi girişini toplamak için otomatik tamamlama ve öneriler ekleyin. Bu öğretici, mevcut bir oteller projesi üzerinde oluşturulur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785862"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak otomatik tamamlama ve öneriler ekleme

Kullanıcı bir arama kutusuna yazmaya başladığında otomatik tamamlamayı (typeahead sorguları ve önerilen sonuçlar) uygulamayı öğrenin. Bu öğreticide, oto tamamlanmış sorguları ve önerilen sonuçları ayrı olarak ve ardından birlikte göstereceğiz. Bir kullanıcının kullanılabilir tüm sonuçları bulmak için yalnızca iki veya üç karakter yazmanız gerekebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Öneri ekleme
> * Önerilere vurgulama ekleyin
> * Otomatik tamamlama Ekle
> * Otomatik tamamlamayı ve önerileri birleştirme

## <a name="overview"></a>Genel Bakış

Bu öğretici, [Arama sonuçlarına yönelik önceki sayfalama Ekle](tutorial-csharp-paging.md) öğreticisine otomatik tamamlama ve önerilen sonuçlar ekler.

Bu öğreticideki kodun tamamlanmış bir sürümü aşağıdaki projede bulunabilir:

* [3-Add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Önkoşullar

* [2A-Add-sayfalama (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) çözümü. Bu proje, önceki öğreticiden ya da GitHub 'dan bir kopyadan oluşturulmuş kendi sürümünüz olabilir.

Bu öğretici, [Azure.Search.Documstalar (sürüm 11)](https://www.nuget.org/packages/Azure.Search.Documents/) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için bkz. [Microsoft. Azure. Search (sürüm 10) kod örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="add-suggestions"></a>Öneri ekleme

Kullanıcıya alternatifler sunma konusunda en basit durum ile başlayalım: önerilen sonuçların açılan listesi.

1. İndex. cshtml dosyasında, `@id` **TextBoxFor** ifadesinin **azureautoöner** olarak değiştirilmesi.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Bu bildirimin ardından, kapanış **&lt; /div &gt;** öğesinden sonra bu betiği girin. Bu betik, önerilen sonuçların açılan listesini sunmak için açık kaynak jQuery kullanıcı arabirimi kitaplığındaki [otomatik tamamlama pencere öğesini](https://api.jqueryui.com/autocomplete/) kullanır.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    KIMLIK, `"azureautosuggest"` Yukarıdaki betiği arama kutusuna bağlar. Pencere öğesinin kaynak seçeneği, önerilen API 'yi iki sorgu parametresiyle çağıran bir öner metoduna ayarlanır: **vurgular** ve **belirsiz**, her ikisi de bu örnekte false olarak ayarlanmıştır. Ayrıca, aramanın tetiklenmesi için en az iki karakter gerekir.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Görünüme jQuery betiklerine başvuru ekleme

1. JQuery kitaplığına erişmek için, &lt; &gt; Görünüm dosyasının baş bölümünü şu kodla değiştirin:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Yeni bir jQuery başvurusu sunduğumuz için, _Layout. cshtml dosyasında ( **Görünümler/paylaşılan** klasöründe) varsayılan jQuery başvurusunu da kaldırmanız veya kullanıma açıklamamız gerekir. Aşağıdaki satırları bulun ve gösterildiği gibi ilk komut satırını açıklama olarak belirleyin. Bu değişiklik jQuery 'e yapılan başvuruları önler.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Artık önceden tanımlanmış AutoComplete jQuery işlevlerini kullanabiliriz.

### <a name="add-the-suggest-action-to-the-controller"></a>Denetleyiciye öner eylemini ekleme

1. Ana denetleyicide, **Mükron** eylemini ( **pageasync** eyleminden sonra) ekleyin.

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    **Boyut** parametresi, döndürülecek sonuç sayısını belirtir (belirtilmemişse, varsayılan 5 ' tir). Dizin oluşturulduğunda arama dizininde bir _öneri aracı_ belirtilir. Microsoft tarafından barındırılan örnek oteller dizininde, öneri aracı adı "SG" olur ve yalnızca **Hotelname** alanında önerilen eşleşmeleri arar.

    Benzer eşleştirme, çıkışa, tek bir düzenleme uzaklığına kadar "neredeyse isabetsizlik" sağlar. **Vurgular** parametresi true olarak ayarlanırsa, ÇıKıŞA kalın HTML etiketleri eklenir. Sonraki bölümde her iki parametreyi de true olarak ayarlayacağız.

2. Bazı sözdizimi hataları alabilirsiniz. Bu durumda, aşağıdaki iki **using** deyimini dosyanın en üstüne ekleyin.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Uygulamayı çalıştırın. "Po" girerken bir dizi seçenek edinirsiniz, örneğin? Şimdi "PA" i deneyin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Yazma * Po * iki öneri ortaya koyar" border="false":::

    Girdiğiniz harflerin bir kelime başlatması _gerektiğini_ ve yalnızca sözcüğe dahil edilmeyeceğini unutmayın.

4. Görünüm betiği içinde, **&belirsiz** ' i true olarak ayarlayın ve uygulamayı yeniden çalıştırın. Şimdi "Po" yazın. Aramada bir harf yanlış olduğunu varsaydığını unutmayın.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="Belirsiz olarak true olarak ayarlanan * PA * yazma" border="false":::

    İlgileniyorsanız, [bilişsel arama Azure 'Daki Lucene sorgu söz dizimi](./query-lucene-syntax.md) , benzer aramalarda ayrıntılı olarak kullanılan mantığı açıklamaktadır.

## <a name="add-highlighting-to-the-suggestions"></a>Önerilere vurgulama ekleyin

**Vurgular** parametresini true olarak ayarlayarak, önerilerin görünümünü iyileştirebiliriz. Ancak, önce kalın metni görüntülemek için görünüme bazı kodlar eklememiz gerekiyor.

1. Görünümde (index. cshtml), `"azureautosuggest"` daha önce açıklanan betikten sonra aşağıdaki betiği ekleyin.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Şimdi, metin kutusunun KIMLIĞINI aşağıdaki şekilde okunacak şekilde değiştirin.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Uygulamayı yeniden çalıştırın ve girilen metninizi önerilerle görmeniz gerekir. "PA" yazmayı deneyin.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="Vurgulamaya sahip * PA * yazma" border="false":::

   Yukarıdaki vurgulama betikte kullanılan mantık, foolkanıt değildir. Aynı ada iki kez görüntülenen bir terim girerseniz, cıvadıklardaki sonuçlar gerçekten istediğiniz gibi değildir. "Mo" yazmayı deneyin.

   Bir geliştiricinin yanıtlaması gereken sorulardan biri, bir komut dosyası "iyi yeterince" ve ne zaman ele alınması gerektiği hakkında bir sorudır. Bu öğreticide herhangi bir şeyi vurgulamıyoruz, ancak kesin bir algoritma bulunması, vurgulamanın verileriniz için etkin olup olmadığını düşünmeyecek bir şeydir. Daha fazla bilgi için bkz. [isabet vurgulama](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Otomatik tamamlama Ekle

Farklı bir varyasyon, önerilerden biraz farklıdır, bir sorgu terimini tamamlayan otomatik tamamlama (bazen "tür-ileri" olarak adlandırılır). Daha sonra, Kullanıcı deneyimini iyileştirmeye başlamadan önce en basit uygulamayla başlayacağız.

1. Önceki betiklerinizin ardından, görünüme aşağıdaki betiği girin.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Şimdi metin kutusunun KIMLIĞINI değiştirin. bu nedenle, aşağıdaki gibi okur.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Ana denetleyicide, **Mübir tasync** eyleminden sonra, **oto tamamlana eşitleme** eylemini girin.

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    Öneriler için yaptığımız gibi otomatik tamamlama aramasında "SG" adlı aynı *öneri aracı* işlevini kullandığımızda (yalnızca otel adlarını otomatik tamamlamayı denememiz gerekir) dikkat edin.

    Bir dizi **AutocompleteMode** ayarı vardır ve **Onetermwithcontext** kullandık. Ek seçeneklerin açıklaması için [otomatik tamamlama API](/rest/api/searchservice/autocomplete) 'sine bakın.

1. Uygulamayı çalıştırın. Açılan listede görüntülenen seçenek aralığının tek sözcüklerdir. "Re" ile başlayan sözcükleri yazmayı deneyin. Daha fazla harf yazıldığında seçenek sayısının nasıl azaldığına dikkat edin.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Temel otomatik tamamlamada yazma" border="false":::

    Bu, daha önce çalıştırdığınız öneri betiği bu otomatik tamamlama betiğinden daha faydalı olabilir. Otomatik tamamlamayı daha kolay hale getirmek için, önerilen sonuçlarla kullanmayı göz önünde bulundurun.

## <a name="combine-autocompletion-and-suggestions"></a>Otomatik tamamlamayı ve önerileri birleştirme

Otomatik tamamlamayı ve önerileri birleştirmek, seçeneklerimizin en karmaşıktır ve muhtemelen en iyi kullanıcı deneyimini sağlar. Ne yaptığımız, yazılan metinle satır içi olarak, metni tekrar tamamlamak için Azure Bilişsel Arama ilk seçidir. Ayrıca, açılan liste olarak bir dizi öneri istiyoruz.

Bu işlevi sunan kitaplıklar vardır-genellikle "satır içi otomatik tamamlama" veya benzer bir ad olarak adlandırılır. Bununla birlikte, API 'Leri keşfedebilmeniz için bu özelliği yerel olarak uygulayacağız. Bu örnekte ilk olarak denetleyicide çalışmaya başlayacağız.

1. Denetleyiciye yalnızca bir otomatik tamamlama sonucu döndüren bir eylem ekleyin ve belirtilen sayıda öneri vardır. Bu eylemi **AutoCompleteAndSuggestAsync** çağıracağız. Giriş denetleyicisinde, diğer yeni eylemlerinizi izleyerek aşağıdaki eylemi ekleyin.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    **Sonuçlar** listesinin en üstünde bir otomatik tamamlama seçeneği döndürülür ve tüm öneriler gelir.

1. Görünümde ilk olarak, açık gri otomatik tamamlama sözcüğünün Kullanıcı tarafından girilen beski metin altında oluşturulması için bir el uyguladık. HTML, bu amaçla Göreli konumlandırmayı içerir. **TextBoxFor** deyimini (ve çevreleyen &lt; div &gt; deyimlerini) aşağıdaki şekilde değiştirin. bu arama kutusu 39 piksel olan bu arama kutusunu varsayılan konumlarından  çekerek, aşağıda gösterildiği gibi bir ikinci arama kutusunun normal arama kutusumuza doğru olduğunu unutmayın!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Bu durumda ID 'yi yeniden **azureautotamamlamayı** olarak değiştirdik.

1. Ayrıca, bu ana kadar girdiğiniz tüm betiklerin ardından, görünümünde aşağıdaki betiği girin. Betiği, işlediği çeşitli giriş davranışları nedeniyle uzun ve karmaşıktır.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Aralık işlevinin, artık kullanıcının yazdıksız bir şekilde eşleşmez ve ayrıca, Kullanıcı yazarken aynı durumu (büyük veya daha düşük) ("PA" ile "PA", "PA", arama sırasında "PA"), ancak çakışan metnin nede olduğu şekilde ayarlamak için **Interval** işlevinin nasıl kullanıldığına dikkat edin.

    Daha fazla bilgi edinmek için betikteki açıklamaları okuyun.

1. Son olarak, iki HTML sınıfının saydam hale getirmek için küçük bir ayarlama yapmanız gerekir. Aşağıdaki satırı, otel. CSS dosyasındaki **Searchboxform** ve **searchbox** sınıflarına ekleyin.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Şimdi uygulamayı çalıştırın. Arama kutusuna "PA" yazın. "PA" içeren iki otel ile birlikte otomatik tamamlama önerisi olarak "damace" edinirsiniz.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Satır içi otomatik tamamlama ve önerilerle yazma" border="false":::

1. Otomatik tamamlama önerisini kabul etmek için sekmeyi ve ok tuşlarını ve Tab tuşunu kullanarak önerileri seçmeyi deneyin, fare ve tek bir tıklama kullanarak yeniden deneyin. Betiğin tüm bu durumları doğru şekilde işlediğini doğrulayın.

    Bu özelliği sizin için sunan bir kitaplıkta yüklenmeye daha basit olduğuna karar verebilirsiniz, ancak artık iş için satır içi otomatik tamamlamayı almanın en az bir yolunu öğrenirsiniz.

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Otomatik tamamlama (aynı zamanda "tür ön" olarak da bilinir) ve öneriler, kullanıcının tam olarak istedikleri şeyi bulmak için yalnızca birkaç anahtar yazmasını sağlayabilir.
* Otomatik tamamlama ve öneriler birlikte çalışarak, zengin bir kullanıcı deneyimi sağlayabilir.
* Otomatik tamamlama işlevlerini her zaman tüm giriş formlarıyla test edin.
* **SetInterval** işlevinin kullanılması, Kullanıcı arabirimi öğelerini doğrulamak ve düzeltmek için yararlı olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide, tek bir tıklama ile aramaları daraltmak için modelleri kullanarak Kullanıcı deneyimini geliştirmenin başka bir yoluna göz atacağız.

> [!div class="nextstepaction"]
> [C# öğreticisi: gezintiye yardımcı olması için modelleri kullanma-Azure Bilişsel Arama](tutorial-csharp-facets.md)
