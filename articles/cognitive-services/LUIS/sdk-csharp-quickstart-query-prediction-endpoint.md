---
title: 'Hızlı Başlangıç C# : SDK sorgu tahmin uç noktası-lusıs'
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler LUIN uygulamasına bir Kullanıcı alıp göndermek ve tahmin almak için C# SDK 'nın nasıl kullanılacağı gösterilir.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f4612f7b3f76cbbfc0deac98668770f92ff054bc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953420"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>Hızlı başlangıç: .NET SDK ile C# v2 tahmin uç noktası sorgulama

Language Understanding (LUSıS) kullanıcısına bir Kullanıcı alıp göndermek ve kullanıcının amaç tahminini almak için [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)üzerinde bulunan .NET SDK 'sını kullanın. 

Bu hızlı başlangıç, `turn on the bedroom light`gibi bir kullanıcıyı ortak Language Understanding uygulamasına gönderir ve tahmin alır ve en yüksek skor amacını `HomeAutomation.TurnOn` ve varlık içinde bulunan `HomeAutomation.Room` varlığı görüntüler. 

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio Community 2017 sürümü](https://visualstudio.microsoft.com/vs/community/)
* C# programlama dili (VS Community 2017 sürümünde bulunur)
* Genel uygulama kimliği: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> Tam çözüm, bilişsel [Hizmetler-dil tarafından anlaşılmayan](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub deposundan bulunabilir.

Daha fazla belge mi arıyorsunuz?

 * [SDK başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Bilişsel hizmetler veya Language Understanding anahtarı al

Ana otomasyon için ortak uygulamayı kullanmak üzere uç nokta tahminleri için geçerli bir anahtara ihtiyacınız vardır. Birçok bilişsel hizmet veya bir `Language Understanding` anahtarı için geçerli olan bilişsel hizmetler anahtarını (Azure CLı ile oluşturulan) kullanabilirsiniz. 

Bilişsel [hizmet anahtarı oluşturmak için aşağıdaki Azure CLI komutunu](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)kullanın:

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core projesi oluştur

Visual Studio Community 2017 ' de bir .NET Core konsol projesi oluşturun.

1. Visual Studio Community 2017 ' i açın.
1. Yeni bir proje oluşturun, **görsel C#**  bölümünde **konsol uygulaması (.NET Core)** öğesini seçin.
1. `QueryPrediction`proje adını girin, kalan varsayılan değerleri bırakın ve **Tamam**' ı seçin.
    Bu, **program.cs**adlı birincil kod dosyası ile basit bir proje oluşturur.

## <a name="add-sdk-with-nuget"></a>NuGet ile SDK ekleme

1. **Çözüm Gezgini**, **Queryprediction**adlı ağaç görünümünde projeyi seçin ve sağ tıklayın. Menüden **NuGet Paketlerini Yönet...** seçeneğini belirleyin.
1. **Gözden** geçirme ' yi seçin ve `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`girin. Paket bilgileri görüntülendiğinde, paketi projeye yüklemek için **yükler** ' i seçin. 
1. Aşağıdaki _using_ deyimlerini **program.cs**üst kısmına ekleyin. `System`için var olan _using_ ifadesini kaldırmayın. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Tahmin için yeni bir yöntem oluşturun

Sorguyu sorgu tahmin uç noktasına göndermek için yeni bir yöntem `GetPrediction` oluşturun. Yöntemi, gerekli tüm nesneleri oluşturur ve yapılandırır ve ardından [`LuisResult`](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.luisresult) tahmin sonuçlarıyla bir `Task` döndürür. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Kimlik bilgileri nesnesi oluştur

Bilişsel hizmet anahtarınızla istemci kimlik bilgilerini oluşturmak için `GetPrediction` yöntemine aşağıdaki kodu ekleyin.

`<REPLACE-WITH-YOUR-KEY>`, bilişsel hizmet anahtarınızın bölgesiyle değiştirin. Anahtar, bu kaynak için anahtarlar sayfasında [Azure Portal](https://portal.azure.com) .

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding istemcisi oluşturma

`GetPrediction` yönteminde, yukarıdaki koddan sonra, [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) istemci nesnesi oluşturmak için aşağıdaki kodu yeni kimlik bilgilerini kullanacak şekilde ekleyin. 

`<REPLACE-WITH-YOUR-KEY-REGION>`, `westus`gibi anahtarın bölgesiyle değiştirin. Bu kaynak için genel bakış sayfasında anahtar bölgesi [Azure Portal](https://portal.azure.com) .

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Sorgu parametrelerini ayarla

`GetPrediction` yönteminde, yukarıdaki koddan sonra sorgu parametrelerini ayarlamak için aşağıdaki kodu ekleyin.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Sorgu tahmin uç noktası

`GetPrediction` yönteminde, yukarıdaki koddan sonra sorgu parametrelerini ayarlamak için aşağıdaki kodu ekleyin:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Tahmin sonuçlarını görüntüleme

Yeni `GetPrediction` yöntemini çağırmak ve tahmin sonuçlarını döndürmek için **Main** yöntemini değiştirin:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Projeyi çalıştırma

Projeyi Studio 'da derleyin ve sorgu çıktısını görmek için projeyi çalıştırın:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Sonraki adımlar

[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) ve [.net başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)hakkında daha fazla bilgi edinin. 

> [!div class="nextstepaction"] 
> [Öğretici: Kullanıcı amaçları 'nı öğrenmek için LUSıS uygulaması oluşturma](luis-quickstart-intents-only.md) 
