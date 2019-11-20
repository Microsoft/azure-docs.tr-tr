---
title: 'Öğretici: Tek sayfalı Web uygulaması Bing Varlık Arama'
titleSuffix: Azure Cognitive Services
description: Bing Varlık Arama API'sinin tek sayfalı bir Web uygulamasında kullanılmasını gösterir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 5a8276f06207eb69ffec0e21c6d92794973f3b83
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423980"
---
# <a name="tutorial-single-page-web-app"></a>Öğretici: Tek sayfalı web uygulaması

Bing Varlık Arama API’si, *varlıklar* ve *yerler* hakkındaki bilgiler için Web araması yapmanızı sağlar. Belirli bir sorguda bir sonuç türünü veya her ikisini de isteyebilirsiniz. Yerlerin ve varlıkların tanımları aşağıda sağlanmıştır.

|||
|-|-|
|Varlıklar|Ada göre bulduğunuz tanınmış kişiler, yerler ve nesneler|
|Yerler|Ada *veya* türe (İtalyan restoranları) bulduğunuz restoranlar, oteller ve diğer yerel işletmeler|

Bu öğreticide, Bing Varlık Arama API'sini kullanarak sayfada arama sonuçlarını görüntüleyen tek sayfalı bir Web uygulaması oluşturuyoruz. Uygulama HTML, CSS ve JavaScript bileşenlerini içeriyor.

API, sonuçları konuma göre önceliklendirmenize izin verir. Bir mobil uygulamada cihazdan kendi konumunu isteyebilirsiniz. Bir web uygulamasında `getPosition()` işlevini kullanabilirsiniz. Ancak bu çağrı yalnızca güvenli bağlamlarda çalışır ve kesin bir konum sağlamayabilir. Ayrıca, kullanıcı kendi konumlarının dışındaki konumun yakınında varlık araması yapabilir.

Uygulamamız, kullanıcı tarafından girilen bir konumdan enlem ve boylamı almak için Bing Haritalar hizmetine çağrı yapar. Kullanıcı simgesel bir yapının adını (“Space Needle”) veya tam ya da kısmi bir adres (“New York City”) girdiğinde Bing Haritalar API’si koordinatları sağlar.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Sayfanın en altındaki JSON ve HTTP başlıklarına tıklandığında, JSON yanıt ve HTTP istek bilgileri gösterilir. Bu ayrıntılar hizmeti keşfederken yararlıdır.

Öğretici uygulamasında aşağıdakilerin nasıl yapılacağı gösterilmektedir:

> [!div class="checklist"]
> * JavaScript'te Bing Varlık Arama API'sine çağrı yapma
> * JavaScript'te Bing Haritalar `locationQuery` API'sine çağrı yapma
> * Arama seçeneklerini API çağrılarına iletme
> * Arama sonuçlarını görüntüleme
> * Bing istemci kimliğini ve API abonelik anahtarlarını işleme
> * Oluşabilecek hataları işleme

Öğretici sayfası tamamen bağımsızdır; dışarıdan hiçbir kare, stil sayfası veya hatta resim dosyası kullanmaz. Yalnızca yaygın olarak desteklenen JavaScript dilinin özelliklerini kullanır ve tüm önemli Web tarayıcılarının geçerli sürümlerinde çalışır.

Bu öğreticide, kaynak kodun yalnızca seçilen bölümlerini açıklıyoruz. Tam kaynak kodu [ayrı bir sayfada](tutorial-bing-entities-search-single-page-app-source.md) yer almaktadır. Bu kodu bir metin düzenleyicisine yapıştırın ve `bing.html` olarak kaydedin.

> [!NOTE]
> Bu öğretici, [tek sayfalı Bing Web Araması uygulaması öğreticisine](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) büyük ölçüde benzer, ancak yalnızca varlık araması sonuçlarını ele alır.

## <a name="app-components"></a>Uygulama bileşenleri

Tüm tek sayfalı Web uygulamaları gibi öğreticinin uygulaması da üç bölümden oluşur:

> [!div class="checklist"]
> * HTML - Sayfanın yapısını ve içeriğini tanımlar
> * CSS - Sayfanın görünümünü tanımlar
> * JavaScript - Sayfanın davranışını tanımlar

HTML veya CSS çok oldukça anlaşılır olduğundan bu çoğu kısımları bu öğreticide ele alınmamıştır.

HTML, kullanıcının sorgu girdiği ve arama seçeneklerini belirttiği bir arama formu içerir. Form, `<form>` etiketinin `onsubmit` özniteliğini kullanarak aramayı gerçekleştiren JavaScript'e bağlanır:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit` işleyicisi, formun sunucuya gönderilmesini engelleyen `false` değerini döndürür. JavaScript kodu, formdan gerekli bilgileri toplama ve aramayı gerçekleştirme çalışmalarını yapar.

Arama iki aşamada gerçekleştirilir. İlk olarak, kullanıcı bir konum kısıtlaması girerse, bunu koordinatlara dönüştürmek için bir Bings Haritalar sorgusu yapılır. Bu sorguya yönelik geri arama, Bing Varlık Araması sorgusunu başlatır.

HTML, arama sonuçlarının gösterildiği bölümleri de (HTML `<div>` etiketleri) içerir.

## <a name="managing-subscription-keys"></a>Abonelik anahtarlarını yönetme

> [!NOTE]
> Bu uygulama, hem Bing Arama API’si hem de Bing Haritalar API’si için abonelik anahtarlarını gerektirir. [deneme Bing Arama anahtarı](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ve [temel Bing Haritalar anahtarı](https://www.microsoft.com/maps/create-a-bing-maps-key) kullanabilirsiniz.

Bing Arama ve Bing Haritalar API'si abonelik anahtarlarını koda eklemek zorunda kalmamak için, bunları tarayıcının kalıcı depolamasını kullanarak depolarız. Anahtarların biri depolanmazsa bunu isteriz ve daha sonra kullanmak üzere depolarız. Anahtar daha sonra API tarafından reddedilirse, depolanan anahtarı geçersiz kılarız ve böylelikle kullanıcıdan sonraki aramasında anahtar istenir.

`localStorage` nesnesini (tarayıcı destekliyorsa) veya bir tanımlama bilgisi kullanan `storeValue` ve `retrieveValue` işlevlerini tanımlarız. `getSubscriptionKey()` işlevimiz, bu işlevleri kullanarak kullanıcının anahtarını depolar ve alır.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML `<body>` etiketi, sayfanın yüklenmesi tamamlandığında `getSearchSubscriptionKey()` ve `getMapsSubscriptionKey()` çağıran bir `onload` özniteliği içerir. Bu çağrılar, henüz girmediyse kullanıcıdan hemen anahtarlarını istemek için kullanılır.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Arama seçeneklerini belirtme

![[Bing Varlık Arama formu]](media/entity-search-spa-form.png)

HTML formu aşağıdaki denetimleri içerir:

| | |
|-|-|
|`where`|Aramada kullanılan pazarı (konum ve dil) seçmek için açılan menü.|
|`query`|Arama terimlerinin girildiği metin alanı.|
|`safe`|SafeSearch özelliğinin açık olup olmadığını gösteren bir onay kutusu ("yetişkin" sonuçlarını kısıtlar)|
|`what`|Varlıkları, yerleri veya her ikisini de arama tercihinin yapıldığı bir menü.|
|`mapquery`|Bing Varlık Aramanın daha ilgili sonuçlar döndürmesine yardımcı olmak için kullanıcının tam veya kısmi adres, simgesel yapı vb. girebileceği metin alanı.|

> [!NOTE]
> Yer sonuçları şu anda yalnızca Birleşik Devletler’de kullanılabilir. `where` ve `what` menülerinde bu kısıtlamayı uygulayan kod vardır. `what` menüsünde Yerler seçili durumdayken ABD dışı bir pazar seçerseniz, `what` Herhangi Bir Şey olarak değişir. `where` menüsünde ABD dışı bir pazar seçiliyken Yerler’i seçerseniz, `where` ABD olarak değişir.

`bingSearchOptions()` JavaScript işlevimiz bu alanları Bing Arama API’si için kısmi bir sorgu dizesine dönüştürür.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Örneğin, SafeSearch özelliği `strict`, `moderate` veya `off` olabilir (`moderate` varsayılan değerdir). Ancak, formumuzda yalnızca iki durumu olan bir onay kutusu kullanılıyor. JavaScript kodu bu ayarı `strict` veya `off` ayarına dönüştürür (`moderate` kullanılmaz).

`mapquery` alanı Bing Haritalar konum sorgusunda kullanılıp Bing Varlık Arama için kullanılmadığından `bingSearchOptions()` içerisinde işlenmez.

## <a name="obtaining-a-location"></a>Konum alma

Bing Haritalar API’si, kullanıcının girdiği konumun enlemini ve boylamını almak için kullandığımız [`locationQuery` yöntemini](//msdn.microsoft.com/library/ff701711.aspx) sunar. Bu koordinatlar daha sonra kullanıcının isteğiyle Bing Varlık Arama API’sine iletilir. Arama sonuçları, belirtilen konuma yakın olan varlıkları ve yerleri önceliklendirir.

Hizmet çıkış noktaları arası sorguları desteklemediğinden Web uygulamasında sıradan bir `XMLHttpRequest` kullanarak Bing Haritalar API’sine erişemeyiz. Neyse ki JSONP ("P" harfi "doldurulmuş" anlamı taşır) desteği sağlıyor. JSONP yanıtı, bir işlev çağrısına sarmalanmış sıradan bir JSON yanıtıdır. İstek, belgeye bir `<script>` etiketi eklenerek yapılır. (Betiklerin yüklenmesi tarayıcı güvenlik ilkelerine tabi değildir.)

`bingMapsLocate()` işlevi sorgu için `<script>` etiketini oluşturur ve ekler. Sorgu dizesinin `jsonp=bingMapsCallback` segmenti, yanıtla birlikte çağrılacak işlevin adını belirtir.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Bing Haritalar API’si yanıt vermezse `bingMapsCallBack()` işlevi asla çağrılmaz. Normalde, `bingEntitySearch()` öğesinin çağrılmadığı anlamına gelir ve varlık arama sonuçları görünmez. Bu senaryodan kaçınmak için `bingMapsLocate()`, beş saniye sonra `bingEntitySearch()` öğesine çağrı yapmak için bir zamanlayıcı da ayarlar. Geri arama işlevinde varlık aramayı iki kez gerçekleştirmeyi engelleme mantığı vardır.

Sorgular tamamlandığında `bingMapsCallback()` işlevi istenen şekilde çağrılır.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Bing Varlık Arama sorgusu enlem ve boylamın yanı sıra konum bilgilerinin kesinliğini belirten bir *yarıçap* gerektirir. Yarı çapı, Bing Haritalar yanıtında sağlanan *sınırlayıcı kutuyu* kullanarak hesaplarız. Sınırlayıcı kutu konumun tamamını çevreleyen bir dikdörtgendir. Örneğin, kullanıcı `NYC` girerse, sonuç New York City’nin yaklaşık olarak merkezi koordinatlarını ve şehri çevreleyen bir sınırlayıcı kutu içerir. 

Önce `haversineDistance()` işlevini kullanarak (gösterilmiyor) koordinatlardan sınırlayıcı kutunun dört köşesinin her birine olan uzaklıkları hesaplarız. Bu dört uzaklığın en büyüğünü yarıçap olarak kullanırız. En düşük yarıçap bir kilometredir. Bu değer, yanıtta sınırlayıcı kutu sağlanmaması durumunda varsayılan olarak da kullanılır.

Koordinatları ve yarıçapı elde ettikten sonra aramayı yapmak için `bingEntitySearch()` öğesine çağrı yaparız.

## <a name="performing-the-search"></a>Aramayı gerçekleştirme

Sorguyu, konumu, seçenekler dizesini ve API anahtarını dikkate alarak `BingEntitySearch()` işlevi Bing Varlık Arama isteğinde bulunur.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

HTTP isteği başarıyla tamamlanınca, API'ye yönelik başarılı HTTP GET isteğini işlemek için JavaScript `load` olay işleyicimiz olan `handleBingResponse()` işlevini çağırır. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Başarılı bir HTTP isteği, aramanın kendisinin başarılı olduğu anlamına *gelmeyebilir*. Arama işleminde hata oluşursa, Bing Varlık Arama API'si 200 olmayan bir HTTP durum kodu döndürür ve JSON yanıtına hata bilgilerini ekler. Buna ek olarak, istekte hız sınırlaması varsa API boş yanıt döndürür.

Önceki işlevlerin ikisinde de kodun büyük bölümü hata işlemeye ayrılmıştır. Şu aşamalarda hata oluşabilir:

|Aşama|Olası hatalar|İşleyen|
|-|-|-|
|JavaScript istek nesnesi oluşturma|Geçersiz URL|`try`/`catch` bloğu|
|İstekte bulunma|Ağ hataları, durdurulan bağlantılar|`error` ve `abort` olay işleyicileri|
|Aramayı gerçekleştirme|Geçersiz istek, geçersiz JSON, hız sınırları|`load` olay işleyicisindeki testler|

Hatalar, hata hakkındaki bilinen tüm ayrıntılarla birlikte `renderErrorMessage()` çağrılarak işlenir. Yanıt tüm hata testlerinden geçerse, arama sonuçlarını sayfada görüntülemek için `renderSearchResults()` işlevini çağırırız.

## <a name="displaying-search-results"></a>Arama sonuçlarını görüntüleme

Bing Varlık Arama API’si, [sonuçları belirli bir sırada görüntülemenizi gerektirir](use-display-requirements.md). API iki farklı yanıt türü döndürebileceğinden, JSON yanıtında üst düzey `Entities` veya `Places` koleksiyonu üzerinden yineleme yapmak ve bu sonuçları görüntülemek yeterli değildir. (Yalnızca bir sonuç türü istiyorsanız `responseFilter` sorgu parametresini kullanın.)

Bunun yerine, görüntülenecek sonuçları sıralamak için arama sonuçlarında `rankingResponse` koleksiyonunu kullanırız. Bu nesne `Entitiess` ve/veya `Places` koleksiyonlarındaki öğeleri ifade eder.

`rankingResponse` arama sonuçlarında `pole`, `mainline` ve `sidebar` olarak belirlenmiş üç koleksiyon içerebilir. 

`pole` mevcutsa en ilgili arama sonucudur ve belirgin şekilde görüntülenmelidir. `mainline` arama sonuçlarını toplu olarak ifade eder. Ana hat sonuçları `pole` öğesinin hemen ardından görüntülenmelidir (veya `pole` mevcut değilse ilk olarak). 

Son olarak. `sidebar` ikincil arama sonuçlarını ifade eder. Kenar çubuğunda veya ana hat sonuçlarından sonra görüntülenebilirler. Öğretici uygulamamız için ikinci seçeneği belirledik.

`rankingResponse` koleksiyonundaki her öğe, gerçek arama sonucu öğelerini iki farklı (ancak eşdeğer) şekilde ifade eder.

| | |
|-|-|
|`id`|`id` bir URL gibi görünür, ancak bağlantılar için kullanılmamalıdır. `id` türündeki bir sıralama sonucu, bir yanıt koleksiyonundaki bir arama sonucu öğesinin `id` ölçütünü *veya* bir yanıt koleksiyonunun tamamını (`Entities` gibi) eşleştirir.
|`answerType`<br>`resultIndex`|`answerType`, sonucu içeren üst düzey yanıt koleksiyonunu ifade eder (örneğin, `Entities`). `resultIndex`, o koleksiyondaki sonucun dizinini ifade eder. `resultIndex` atlanırsa, sıralama sonucu koleksiyonun tamamını ifade eder.

> [!NOTE]
> Arama yanıtının bu bölümüyle ilgili daha fazla bilgi için bkz. [Sıralama Sonuçları](rank-results.md).

Uygulamanız için en uygun olan başvurulan arama sonucu öğesini bulma yöntemini kullanabilirsiniz. Öğretici kodumuzda, her bir arama sonucunu bulmak için `answerType` ve `resultIndex` kullanırız.

Son olarak, `renderSearchResults()` işlevimize bakma zamanı geldi. Bu fonksiyon, arama sonuçlarının üç bölümünü temsil eden üç `rankingResponse` koleksiyonu üzerinden yinelenir. Her bölüm için, o bölümün sonuçlarını göstermek üzere `renderResultsItems()` öğesine çağrı yaparız.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Sonuç öğelerini işleme

JavaScript kodumuzda `searchItemRenderers` nesnesi, her tür arama sonucu için HTML oluşturan *renderers:* işlevlerini içerir.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

İşleyici işlevi aşağıdaki parametreleri kabul eder:

| | |
|-|-|
|`item`|Öğenin özelliklerini, örneğin URL'sini ve açıklamasını içeren JavaScript nesnesi.|
|`index`|Kendi koleksiyonu içindeki arama öğesinin dizini.|
|`count`|Arama sonucu öğesinin koleksiyonundaki öğelerin sayısı.|

Sonuçları saymak, koleksiyonun başlangıcı veya sonuna özel HTML oluşturmak ve belirli sayıdaki öğeden sonra satır sonu eklemek gibi işlemler için `index` ve `count` parametreleri kullanılabilir. İşleyicinin bu işleve ihtiyacı yoksa, bu iki parametreyi kabul etmesi gerekmez. Aslında öğretici uygulamamızda bunları işleyicilerde kullanmıyoruz.

`entities` işleyicisine daha yakından bakalım:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Varlık işleyici işlevimiz:

> [!div class="checklist"]
> * Varsa resmin küçük resmini görüntülemek için HTML `<img>` etiketini oluşturur. 
> * Resmi içeren sayfaya bağlanan HTML `<a>` etiketini oluşturur.
> * Görüntü ve bu görüntünün bulunduğu site hakkındaki bilgileri görüntüleyen bir açıklama oluşturur.
> * Varsa görüntüleme ipuçlarını kullanarak varlığın sınıflandırmasını dahil eder.
> * Varlık hakkında daha fazla bilgi almak için kullanılan Bing arama bağlantısını dahil eder.
> * Veri kaynaklarının gerektirdiği lisans veya alıntı bilgilerini görüntüler.

## <a name="persisting-client-id"></a>Kalıcı istemci kimliği

Bing arama API’lerinden gelen yanıtlar, başarılı isteklerle birlikte API’ye geri gönderilmesi gereken bir `X-MSEdge-ClientID` üst bilgisi içerir. Birden çok Bing Arama API’si kullanılıyorsa, mümkün olduğunca bu API’lerin tümünde aynı istemci kimliği kullanılmalıdır.

Böylelikle `X-MSEdge-ClientID` üst bilgisi sayesinde Bing API'leri kullanıcının tüm aramalarını ilişkilendirebilir. Bunun iki önemli avantajı vardır.

İlk olarak, Bing arama alt yapısının geçmiş bağlamı aramalara uygulayarak kullanıcıya daha uygun sonuçlar bulabilmesini sağlar. Örneğin kullanıcı daha önce yelkencilikle ilgili terim aramaları yaptıysa, daha sonra yapılan "rıhtımlar" araması tercihen yelkenlinin yanaştırılabileceği yerler hakkında bilgi döndürebilir.

İkincisi, Bing yeni özellikleri geniş ölçekte kullanıma sunmadan önce bunları denemesi için rastgele kullanıcılar seçebilir. Her istekle birlikte aynı istemci kimliğinin sağlanması, özelliği görmesi tercih edilen kullanıcıların bunu sürekli görebilmesini sağlar. İstemci kimliği olmadan, özellik kullanıcıya arama sonuçlarında rastgele gösterilebilir ve gösterilmeyebilir.

Tarayıcı güvenlik ilkeleri (CORS) `X-MSEdge-ClientID` üst bilgisinin JavaScript'in kullanımına sunulmasını engelleyebilir. Bu sınırlama, arama sonucunun kaynağı istekte bulunan sayfadan farklı olduğunda ortaya çıkar. Üretim ortamında, Web sayfasıyla aynı etki alanında API çağrısı yapan bir sunucu tarafı betiği barındırarak bu ilkeye uymalısınız. Betiğin kaynağı Web sayfasıyla aynı olduğundan, `X-MSEdge-ClientID` üst bilgisi JavaScript'in kullanımına sunulur.

> [!NOTE]
> Üretim ortamındaki bir Web uygulamasında, isteği sunucu tarafından gerçekleştirmeniz gerekir. Aksi takdirde, Bing Arama API’si anahtarınızın Web sayfasına eklenmesi gerekir ve bu durumda kaynağı görüntüleyen herkes tarafından görülebilir. API abonelik anahtarınız altında gerçekleştirilen tüm kullanım, yetkisiz tarafların yaptığı istekler bile size faturalandırılır; dolayısıyla anahtarınızı açıklamamanız önemlidir.

Geliştirme amacıyla, Bing Web Araması API’si isteğini CORS ara sunucusu aracılığıyla yapabilirsiniz. Böyle bir ara sunucudan gelen yanıtta, yanıt üst bilgilerini beyaz listeye alan ve JavaScript’in kullanımına sunan `Access-Control-Expose-Headers` üst bilgisi bulunur.

Öğretici uygulamamızın istemci kimliği üst bilgisine erişebilmesi için CORS ara sunucusu kolayca yüklenebilir. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Ardından komut penceresinde aşağıdaki komutu yürütün:

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Web Araması uç noktasını şöyle değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Varlık Arama API’si başvurusu](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing Haritalar API’si belgeleri](//msdn.microsoft.com/library/dd877180.aspx)
