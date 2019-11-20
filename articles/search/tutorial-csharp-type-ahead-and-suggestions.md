---
title: C#otomatik tamamlama ve önerilerle ilgili öğretici
titleSuffix: Azure Cognitive Search
description: Bu öğreticide, açılan liste kullanarak kullanıcılardan arama terimi girişini toplamanın bir yolu olarak otomatik tamamlama ve öneriler gösterilmektedir. Mevcut bir oteller projesi üzerinde oluşturur.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b542476ac1c9b6d4368d97eb4db76518eb2dba03
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114571"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C#Öğretici: otomatik tamamlama ve öneriler ekleme-Azure Bilişsel Arama

Kullanıcı, arama kutusuna yazmaya başladığında otomatik tamamlamayı (tür ön ve öneriler) nasıl uygulayacağınızı öğrenin. Bu öğreticide, önceden yapılan sonuçları ve öneri sonuçlarını ayrı olarak göstereceğiz, daha zengin bir kullanıcı deneyimi oluşturmak için bunları birleştirme yöntemini göstereceğiz. Bir kullanıcının kullanılabilir tüm sonuçları bulmak için yalnızca iki veya üç anahtar yazmanız gerekebilir. Bu öğretici [ C# öğreticide oluşturulan sayfalama projesi üzerinde oluşturulmuştur: arama sonuçları sayfalandırma-Azure bilişsel arama](tutorial-csharp-paging.md) öğreticisi.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Öneri ekleme
> * Önerilere vurgulama ekleyin
> * Otomatik tamamlama Ekle
> * Otomatik tamamlamayı ve önerileri birleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

[Öğreticiye sahip olmak: arama sonuçları sayfalandırma-Azure bilişsel arama projesi çalışıyor ve çalışıyor. C# ](tutorial-csharp-paging.md) Bu proje kendi sürümünüz olabilir, önceki öğreticide tamamladığınız veya GitHub: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples)'dan yükleyebilirsiniz.

## <a name="add-suggestions"></a>Öneri ekleme

Kullanıcıya alternatifler sağlamanın en basit durumu ile başlayalım: bir öneriler listesi açılır.

1. İndex. cshtml dosyasında, **TextBoxFor** ifadesini aşağıdaki şekilde değiştirin.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    Buradaki anahtar, arama kutusunun KIMLIĞINI **azureautoöner**olarak belirledik.

2. Bu bildirimin ardından, kapanış **&lt;/div&gt;** sonra bu betiği girin.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Bu betiği, aynı KIMLIK ile arama kutusuna bağladık. Ayrıca, aramanın tetiklenmesi için en az iki karakter gerekir ve giriş denetleyicisinde iki sorgu parametresi ile **önerme** eylemini çağırıyoruz: **vurguları** ve **belirsiz**, her ikisi de bu örnekte false olarak ayarlanmıştır.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Görünüme jQuery betiklerine başvuru ekleme

Yukarıdaki betikte çağrılan otomatik tamamlama işlevi, jQuery kitaplığı 'nda kullanılabilir olduğu için kendimize yazmak zorunda olduğumuz bir şeydir. 

1. JQuery kitaplığına erişmek için, görünüm dosyasının &lt;Head&gt; bölümünü aşağıdaki kodla değiştirin.

    ```cs
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

2. Ayrıca, _Layout. cshtml dosyasında jQuery 'e başvuruda bulunan bir satırı ( **Görünümler/paylaşılan** klasörde) da kaldırmanız veya bir açıklama olarak belirlemeniz gerekir. Aşağıdaki satırları bulun ve gösterildiği gibi ilk komut satırını açıklama olarak belirleyin. Bu değişiklik jQuery 'e yapılan başvuruları önler.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Artık önceden tanımlanmış AutoComplete jQuery işlevlerini kullanabiliriz.

### <a name="add-the-suggest-action-to-the-controller"></a>Denetleyiciye öner eylemini ekleme

1. Giriş denetleyicisinde, **öner** eylemini (say, **sayfa** eyleminden sonra) ekleyin.

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    **En üstteki** parametre döndürülecek sonuç sayısını belirtir (belirtilmemişse, varsayılan 5 ' tir). Bir _öneri aracı_ , bu öğretici gibi bir istemci uygulaması tarafından değil, veriler ayarlandığında yapılan Azure dizininde belirtilir. Bu durumda, öneri aracı "SG" olarak adlandırılır ve **Hotelname** alanını arar, başka bir şey değildir. 

    Benzer eşleştirme, çıkışa "yakın isabetsizlik" sağlar. **Vurgular** parametresi true olarak ayarlanırsa, ÇıKıŞA kalın HTML etiketleri eklenir. Sonraki bölümde bu iki parametreyi true olarak ayarlayacağız.

2. Bazı sözdizimi hataları alabilirsiniz. Bu durumda, aşağıdaki iki **using** deyimini dosyanın en üstüne ekleyin.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Uygulamayı çalıştırın. "Po" girerken bir dizi seçenek edinirsiniz, örneğin? Şimdi "PA" i deneyin.

    !["Po" yazıldığında iki öneri ortaya çıkarır](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Girdiğiniz harflerin bir kelime başlatması _gerektiğini_ ve yalnızca sözcüğe dahil edilmeyeceğini unutmayın.

4. Görünüm betiği içinde, **& belirsiz** ' i true olarak ayarlayın ve uygulamayı yeniden çalıştırın. Şimdi "Po" yazın. Aramada bir harf yanlış olduğunu varsaydığını unutmayın!
 
    ![Benzer şekilde true olarak ayarlanmış "PA" yazıldığında](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    İlgileniyorsanız, [bilişsel arama Azure 'Daki Lucene sorgu söz dizimi](https://docs.microsoft.com/azure/search/query-lucene-syntax) , benzer aramalarda ayrıntılı olarak kullanılan mantığı açıklamaktadır.

## <a name="add-highlighting-to-the-suggestions"></a>Önerilere vurgulama ekleyin

**Vurgular** parametresini true olarak ayarlayarak, önerilerin kullanıcıya yönelik görünümünü iyileştirebiliriz. Ancak, önce kalın metni görüntülemek için görünüme bazı kodlar eklememiz gerekiyor.

1. Görünümde (index. cshtml), yukarıda girdiğiniz **azureautoöner betiğinden** sonra aşağıdaki betiği ekleyin.

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

2. Şimdi, metin kutusunun KIMLIĞINI aşağıdaki şekilde okunacak şekilde değiştirin.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Uygulamayı yeniden çalıştırın ve girilen metninizi önerilerle görmeniz gerekir. Söyleyin, "PA" yazmayı deneyin.
 
    ![Vurgulamaya sahip "PA" yazma](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Yukarıdaki vurgulama betikte kullanılan mantık, foolkanıt değildir. Aynı ada iki kez görüntülenen bir terim girerseniz, cıvadıklardaki sonuçlar gerçekten istediğiniz gibi değildir. "Mo" yazmayı deneyin.

    Bir geliştiricinin yanıtlaması gereken sorulardan biri, bir komut dosyası "iyi yeterince" ve ne zaman ele alınması gerektiği hakkında bir sorudır. Bu öğreticide herhangi bir şeyi vurgulamayacak, ancak kesin bir algoritma bulmanın, vurgulamanın daha fazla olması için dikkate alınması gereken bir şeydir.

## <a name="add-autocompletion"></a>Otomatik tamamlama Ekle

Önerilerden biraz farklı olan başka bir çeşitleme otomatik tamamlama (bazen "tür-ileri" olarak adlandırılır). Daha sonra, Kullanıcı deneyimini iyileştirmeye geçmeden önce en basit uygulamayla başlayacağız.

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

2. Şimdi metin kutusunun KIMLIĞINI değiştirin. bu nedenle, aşağıdaki gibi okur.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Giriş denetleyicisinde, **öner** eyleminin altında bulunan **otomatik tamamlama** eylemini girmemiz gerekir.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Öneriler için yaptığımız gibi otomatik tamamlama aramasında "SG" adlı aynı *öneri aracı* işlevini kullandığımızda (yalnızca otel adlarını otomatik tamamlamayı denememiz gerekir) dikkat edin.

    Bir dizi **AutocompleteMode** ayarı vardır ve **Onetermwithcontext**kullandık. Buradaki seçenek aralığının açıklaması için [Azure AutoComplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ' e bakın.

4. Uygulamayı çalıştırın. Açılan listede görüntülenen seçenek aralığının tek sözcüklerdir. "Re" ile başlayan sözcükleri yazmayı deneyin. Daha fazla harf yazıldığında seçenek sayısının nasıl azaldığına dikkat edin.

    ![Temel otomatik tamamlamada yazma](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Bu, daha önce çalıştırdığınız öneri betiği bu otomatik tamamlama betiğinden daha faydalı olabilir. Otomatik tamamlamayı daha kolay hale getirmek için öneri aramasına en iyi şekilde eklenmeleri gerekir.

## <a name="combine-autocompletion-and-suggestions"></a>Otomatik tamamlamayı ve önerileri birleştirme

Otomatik tamamlamayı ve önerileri birleştirmek, seçeneklerimizin en karmaşıktır ve muhtemelen en iyi kullanıcı deneyimini sağlar. Ne yaptığımız, yazılmakta olan metinle satır içi olarak, metni tekrar tamamlamak için ilk tercih edilen Azure Bilişsel Arama. Ayrıca, açılan liste olarak bir dizi öneri istiyoruz.

Bu işlevi sunan kitaplıklar vardır-genellikle "satır içi otomatik tamamlama" veya benzer bir ad olarak adlandırılır. Bununla birlikte, bu özelliği yerel olarak uygulayacağız, bu sayede neler olduğunu görebiliriz. Bu örnekte ilk olarak denetleyicide çalışmaya başlayacağız.

1. Denetleyiciye yalnızca bir otomatik tamamlama sonucu döndüren bir eylem eklememiz gerekiyor, ancak belirtilen sayıda öneri. Bu eylemi **AutocompleteAndSuggest**çağıracağız. Giriş denetleyicisinde, diğer yeni eylemlerinizi izleyerek aşağıdaki eylemi ekleyin.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    **Sonuçlar** listesinin en üstünde bir otomatik tamamlama seçeneği döndürülür ve tüm öneriler gelir.

2. Görünümde ilk olarak, açık gri otomatik tamamlama sözcüğünün Kullanıcı tarafından girilen beski metin altında oluşturulması için bir el uyguladık. HTML, bu amaçla Göreli konumlandırmayı içerir. **TextBoxFor** deyimini (ve çevreleyen &lt;div&gt; deyimlerini) aşağıdaki şekilde değiştirin. bu arama kutusu 39 piksel olan bu arama kutusunu çekerek normal arama kutusumuzda doğru olarak tanımlanan ikinci bir arama kutusu **aşağıda verilmiştir!**

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Bu durumda KIMLIĞI yeniden **azureautotamamlamayı** olarak değiştirdik.

3. Ayrıca, bu ana kadar girdiğiniz tüm betiklerin ardından, görünümünde aşağıdaki betiği girin. Tam olarak çok büyük bir şey vardır.

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
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
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

    Aralık işlevinin, kullanıcının yazmasıyla aynı büyük/küçük harf (büyük veya daha düşük) ve Kullanıcı yazarken ("PA" ile "PA", "PA", "PA", arama sırasında "PA" ile eşleşir), ancak çakışan metnin nede olduğu şekilde ("PA" ile) aynı durumu (büyük veya küçük) belirlemek için **Interval** işlevinin zekice kullanımına dikkat edin.

    Daha fazla bilgi edinmek için betikteki açıklamaları okuyun.

4. Son olarak, iki HTML sınıfının saydam hale getirmek için küçük bir ayarlama yapmanız gerekir. Aşağıdaki satırı, otel. CSS dosyasındaki **Searchboxform** ve **searchbox** sınıflarına ekleyin.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Şimdi uygulamayı çalıştırın. Arama kutusuna "PA" yazın. "PA" içeren iki otel ile birlikte otomatik tamamlama önerisi olarak "damace" edinirsiniz.

    ![Satır içi otomatik tamamlama ve önerilerle yazma](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Otomatik tamamlama önerisini kabul etmek için sekmeyi ve ok tuşlarını ve Tab tuşunu kullanarak önerileri seçmeyi deneyin, fare ve tek bir tıklama kullanarak yeniden deneyin. Betiğin tüm bu durumları doğru şekilde işlediğini doğrulayın.

    Bu özelliği sizin için sunan bir kitaplıkta yüklenmeye daha basit olduğuna karar verebilirsiniz, ancak artık iş için satır içi otomatik tamamlamayı tamamlamak için en az bir yol öğrenirsiniz!

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Otomatik tamamlama (aynı zamanda "tür ön" olarak da bilinir) ve öneriler, kullanıcının tam olarak istedikleri şeyi bulmak için yalnızca birkaç anahtar yazmasını sağlayabilir.
* Otomatik tamamlama ve öneriler birlikte çalışarak, zengin bir kullanıcı deneyimi sağlayabilir.
* Otomatik tamamlama işlevlerini her zaman tüm giriş formlarıyla test edin.
* **SetInterval** işlevinin kullanılması, Kullanıcı arabirimi öğelerini doğrulamak ve düzeltmek için yararlı olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide, tek bir tıklama ile aramaları daraltmak için modelleri kullanarak Kullanıcı deneyimini geliştirmenin başka bir yoluna göz atacağız.

> [!div class="nextstepaction"]
> [C#Öğretici: Azure Bilişsel Arama Yardım için modelleri kullanma](tutorial-csharp-facets.md)


