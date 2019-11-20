---
title: 'Öğretici: Tek sayfalı Bing Video Arama uygulaması oluşturma'
titleSuffix: Azure Cognitive Services
description: Bing Video Arama API'sinin tek sayfalı bir Web uygulamasında kullanılmasını açıklar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: d2cd3d37801fc1a42a9bcbd5f70a6a55e78aaf08
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500068"
---
# <a name="tutorial-single-page-video-search-app"></a>Öğretici: Tek sayfalı Video Arama uygulaması
Bing Video Arama API'si Web'de arama yapmanızı ve arama sorgusuna uyan video sonuçları almanızı sağlar. Bu öğreticide, Bing arama API'sini kullanarak sayfada arama sonuçlarını görüntüleyen tek sayfalı bir Web uygulaması oluşturuyoruz. Uygulama HTML, CSS ve JavaScript bileşenlerini içeriyor.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> Sayfanın en altındaki JSON ve HTTP başlıklarına tıklandığında, JSON yanıt ve HTTP istek bilgileri gösterilir. Bu ayrıntılar hizmeti keşfederken yararlı olabilir.

![JSON, HTTP ham sonuçları](./media/json-http-raw-results.png)

Bu öğretici uygulaması şunları gösteriyor:
> [!div class="checklist"]
> * JavaScript'te Bing Video Arama API'sine çağrı yapma
> * Arama seçeneklerini Bing Arama API'sine geçirme
> * Video arama sonuçlarını görüntüleme ya da isteğe bağlı olarak Web sayfaları, haberler veya resimler ekleme
> * 24 saatlik, geçen haftaya, aya veya tüm kullanılabilir zamana ait zaman çerçevelerinde arama yapma
> * Arama sonuçları sayfalarında gezinme
> * Bing istemci kimliğini ve API abonelik anahtarını işleme
> * Oluşabilecek hataları işleme

Öğretici sayfası tamamen bağımsızdır; dışarıdan hiçbir kare, stil sayfası veya resim dosyası kullanmaz. Yalnızca yaygın olarak desteklenen JavaScript dilinin özelliklerini kullanır ve tüm önemli Web tarayıcılarının geçerli sürümlerinde çalışır.

Bu öğreticide, kaynak kodun seçilen bölümlerini açıklıyoruz. [Kaynak kodun](tutorial-bing-video-search-single-page-app-source.md) tamamı sağlanır. Örneği çalıştırmak için, kaynak kodu kopyalayıp bir metin düzenleyiciye yapıştırın ve `bing.html` olarak kaydedin.

## <a name="app-components"></a>Uygulama bileşenleri
Tüm tek sayfalı Web uygulamaları gibi bu öğreticinin uygulaması da üç bölümden oluşur:

> [!div class="checklist"]
> * HTML - Sayfanın yapısını ve içeriğini tanımlar
> * CSS - Sayfanın görünümünü tanımlar
> * JavaScript - Sayfanın davranışını tanımlar

HTML ile CSS'nin büyük bölümü bilinen şeylerdir, dolayısıyla öğreticide bunlar açıklanmayacak. HTML, kullanıcının sorgu girdiği ve arama seçeneklerini belirttiği bir arama formu içerir. Form, `<form>` etiketinin `onsubmit` özniteliğini kullanarak aramayı yapan JavaScript'e bağlanır:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
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

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
Aşağıdaki şekilde sorgu metin kutusu ile aramayı tanımlayan seçenekler gösterilir.

![Bing Haber Arama seçenekleri](media/video-search-options.png)

HTML formu, adları aşağıda gösterilen öğeleri içerir:

|Öğe|Açıklama|
|-|-|
| `where` | Aramada kullanılan pazarı (konum ve dil) seçmek için açılan menü. |
| `query` | Arama terimlerinin girileceği metin alanı. |
| `modules` | Belirli sonuç modüllerini, tüm sonuçları veya ilgili videoları yükseltmek için onay kutuları. |
| `when` | Aramayı isteğe bağlı olarak en son günle, haftayla veya ayla sınırlamak için açılan menü. |
| `safe` | "Yetişkinlere yönelik" sonuçları filtrelemek için Bing Güvenli Arama özelliğinin kullanılıp kullanılmayacağını belirten onay kutusu. |
| `count` | Gizli alan. Her istekte döndürülecek arama sonuçlarının sayısı. Sayfada daha az veya daha fazla sonuç görüntülemek için bunu değiştirin. |
| `offset`|  Gizli alan. İstekteki ilk arama sonucunun göreli konumu; sayfalama için kullanılır. Yeni istekte `0` değerine sıfırlanır. |

> [!NOTE]
> Bing Web Araması başka sorgu parametreleri de sunar. Biz yalnızca birkaç tanesini kullanıyoruz.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Örneğin, gerçek API çağrısındaki `SafeSearch` parametresi `strict`, `moderate` veya `off` olabilir; varsayılan değer `moderate` ayarıdır. Öte yandan, formumuzda yalnızca iki durumu olan bir onay kutusu kullanılıyor. JavaScript kodu bu ayarı `strict` veya `off` ayarına dönüştürür (`moderate` kullanılmaz).

## <a name="performing-the-request"></a>İsteği gerçekleştirme
Sorgu, seçenekler dizesi ve API anahtarı verili durumdayken, `BingWebSearch` işlevi Bing Arama uç noktasına isteği yöneltmek için bir `XMLHttpRequest` nesnesi kullanır.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
HTTP isteği başarıyla tamamlanınca, API'ye yönelik başarılı HTTP GET isteğini işlemek için JavaScript `load` olay işleyicisini çağırır (`handleOnLoad()`). 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Arama işleminde hata oluşursa, Bing Haber Arama API'si 200 olmayan bir HTTP durum kodu döndürür ve JSON yanıtına hata bilgilerini ekler. Buna ek olarak, istekte hız sınırlaması varsa API boş yanıt döndürür.
Başarılı bir HTTP isteği, aramanın kendisinin başarılı olduğu anlamına *gelmeyebilir*. 

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

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Arama sonuçları JSON yanıtında en üst düzey `value` nesnesi olarak döndürülür. Bunları `renderResultsItems()` işlevimize geçiririz. Bu işlev sonuçları tekrarlar ve her öğeyi HTML olarak işlemek için bir işlev çağırır. Sonuçta elde edilen HTML `renderSearchResults()` işlevine döndürülür ve burada sayfadaki `results` bölümüne eklenir.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
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

JavaScript kodunda `searchItemRenderers` nesnesi, her tür arama sonucu için HTML oluşturan *renderers:* işlevlerini içerebilir. Video arama sayfasında yalnızca `videos` kullanılır. Çeşitli işleyici türleri için diğer öğreticilere bakın.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
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

Aşağıdaki JavaScript alıntısında `video` işleyicisi gösterilir. Videos uç noktası kullanıldığında, tüm sonuçlar `Videos` türünde olur. Aşağıdaki kod segmentinde `searchItemRenderers` gösterilir.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

İşleyici işlevi:
> [!div class="checklist"]
> * Paragraf etiketi oluşturur, bu etiketi `images` sınıfına atar ve html dizisine gönderir.
> * Resmin küçük resim boyutunu hesaplar (genişlik 60 piksele sabitlenir, yükseklik buna orantılı olarak hesaplanır).
> * Resmin küçük resmini görüntülemek için HTML `<img>` etiketini oluşturur. 
> * Görüntüye ve bu görüntüyü içeren sayfaya bağlanan HTML `<a>` etiketlerini oluşturur.
> * Resim ve bu resmin bulunduğu site hakkındaki bilgileri görüntüleyen bir açıklama oluşturur.

Küçük resim boyutu hem `<img>` etiketinde hem de küçük resmin URL'sindeki `h` ve `w` alanlarında kullanılır. Bing, tam olarak bu boyutun [küçük bir resmini](../bing-web-search/resize-and-crop-thumbnails.md) döndürür.

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
> [Bing Video Arama API'si başvurusu](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
