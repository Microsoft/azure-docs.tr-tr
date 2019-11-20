---
title: "Öğretici: Bing Haber Arama API'si kullanarak tek sayfalı bir Web uygulaması oluşturma"
titleSuffix: Azure Cognitive Services
description: Bing Haberler API 'sine arama sorguları gönderebilen tek sayfalı bir Web uygulaması oluşturmak ve sonuçları Web sayfası içinde görüntülemek için bu öğreticiyi kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 424fdc9fa0f31b3de664945ff49b119939488fed
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423611"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Öğretici: Tek sayfalı web uygulaması oluşturma

Bing Haber Arama API'si Web'de arama yapmanızı ve arama sorgusuna uyan haber türündeki sonuçları almanızı sağlar. Bu öğreticide, Bing Haber Arama API'sini kullanarak sayfada arama sonuçlarını görüntüleyen tek sayfalı bir Web uygulaması oluşturuyoruz. Uygulama HTML, CSS ve JavaScript bileşenlerini içeriyor. Bu örneğin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html)’da mevcuttur.

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Sayfanın en altındaki JSON ve HTTP başlıklarına tıklandığında, JSON yanıt ve HTTP istek bilgileri gösterilir. Bu ayrıntılar hizmeti keşfederken yararlı olabilir.

Öğretici uygulamasında aşağıdakilerin nasıl yapılacağı gösterilmektedir:
> [!div class="checklist"]
> * JavaScript'te Bing Haber Arama API'sine çağrı yapma
> * Arama seçeneklerini Bing Haber API'sine geçirme
> * Dört kategoriye ait haber araması sonuçlarını görüntüleme: 24 saat, son bir hafta, ay veya tüm zamanlar için herhangi bir tür, iş dünyası, sağlık veya siyaset haberleri
> * Arama sonuçları sayfalarında gezinme
> * Bing istemci kimliğini ve API abonelik anahtarını işleme
> * Oluşabilecek hataları işleme

Öğretici sayfası tamamen bağımsızdır; dışarıdan hiçbir kare, stil sayfası veya resim dosyası kullanmaz. Yalnızca yaygın olarak desteklenen JavaScript dilinin özelliklerini kullanır ve tüm önemli Web tarayıcılarının geçerli sürümlerinde çalışır.

## <a name="app-components"></a>Uygulama bileşenleri
Tüm tek sayfalı Web uygulamaları gibi bu öğreticinin uygulaması da üç bölümden oluşur:

> [!div class="checklist"]
> * HTML - Sayfanın yapısını ve içeriğini tanımlar
> * CSS - Sayfanın görünümünü tanımlar
> * JavaScript - Sayfanın davranışını tanımlar

HTML ile CSS'nin büyük bölümü bilinen şeylerdir, dolayısıyla öğreticide bunlar açıklanmayacak. HTML, kullanıcının sorgu girdiği ve arama seçeneklerini belirttiği bir arama formu içerir. Form, `<form>` etiketinin `onsubmit` özniteliğini kullanarak aramayı gerçekleştiren JavaScript'e bağlanır:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
`onsubmit` işleyicisi, formun sunucuya gönderilmesini engelleyen `false` değerini döndürür. JavaScript kodu, formdan gerekli bilgileri toplama ve aramayı gerçekleştirme çalışmalarını yapar.

HTML, arama sonuçlarının gösterildiği bölümleri de (HTML `<div>` etiketleri) içerir.

## <a name="managing-subscription-key"></a>Abonelik anahtarını yönetme

Bing Arama API'si abonelik anahtarını koda eklemek zorunda kalmamak için, tarayıcının kalıcı depolamasını kullanarak anahtarı depolarız. Anahtar depolanmadan önce, kullanıcıdan anahtarı isteriz. Anahtar daha sonra API tarafından reddedilirse, depolanan anahtarı geçersiz kılarız. Böylelikle kullanıcıdan yeniden anahtar istenir.

`localStorage` nesnesini (tüm tarayıcılar bunu desteklemez) veya bir tanımlama bilgisi kullanan `storeValue` ve `retrieveValue` işlevlerini tanımlarız. `getSubscriptionKey()` işlevi, bu işlevleri kullanarak kullanıcının anahtarını depolar ve alır.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
HTML `<form>` etiketi `onsubmit` arama sonuçlarını döndürmek için `bingWebSearch` işlevini çağırır. `bingWebSearch`, her sorgunun kimliğini doğrulamak için `getSubscriptionKey()` kullanır. Önceki tanımda gösterildiği gibi, anahtar girilmediyse `getSubscriptionKey` kullanıcıdan anahtarı ister. Ardından anahtar uygulama tarafından sürekli kullanılmak üzere depolanır.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Arama seçeneklerini belirtme
Aşağıdaki şekilde sorgu metin kutusu ile bursla ilgili haberler için bir arama tanımlayan seçenekler gösterilir.

![Bing Haber Arama seçenekleri](media/news-search-categories.png)

HTML formu, adları aşağıda gösterilen öğeleri içerir:

|Öğe|Açıklama|
|-|-|
| `where` | Aramada kullanılan pazarı (konum ve dil) seçmek için açılan menü. |
| `query` | Arama terimlerinin girileceği metin alanı. |
| `category` | Belirli sonuç türlerini öne çıkarmak için onay kutuları. Örneğin Sağlık türünün öne çıkarılması sağlık haberlerinin daha üst sıralarda görüntülenmesini sağlar. |
| `when` | Aramayı isteğe bağlı olarak en son günle, haftayla veya ayla sınırlamak için açılan menü. |
| `safe` | "Yetişkinlere yönelik" sonuçları filtrelemek için Bing Güvenli Arama özelliğinin kullanılıp kullanılmayacağını belirten onay kutusu. |
| `count` | Gizli alan. Her istekte döndürülecek arama sonuçlarının sayısı. Sayfada daha az veya daha fazla sonuç görüntülemek için bunu değiştirin. |
| `offset`|  Gizli alan. İstekteki ilk arama sonucunun göreli konumu; sayfalama için kullanılır. Yeni istekte `0` değerine sıfırlanır. |

> [!NOTE]
> Bing Web Araması başka sorgu parametreleri de sunar. Biz yalnızca birkaç tanesini kullanıyoruz.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Örneğin, gerçek API çağrısındaki `SafeSearch` parametresi `strict`, `moderate` veya `off` olabilir; varsayılan değer `moderate` ayarıdır. Öte yandan, formumuzda yalnızca iki durumu olan bir onay kutusu kullanılıyor. JavaScript kodu bu ayarı `strict` veya `off` ayarına dönüştürür (`moderate` kullanılmaz).

## <a name="performing-the-request"></a>İsteği gerçekleştirme
Sorgu, seçenekler dizesi ve API anahtarı verili durumdayken, `BingNewsSearch` işlevi Bing Haber Arama uç noktasına isteği yöneltmek için bir `XMLHttpRequest` nesnesi kullanır.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
HTTP isteği başarıyla tamamlanınca, API'ye yönelik başarılı HTTP GET isteğini işlemek için JavaScript `load` olay işleyicisi olan `handleBingResponse()` işlevini çağırır. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "News") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Başarılı bir HTTP isteği, aramanın kendisinin başarılı olduğu anlamına *gelmeyebilir*. Arama işleminde hata oluşursa, Bing Haber Arama API'si 200 olmayan bir HTTP durum kodu döndürür ve JSON yanıtına hata bilgilerini ekler. Buna ek olarak, istekte hız sınırlaması varsa API boş yanıt döndürür.

Önceki işlevlerin ikisinde de kodun büyük bölümü hata işlemeye ayrılmıştır. Şu aşamalarda hata oluşabilir:

|Aşama|Olası hatalar|İşleyen|
|-|-|-|
|JavaScript istek nesnesi oluşturma|Geçersiz URL|`try`/`catch` bloğu|
|İstekte bulunma|Ağ hataları, durdurulan bağlantılar|`error` ve `abort` olay işleyicileri|
|Aramayı gerçekleştirme|Geçersiz istek, geçersiz JSON, hız sınırları|`load` olay işleyicisindeki testler|

Hatalar, hata hakkındaki bilinen tüm ayrıntılarla birlikte `renderErrorMessage()` çağrılarak işlenir. Yanıt tüm hata testlerinden geçerse, arama sonuçlarını sayfada görüntülemek için `renderSearchResults()` işlevini çağırırız.

## <a name="displaying-search-results"></a>Arama sonuçlarını görüntüleme
Arama sonuçlarını görüntülemeye yönelik ana işlev `renderSearchResults()` işlevidir. Bu işlev, Bing Haber Arama hizmeti tarafından döndürülen JSON'u alır, sonra da haber sonuçlarını ve varsa ilgili aramaları işler.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Ana arama sonuçları JSON yanıtında en üst düzey `value` nesnesi olarak döndürülür. Bunları `renderResults()` işlevimize geçiririz. Bu işlev sonuçları tekrarlar ve her öğeyi HTML olarak işlemek için ayrı bir işlev çağırır. Sonuçta elde edilen HTML `renderSearchResults()` işlevine döndürülür ve burada sayfadaki `results` bölümüne eklenir.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Bing Haber Arama API'si, her biri kendi üst düzey nesnesinin içinde olmak üzere en çok dört farklı türde ilgili sonuç döndürür. Bunlar:

|İlişki|Açıklama|
|-|-|
|`pivotSuggestions`|Özgün aramadaki pivot sözcüğü, farklı biriyle değiştiren sorgular. Örneğin, "kırmızı çiçekler" araması yaparsanız pivot sözcüğü "kırmızı" ve pivot öneri de "sarı çiçekler" olabilir.|
|`queryExpansions`|Daha fazla terim ekleyerek özgün aramayı daraltan sorgular. Örneğin, "Microsoft Surface" araması yaparsanız genişletilmiş sorgu "Microsoft Surface Pro" olabilir.|
|`relatedSearches`|Özgün aramayı giren diğer kullanıcılar tarafından da girilmiş olan sorgular. Örneğin, "Mount Rainier" araması yaparsanız, ilgili arama "Mt. Saint Helens" olabilir.|
|`similarTerms`|Özgün aramayla benzer anlamı olan sorgular. Örneğin, "okullar" araması yaparsanız benzer bir terim "eğitim" olabilir.|

`renderSearchResults()` için daha önce gördüğümüz gibi, yalnızca `relatedItems` önerilerini işleriz ve sonuçta elde edilen bağlantıları sayfanın kenar çubuğuna yerleştiririz.

## <a name="rendering-result-items"></a>Sonuç öğelerini işleme

JavaScript kodunda `searchItemRenderers` nesnesi, her tür arama sonucu için HTML oluşturan *renderers:* işlevlerini içerir.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
İşleyici işlevi aşağıdaki parametreleri kabul edebilir:

|Parametre|Açıklama|
|-|-|
|`item`| Öğenin özelliklerini, örneğin URL'sini ve açıklamasını içeren JavaScript nesnesi.|
|`index`| Kendi koleksiyonu içindeki arama öğesinin dizini.|
|`count`| Arama sonucu öğesinin koleksiyonundaki öğelerin sayısı.|

Sonuçları saymak, koleksiyonun başlangıcı veya sonuna özel HTML oluşturmak ve belirli sayıdaki öğeden sonra satır sonu eklemek gibi işlemler için `index` ve `count` parametreleri kullanılabilir. İşleyicinin bu işleve ihtiyacı yoksa, bu iki parametreyi kabul etmesi gerekmez.

Aşağıdaki JavaScript alıntısında `news` işleyicisi gösterilir:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Haber işleyici işlevi:
> [!div class="checklist"]
> * Paragraf etiketi oluşturur, bu etiketi `news` sınıfına atar ve html dizisine gönderir.
> * Resmin küçük resim boyutunu hesaplar (genişlik 60 piksele sabitlenir, yükseklik buna orantılı olarak hesaplanır).
> * Resmin küçük resmini görüntülemek için HTML `<img>` etiketini oluşturur. 
> * Görüntüye ve bu görüntüyü içeren sayfaya bağlanan HTML `<a>` etiketlerini oluşturur.
> * Resim ve bu resmin bulunduğu site hakkındaki bilgileri görüntüleyen bir açıklama oluşturur.

Küçük resim boyutu hem `<img>` etiketinde hem de küçük resmin URL'sindeki `h` ve `w` alanlarında kullanılır. Ardından [Bing küçük resim hizmeti](../bing-web-search/resize-and-crop-thumbnails.md) tam olarak bu boyutta bir küçük resim verir.

## <a name="persisting-client-id"></a>Kalıcı istemci kimliği
Bing arama API'lerinden gelen yanıtlar, başarılı isteklerle birlikte API'ye geri gönderilmesi gereken bir `X-MSEdge-ClientID` üst bilgisi içerir. Birden çok Bing Arama API'si kullanılıyorsa, mümkün olduğunca bu API'lerin tümünde aynı istemci kimliği kullanılmalıdır.

Böylelikle `X-MSEdge-ClientID` üst bilgisi sayesinde Bing API'leri kullanıcının tüm aramalarını ilişkilendirebilir. Bunun iki önemli avantajı vardır.

İlk olarak, Bing arama alt yapısının geçmiş bağlamı aramalara uygulayarak kullanıcıya daha uygun sonuçlar bulabilmesini sağlar. Kullanıcı daha önce yelkencilikle ilgili terim aramaları yaptıysa, daha sonra yapılan "düğümler" araması tercihen yelkencilikte kullanılan düğümler hakkında bilgi döndürebilir.

İkincisi, Bing yeni özellikleri geniş ölçekte kullanıma sunmadan önce bunları denemesi için rastgele kullanıcılar seçebilir. Her istekle birlikte aynı istemci kimliğinin sağlanması, özelliği gören kullanıcıların bunu sürekli görebilmesini sağlar. İstemci kimliği olmadan, özellik kullanıcıya arama sonuçlarında rastgele gösterilebilir ve gösterilmeyebilir.

Tarayıcı güvenlik ilkeleri (CORS) `X-MSEdge-ClientID` üst bilgisinin JavaScript'in kullanımına sunulmasını engelleyebilir. Bu sınırlama, arama sonucunun kaynağı istekte bulunan sayfadan farklı olduğunda ortaya çıkar. Üretim ortamında, Web sayfasıyla aynı etki alanında API çağrısı yapan bir sunucu tarafı betiği barındırarak bu ilkeye uymalısınız. Betiğin kaynağı Web sayfasıyla aynı olduğundan, `X-MSEdge-ClientID` üst bilgisi JavaScript'in kullanımına sunulur.

> [!NOTE]
> Üretim ortamındaki bir Web uygulamasında, isteği sunucu tarafından gerçekleştirmeniz gerekir. Aksi takdirde, Bing Arama API'si anahtarınızın Web sayfasına eklenmesi gerekir ve bu durumda kaynağı görüntüleyen herkes tarafından görülebilir. API abonelik anahtarınız altında gerçekleştirilen tüm kullanım, yetkisiz tarafların yaptığı istekler bile size faturalandırılır; dolayısıyla anahtarınızı açıklamamanız önemlidir.

Geliştirme amacıyla, Bing Web Araması API’si isteğini CORS ara sunucusu aracılığıyla yapabilirsiniz. Bu tür bir ara sunucudan alınan yanıtın, `Access-Control-Expose-Headers` yanıt üst bilgilerine izin veren ve JavaScript için kullanılabilir hale getiren bir üstbilgisi vardır.

Öğretici uygulamamızın istemci kimliği üst bilgisine erişebilmesi için CORS ara sunucusu kolayca yüklenebilir. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Ardından komut penceresinde aşağıdaki komutu yürütün:

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Web Araması uç noktasını şöyle değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Bing Haber Arama API'si başvurusu](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)
