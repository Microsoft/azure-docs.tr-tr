---
title: 'Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Python ile yazım denetimi yapma'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek için Bing Yazım Denetimi REST API kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: fef329a46594071b5f1621e9a46140f407eeb415
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996733"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Hızlı Başlangıç: Bing Yazım Denetimi REST API ve Python ile yazım denetimi yapma

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit Python uygulaması, API 'ye bir istek gönderir ve önerilen düzeltmelerin bir listesini döndürür. Bu uygulama Python ile yazılmış olmakla birlikte API, çoğu programlama diliyle uyumlu bir RESTful Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 'da kullanılabilir

## <a name="prerequisites"></a>Önkoşullar

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Uygulamayı başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir Python dosyası oluşturun ve aşağıdaki içeri aktarma ifadesini ekleyin.

   ```python
   import requests
   import json
   ```

2. Yazım denetimi yapmak istediğiniz metin, abonelik anahtarınız ve Bing Yazım Denetimi uç noktanız için değişkenler oluşturun.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>İstek için parametreleri oluşturma

1. Anahtar olarak ile `text` yeni bir sözlük ve değer olarak metninizi oluşturun.

    ```python
    data = {'text': example_text}
    ```

2. İsteğiniz için parametreleri ekleyin. Pazar kodunuzu `mkt=`sonuna ekleyin. Pazar kodu, isteği yaptığınız ülkeniz. Ayrıca, daha sonra `&mode=`yazım denetimi modlarınızı ekleyin. Mod `proof` (en fazla yazım/dilbilgisi hatalarını yakalar) veya `spell` (çok sayıda dilbilgisi hatası değil, en fazla yazım yakalar).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Üst bilgiye ve abonelik anahtarınızı `Ocp-Apim-Subscription-Key` ekleyin. `Content-Type`

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>İsteği gönder ve yanıtı oku

1. İstekler kitaplığını kullanarak POST isteğini gönderin.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. JSON yanıtını alın ve yazdırın.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
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
