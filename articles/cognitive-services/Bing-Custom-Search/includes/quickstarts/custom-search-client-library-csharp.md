---
title: Bing Özel Arama C# istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 504057be85902307a121a57dd421254d94167341
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879500"
---
C# için Bing Özel Arama istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Bing Özel Arama API'si, ilgilendiğiniz konular için özel, reklam ücretsiz arama deneyimleri oluşturmanıza olanak sağlar. Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)' da bulunabilir.

C# için Bing Özel Arama istemci kitaplığını kullanarak şunları yapın:
* Bing Özel Arama örneğinden Web 'de arama sonuçları bulun.

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/bing-custom-search-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  |  [Örnekler](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Önkoşullar

- Bir Bing Özel Arama örneği. Daha fazla bilgi için bkz. [hızlı başlangıç: ilk Bing özel arama örneğinizi oluşturma](../../quick-start.md) .
- Microsoft [.NET Core](https://dotnet.microsoft.com/download)
- Herhangi bir [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/downloads/) sürümü
- Linux/MacOS kullanıyorsanız bu uygulama, [Mono](https://www.mono-project.com/) kullanılarak çalıştırılabilir.
- [Bing özel arama](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet paketi. 
    - Visual Studio 'daki **Çözüm Gezgini** , projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` paketini yükleyin. NuGet Özel Arama paketini yüklediğinizde aşağıdaki derlemeler de yüklenir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. Visual Studio 'da yeni bir C# konsol uygulaması oluşturun. Ardından projenize aşağıdaki paketleri ekleyin.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Uygulamanızın ana yönteminde API anahtarınızla Search Client örneğini oluşturun.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Arama isteğini gönderme ve yanıt alma
    
1. İstemci yöntemini kullanarak bir arama sorgusu gönderin `SearchAsync()` ve yanıtı kaydedin. Dosyanızı `YOUR-CUSTOM-CONFIG-ID` örneğinizin yapılandırma kimliğiyle değiştirdiğinizden emin olun (kimliği [Bing özel arama portalında](https://www.customsearch.ai/)bulabilirsiniz). Bu örnek, "Xbox" için arama yapar.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` metodu bir `WebData` nesnesi döndürür. Bulduğu her türlü yinelemek için nesnesini kullanın `WebPages` . Bu kod ilk web sayfası sonucunu bulur ve web sayfasının `Name` ile `URL` değerlerini yazdırır.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel bir arama Web uygulaması oluşturma](../../tutorials/custom-search-web-page.md)
