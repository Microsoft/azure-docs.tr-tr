---
title: .NET-Content Moderator kullanarak denetleme işlerini kullanma
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator görüntü veya metin içeriğine yönelik uçtan uca içerik denetleme işleri başlatmak için Content Moderator .NET SDK 'sını kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: bdc9e8cbea77b504cfdc2c3b1c83c74ea4ab8ed1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935969"
---
# <a name="define-and-use-moderation-jobs-net"></a>Denetleme işlerini tanımlama ve kullanma (.NET)

Bir denetleme işi, içerik denetleme, iş akışları ve İncelemeler işlevleri için bir sarmalayıcı türü görevi görür. Bu kılavuzda, [.NET için Content moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 'yı kullanmaya başlamanıza yardımcı olacak bilgiler ve kod örnekleri sunulmaktadır:

- Tarama ve insan denetimciler için incelemeler oluşturma için bir denetim işi başlatma
- Bekleyen incelemenin durumunu alma
- İncelemeyi izleme ve son durumunu alma
- İnceleme sonuçlarını geri çağırma URL 'sine gönder

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>API anahtarınızın inceleme oluşturma amacıyla inceleme API'sini çağırabildiğinden emin olun

Önceki adımları tamamladıktan sonra, başlangıcı Azure portaldan yaptıysanız şu anda iki Content Moderator anahtarınız olmalıdır.

SDK örneğinizde Azure tarafından sağlanan API anahtarını kullanmayı planlıyorsanız, uygulamanızın inceleme API’sini çağırmasına ve incelemeler oluşturmasına izin vermek için [inceleme API'siyle Azure anahtarını kullanma](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) bölümünde anlatılan adımları izleyin.

İnceleme aracı tarafından oluşturulan ücretsiz deneme anahtarını kullanırsanız, inceleme aracı hesabınız anahtarı zaten tanıyordur ve bu nedenle ek bir adım gerekli değildir.

## <a name="define-a-custom-moderation-workflow"></a>Özel bir denetim iş akışı tanımlama

Bir denetimi işi, API'leri kullanarak içeriğinizi tarar ve inceleme oluşturmak gerekip gerekmediğini belirlemek için bir **iş akışı** kullanır.
İnceleme aracı varsayılan bir iş akışı içermekle birlikte bu hızlı başlangıç için [özel bir iş akışı tanımlayacağız](Review-Tool-User-Guide/Workflows.md).

İş akışının adını denetim işini başlatan kodunuzda kullanırsınız.

## <a name="create-your-visual-studio-project"></a>Visual Studio projenizi oluşturun

1. Çözümünüze yeni bir **Konsol uygulaması (.NET Framework)** projesi ekleyin.

   Örnek kodda, projeyi **CreateReviews** olarak adlandırın.

1. Bu projeyi çözümün tekil başlangıç projesi olarak seçin.

### <a name="install-required-packages"></a>Gerekli paketleri yükleme

Aşağıdaki NuGet paketlerini yükleyin:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Programı deyimler kullanarak güncelleştirme

Programı deyimler kullanarak değiştirin.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator istemcisini oluşturma

Aboneliğiniz için bir Content Moderator istemcisi oluşturmak üzere aşağıdaki kodu ekleyin.

> [!IMPORTANT]
> **AzureEndpoint** ve **cmsubscriptionkey** alanlarını Endpoint URL 'nizin ve abonelik anahtarınızın değerleriyle güncelleştirin.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Uygulamaya özgü ayarları başlatma

Aşağıdaki sabitleri ve statik alanları Program.cs dosyasında **Program** sınıfına ekleyin.

> [!NOTE]
> TeamName sabitini, Content Moderator aboneliğinizi oluştururken kullandığınız ada ayarlayın. TeamName değerini [Content Moderator Web sitesinden](https://westus.contentmoderator.cognitive.microsoft.com/) alın.
> Oturum açtıktan sonra **Ayarlar** (araçlar) menüsünden **Kimlik Bilgileri**'ni seçin.
>
> Takım adınız **API** bölümündeki **Id** alanının değeridir.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Otomatik denetim, denetim oluşturma ve iş ayrıntılarını alma için kod ekleme

> [!Note]
> Uygulamada, geri çağırma URL'si olan **CallbackEndpoint** değişkenini doğrudan yapılan incelemenin sonuçlarını (bir HTTP POST isteği aracılığıyla) alan URL'ye ayarlarsınız.

Aşağıdaki kodu **Main** yöntemine ekleyerek başlayın.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> Content Moderator hizmet anahtarınızın saniyede istek sayısı (RPS) hız sınırı vardır. Sınırı aşarsanız, SDK 429 hata koduyla bir özel durum oluşturulur.
>
> Ücretsiz katmanı anahtarı bir RPS’lik hız sınırına sahiptir.

## <a name="run-the-program-and-review-the-output"></a>Programı çalıştırma ve çıktıyı gözden geçirme

Konsolda şu örnek çıktıyı görürsünüz:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Bekleyen görüntü incelemesini görmek için Content Moderator inceleme aracında oturum açın.

Göndermek için **İleri** düğmesini kullanın.

![İnsan moderatörler için görüntü incelemesi](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Günlük dosyasında örnek çıktıyı görme

> [!NOTE]
> Çıktı dosyanızda, **Teamname**, **ContentId**, **CallBackEndpoint** ve **WorkflowId** dizeleri daha önce kullandığınız değerleri yansıtır.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Belirtilmişse geri arama Url’niz bu yanıtı alır

Aşağıdaki örneğe benzer bir yanıt alırsınız:

> [!NOTE]
> Geri çağırma yanıtınızda **ContentId** ve **WorkflowId** dizeleri daha önce kullandığınız değerleri yansıtır.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu ve diğer .NET için Content Moderator hızlı başlangıçları için [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ve [Visual Studio çözümünü](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) edinin ve tümleştirmeniz üzerinde çalışmaya başlayın.
