---
title: Bing Web Araması API'si yanıt yapısı ve yanıt türleri
titleSuffix: Azure Cognitive Services
description: Bir arama isteği Bing Web Araması gönderdiğinizde, yanıt gövdesinde bir `SearchResponse` nesnesi döndürür.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110624"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Araması API'si yanıt yapısı ve yanıt türleri  

Bir arama isteği Bing Web Araması gönderdiğinizde, yanıt gövdesinde bir [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) nesnesi döndürür. Nesnesi, Bing 'in sorguyla ilgili olduğunu tespit eden her yanıt için bir alan içerir. Bu örnek, Bing tüm yanıtları döndürmediğinde bir yanıt nesnesini gösterir:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Genellikle, Bing Web Araması yanıtların bir alt kümesini döndürür. Örneğin, Sorgu terimi *saptıklandı*ise, yanıt `webPages`, `images`ve `rankingResponse`içerebilir. Web sayfalarını filtrelemek için [Responsefilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) kullanmadığınız müddetçe, yanıt her zaman `webpages` ve `rankingResponse` yanıtlarını içerir.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Web sayfası yanıtı

[Web sayfası](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) yanıtı, sorguyla ilgili Bing Web araması belirlenen Web sayfalarına bağlantıların bir listesini içerir. Listedeki her bir [Web sayfası](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) şunları içerir: sayfanın adı, URL 'si, görüntüleme URL 'si, içeriğin kısa bir açıklaması ve Bing 'in içeriği bulduğu tarih.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Kullanıcıyı Web sayfasına alan bir köprü oluşturmak için `name` ve `url` kullanın.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Görüntü yanıtı

[Görüntüler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) yanıtı, Bing 'in sorguyla ilgili olduğunu düşündük görüntülerin bir listesini içerir. Listedeki her [görüntüde](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) görüntünün URL 'si, boyutu, boyutları ve kodlama biçimi bulunur. Görüntü nesnesi aynı zamanda görüntü küçük resminin URL'sini ve küçük resmin boyutlarını içerir.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Kullanıcının cihazına bağlı olarak, genellikle küçük resimlerin bir alt kümesini görüntülenir ve bu, kullanıcının kalan görüntülerde [sayfa](paging-webpages.md) sonuna kadar bir seçeneğe sahip olmasını sağlar.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Küçük resimlerin, kullanıcı imleci üzerine getirdiğinde büyütülmesini de sağlayabilirsiniz. Büyütmeniz halinde görüntüye bağlam sağlamayı unutmayın. Örneğin, `hostPageDisplayUrl` konaktan çıkararak ve görüntünün altında görüntüleyerek. Küçük resmi yeniden boyutlandırma hakkında bilgi için bkz. [Küçük Resimleri Yeniden Boyutlandırma ve Kırpma](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Kullanıcı küçük resme tıklarsa, kullanıcıyı görüntünün bir collajı içeren görüntüler için Bing arama sonuçları sayfasına götürmek üzere `webSearchUrl` kullanın.

Görüntü yanıtı ve görüntüleri hakkında daha fazla bilgi için bkz. [resım arama API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>İlgili aramalar yanıtı

[Relatedaramaları](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) yanıtı, diğer kullanıcılar tarafından yapılan en popüler ilgili sorguların listesini içerir. Listedeki her [sorgu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) , bir sorgu dizesi (`text`), isabet vurgulama karakterleri içeren bir sorgu dizesi (`displayText`) ve bu sorgu için Bing 'in arama sonuçları SAYFASıNA bir URL (`webSearchUrl`) içerir.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Kullanıcıyı ilgili sorgu için Bing arama sonuçları sayfasına alan bir köprü oluşturmak için `displayText` sorgu dizesini ve `webSearchUrl` URL 'sini kullanın. Ayrıca, kendi Web Araması API sorgunuzda `text` sorgu dizesini kullanabilir ve sonuçları kendiniz görüntüleyebilirsiniz.

`displayText`vurgu işaretleyicilerini işleme hakkında daha fazla bilgi için bkz. [Isabet vurgulama](../bing-web-search/hit-highlighting.md).

Aşağıda, Bing.com içinde ilgili sorgu kullanımının bir örneği gösterilmektedir.

![Bing üzerinde ilgili aramalar örneği](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videolar yanıtı

[Videolar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) yanıtı, Bing 'in sorguyla ilgili olduğu videoların bir listesini içerir. Listedeki her [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) videonun URL 'sini, süresini, boyutlarını ve kodlama biçimini içerir. Video nesnesi aynı zamanda video küçük resminin URL'sini ve küçük resmin boyutlarını içerir.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Kullanıcının cihazına bağlı olarak, kullanıcıların kalan videoları görüntülemesi için bir seçenek içeren videoların bir alt kümesini görüntüleriz. Videonun uzunluğu, açıklaması (adı) ve atısyon (yayımcı) ile videonun küçük resmini görüntüleyebilirsiniz.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Kullanıcı küçük resmin üzerine geldiğinde, videonun küçük resim sürümünü oynatmak için `motionThumbnailUrl` kullanabilirsiniz. Hareket küçük resmini görüntülediğinizde öznitelik belirlediğinizden emin olun.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Kullanıcı küçük resme tıklarsa, aşağıdaki video görüntüleme seçenekleri sunulur:

- Ana bilgisayar web sitesinde videoyu görüntülemek için `hostPageUrl` kullanın (örneğin, YouTube)
- Bing video tarayıcısı 'ndaki videoyu görüntülemek için `webSearchUrl` kullanın
- Videoyu kendi deneyiminize eklemek için `embedHtml` kullanın

Video yanıtı ve videoları hakkında daha fazla bilgi için bkz. [VIDEO arama API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Haber yanıtı

[Haber](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) yanıtı, Bing 'in sorguyla ilgili olduğu haber makalelerinin bir listesini içerir. Listedeki her [haber makalesi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle), makalenin adı, açıklaması ve konağın web sitesindeki makalenin URL’sini içerir. Makale bir görüntü içeriyorsa, nesne görüntünün küçük resmini içerir.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Kullanıcının cihazına bağlı olarak, kullanıcının kalan makaleleri görüntülemesine yönelik bir seçeneğe sahip haber makalelerinin bir alt kümesini görüntülerdiniz. Kullanıcıyı konağın sitesindeki haber makalesine götüren bir köprü bağlantı oluşturmak için `name` ve `url` kullanın. Makale bir görüntü içeriyorsa, `url`kullanarak görüntüyü tıklatılabilir hale getirin. Makaleyi ilişkilendirmek için `provider` kullandığınızdan emin olun.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Haber yanıtı ve haber makaleleri hakkında daha fazla bilgi için bkz. [Haber Arama API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Hesaplama yanıtı

Kullanıcı bir matematik ifadesi veya bir birim dönüştürme sorgusu girerse, yanıt bir [Hesaplama](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) yanıtı içerebilir. `computation` yanıtı, normalleştirilmiş ifadeyi ve sonucunu içerir.

Birim dönüştürme sorgusu, bir birimi diğerine dönüştüren bir sorgudur. Örneğin, *10 metrede* kaç metre veya *1/4 kupa bir kaç tane*

Aşağıda, *10 metrede kaç fit* 'in `computation` yanıtı gösterilmektedir?

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Aşağıda matematik sorgularının örnekleri ve bunlara karşılık gelen `computation` yanıtları gösterilmektedir.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Matematik ifadesi aşağıdaki sembolleri içerebilir:

|Sembol|Açıklama|
|------------|-----------------|
|+|Ek olarak|
|-|StrA|
|/|Bölmenin|
|*|Anda|
|^|Güç|
|!|Imı|
|.|Ondalık|
|()|Öncelik gruplandırması|
|[]|İşlev|

Matematik ifadesinde aşağıdaki sabitler bulunabilir:

|Sembol|Açıklama|
|------------|-----------------|
|PI|3,14159...|
|Ölçüde|Ölçüde|
|Ben|Sanal sayı|
|A|e, 2,71828...|
|GoldenRatio|Altın oran, 1,61803...|

Matematik ifadesi aşağıdaki işlevleri içerebilir:

|Sembol|Açıklama|
|------------|-----------------|
|Sırala|Kare kök|
|Sin [x], cos [x], tan [x]<br />CSC [x], SEC [x], COT [x]|Trigonometrik İşlevler (radyan cinsinden bağımsız değişkenlerle)|
|ArcSin [x], ArcCos [x], ArcTan [x]<br />ArcCsc [x], ArcSec [x], ArcCot [x]|Ters Trigonometrik İşlevler (radyan cinsinden sonuçlar verir)|
|Exp [x], E ^ x|Üstel işlevi|
|[X] günlüğü|Doğal logaritma|
|Sinh [x], cosh [x], tanh [x]<br />Csch [x], sech [x], coth [x]|Hiperbolik İşlevler|
|ArcSinh [x], ArcCosh [x], ArcTanh [x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Ters hiperbolik işlevler|

Değişkenleri içeren matematik ifadeleri (örneğin, 4X + 6 = 18, burada x değişkendir) desteklenmez.

## <a name="timezone-answer"></a>Saat dilimi yanıtı

Kullanıcı bir saat veya tarih sorgusu girerse, yanıt bir [saat dilimi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) yanıtı içerebilir. Bu yanıt örtük veya açık sorguları destekler. Kapalı bir sorgu; Örneğin, *ne zaman*olduğu gibi, kullanıcının konumuna göre yerel saati döndürür. Bir açık sorgu (örneğin, *Seattle 'Da ne zaman)?* , Seattle, WA için yerel saati döndürür.

`timeZone` yanıtı konumun adını, belirtilen konumdaki geçerli UTC tarihini ve saatini ve UTC sapmasını sağlar. Konumun sınırı birden çok saat dilimi içindeyse, yanıt, sınırın içindeki tüm saat dilimlerinin geçerli UTC Tarih ve saatini içerir. Örneğin, Florida durumu iki saat diliminin içinde olduğundan, yanıt her iki saat dilimindeki yerel tarih ve saati içerir.  

Sorgu bir eyalet veya ülke/bölge zamanını isterse, Bing birincil şehri konumun coğrafi sınırının içinde belirler ve `primaryCityTime` alanına geri döndürür. Sınır birden çok saat dilimi içeriyorsa, kalan saat dilimleri `otherCityTimes` alanında döndürülür.

Aşağıda `timeZone` yanıtını döndüren örnek sorgular gösterilmiştir.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Yazım önerisi yanıtı

Bing, kullanıcının farklı bir şeyi aramak için tasarlanmış olabileceğini belirlerse, yanıt bir [yazım önerisi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) nesnesi içerir. Örneğin, Kullanıcı *Carlos Pen*'u arayıyorsa, Bing kullanıcının bunun yerine Carlos pena aramak için (yani, *Carlos Pen*'ın başkaları tarafından geçmiş aramalara bağlı olarak) arama yapma amacını belirleyebilir. Aşağıda örnek bir yazım yanıtı gösterilmektedir.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Yanıt üst bilgileri

Bing Web Araması API'si yanıtları aşağıdaki üst bilgileri içerebilir:

|||
|-|-|
|`X-MSEdge-ClientID`|Bing 'in kullanıcıya atadığı benzersiz KIMLIK|
|`BingAPIs-Market`|İsteği yerine getirmek için kullanılan Pazar|
|`BingAPIs-TraceId`|Bu istek için Bing API sunucusundaki günlük girdisi (destek için)|

İstemci KIMLIĞINI kalıcı hale getirmek ve sonraki isteklerle döndürmek özellikle önemlidir. Bunu yaptığınızda arama, arama sonuçlarında geçmiş bağlam kullanır ve ayrıca tutarlı bir kullanıcı deneyimi sağlar.

Ancak, JavaScript 'ten Bing Web Araması API'si çağırdığınızda, tarayıcınızın yerleşik güvenlik özellikleri (CORS) bu üstbilgilerin değerlerine erişmenizi engelleyebilir.

Üstbilgilere erişim kazanmak için bir CORS proxy 'si aracılığıyla Bing Web Araması API'si isteği yapabilirsiniz. Böyle bir ara sunucudan gelen yanıtta, yanıt üst bilgilerini beyaz listeye alan ve JavaScript’in kullanımına sunan `Access-Control-Expose-Headers` üst bilgisi bulunur.

[Öğretici uygulamamız](tutorial-bing-web-search-single-page-app.md) , isteğe bağlı istemci üst bilgilerine erişmesine izin vermek IÇIN bir CORS proxy yüklemek kolaydır. İlk olarak, henüz yüklemediyseniz [Node.js'yi yükleyin](https://nodejs.org/en/download/). Sonra bir komut isteminde aşağıdaki komutu girin.

    npm install -g cors-proxy-server

Sonra, HTML dosyasındaki Bing Web Araması API'si uç noktasını şu şekilde değiştirin:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Son olarak, aşağıdaki komutla CORS ara sunucusunu başlatın:

    cors-proxy-server

Öğretici uygulamasını kullanırken komut penceresini açık bırakın; pencere kapatılırsa ara sunucu durdurulur. Arama sonuçlarının altındaki genişletilebilir HTTP Üst Bilgileri bölümünde artık `X-MSEdge-ClientID` üst bilgisini (diğerleriyle birlikte) görebilir ve bunun her istekte aynı olduğunu doğrulayabilirsiniz.

## <a name="response-headers-in-production"></a>Üretimdeki yanıt üst bilgileri

Önceki cevap bölümünde açıklanan CORS proxy yaklaşımı geliştirme, test ve öğrenme için uygundur.

Bir üretim ortamında, Bing Web Araması API'si kullanan Web sayfasıyla aynı etki alanında bir sunucu tarafı betiği barındırmalısınız. Bu betik, Web sayfası JavaScript 'ten istek üzerine API çağrıları yapıp, üst bilgiler dahil olmak üzere tüm sonuçları istemciye geri iletmelidir. İki kaynak (sayfa ve betik) bir kaynağı paylaştığından, CORS kullanılmaz ve özel üst bilgilere Web sayfasındaki JavaScript 'in erişimine erişilebilir.

Bu yaklaşım ayrıca API anahtarınızı herkese açık hale karşı korur, çünkü yalnızca sunucu tarafı betiği için gereklidir. Betik, isteğin yetkilendirildiğinden emin olmak için başka bir yöntem kullanabilir.

Aşağıda, Bing 'in yazım önerisi nasıl kullanıldığı gösterilmektedir.

![Bing yazım denetimi önerisi örneği](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Sonraki adımlar  

* [İstek azaltma](throttling-requests.md) belgelerini gözden geçirin.  

## <a name="see-also"></a>Ayrıca bkz.  

* [Bing Web Araması API'si Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
