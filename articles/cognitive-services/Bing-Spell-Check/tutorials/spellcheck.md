---
title: "Öğretici: Bing Yazım Denetimi API'si ile Yazım Denetimi Sonuçlarını Alma"
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, kullanıcıların Bing Yazım Denetimi API'si sorgulamasını ve sonuçları görüntülemesini sağlayan bir Web sayfası oluşturacağız.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 4ef45d0d67d44ee1bcf0f06f41425ca5366ce46d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111460"
---
# <a name="tutorial-build-a-web-page-spell-check-client"></a>Öğretici: Web sayfası şeklinde bir Yazım Denetimi istemcisi oluşturma

Bu öğreticide kullanıcıların Bing Yazım Denetimi API'sini sorgulamasını sağlayan bir Web sayfası oluşturacağız. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingSpellCheckApp.html)' da kullanılabilir.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> - Bing Yazım Denetimi API'sine basit bir sorgu gönderme
> - Sorgu sonuçlarını görüntüleme

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi takip edebilmek için Bing Yazım Denetimi API'si için bir abonelik anahtarına ihtiyacınız olacaktır. Bir aboneliğiniz yoksa [ücretsiz deneme sürümüne kaydolun](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

## <a name="create-a-new-web-page"></a>Yeni bir Web sayfası oluşturma

Bir metin düzenleyicisi açın. Yeni bir dosya oluşturup spellcheck.html gibi bir ad verin.

## <a name="add-html-header"></a>HTML üst bilgisini ekleme

Aşağıda gösterilen şekilde HTML üst bilgisini ekleyin ve betiği başlatın.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"> 
    <title>Bing Spell Check</title>

<style type="text/css">
    html, body, div, p, h1, h2 {font-family: Verdana, "Lucida Sans", sans-serif;}

    html, body, div, p  {font-weight: normal;}
    h1, h2 {font-weight: bold;}
    sup {font-weight: normal;}

    html, body, div, p  {font-size: 12px;}
    h1 {font-size: 20px;}
    h2 {font-size: 16px;}
    h1, h2 {clear: left;}

    img#logo {float: right;
</style>

<script type="text/javascript">
```

## <a name="getsubscriptionkey-function"></a>getSubscriptionKey işlevi

getSubscriptionKey işlevi, Bing Yazım Denetimi API'si anahtarını döndürür. Anahtarı yerel depolama alanından (tanımlama bilgisi) alır veya gerekirse kullanıcıya sorar.

getSubscriptionKey işlevini başlatın ve tanımlama bilgisi adını aşağıda gösterilen şekilde bildirin.

```html
getSubscriptionKey = function() {

    var COOKIE = "bing-spell-check-api-key";   // name used to store API key in key/value storage
```

findCookie yardımcı işlevi belirtilen tanımlama bilgisinin değerini döndürür. Tanımlama bilgisi bulunamazsa boş bir dize döndürür.

```html
    function findCookie(name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) {
                return keyvalue[1];
            }
        }
        return "";
        }
```

getSubscriptionKeyCookie yardımcı işlevi, kullanıcıya Bing Yazım Denetimi API'si anahtarının değerini sorar ve anahtar değerini döndürür.

```html
    function getSubscriptionKeyCookie() {
        var key = findCookie(COOKIE);
        while (key.length !== 32) {
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
            var expiry = new Date();
            expiry.setFullYear(expiry.getFullYear() + 2);
            document.cookie = COOKIE + "=" + key.trim() + "; expires=" + expiry.toUTCString();
        }
        return key;
    }
```

getSubscriptionKeyLocalStorage yardımcı işlevi Bing Yazım Denetimi API'si anahtarını almak için önce uygun tanımlama bilgisini arar. Tanımlama bilgisini bulamazsa anahtar değerini kullanıcıya sorar. Ardından anahtar değerini döndürür.

```html
    function getSubscriptionKeyLocalStorage() {
        var key = localStorage.getItem(COOKIE) || "";
        while (key.length !== 32)
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
        localStorage.setItem(COOKIE, key)
        return key;
    }
```

getSubscriptionKey yardımcı işlevi yalnızca **invalidate** parametresini alır. **invalidate** parametresi **true** olduğunda getSubscriptionKey, Bing Yazım Denetimi API'si anahtarını içeren tanımlama bilgisini siler. **invalidate** parametresi **false** olduğunda getSubscriptionKey, Bing Yazım Denetimi API'si anahtarının değerini döndürür.

```html
    function getSubscriptionKey(invalidate) {
        if (invalidate) {
            try {
                localStorage.removeItem(COOKIE);
            } catch (e) {
                document.cookie = COOKIE + "=";
            }
        } else {
            try {
                return getSubscriptionKeyLocalStorage();
            } catch (e) {
                return getSubscriptionKeyCookie();
            }
        }
    }
```

getSubscriptionKey yardımcı işlevini dış getSubscriptionKey işlevinin sonucu olarak döndürün. Dış getSubscriptionKey işlevinin tanımını kapatın.

```html
    return getSubscriptionKey;

}();
```

## <a name="helper-functions"></a>Yardımcı işlevleri

pre yardımcı işlevi, belirtilen metni [pre](https://www.w3schools.com/tags/tag_pre.asp) HTML etiketiyle önceden biçimlendirilmiş şekilde döndürür.

```html
function pre(text) {
    return "<pre>" + text.replace(/&/g, "&amp;").replace(/</g, "&lt;") + "</pre>"
}
```

renderSearchResults işlevi Bing Yazım Denetimi API'sinden belirtilen sonuçları döndürür ve JSON düzgün yazdırma özelliğini kullanır.

```html
function renderSearchResults(results) {
    document.getElementById("results").innerHTML = pre(JSON.stringify(results, null, 2));
}
```

renderErrorMessage işlevi belirtilen hata iletisini ve hata kodunu döndürür.

```html
function renderErrorMessage(message, code) {
    if (code)
        document.getElementById("results").innerHTML = "<pre>Status " + code + ": " + message + "</pre>";
    else
        document.getElementById("results").innerHTML = "<pre>" + message + "</pre>";
}
```

## <a name="bingspellcheck-function"></a>bingSpellCheck işlevi

bingSpellCheck işlevi kullanıcı HTML formu alanına metin girdiğinde çağrılır.
İki parametre alır: HTML form alanının içeriği ve Bing Yazım Denetimi API'si anahtarı.

```html
function bingSpellCheck(query, key) {
```

Bing Yazım Denetimi API'si uç noktasını belirtin ve uç noktaya istek göndermek için kullanacağımız bir XMLHttpRequest nesnesi bildirin.

```html
    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    var request = new XMLHttpRequest();

    try {
        request.open("GET", endpoint + "?mode=proof&mkt=en-US&text=" + encodeURIComponent(query));
    }
    catch (e) {
        renderErrorMessage("Bad request");
        return false;
    }
```

**Ocp-Apim-Subscription-Key** üst bilgisini Bing Yazım Denetimi API'si anahtarının değeri olarak ayarlayın.

```html
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
```

Uç noktadan gelen yanıtı işleyin. Durum 200 (OK) ise sonuçları görüntüleyin, değilse hata iletisini görüntüleyin.

```html
    request.addEventListener("load", function() {
        if (this.status === 200) {
            renderSearchResults(JSON.parse(this.responseText));
        }
        else {
            if (this.status === 401) getSubscriptionKey(true);
            renderErrorMessage(this.statusText, this.status);
        }
    });
```

Ayrıca XMLHttpRequest nesnesinden gelebilecek olası hata olaylarını da işleyin.

```html
    request.addEventListener("error", function() {
        renderErrorMessage("Network error");
    });

    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });
```

İsteği gönderin. bingSpellCheck işlevini, **script** etiketini ve **head** etiketini kapatın.

```html
    request.send();
    return false;
}
// --></script>

</head>
```

## <a name="html-body"></a>HTML gövdesi

Web sayfası yüklendiğinde Bing Yazım Denetimi API'si anahtarının mevcut olduğundan emin olun ve gerekirse kullanıcıdan isteyin.

```html
<body onload="document.forms.bing.query.focus(); getSubscriptionKey();">
```

Bing logosunu görüntüleyin.

```html
<img id="logo" align=base src="data:image/png;base64,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">
```

Metin alanına sahip bir HTML formu oluşturun. **onsubmit** olayını işleyin ve bingSpellCheck işlevini çağırıp metin alanının içeriğini ve Bing Yazım Denetimi API'si anahtarını geçirin.

```html
<form name="bing" onsubmit="return bingSpellCheck(this.query.value, getSubscriptionKey())">
    <h2>Spell Check</h2>
    <input type="text" name="query" size="80" placeholder="Spell Check" autocomplete=off>
</form>
```

Sonuçları görüntülemek için kullandığımız HTML **div** etiketini ekleyin. Önceden tanımladığımız JavaScript kodu, bu **div** etiketine başvurur.

```html
<h2>Results</h2>
<div id="results">
<p>None yet.</p>

</div>

</body>
</html>
```

Dosyayı kaydedin.

## <a name="display-results"></a>Sonuçları görüntüleme

Web sayfasını tarayıcınızda açın. İstendiğinde Bing Yazım Denetimi API'si abonelik anahtarınızı girin. **Spell Check** (Yazım Denetimi) metin kutusuna bir sorgu (örneğin, "Hollo, wlrd!") yazın ve **Enter** tuşuna basın. Web sayfasında sorgu sonuçları görüntülenir.

```json
{
  "_type": "SpellCheck",
  "flaggedTokens": [
    {
      "offset": 0,
      "token": "Hollo",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "Hello",
          "score": 0.856629936217145
        },
        {
          "suggestion": "Hollow",
          "score": 0.816717853225633
        }
      ]
    },
    {
      "offset": 7,
      "token": "wlrd",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "world",
          "score": 0.856629936217145
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kullanım ve görüntüleme gereksinimleri](../UseAndDisplayRequirements.md)
