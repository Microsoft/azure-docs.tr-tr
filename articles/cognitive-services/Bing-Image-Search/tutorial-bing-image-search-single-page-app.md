---
title: "Öğretici: Bing resim arama API'si - tek sayfa web uygulaması oluşturma"
titleSuffix: Azure cognitive services
description: Bing Resim Arama API’si, web’de yüksek kaliteli, alakalı görüntüleri aramanıza olanak sağlar. API’ye arama sorguları gönderebilen ve sonuçları web sayfasında görüntüleyebilen tek sayfalı bir web uygulaması derlemek için bu öğreticiyi kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: 7b530b3d415761956cbdb45fdc92bfed55a1bae5
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868250"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Öğretici: Bing resim arama API'si kullanarak tek sayfalı uygulama oluşturma

Bing Resim Arama API’si, web’de yüksek kaliteli, alakalı görüntüleri aramanıza olanak sağlar. API’ye arama sorguları gönderebilen ve sonuçları web sayfasında görüntüleyebilen tek sayfalı bir web uygulaması derlemek için bu öğreticiyi kullanın. Bu öğretici, Bing Web Araması’nın [ilgili öğreticisine](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) benzerdir.

Öğretici uygulamasında aşağıdakilerin nasıl yapılacağı gösterilmektedir:

> [!div class="checklist"]
> * JavaScript’te Bing Resim Arama API’si çağrısı gerçekleştirme
> * Arama seçeneklerini kullanarak arama sonuçlarını iyileştirme
> * Arama sonuçlarını görüntüleme ve arama sonuçları sayfalarında gezinme
> * API abonelik anahtarı ve Bing istemci kimliği isteme ve işleme.

Bu öğreticinin tam kaynak kodu, [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search)’da mevcuttur.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/)’in en son sürümü.
* Node.js için [Express.js](https://expressjs.com/) çerçevesi. Yükleme yönergeleri için kaynak kodunu GitHub örnek readme dosyasında kullanılabilir.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Kullanıcı abonelik anahtarlarını yönetme ve depolama

Bu uygulama, API abonelik anahtarlarını depolamak için web tarayıcılarının kalıcı depolama alanını kullanır. Herhangi bir anahtar depolanmıyorsa web sayfası, kullanıcıdan anahtarını belirtmesini ve daha sonra kullanmak üzere anahtarını depolamasını ister. Anahtar daha sonra API tarafından reddedilirse uygulama bunu depolama alanından kaldırır.


`localStorage` nesnesini (tarayıcı destekliyorsa) veya bir tanımlama bilgisini kullanmak için `storeValue` ve `retrieveValue` işlevlerini tanımlayın.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

`getSubscriptionKey()` işlevi, `retrieveValue` öğesini kullanarak önceden depolanan bir anahtarı almaya çalışır. Biri bulunamazsa, kullanıcıdan anahtarını belirtmesini ve `storeValue` öğesini kullanarak depolamasını ister.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

HTML `<form>` etiketi `onsubmit` arama sonuçlarını döndürmek için `bingWebSearch` işlevini çağırır. `bingWebSearch`, her sorgunun kimliğini doğrulamak için `getSubscriptionKey` kullanır. Önceki tanımda gösterildiği gibi, anahtar girilmediyse `getSubscriptionKey` kullanıcıdan anahtarı ister. Ardından anahtar uygulama tarafından sürekli kullanılmak üzere depolanır.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Arama istekleri gönderme

Bu uygulama, `newBingImageSearch()` çağrısı yapmak için `onsubmit` özniteliğini kullanarak başlangıçta kullanıcı arama istekleri göndermek için HTML `<form>` öğesini kullanır.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Varsayılan olarak `onsubmit` işleyicisi, formun gönderilmesini engelleyen `false` değerini döndürür.

## <a name="select-search-options"></a>Arama seçeneklerini belirleme

![[Bing Resim Arama formu]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Bing Resim Arama API’si, arama sonuçlarını daraltmak ve filtrelemek için birçok [filtre sorgusu parametresi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters) sunar. Bu uygulamadaki HTML formu, aşağıdaki parametre seçeneklerini kullanır ve görüntüler:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Aramada kullanılan pazarı (konum ve dil) seçmek için açılan menü.                                                                                             |
| `query`      | Arama terimlerinin girileceği metin alanı.                                                                                                                                 |
| `aspect`     | Bulunan görüntülerin oranlarını seçmek için kullanılan radyo düğmeleri: kabaca kare, geniş veya uzun.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Aramayı isteğe bağlı olarak en son günle, haftayla veya ayla sınırlamak için açılan menü.                                                                                          |
| `safe`       | "Yetişkinlere yönelik" sonuçları filtrelemek için Bing Güvenli Arama özelliğinin kullanılıp kullanılmayacağını belirten onay kutusu.                                                                                      |
| `count`      | Gizli alan. Her istekte döndürülecek arama sonuçlarının sayısı. Sayfada daha az veya daha fazla sonuç görüntülemek için bunu değiştirin.                                                            |
| `offset`     | Gizli alan. İstekteki ilk arama sonucunun göreli konumu; sayfalama için kullanılır. Yeni istekte `0` değerine sıfırlanır.                                                           |
| `nextoffset` | Gizli alan. Arama sonucu alındıktan sonra bu alan, yanıttaki `nextOffset` değerine ayarlanır. Bu alan kullanıldığında, ardışık sayfalarda sonuçların çakışması engellenir. |
| `stack`      | Gizli alan. Önceki sayfalara geri dönmek için, arama sonuçlarının önceki sayfalarının göreli konumlarının JSON olarak kodlanmış bir listesi.                                                      |

`bingSearchOptions()` işlevi, bu seçenekleri kısmi bir sorgu dizesi olarak biçimlendirir ve bu dize de uygulamanın API isteklerinde kullanılabilir.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>İsteği gerçekleştirme

`BingImageSearch()` işlevi, arama sorgusunu, seçenekler dizesini ve API anahtarını kullanarak, Bing Resim Arama uç noktasına istekte bulunmak için bir XMLHttpRequest nesnesi kullanır.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

HTTP isteği başarıyla tamamlanınca JavaScript, başarılı bir HTTP GET isteğini işlemek için "yük" olay işleyicisini (`handleBingResponse()`) çağırır.

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> Başarılı HTTP istekleri, başarısız arama bilgilerini içerebilir. Arama işlemi sırasında bir hata oluşursa, Bing Resim Arama API’si, JSON yanıtında hata bilgilerini ve 200 olmayan bir HTTP durum kodunu döndürür. Buna ek olarak, istekte hız sınırlaması varsa API boş yanıt döndürür.

## <a name="display-the-search-results"></a>Arama sonuçlarını görüntüleme

Arama sonuçları, `renderSearchResults()` işlevi tarafından görüntülenir. BU işlev, Bing Resim Arama hizmeti tarafından döndürülen JSON’ı alır ve döndürülen görüntülerde ve ilgili aramalarda uygun bir işlenen işlevi çağırır.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Görüntü arama sonuçları, JSON yanıtında üst düzey `value` nesnesinde bulunur. Bunlar `renderImageResults()` öğesine geçirilir ve burada sonuçlar aracılığıyla yineleme yapıp her bir öğeyi HTML’e dönüştürür.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Bing Resim Arama API’si, kullanıcıların arama deneyimlerine yol göstermesi için dört tür arama önerisi döndürebilir; bunların her biri kendi üst düzey nesnesinde yer alır:

| Öneri         | Açıklama                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Özgün aramadaki pivot sözcüğü, farklı biriyle değiştiren sorgular. Örneğin, "kırmızı çiçekler" araması yaparsanız pivot sözcüğü "kırmızı" ve pivot öneri de "sarı çiçekler" olabilir. |
| `queryExpansions`  | Daha fazla terim ekleyerek özgün aramayı daraltan sorgular. Örneğin, "Microsoft Surface" araması yaparsanız genişletilmiş sorgu "Microsoft Surface Pro" olabilir.                                   |
| `relatedSearches`  | Özgün aramayı giren diğer kullanıcılar tarafından da girilmiş olan sorgular. Örneğin, "Mount Rainier" araması yaparsanız, ilgili arama "Mt. Saint Helens" olabilir.                       |
| `similarTerms`     | Özgün aramaya yönelik benzer anlamı olan sorgular. Örneğin, "yavru kedi" araması yaparsanız benzer bir terim de "şirin" olabilir.                                                                   |

Bu uygulama yalnızca `relatedItems` önerilerini işler ve sonuçta elde edilen bağlantıları sayfanın kenar çubuğuna yerleştirir.

## <a name="rendering-search-results"></a>Arama sonuçlarını işleme

Bu uygulamada `searchItemRenderers` nesnesi, her arama sonucu türü için HTML oluşturan işleyici işlevlerini içerir.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Bu işleyici işlevleri aşağıdaki parametreleri kabul eder:

| Parametre         | Açıklama                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Öğenin özelliklerini, örneğin URL'sini ve açıklamasını içeren JavaScript nesnesi. |
| `index` | Kendi koleksiyonu içindeki arama öğesinin dizini.                                          |
| `count` | Arama sonucu öğesinin koleksiyonundaki öğelerin sayısı.                                  |

`index` ve `count` parametreleri, sonuçları numaralandırmak, koleksiyonlara yönelik HTML oluşturmak ve içeriği düzenlemek için kullanılır. Özellikle:

* Görüntünün küçük resim boyutunu hesaplar (genişlik, minimum 120 piksel olacak şekilde değişiklik gösterir, yükseklik ise 90 piksel’e sabittir).
* Görüntünün küçük resmini görüntülemek için HTML `<img>` etiketini oluşturur.
* Görüntüye ve bu görüntüyü içeren sayfaya bağlanan HTML `<a>` etiketlerini oluşturur.
* Görüntü ve bu görüntünün bulunduğu site hakkındaki bilgileri görüntüleyen bir açıklama oluşturur.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Küçük resim görüntüsünün `height` ve `width` öğesi, hem `<img>` etiketinde hem de küçük resim URL’sindeki `h` ve `w` alanlarında kullanılır. Böylece Bing’in tam olarak o boyutta [bir küçük resim](../bing-web-search/resize-and-crop-thumbnails.md) döndürmesi sağlanır.

## <a name="persisting-client-id"></a>Kalıcı istemci kimliği

Bing arama API’lerinden gelen yanıtlar, başarılı isteklerle birlikte API’ye geri gönderilmesi gereken bir `X-MSEdge-ClientID` üst bilgisi içerir. Birden çok Bing Arama API’si kullanılıyorsa, mümkün olduğunca bu API’lerin tümünde aynı istemci kimliği kullanılmalıdır.

Böylelikle `X-MSEdge-ClientID` üst bilgisi sayesinde Bing API’leri kullanıcının tüm aramalarını ilişkilendirebilir. Bunun önemli avantajı vardır

İlk olarak, Bing arama alt yapısının geçmiş bağlamı aramalara uygulayarak kullanıcıya daha uygun sonuçlar bulabilmesini sağlar. Kullanıcı daha önce yelkencilikle ilgili terim aramaları yaptıysa, daha sonra yapılan "düğümler" araması tercihen yelkencilikte kullanılan düğümler hakkında bilgi döndürebilir.

İkincisi, Bing yeni özellikleri geniş ölçekte kullanıma sunmadan önce bunları denemesi için rastgele kullanıcılar seçebilir. Her istekle birlikte aynı istemci kimliğinin sağlanması, özelliği görmesi tercih edilen kullanıcıların bunu sürekli görebilmesini sağlar. İstemci kimliği olmadan, özellik kullanıcıya arama sonuçlarında rastgele gösterilebilir ve gösterilmeyebilir.

Tarayıcı güvenlik ilkeleri (CORS) `X-MSEdge-ClientID` üst bilgisinin JavaScript'in kullanımına sunulmasını engelleyebilir. Bu sınırlama, arama sonucunun kaynağı istekte bulunan sayfadan farklı olduğunda ortaya çıkar. Üretim ortamında, Web sayfasıyla aynı etki alanında API çağrısı yapan bir sunucu tarafı betiği barındırarak bu ilkeye uymalısınız. Betiğin kaynağı Web sayfasıyla aynı olduğundan, `X-MSEdge-ClientID` üst bilgisi JavaScript'in kullanımına sunulur.

> [!NOTE]
> Üretim ortamındaki bir Web uygulamasında, isteği sunucu tarafından gerçekleştirmeniz gerekir. Aksi takdirde, Bing Arama API’si anahtarınızın Web sayfasına eklenmesi gerekir ve bu durumda kaynağı görüntüleyen herkes tarafından görülebilir. API abonelik anahtarınız altında gerçekleştirilen tüm kullanım, yetkisiz tarafların yaptığı istekler bile size faturalandırılır; dolayısıyla anahtarınızı açıklamamanız önemlidir.

Geliştirme amacıyla, Bing Web Araması API’si isteğini CORS ara sunucusu aracılığıyla yapabilirsiniz. Böyle bir ara sunucu yanıtı sahip bir `Access-Control-Expose-Headers` üst bilgisi yanıt üst bilgilerini sağlar ve JavaScript için kullanılabilir hale getirir.

Öğretici uygulamamızın istemci kimliği üst bilgisine erişebilmesi için CORS ara sunucusu kolayca yüklenebilir. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Ardından komut penceresinde aşağıdaki komutu yürütün:

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Web Araması uç noktasını şöyle değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Resim Arama API’si kullanarak görüntü ayrıntılarını ayıklama](tutorial-image-post.md)

## <a name="see-also"></a>Ayrıca bkz.

* [Bing Resim Arama API’si başvurusu](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
