---
title: 'Hızlı Başlangıç: Node. js kullanarak Bing Varlık Arama REST API bir arama isteği gönderme'
titleSuffix: Azure Cognitive Services
description: Kullanarak C#Bing varlık arama REST API bir istek göndermek ve bir JSON yanıtı almak için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: e56bf0b3daa38f79e637ece947414b885de3797a
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478975"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Hızlı Başlangıç: Node. js kullanarak Bing Varlık Arama REST API bir arama isteği gönderme

Bing Varlık Arama API'si ilk çağrısını yapmak ve JSON yanıtını görüntülemek için bu hızlı başlangıcı kullanın. Bu basit JavaScript uygulaması, API 'ye bir haber arama sorgusu gönderir ve yanıtı görüntüler. Bu örneğin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js)’da mevcuttur.

Bu uygulama JavaScript 'e yazılırken, API birçok programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/en/download/)’in en son sürümü.

* [JavaScript Istek kitaplığı](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. Sık kullandığınız IDE’de veya düzenleyicide yeni bir JavaScript dosyası oluşturun ve katılık ve https gereksinimlerini ayarlayın.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. API uç noktası, abonelik anahtarınız ve arama sorgunuz için değişkenler oluşturun.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Pazar ve sorgu parametrelerinizi adlı `query`bir dizeye ekleyin. Sorgunuzu URL ile `encodeURI()`kodlayıp kodlayadığınızdan emin olun.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Yanıtı işleme ve ayrıştırma

1. Parametresi olarak `response`, http `response_handler` çağrısını alan adlı bir işlev tanımlayın. Bu işlev içinde aşağıdaki adımları gerçekleştirin:

    1. JSON yanıtının gövdesini içerecek bir değişken tanımlayın.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. **Veri** işareti çağrıldığında yanıtın gövdesini depolama
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Bir **bitiş** bayrağına işaret EDILDIĞINDE, JSON 'ı ayrıştırır ve yazdırın.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>İstek gönderme

1. Arama isteği göndermek için `Search` adlı bir işlev oluşturun. Burada, aşağıdaki adımları gerçekleştirin.

   1. İstek parametrelerinizi içeren bir JSON nesnesi oluşturun: yöntemi `Get` için kullanın ve ana bilgisayar ve yol bilgilerinizi ekleyin. Abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye ekleyin. 
   2. Daha `https.request()` önce oluşturulan yanıt işleyicisine ve arama parametreleriniz ile isteği göndermek için kullanın.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. `Search()` İşlevi çağırın.

## <a name="example-json-response"></a>Örnek JSON yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı Web uygulaması oluşturma](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Varlık Arama API'si nedir?](../overview.md )
* [Bing Varlık Arama API'si Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
