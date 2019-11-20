---
title: Imageınsibustoken-Bing Görsel Arama kullanarak önceki aramalardan benzer görüntüleri bulma
titleSuffix: Azure Cognitive Services
description: Imageınsi, Stoken tarafından belirtilen görüntülerin URL 'Lerini almak için Bing Görsel Arama SDK 'sını kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 5f4faa290fe4ed02ab1ed75d23755af5dc20f215
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880602"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Imageınsilım sToken kullanarak önceki aramalardan benzer görüntüleri bulma

Görsel Arama SDK, önceki aramalardan `ImageInsightsToken`resimleri çevrimiçi olarak bulmanızı sağlar. Bu uygulama bir `ImageInsightsToken` alır ve sonraki aramada belirtecini kullanır. Daha sonra bunu Bing `ImageInsightsToken` 'e gönderir ve Bing arama URL 'leri ve çevrimiçi bulunan benzer görüntülerin URL 'lerini içeren sonuçları döndürür.

Bu öğreticinin tam kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs)üzerinde ek hata işleme ve ek açıklamalarla bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

* Herhangi bir [Visual Studio 2019](https://www.visualstudio.com/downloads/)sürümü.
* Linux/MacOS kullanıyorsanız bu uygulamayı [mono](https://www.mono-project.com/)kullanarak çalıştırabilirsiniz.
* NuGet Görsel Arama ve Resim Arama paketleri.
    - Visual Studio 'daki Çözüm Gezgini projenize sağ tıklayın ve menüden **NuGet Paketlerini Yönet** ' i seçin. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` Paketini`Microsoft.Azure.CognitiveServices.Search.ImageSearch` ve paketini yükler. NuGet paketlerini yüklemek aşağıdakileri de yapar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Bing Resim Arama SDK 'dan ımageınsibir sToken alın

Bu uygulama, `ImageInsightsToken` [Bing resim arama SDK 'sı](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)aracılığıyla elde edilen bir kullanır. Yeni C# bir konsol uygulamasında API 'yi kullanarak `ImageSearchClient()`çağırmak için bir istemci oluşturun. Sonra Sorgunuzla `SearchAsync()` birlikte kullanın:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

' Yi kullanarak `imageResults.Value.First()`ilk arama sonucunu depolayın ve ardından görüntü öngörülerini `ImageInsightsToken`saklayın.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Bu `ImageInsightsToken` , bir istekte Bing Görsel Arama gönderilir.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Görsel Arama isteğine ımageınsibir sToken ekleyin

Bing Görsel Arama yanıtlarındaki yanıtlardan `ImageInsightsToken` bulunan öğesinden bir `ImageInfo` nesne oluşturarak görsel arama isteği içinbelirtin.`ImageInsightsToken`

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Imageınsilım sToken görüntülerini bulmak için Bing Görsel Arama kullanma

Nesnesi, aranacağı içindeki `ImageInfo` görüntüyle ilgili bilgiler içerir. `VisualSearchRequest` `VisualSearchMethodAsync()` yöntemi sonuçları alır. Görüntü, belirteç tarafından temsil edildiği için bir görüntü ikilisi sağlamanız gerekmez.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Görsel Arama sonuçları boyunca yineleme

Görsel Arama sonuçları `ImageTag` nesneleridir. Her etiket bir `ImageAction` nesneleri listesi içerir. Her `ImageAction` biri, `Data` eylem türüne bağlı değerlerin listesi olan bir alan içerir. `ImageAction` İçindeki `ImageTag` nesneler`visualSearchResults.Tags`üzerinde yineleyebilirsiniz ve içindeki etiketi alabilirsiniz. Aşağıdaki örnekte `PagesIncluding` eylemlerin ayrıntıları yazdırılır:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>ActionTypes dahil Pagestypes

Eylem türlerinden gerçek görüntü URL 'lerinin alınması, bir değer listesi içeren bir `ActionType` `Data` öğe içeren `ImageModuleAction`bir olarak okuyan bir tür dönüştürme gerektirir. Her değer, bir görüntünün URL’sidir.  Aşağıdaki `PagesIncluding` eylem türünü öğesine `ImageModuleAction` yayınlar ve değerleri okur:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Bu veri türleri hakkında daha fazla bilgi için bkz. [Görüntüler - Görsel Arama](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Döndürülen URL 'Ler

Uygulamanın tamamı aşağıdaki URL 'Leri döndürür:

|EylemTürü  |URL  | |
|---------|---------|---------|
|Moyeniden boyutlanıyor-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|Imagebyıd-> WebSearchUrl    |         |
|Relatedaramalarını-> WebSearchUrl:    |         |
|Documentlevelönerilere-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3A%2F%2Fwww.Bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|Imageresults-> WebSearchUrl    |  https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3A%2F%2Fwww.Bing.com%2fimages%2fsearch%3Fq%3doutdoor&p=DevEx,5831.1       |

Yukarıda gösterildiği gibi, `TopicResults` ve `ImageResults` türleri ilgili görüntüler için sorgular içerir. URL 'Ler Bing arama sonuçlarına bağlanır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Görsel Arama tek sayfalı Web uygulaması oluşturma](tutorial-bing-visual-search-single-page-app.md)
