---
title: Arama kutusuna otomatik tamamlama ekleme
titleSuffix: Azure Cognitive Search
description: Öneri araçları oluşturarak ve bir arama kutusunu tamamlanmış koşullara veya tümceciklere göre otomatik hale getirerek oluşan istekleri formülleyerek Azure Bilişsel Arama 'de arama yaparken arama eylemlerini etkinleştirin. Ayrıca, önerilen eşleşmeleri de döndürebilirsiniz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b260793cc3c0b5ac3ffce8fac909e2db8991c713
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307818"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Azure Bilişsel Arama kullanarak istemci uygulamalarına otomatik tamamlama ve öneriler ekleme

Yazarken ara, sorgu üretkenliğini iyileştirmeye yönelik yaygın bir tekniktir. Azure Bilişsel Arama, bu deneyim kısmi girişe dayalı bir terimi veya tümceciği tamamlayan *AutoComplete* aracılığıyla desteklenir ("Micro" ile "Microsoft" arasında tamamlanır). İkinci bir kullanıcı deneyimi, *öneriler* veya eşleşen belgelerin kısa bir listesidir (Bu kitapta bir ayrıntı sayfasına bağlantı yapabilmeniz IÇIN bir kimliğe sahip kitap başlıkları döndürülüyor). Hem otomatik tamamlama hem de öneriler dizindeki bir eşleşmeden tahmin edilir. Hizmet, sıfır sonuç döndüren sorgular sunmaz.

Bu deneyimleri Azure Bilişsel Arama uygulamak için şunlar gerekir:

+ Dizin şemasına gömülü bir *öneri aracı* tanımı.
+ İstekte [otomatik tamamlama](/rest/api/searchservice/autocomplete) veya [öneriler](/rest/api/searchservice/suggestions) API 'si belirten bir *sorgu* .
+ İstemci uygulamanızda arama-yazma etkileşimini işlemek için bir *Kullanıcı arabirimi denetimi* . Bu amaçla mevcut bir JavaScript kitaplığı kullanmanızı öneririz.

Azure Bilişsel Arama 'de, bir öneri aracı kaydettiğiniz seçili alanlardan, oto tamamlanmış sorgular ve önerilen sonuçlar arama dizininden alınır. Bir öneri aracı, dizinin bir parçasıdır ve bir sorguyu tamamlayan, bir sonuç öneren veya her ikisini de yapan içeriği belirtir. Dizin oluşturulup yüklendiğinde, kısmi sorgularda eşleşme için kullanılan önekleri depolamak üzere dahili olarak bir öneri aracı veri yapısı oluşturulur. Öneriler için, deneyim için benzersiz olan veya en az tekrarlanmayan uygun alanları seçme için gereklidir. Daha fazla bilgi için bkz. [Create a öneri aracı](index-add-suggesters.md).

Bu makalenin geri kalanı sorgulara ve istemci koduna odaklanılmıştır. Anahtar noktalarını göstermek Için JavaScript ve C# kullanır. REST API örnekleri, her işlemi öz için kullanılır. Uçtan uca kod örneklerine bağlantılar için bkz. [sonraki adımlar](#next-steps).

## <a name="set-up-a-request"></a>İstek ayarlama

Bir isteğin öğeleri, arama türü API 'Lerinden, kısmi bir sorgudan ve bir öneri aracı oluşur. Aşağıdaki betik, bir isteğin bileşenlerini bir örnek olarak otomatik tamamlama REST API kullanarak gösterir.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

"SuggesterName", hüküm veya önerileri tamamlamaya yönelik olarak kullanılan öneri aracı uyumlu alanları sağlar. Özellikle ilgili öneriler için, alan listesi, eşleşen sonuçlar arasında açık seçimler sunan olanlardan oluşmalıdır. Bilgisayar oyunları satan bir sitede, alan oyun başlığı olabilir.

"Arama" parametresi, bir jQuery AutoComplete denetimi aracılığıyla karakterlerin sorgu isteğine beslendiği kısmi sorguyu sağlar. Yukarıdaki örnekte, "minecraf" denetimin ne şekilde geçtiğini gösteren statik bir örnektir.

API 'Ler kısmi sorguda minimum uzunluk gereksinimlerini karşılamıyor; bir karakter çok az olabilir. Ancak jQuery AutoComplete, en az bir uzunluk sağlar. En az iki veya üç karakter normaldir.

Eşleşmeler, giriş dizesinin herhangi bir yerindeki bir terim başlangıcıdır. "Hızlı kahverengi Fox" verildiğinde, hem otomatik tamamlama hem de öneriler "rown" veya "Ox" gibi kısmi bir not, "yeni", "hızlı", "kahverengi" veya "Fox" gibi kısmi sürümlerde eşleşir. Ayrıca, her eşleşme aşağı akış genişleimleri için kapsamı ayarlar. "Hızlı" veya "hızlı içerik" için kısmi bir sorgu "hızlı kahverengi" veya "hızlı içerik" ile eşleşir, ancak "hızlı" işaretinden önce "kahverengi" veya "içerik" eşleşmesi gerekir.

### <a name="apis-for-search-as-you-type"></a>Yazarken arama için API 'Ler

REST ve .NET SDK başvuru sayfaları için bu bağlantıları izleyin:

+ [Öneriler REST API](/rest/api/searchservice/suggestions) 
+ [Otomatik tamamlama REST API](/rest/api/searchservice/autocomplete) 
+ [Mümütasync yöntemi](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Oto Tamteasync yöntemi](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Yanıt yapısı

Otomatik tamamlama ve önerilerle ilgili yanıtlar, bu model için beklediğiniz şeydir: [otomatik tamamlama](/rest/api/searchservice/autocomplete#response) bir terim listesi döndürüyor, [ÖNERILER](/rest/api/searchservice/suggestions#response) bir belge kimliği ve belgeyi alabilmeniz için (belirli bir belgeyi bir ayrıntı sayfasına getirmek üzere [Arama belgesi](/rest/api/searchservice/lookup-document) API 'sini kullanın) sağlar.

Yanıtlar istekteki parametrelere göre şekillendirilir:

+ Otomatik tamamlama için, bir veya iki terim üzerinde metin tamamlanmasının yapılıp yapılmayacağını anlamak için [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) ayarlayın. 

+ Öneriler için [$Select](/rest/api/searchservice/suggestions#query-parameters) , adlar ve açıklama gibi benzersiz veya farklılaştırıcı değerler içeren alanları döndürecek şekilde ayarlayın. Yinelenen değerler içeren alanlardan kaçının (örneğin, kategori veya şehir).

Aşağıdaki ek parametreler hem otomatik tamamlama hem de öneriler için geçerlidir, ancak özellikle de bir öneri aracı birden çok alan içerdiğinde öneriler için daha fazla gerekli olabilir.

| Parametre | Kullanım |
|-----------|-------|
| searchFields | Sorguyu belirli alanlarla sınırlayın. |
| $filter | Sonuç kümesinde () eşleşme ölçütlerini uygulayın `$filter=Category eq 'ActionAdventure'` . |
| $top | Sonuçları belirli bir sayıyla ( `$top=5` ) sınırlayın.|

## <a name="add-user-interaction-code"></a>Kullanıcı etkileşimi kodu ekle

Bir sorgu terimini otomatik olarak doldurma veya eşleşen bağlantıların bir listesini bırakma, Kullanıcı etkileşim kodu, tipik olarak JavaScript, bir Azure Search bilişsel dizine karşı otomatik tamamlama veya öneri sorguları gibi dış kaynaklardan gelen istekleri kullanabilen Kullanıcı etkileşimi kodu gerektirir.

Bu kodu yerel olarak yazabilseniz de, aşağıdakilerden biri gibi mevcut JavaScript kitaplığındaki işlevleri kullanmak çok daha kolay. 

+ [Otomatik tamamlama pencere öğesi (jQuery kullanıcı arabirimi)](https://jqueryui.com/autocomplete/) , öneri kodu parçacığında görüntülenir. Bir arama kutusu oluşturabilir ve sonra otomatik tamamlama pencere öğesini kullanan bir JavaScript işlevinde buna başvurabilirsiniz. Pencere öğesi üzerindeki özellikler, kaynak (otomatik tamamlama veya öneriler işlevi), eylem yapılmadan önce en az giriş karakteri uzunluğu ve konumlandırmayı ayarlar.

+ [Xdsoft AutoComplete eklentisi](https://xdsoft.net/jqplugins/autocomplete/) , otomatik tamamlama kod parçacığında görünür.

+ [öneriler](https://www.npmjs.com/package/suggestions) , [JavaScript öğreticisi](tutorial-javascript-overview.md) ve kod örneği ' nde görünür.

Hem önerileri hem de otomatik tamamlamayı destekleyen bir arama kutusu oluşturmak için istemcideki bu kitaplıkları kullanın. Arama kutusunda toplanan girişler daha sonra arama hizmetindeki önerilerle ve otomatik tamamlama eylemleriyle eşleştirilebilir.

## <a name="suggestions"></a>Öneriler

Bu bölüm, arama kutusu tanımıyla başlayarak önerilen sonuçların bir uygulamasında size kılavuzluk eder. Ayrıca, bu makalede başvurulan ilk JavaScript otomatik tamamlama kitaplığını çağıran nasıl ve betiğin olduğunu gösterir.

### <a name="create-a-search-box"></a>Arama kutusu oluşturma

[JQuery kullanıcı arabirimi otomatik tamamlama kitaplığı](https://jqueryui.com/autocomplete/) ve C# ' de MVC projesi varsayıldığında, **Dizin. cshtml** dosyasında JavaScript kullanarak arama kutusunu tanımlayabilirsiniz. Bu kitaplık, önerileri almak için MVC denetleyicisine zaman uyumsuz çağrılar yaparak arama kutusuna arama yapmak için arama yapma etkileşimini ekler.

\Views\Home klasörünün altındaki **Index. cshtml** dosyasında, arama kutusu oluşturmak için bir satır aşağıdaki gibi olabilir:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Bu örnek, stil, JavaScript ve yer tutucu metni için bir sınıf olan basit bir giriş metin kutusudur.  

Aynı dosya içinde, arama kutusuna başvuran JavaScript 'ı ekleyin. Aşağıdaki işlev, kısmi dönem girdilerine göre önerilen eşleşen belgeleri isteyen önerme API 'sini çağırır:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source`JQuery kullanıcı arabirimi otomatik tamamlama işlevine, arama kutusu altında gösterilecek öğelerin listesinin nereden alınacağını söyler. Bu proje bir MVC projesi olduğundan, sorgu önerilerini döndürme mantığını içeren **HomeController. cs** içindeki **önerme** işlevini çağırır. Bu işlev, vurguları, belirsiz eşleştirmeyi ve terimi denetlemek için birkaç parametreyi de geçirir. AutoComplete JavaScript API 'SI, term parametresini ekler.

, `minLength: 3` Arama kutusunda yalnızca en az üç karakter olduğunda önerilerin gösterilmesi sağlanır.

### <a name="enable-fuzzy-matching"></a>Benzer eşleştirmeyi etkinleştir

Benzer öğe araması, kullanıcı bir kelimeyi arama kutusuna yanlış yazsa bile yakın eşleşmelere göre sonuçlara ulaşmanızı sağlar. Düzenleme uzaklığı 1 ' dir. Bu, Kullanıcı girişi ile eşleşme arasında bir karakter üzerinde en fazla tutarsızlık olabileceği anlamına gelir. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Vurgulamayı etkinleştir

Vurgulama, girilen yazı tipi stilini, girişe karşılık gelen sonuçdaki karakterlere uygular. Örneğin, kısmi giriş "mikro" ise, sonuç **mikro** Soft, **mikro** kapsam vs. olarak görünür. Vurgulama, öneri işleviyle satır içi olarak tanımlanmış HighlightPreTag ve HighlightPostTag parametrelerini temel alır.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Öner işlevi

C# kullanıyorsanız ve MVC uygulaması, denetleyiciler dizini altındaki **HomeController. cs** dosyası, önerilen sonuçlar için bir sınıf oluşturabileceğiniz yerdir. .NET ' te, bir önerme işlevi, [mübir tasync yöntemi](/dotnet/api/azure.search.documents.searchclient.suggestasync)temel alır. .NET SDK hakkında daha fazla bilgi için bkz. [.NET uygulamasından Azure bilişsel arama kullanma](search-howto-dotnet-sdk.md).

`InitSearch`Yöntemi, Azure bilişsel arama hizmetine kimliği doğrulanmış BIR http Dizin istemcisi oluşturur. [Mür TOptions](/dotnet/api/azure.search.documents.suggestoptions) sınıfının özellikleri, sonuçlarda hangi alanların arandığını ve döndürüleceğini, eşleşmelerin sayısını ve belirsiz eşleşme kullanılıp kullanılmadığını belirleme. 

Otomatik tamamlama için, belirsiz eşleştirme bir düzenleme mesafesi (Atlanan veya yanlış yerleştirilmiş bir karakter) ile sınırlıdır. Otomatik tamamlama sorgularında benzer bir eşleştirmede bazen Dizin boyutuna ve bunların nasıl parçalı olduğuna bağlı olarak beklenmedik sonuçlar üretebileceğini unutmayın. Daha fazla bilgi için bkz. [bölüm ve parça kavramları](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

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

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

Mümülen Tasync işlevi, arama terimi girişine ek olarak, vuruş vurgulamaları döndürülüp döndürülmeyeceğini veya benzer eşleşme kullanıldığını belirleyecek iki parametre alır. Önerilen sonuçlara kadar en fazla sekiz eşleşme eklenebilir. Yöntemi, öner API 'sine geçirilen bir [Müde bir TOptions nesnesi](/dotnet/api/azure.search.documents.suggestoptions)oluşturur. Ardından alınan sonuç istemcide gösterilebilmesi için JSON biçimine dönüştürülür.

## <a name="autocomplete"></a>Otomatik Tamamlama

Şimdiye kadar, arama UX kodu önerilerle ortalandı. Sonraki kod bloğunda, Azure Bilişsel Arama AutoComplete için bir istek geçirerek XDSoft jQuery UI AutoComplete işlevini kullanarak AutoComplete gösterilmektedir. Önerilerle birlikte, bir C# uygulamasında, kullanıcı etkileşimini destekleyen kod **index. cshtml**'ye gider.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>AutoComplete işlevi

AutoComplete, otomatik olarak oluşturulan [Teasync yöntemini](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)temel alır. Önerilerle birlikte bu kod bloğu **HomeController. cs** dosyasına gider.

```csharp
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

AutoComplete işlevi, arama terimi girişini alır. Yöntemi bir [AutoCompleteParameters nesnesi](/rest/api/searchservice/autocomplete)oluşturur. Ardından alınan sonuç istemcide gösterilebilmesi için JSON biçimine dönüştürülür.

## <a name="next-steps"></a>Sonraki adımlar

Uçtan uca yönergeler veya hem arama hem de arama-yazma deneyimlerini gösteren kod için bu bağlantıları izleyin. Örnek, önerilerin karma uygulamasını ve birlikte otomatik tamamlamayı gösterir.

+ [Bir Web sitesine arama ekleme (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) , istemci uygulamasındaki kısmi dönem tamamlaması için açık kaynak öneriler paketini kullanır.
+ [Öğretici: c# ' de (Ders 3) ilk uygulamanızı](tutorial-csharp-type-ahead-and-suggestions.md) , Ilişkili  [C# kod örneğiyle oluşturun: Azure-Search-DotNet-Samples/Create-First-App/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) typeahead için yerel desteği gösterir.
