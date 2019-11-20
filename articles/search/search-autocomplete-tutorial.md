---
title: Arama kutusuna öneriler ve otomatik tamamlama ekleme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de typeahead sorgu eylemlerini, öneri araçları oluşturarak ve bir arama kutusunu tamamlanmış koşullarla veya tümceciklerle dolduran istekleri formülleyerek etkinleştirin.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792518"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Azure Bilişsel Arama uygulamanıza öneriler veya otomatik tamamlama ekleyin

Bu makalede, arama türü davranışlarını destekleyen güçlü bir arama kutusu oluşturmak için [öneriler](https://docs.microsoft.com/rest/api/searchservice/suggestions) ve [otomatik tamamlama özelliklerini](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nasıl kullanacağınızı öğrenin.

+ *Öneriler* , siz yazarken oluşturulan önerilen sonuçlardır; burada her öneri, şimdiye kadar yazmış olduğunuz dizinden eşleşen tek bir sonucudur. 

+ *Otomatik tamamlama* kullanıcının şu anda yazmakta olduğu sözcüğü veya tümceciği "sonlandırır". Sonuçları döndürmek yerine, sonuçları döndürmek için yürütebilen bir sorgu tamamlar. Önerilerle birlikte, sorgudaki tamamlanmış bir sözcük veya tümcecik, dizindeki bir eşleşmeyle tahmin edilir. Hizmet, dizinde sıfır sonuç döndüren sorgular sunmaz.

Bu özellikleri değerlendirmek için, **Dotnethowtoautocomplete** içinde örnek kodu indirebilir ve çalıştırabilirsiniz. Örnek kod, [NYCJobs demo verileriyle](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)doldurulmuş önceden oluşturulmuş bir dizini hedefler. NYCJobs dizini, öneriler veya otomatik tamamlama kullanımı için bir gereksinim olan bir [öneri aracı yapısı](index-add-suggesters.md)içerir. Bir sandbox hizmetinde barındırılan hazırlanan dizini kullanabilir veya NYCJobs örnek çözümünde bir veri yükleyicisi kullanarak [kendi dizininizi doldurabilirsiniz](#configure-app) . 

**Dotnethowtoautocomplete** örneği hem hem de JavaScript dil sürümlerindeki önerileri ve otomatik C# tamamlamayı gösterir. C#Geliştiriciler, [Azure bilişsel arama .NET SDK 'sını](https://aka.ms/search-sdk)kullanan ASP.NET MVC tabanlı bir uygulama aracılığıyla ileredebilir. Otomatik tamamlama ve önerilen sorgu çağrılarını yapma mantığı HomeController.cs dosyasında bulunabilir. JavaScript geliştiricileri, [Azure Bilişsel Arama REST API](https://docs.microsoft.com/rest/api/searchservice/)doğrudan çağrılarını Içeren ındexjavascript. cshtml içinde eşdeğer sorgu mantığını bulacaktır. 

Her iki dil sürümünde de ön uç Kullanıcı deneyimi [jQuery kullanıcı arabirimi](https://jqueryui.com/autocomplete/) ve [xdsoft](https://xdsoft.net/jqplugins/autocomplete/) kitaplıklarını temel alır. Bu kitaplıkları, hem önerileri hem de otomatik tamamlamayı destekleyen arama kutusunu oluşturmak için kullanırız. Arama kutusunda toplanan girişler, HomeController.cs veya ındexjavascript. cshtml içinde tanımlananlar gibi öneriler ve AutoComplete eylemleri ile eşleştirilir.

Bu alıştırma aşağıdaki görevlerde size kılavuzluk eder:

> [!div class="checklist"]
> * JavaScript 'te bir arama giriş kutusu uygulayın ve önerilen eşleşmeler veya oto tamamlanmış şartlar için sorun istekleri yapın
> * İçinde C#, HomeController.cs 'de öneriler ve otomatik tamamlama eylemleri tanımlayın
> * JavaScript 'te, aynı işlevselliği sağlamak için REST API 'Lerini doğrudan çağırın

## <a name="prerequisites"></a>Önkoşullar

Çözüm, Prepared NYCJobs demo dizinini barındıran canlı bir korumalı alan hizmeti kullandığından, bu alıştırma için bir Azure Bilişsel Arama hizmeti isteğe bağlıdır. Bu örneği kendi arama hizmetinizde çalıştırmak istiyorsanız, yönergeler için bkz. [NYC işleri dizinini yapılandırma](#configure-app) .

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), herhangi bir sürüm. Örnek kod ve yönergeler ücretsiz topluluk sürümünde sınanmıştır.

* [Dotnethowtoautocomplete örneğini](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)indirin.

Örnek, öneriler, otomatik tamamlama, çok yönlü gezinme ve istemci tarafı önbelleğe alma işlemlerini kapsayan kapsamlı bir örnektir. Örnek tekliflerinin tam açıklaması için README ve Comments ' i gözden geçirin.

## <a name="run-the-sample"></a>Örneği çalıştırma

1. Visual Studio 'da **AutocompleteTutorial. sln** 'yi açın. Çözüm, NYC Işleri tanıtım dizini bağlantısı olan bir ASP.NET MVC projesi içerir.

2. Projeyi çalıştırmak ve sayfayı istediğiniz tarayıcıda yüklemek için F5'e basın.

En üstte C# ve JavaScript seçeneklerini göreceksiniz. C# Seçeneği, tarayıcıdan HomeController öğesine çağrı yapılır ve sonuçları almak için Azure bilişsel arama .NET SDK 'sını kullanır. 

JavaScript seçeneği doğrudan tarayıcıdan Azure Bilişsel Arama REST API çağırır. Bu seçenek, denetleyiciyi akışın dışına çıkmasından bu yana önemli ölçüde daha iyi performansa sahip olur. İhtiyaçlarınıza ve dil tercihlerinize uygun seçeneği tercih edebilirsiniz. Sayfada, her biri için bazı kılavuzlarla birkaç AutoComplete örneği vardır. Her örnekte deneyebileceğiniz önerilen örnek metin vardır.  

Gerçekleştirilen işlemleri görmek için her arama kutusuna birkaç harf yazmayı deneyin.

## <a name="search-box"></a>Arama kutusu

Hem hem C# de JavaScript sürümleri için arama kutusu uygulamasının tamamen aynı olması gerekir. 

Kodu görüntülemek için \Views\Home klasörünün altındaki **Index. cshtml** dosyasını açın:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Bu örnek, stil, JavaScript ve yer tutucu metni için bir sınıf olan basit bir giriş metin kutusudur.  Magic, katıştırılmış JavaScript 'te.

C# Dil örneği, [jQuery kullanıcı arabirimi otomatik tamamlama kitaplığından](https://jqueryui.com/autocomplete/)yararlanmak için Index. cshtml içindeki JavaScript 'i kullanır. Bu kitaplık, önerileri almak için MVC denetleyicisine zaman uyumsuz çağrılar yaparak otomatik tamamlama deneyimini arama kutusuna ekler. JavaScript dili sürümü ındexjavascript. cshtml içinde bulunur. Arama çubuğu için aşağıdaki betiği ve REST API Azure Bilişsel Arama çağrılarını içerir.

Bir öneri isteği geçirerek jQuery UI AutoComplete işlevini çağıran ilk örnek için JavaScript koduna bakalım:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Yukarıdaki kod, "example1a" giriş kutusu için jQuery kullanıcı arabirimi otomatik tamamlamayı yapılandırmak üzere sayfa yükünün tarayıcısında çalışır.  `minLength: 3`, önerilerin yalnızca arama kutusuna en az üç karakter girildiğinde gösterilmesini sağlar.  Kaynak değeri önemlidir:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Yukarıdaki çizgi, jQuery kullanıcı arabirimi otomatik tamamlama işlevine, arama kutusu altında gösterilecek öğelerin listesinin nereden alınacağını söyler. Bu proje bir MVC projesi olduğundan, sorgu önerilerini döndürmeyle ilgili mantığı içeren HomeController.cs 'de öner işlevini çağırır (sonraki bölümde öneri hakkında daha fazla bilgi). Bu işlev, vurguları, belirsiz eşleştirmeyi ve terimi denetlemek için birkaç parametreyi de geçirir. AutoComplete JavaScript API 'SI, term parametresini ekler.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Örneği benzer öğe eşleştirmeyi destekleyecek şekilde genişletme

Benzer öğe araması, kullanıcı bir kelimeyi arama kutusuna yanlış yazsa bile yakın eşleşmelere göre sonuçlara ulaşmanızı sağlar. Gerekli olmasa da, bir typeahead deneyiminin sağlamlığını önemli ölçüde artırır. Şimdi kaynak satırını benzer öğe eşleştirmeyi etkinleştirecek şekilde değiştirerek bir deneme yapalım.

Aşağıdaki satırı değiştirin:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

şu şekilde:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

F5 tuşuna basarak uygulamayı başlatın.

"execative" gibi bir terim yazmayı deneyin. Yazdığınız harflerle tam olarak eşleşmediği halde "executive" terimi sonuçlarının döndürüldüğünü göreceksiniz.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Azure Bilişsel Arama otomatik tamamlama tarafından desteklenen jQuery otomatik tamamlama

Şimdiye kadar, arama UX kodu önerilere göre ortalandı. Sonraki kod bloğunda, jQuery UI AutoComplete işlevi (index. cshtml 'de satır 91), Azure Bilişsel Arama AutoComplete isteği için bir istek geçirerek gösterilmektedir:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C#örneğinde

Web sayfası için JavaScript kodunu incelediğimiz için, Azure Bilişsel Arama .NET SDK kullanarak önerilen eşleşmeleri gerçekten alan C# sunucu tarafı denetleyici koduna bakalım.

**HomeController.cs** dosyasını denetleyiciler dizininde açın. 

İlk şey, `InitSearch`adlı sınıfın en üstünde bir yöntemdir. Bu yöntem, Azure Bilişsel Arama hizmetine kimliği doğrulanmış bir HTTP Dizin istemcisi oluşturur. Daha fazla bilgi için bkz. [.NET uygulamasından Azure bilişsel arama kullanma](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

41. satırda, önerme işlevine dikkat edin. [Documentsoperationsextensions. önerme metodunu](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)temel alır.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest işlevi, arama terimi girişine ek olarak isabet vurgularının veya benzer öğe eşleştirme özelliğinin kullanılıp kullanılmadığını belirleyen iki parametre alır. Yöntemi, daha sonra öneri API 'sine geçirilen bir [SuggestParameters nesnesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)oluşturur. Ardından alınan sonuç istemcide gösterilebilmesi için JSON biçimine dönüştürülür.

Satır 69 ' de AutoComplete işlevine dikkat edin. [Documentsoperationsextensions. AutoComplete metodunu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)temel alır.

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

AutoComplete işlevi, arama terimi girişini alır. Yöntemi bir [AutoCompleteParameters nesnesi](https://docs.microsoft.com/rest/api/searchservice/autocomplete)oluşturur. Ardından alınan sonuç istemcide gösterilebilmesi için JSON biçimine dönüştürülür.

(İsteğe bağlı) Suggest işlevinin başlangıcına bir kesme noktası ekleyin ve kodu adım adım inceleyin. SDK tarafından döndürülen yanıtı ve yöntemin döndürülen sonuca nasıl dönüştürüleceğini fark edin.

Sayfadaki diğer örnekler, otomatik tamamlama sonuçlarının istemci tarafında önbelleğe alınmasını desteklemek üzere isabet vurgulama ve modeller eklemek için aynı kalıbı izler. Her birini gözden geçirerek nasıl çalıştıklarını ve arama deneyiminizde nasıl kullanabileceğinizi görebilirsiniz.

## <a name="javascript-example"></a>JavaScript örneği

Otomatik tamamlama ve önerilerle bir JavaScript uygulamasının, dizin ve işlemi belirtmek için kaynak olarak bir URI kullanarak REST API çağırır. 

JavaScript uygulamasını gözden geçirmek için **ındexjavascript. cshtml**dosyasını açın. JQuery UI AutoComplete işlevinin arama kutusu için de kullanıldığını, arama terimi girişlerini toplamasını ve önerilen eşleşmeleri veya tamamlanmış koşulları almak için Azure Bilişsel Arama zaman uyumsuz çağrılar yapmayı unutmayın. 

İlk örneğin JavaScript koduna bakalım:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Bu örneği, ana denetleyiciyi çağıran Yukarıdaki örnekle karşılaştırırsanız birkaç benzerlikler fark edersiniz.  `minLength` ve `position` için otomatik tamamlama yapılandırması tamamen aynıdır. 

Buradaki önemli değişiklik kaynaktır. Giriş denetleyicisinde öner metodunu çağırmak yerine bir JavaScript işlevinde bir REST isteği oluşturulur ve Ajax kullanılarak yürütülür. Ardından gelen yanıt "success" parametresinde işlenir ve kaynak olarak kullanılır.

REST çağrıları, bir [otomatik tamamlama](https://docs.microsoft.com/rest/api/searchservice/autocomplete) veya [öneriler](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 'si çağrısının yapılıp yapılmadığını belirtmek için URI kullanır. Aşağıdaki URI 'Ler sırasıyla 9 ve 10 satırlarında bulunur.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Satır 148 ' de `autocompleteUri`çağıran bir komut dosyası bulabilirsiniz. `suggestUri` ilk çağrısı 39. satırda yer alır.

> [!Note]
> JavaScript 'te hizmete REST çağrıları yapmak, REST API kullanışlı bir gösterim olarak sunulur, ancak en iyi yöntem veya öneri olarak yorumlanmamalıdır olmamalıdır. Bir betikte bir API anahtarı ve uç nokta eklemek, hizmetinizi bu değerleri komut dosyası dışında okuyabilen herkese hizmet reddi saldırılarına karşı açar. Ücretsiz hizmette barındırılan dizinlerde, belki de öğrenme amacıyla JavaScript kullanımı güvenli olsa da, Java veya üretim kodundaki sorgu işlemleri için Java veya C# sorgulama işlemlerini kullanmanızı öneririz. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>NYCJobs 'i hizmetinizde çalışacak şekilde yapılandırma

Bu aşamada, barındırılan NYCJobs demo dizinini kullanıyorsunuz. Dizin dahil olmak üzere tüm kodun tam görünürlüğünü isterseniz, kendi arama hizmetinize dizin oluşturup yüklemek için bu yönergeleri izleyin.

1. Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu örnek için ücretsiz bir hizmet kullanabilirsiniz. 

   > [!Note]
   > Ücretsiz Azure Bilişsel Arama hizmeti kullanıyorsanız, üç dizin ile sınırlı olursunuz. NYCJobs veri yükleyici iki dizin oluşturur. Hizmetinizde yeni dizinleri kabul edecek kadar yer olduğundan emin olun.

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) örnek kodunu indirin.

1. NYCJobs örnek kodunun DataLoader klasöründe, Visual Studio 'da **Dataloader. sln** ' yi açın.

1. Azure Bilişsel Arama hizmetinize yönelik bağlantı bilgilerini ekleyin. DataLoader projesi içinde App. config dosyasını açın ve TargetSearchServiceName ve TargetSearchServiceApiKey appSettings 'i Azure Bilişsel Arama hizmetinizi ve Azure Bilişsel Arama hizmeti API anahtarınızı yansıtacak şekilde değiştirin. Bu bilgiler Azure portal bulunabilir.

1. Uygulamayı başlatmak için F5 tuşuna basın, iki dizin oluşturun ve NYCJob örnek verilerini içeri aktarın.

1. **AutocompleteTutorial. sln** dosyasını açın ve **AutocompleteTutorial** projesinde Web. config dosyasını düzenleyin. SearchServiceName ve SearchServiceApiKey değerlerini, arama hizmetiniz için geçerli olan değerlerle değiştirin.

1. Uygulamayı çalıştırmak için F5'e basın. Örnek Web uygulaması varsayılan tarayıcıda açılır. Deneyim, korumalı alan sürümü ile aynıdır; yalnızca dizin ve veriler hizmetinizde barındırılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnek, otomatik tamamlama ve önerileri destekleyen bir arama kutusu oluşturmaya yönelik temel adımları gösterir. ASP.NET MVC uygulamasını nasıl geliştirebileceğimiz ve önerileri almak için Azure Bilişsel Arama .NET SDK veya REST API nasıl kullanabileceğinizi gördünüz.

Bir sonraki adım olarak, önerileri tümleştirmeye ve arama deneyiminizle otomatik tamamlamayı deneyin. Aşağıdaki başvuru makaleleri yardımcı olmalıdır.

> [!div class="nextstepaction"]
> [Autocomplete REST API'si](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API'si](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Create Index REST API'sindeki modeller dizini özniteliği](https://docs.microsoft.com/rest/api/searchservice/create-index)

