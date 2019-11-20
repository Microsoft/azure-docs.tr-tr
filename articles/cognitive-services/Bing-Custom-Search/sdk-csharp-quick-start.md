---
title: "Hızlı Başlangıç: C# SDK 'yı kullanarak Bing özel arama uç noktanızı çağırın | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: C# SDK kullanarak Bing özel arama örneğinizi arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: scottwhi
ms.openlocfilehash: c7ac6d051c8333a6329a3c2ed238d78fb9da4a30
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565710"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Hızlı Başlangıç: C# SDK kullanarak Bing özel arama uç noktanızı çağırma 

C# SDK 'yı kullanarak Bing özel arama örneğinden arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın. Bing Özel Arama birçok programlama dili ile uyumlu bir REST API sahip olsa da Bing Özel Arama SDK, hizmeti uygulamalarınızla tümleştirmenin kolay bir yolunu sunar. Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)' da bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

- Bir Bing Özel Arama örneği. Bkz [. hızlı başlangıç: Daha fazla bilgi için ilk](quick-start.md) Bing özel arama örneğinizi oluşturun.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Herhangi bir [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/downloads/) sürümü
- Linux/MacOS kullanıyorsanız bu uygulama, [Mono](https://www.mono-project.com/) kullanılarak çalıştırılabilir.
- [Bing özel arama](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet paketi. 
    - Visual Studio 'daki **Çözüm Gezgini** , projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` paketini yükleyin. NuGet Özel Arama paketini yüklediğinizde aşağıdaki derlemeler de yüklenir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. Visual Studio 'da C# yeni bir konsol uygulaması oluşturun. Ardından projenize aşağıdaki paketleri ekleyin.

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
    
1. İstemci `SearchAsync()` yöntemini kullanarak bir arama sorgusu gönderin ve yanıtı kaydedin. Dosyanızı `YOUR-CUSTOM-CONFIG-ID` örneğinizin yapılandırma kimliğiyle değiştirdiğinizden emin olun (kimliği [Bing özel arama portalında](https://www.customsearch.ai/)bulabilirsiniz). Bu örnek, "Xbox" için arama yapar.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` metodu bir `WebData` nesnesi döndürür. Bulduğu her türlü `WebPages` yinelemek için nesnesini kullanın. Bu kod ilk web sayfası sonucunu bulur ve web sayfasının `Name` ile `URL` değerlerini yazdırır.

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
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
