---
title: 'Hızlı başlangıç: REST API ve Python ile yazım denetimi-Bing Yazım Denetimi'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek için Bing Yazım Denetimi REST API ve Python 'ı kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 27e5a336b0a7e6fa0e47e20ad0d3c6204cef7757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352669"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Hızlı başlangıç: Bing Yazım Denetimi REST API ve Python ile yazım denetimi yapma

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit Python uygulaması, API 'ye bir istek gönderir ve önerilen düzeltmelerin bir listesini döndürür. 

Bu uygulama Python 'da yazılmış olsa da, API birçok programlama dili ile uyumlu olan bir yenilenmiş Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 'da kullanılabilir

## <a name="prerequisites"></a>Önkoşullar

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Uygulamayı başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir Python dosyası oluşturun ve aşağıdaki içeri aktarma deyimlerini ekleyin:

   ```python
   import requests
   import json
   ```

2. Yazım denetimi yapmak istediğiniz metin, abonelik anahtarınız ve Bing Yazım Denetimi uç noktanız için değişkenler oluşturun. Aşağıdaki kodda genel uç noktasını kullanabilir veya kaynağınız için Azure portal görüntülenmiş [özel alt etki alanı](../../../cognitive-services/cognitive-services-custom-subdomains.md) uç noktasını kullanabilirsiniz.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>İstek için parametreleri oluşturma

1. Anahtar olarak ile yeni bir sözlük `text` ve değer olarak metninizi oluşturun.

    ```python
    data = {'text': example_text}
    ```

2. İsteğiniz için parametreleri ekleyin: 

   1. Pazar kodunuzu `mkt` parametreye, `=` işleçle atayın. Pazar kodu, isteği yaptığınız ülkenin/bölgenin kodudur. 

   1. `mode`Parametresini `&` işleçle ekleyin ve ardından yazım denetimi modunu atayın. Mod `proof` (en fazla yazım/dilbilgisi hatalarını yakalar) ya da `spell` (en fazla yazım hatalarını yakalar, ancak çok sayıda dilbilgisi hatası) olabilir. 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Üst bilgiye `Content-Type` ve abonelik anahtarınızı ekleyin `Ocp-Apim-Subscription-Key` .

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>İsteği gönder ve yanıtı oku

1. İstek kitaplığını kullanarak POST isteğini gönderin.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. JSON yanıtını alın ve yazdırın.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Uygulamayı çalıştırma

Komut satırını kullanıyorsanız, uygulamayı çalıştırmak için aşağıdaki komutu kullanın:

```bash
python <FILE_NAME>.py
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
> [Tek sayfalı web uygulaması oluşturma](../tutorials/spellcheck.md)

- [Bing Yazım Denetimi API’si nedir?](../overview.md)
- [Bing Yazım Denetimi API'si v7 başvurusu](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)