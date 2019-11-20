---
title: 'Hızlı başlangıç: Bing Yazım Denetimi REST API ile yazım denetimi yapın veC#'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek için Bing Yazım Denetimi REST API kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 93b5c395a0d121305c092229d862bf9ecaa4789c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936053"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Hızlı başlangıç: Bing Yazım Denetimi REST API ile yazım denetimi yapın veC#

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit C# uygulama, API 'ye bir istek gönderir ve önerilen düzeltmelerin bir listesini döndürür. Bu uygulama C# ile yazılmış olmakla birlikte API, çoğu programlama diliyle uyumlu bir RESTful Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs)' da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Herhangi bir [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/downloads/)sürümü.
* `Newtonsoft.Json`, Visual Studio 'da bir NuGet paketi olarak yüklemek için:
    1. **Çözüm Gezgini**, çözüm dosyasına sağ tıklayın.
    1. **Çözüm Için NuGet Paketlerini Yönet**' i seçin.
    1. `Newtonsoft.Json` arayın ve paketi yükler.
* Linux/MacOS kullanıyorsanız, bu uygulama [mono](https://www.mono-project.com/)kullanılarak çalıştırılabilir.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Proje oluşturma ve başlatma

1. Visual Studio 'da `SpellCheckSample` adlı yeni bir konsol çözümü oluşturun. Ardından ana kod dosyasına aşağıdaki ad alanlarını ekleyin.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. API uç noktası, abonelik anahtarınız ve yazım denetimi yapılacak metin için değişkenler oluşturun.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Arama parametreleriniz için bir değişken oluşturun. `mkt=`sonra Pazar kodunuzu ekleyin. Pazar kodu, isteği yaptığınız ülkeniz. Ayrıca, `&mode=`sonra yazım denetimi modlarınızı ekleyin. Mod `proof` (en fazla yazım/dilbilgisi hatalarını yakalar) veya `spell` (en fazla yazım denetimi hatası değil, en çok yazım yakalar).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Yazım denetimi isteği oluşturma ve gönderme

1. API 'ye bir istek göndermek için `SpellCheck()` adlı zaman uyumsuz bir işlev oluşturun. Bir `HttpClient`oluşturun ve abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgisine ekleyin. Sonra işlevi içinde aşağıdaki adımları gerçekleştirin.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Konağınız, yolunuz ve parametrelerinizi ekleyerek isteğiniz için URI 'yi oluşturun.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Metninizi içeren `KeyValuePair` nesne içeren bir liste oluşturun ve `FormUrlEncodedContent` bir nesne oluşturmak için bunu kullanın. Üstbilgi bilgilerini ayarlayın ve isteği göndermek için `PostAsync()` kullanın.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>API yanıtını al ve Yazdır

### <a name="get-the-client-id-header"></a>İstemci KIMLIĞI üst bilgisini al

Yanıt bir `X-MSEdge-ClientID` üst bilgisi içeriyorsa, değeri alın ve yazdırın.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Yanıtı al

API 'den gelen yanıtı alın. JSON nesnesinin serisini kaldırma ve konsola yazdırma.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Yazım denetimi işlevini çağırın

Projenizin ana işlevinde `SpellCheck()`çağırın.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
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

- [Bing Yazım Denetimi API'si nedir?](../overview.md)
- [Bing Yazım Denetimi API’si v7 Başvurusu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
