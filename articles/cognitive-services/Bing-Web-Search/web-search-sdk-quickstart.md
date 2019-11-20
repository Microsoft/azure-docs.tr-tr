---
title: "Hızlı Başlangıç: İçin Bing Web Araması SDK 'sını kullanınC#"
titleSuffix: Azure Cognitive Services
description: Bing Web Araması SDK'sı, Bing Web Araması özelliklerini C# uygulamanızla tümleştirmeyi kolaylaştırır. Bu hızlı başlangıçta istemci başlatmayı, istek göndermeyi ve yanıtı yazdırmayı öğreneceksiniz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: aahi
ms.openlocfilehash: 5e893fda5a0e71703eb867c045de19d391c012ed
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018221"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Hızlı Başlangıç: İçin Bing Web Araması SDK 'sını kullanınC#

Bing Web Araması SDK'sı, Bing Web Araması özelliklerini C# uygulamanızla tümleştirmeyi kolaylaştırır. Bu hızlı başlangıçta istemci başlatmayı, istek göndermeyi ve yanıtı yazdırmayı öğreneceksiniz.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Ayrıca bkz: [Bilişsel hizmetler fiyatlandırması - Bing arama API'si](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıcı çalıştırmak için aşağıdakilere ihtiyacınız olacaktır:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) veya
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [Visual Studio Code için C#](https://visualstudio.microsoft.com/downloads/)
  * [NuGet Paket Yöneticisi](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Proje oluşturma ve bağımlılıkları yükleme

> [!TIP]
> Güncel kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)'dan Visual Studio çözümü olarak alın.

İlk adım yeni bir konsol projesi oluşturmaktır. Bir konsol projesi ayarlamayla ilgili yardıma ihtiyacınız varsa, bkz. [Merhaba Dünya--Ilk programınız (C# Programlama Kılavuzu)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Bing Web Araması SDK'sını uygulamanızda kullanmak için NuGet Paket Yöneticisi'ni kullanarak `Microsoft.Azure.CognitiveServices.Search.WebSearch` paketini yüklemeniz gerekir.

[Web Arama SDK'sı paketi](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) şunları da yükler:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Bağımlılıkları tanımlama

Projenizi Visual Studio veya Visual Studio Code ile açıp şu bağımlılıkları içeri aktarın:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Proje yapı iskelesini oluşturma

Yeni konsol projenizi oluşturduğunuzda uygulamanız için bir ad alanı ve sınıf da oluşturulmuş olmalıdır. Programınız şu örnekteki gibi görünmelidir:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

Aşağıdaki bölümlerde örnek uygulamamızı bu sınıfı kullanarak derleyeceğiz.

## <a name="construct-a-request"></a>İstek oluşturma

Bu kod, arama sorgusunu oluşturur.

```csharp
public static async void WebResults(WebSearchClient client)
{
    try
    {
        var webData = await client.Web.SearchAsync(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Yanıtı işleme

Şimdi yanıtı ayrıştırmak ve sonuçları yazdırmak için kod ekleyelim. Yanıt nesnesinde mevcutsa ilk web sayfası, görüntü, haber ve video için `Name` ile `Url` değeri yazdırılır.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>main metodunu tanımlama

Bu uygulamada main metodu istemciyi başlatan, `subscriptionKey` değerini doğrulayan ve `WebResults` çağrısı yapan kodu içerir. Devam etmeden önce Azure hesabınız için geçerli bir abonelik anahtarı girdiğinizden emin olun.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Şimdi uygulamayı çalıştıralım!

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>İşlevleri tanımlama ve sonuçları filtreleme

Bing Web Araması API'sine ilk çağrınızı gönderdiniz. Şimdi sorguları daha ayrıntılı hale getirmek ve sonuçları filtrelemek için kullanabileceğiniz SDK işlevlerine göz atalım. Tüm işlevleri önceki bölümde oluşturulan C# uygulamanıza ekleyebilirsiniz.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Bing tarafından döndürülen sonuç sayısını sınırlama

Bu örnekte "Best restaurants in Seattle" (Seattle'daki en iyi restoranlar) araması için döndürülen sonuçları sınırlandırmak için `count` ve `offset` parametreleri kullanılmıştır. İlk sonucun `Name` ve `Url` değerleri yazdırılır.

1. Konsol projenize şu kodu ekleyin:

    ```csharp
    public static async void WebResultsWithCountAndOffset(WebSearchClient client)
    {
        try
        {
            var webData = await client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20);
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `main` içine `WebResultsWithCountAndOffset` ekleyin:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uygulamayı çalıştırın.

### <a name="filter-for-news"></a>Haberler için filtre

Bu örnekte arama sonuçlarını filtrelemek için `response_filter` parametresi kullanılmıştır. Döndürülen arama sonuçları "Microsoft" haberleriyle sınırlandırılmıştır. İlk sonucun `Name` ve `Url` değerleri yazdırılır.

1. Konsol projenize şu kodu ekleyin:

    ```csharp
    public static async void WebSearchWithResponseFilter(WebSearchClient client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = await client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings);
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `main` içine `WebResultsWithCountAndOffset` ekleyin:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uygulamayı çalıştırın.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Güvenli arama, yanıt sayısı ve yükseltme filtresini kullanma

Bu örnekte "Music Videos" (Müzik Videoları) aramasının sonuçlarını filtrelemek için `answer_count`, `promote` ve `safe_search` parametreleri kullanılmıştır. İlk sonucun `Name` ve `ContentUrl` değerleri görüntülenir.

1. Konsol projenize şu kodu ekleyin:

    ```csharp
    public static async void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchClient client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = await client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict);
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `main` içine `WebResultsWithCountAndOffset` ekleyin:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uygulamayı çalıştırın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu projeyi tamamladıktan sonra abonelik anahtarınızı uygulama kodundan kaldırmayı unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK'sı örnekleri](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
