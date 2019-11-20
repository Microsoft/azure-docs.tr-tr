---
title: 'Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Node. js ile yazım denetimi yapma'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek için Bing Yazım Denetimi REST API kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: c45e9e7743aca2d091b62caed6a7c5a724bacebf
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423512"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Node. js ile yazım denetimi yapma

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit düğüm uygulaması, API 'ye bir istek gönderir ve tanımadığı sözcüklerin bir listesini ve ardından önerilen düzeltmeleri döndürür. Bu uygulama Node. js ' de yazıldığı sırada API, çoğu programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js)' da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* [Node. js 6](https://nodejs.org/en/download/) veya üzeri.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Proje oluşturma ve başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir JavaScript dosyası oluşturun. Striclük ayarla ve gerektir `https`. Ardından API uç noktanızın ana bilgisayar, yol ve abonelik anahtarınız için değişkenler oluşturun.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Arama parametreleriniz ve denetlemek istediğiniz metin için değişkenler oluşturun. Pazar kodunuzu `mkt=`sonuna ekleyin. Pazar kodu, isteği yaptığınız ülkeniz. Ayrıca, daha sonra `&mode=`yazım denetimi modlarınızı ekleyin. Mod `proof` (en fazla yazım/dilbilgisi hatalarını yakalar) veya `spell` (çok sayıda dilbilgisi hatası değil, en fazla yazım yakalar).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>İstek parametrelerini oluşturma

`POST` Yöntemi ile yeni bir nesne oluşturarak istek parametrelerinizi oluşturun. Uç nokta yolunuza ve sorgu dizesine ekleyerek yolunuza ekleyin. Abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye ekleyin.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Yanıt işleyici oluşturma

API 'den JSON yanıtını `response_handler` almak için adlı bir işlev oluşturun ve yazdırın. Yanıt gövdesi için bir değişken oluşturun. `data` Kullanarak`response.on()`bir bayrak alındığında yanıtı ekleyin. Bir `end` bayrak alındığında, JSON gövdesini konsola yazdırın.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>İsteği gönder

İstek parametreleriniz ve `https.request()` yanıt işleyiciniz ile kullanarak API 'yi çağırın. Metninizi API 'ye yazın ve isteği daha sonra sonlandırın.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Örnek JSON yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

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
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı Web uygulaması oluşturma](../tutorials/spellcheck.md)

- [Bing Yazım Denetimi API'si nedir?](../overview.md)
- [Bing Yazım Denetimi API’si v7 Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
