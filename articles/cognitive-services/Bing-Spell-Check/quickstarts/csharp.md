---
title: 'Hızlı başlangıç: REST API ve C# ile yazım denetimi Bing Yazım Denetimi'
titleSuffix: Azure Cognitive Services
description: Yazım ve dilbilgisini denetlemek Için Bing Yazım Denetimi REST API ve C# ' i kullanmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: ace7a0ccaba533c9e72961536159d32af5ab8d98
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352737"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Hızlı başlangıç: Bing Yazım Denetimi REST API ve C ile yazım denetimi #

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Yazım Denetimi REST API ilk çağrlarınızı yapmak için bu hızlı başlangıcı kullanın. Bu basit C# uygulaması, API 'ye bir istek gönderir ve önerilen düzeltmelerin bir listesini döndürür. 

Bu uygulama C# dilinde yazılsa da, API birçok programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir. Bu uygulamanın kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs)' da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Herhangi bir [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/downloads/)sürümü.
* NuGet paketindeki Newtonsoft.Js. 
     
   Bu paketi Visual Studio 'ya yüklemek için:

     1. **Çözüm Gezgini**, çözüm dosyasına sağ tıklayın.
     1. **Çözüm Için NuGet Paketlerini Yönet**' i seçin.
     1. *ÜzerindeNewtonsoft.Js* arayın ve paketi yükler.

* Linux/MacOS kullanıyorsanız, [mono](https://www.mono-project.com/)kullanarak bu uygulamayı çalıştırabilirsiniz.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Proje oluşturma ve başlatma

1. Visual Studio 'da SpellCheckSample adlı yeni bir konsol çözümü oluşturun. Ardından, aşağıdaki ad alanlarını ana kod dosyasına ekleyin:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. API uç noktası, abonelik anahtarınız ve yazım denetimi yapılacak metin için değişkenler oluşturun. Aşağıdaki kodda genel uç noktasını kullanabilir veya kaynağınız için Azure portal görüntülenmiş [özel alt etki alanı](../../../cognitive-services/cognitive-services-custom-subdomains.md) uç noktasını kullanabilirsiniz.

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

3. Arama parametreleriniz için bir dize oluşturun: 

   1. Pazar kodunuzu `mkt` parametreye, `=` işleçle atayın. Pazar kodu, isteği yaptığınız ülkenin/bölgenin kodudur. 

   1. `mode`Parametresini `&` işleçle ekleyin ve ardından yazım denetimi modunu atayın. Mod `proof` (en fazla yazım/dilbilgisi hatalarını yakalar) ya da `spell` (en fazla yazım hatalarını yakalar, ancak çok sayıda dilbilgisi hatası) olabilir.
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Yazım denetimi isteği oluşturma ve gönderme

1. `SpellCheck()`API 'ye bir istek göndermek için çağrılan bir zaman uyumsuz işlev oluşturun. Oluşturun `HttpClient` ve abonelik anahtarınızı `Ocp-Apim-Subscription-Key` üstbilgiye ekleyin. İşlev içinde sonraki adımları izleyin.

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

3. Metninizi içeren bir nesne içeren bir liste oluşturun `KeyValuePair` ve bunu bir nesne oluşturmak için kullanın `FormUrlEncodedContent` . Üst bilgi bilgilerini ayarlayın ve `PostAsync()` isteği göndermek için kullanın.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>API yanıtını al ve Yazdır

### <a name="get-the-client-id-header"></a>İstemci KIMLIĞI üst bilgisini al

Yanıt bir `X-MSEdge-ClientID` başlık içeriyorsa, değeri alın ve yazdırın.

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

`Main()`Projenizin işlevinde, çağırın `SpellCheck()` .

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Projenizi derleyin ve çalıştırın. Visual Studio kullanıyorsanız, dosyada hata ayıklamak için **F5** ' e basın.

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