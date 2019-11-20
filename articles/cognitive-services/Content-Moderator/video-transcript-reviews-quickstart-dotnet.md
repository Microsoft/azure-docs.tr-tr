---
title: .NET-Content Moderator kullanarak video dökümü oluşturma İncelemeleri oluşturun
titleSuffix: Azure Cognitive Services
description: .NET için Azure bilişsel hizmetler Content Moderator SDK 'sını kullanarak video dökümü oluşturma gözden geçirmeleri oluşturmayı öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744383"
---
# <a name="create-video-transcript-reviews-using-net"></a>.NET kullanarak video dökümü oluşturma İncelemeleri oluşturun

Bu makalede, [ C# ile Content moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 'yı kullanmaya hızlı bir şekilde başlamanıza yardımcı olacak bilgiler ve kod örnekleri sunulmaktadır:

- İnsan moderatör için video incelemesi oluşturma
- İncelemeye aracılı bir döküm ekleyin
- İncelemeyi Yayımla

## <a name="prerequisites"></a>Ön koşullar

- Daha önce yapmadıysanız Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.
- Bu makalede [, videoyu](video-moderation-api.md) dağıttığınız ve insan kararı verme için gözden geçirme aracında [video incelemesini oluşturmuş](video-reviews-quickstart-dotnet.md) olduğunuz varsayılmaktadır. Şimdi gözden geçirme aracında aracılı video dökümü eklemek istiyorsunuz.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>API anahtarınızın İnceleme API 'sini (Iş oluşturma) çağırabağlanabildiğinden emin olun

Önceki adımları tamamladıktan sonra, başlangıcı Azure portaldan yaptıysanız şu anda iki Content Moderator anahtarınız olmalıdır.

SDK örneğinizde Azure tarafından sağlanan API anahtarını kullanmayı planlıyorsanız, uygulamanızın inceleme API’sini çağırmasına ve incelemeler oluşturmasına izin vermek için [inceleme API'siyle Azure anahtarını kullanma](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) bölümünde anlatılan adımları izleyin.

İnceleme aracı tarafından oluşturulan ücretsiz deneme anahtarını kullanırsanız, inceleme aracı hesabınız anahtarı zaten tanıdığından ek bir adım gerekmez.

## <a name="prepare-your-video-for-review"></a>Videonuzu gözden geçirilmek üzere hazırlama

Bir video incelemesinin dökümünü ekleyin. Videonun çevrimiçi yayımlanması gerekir. Akış uç noktasına ihtiyacınız vardır. Akış uç noktası, gözden geçirme aracının video yürütücüsünün videoyu oynamasını sağlar.

![Video tanıtımı küçük resmi](images/ams-video-demo-view.PNG)

- Bildirim URL 'SI için bu [Azure Media Services demo](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) sayfasında **URL 'yi** kopyalayın.

## <a name="create-your-visual-studio-project"></a>Visual Studio projenizi oluşturun

1. Çözümünüze yeni bir **Console uygulaması (.NET Framework)** projesi ekleyin.

1. Projeyi **Videotranscriptreviews**olarak adlandırın.

1. Bu projeyi çözümün tek başlatma projesi olarak seçin.

### <a name="install-required-packages"></a>Gerekli paketleri yükleme

TermLists projesi için aşağıdaki NuGet paketlerini yükler.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Programı deyimler kullanarak güncelleştirme

Programın using deyimlerini aşağıdaki şekilde değiştirin.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Özel özellikler ekleme

Aşağıdaki özel özellikleri **Videotranscriptreviews**, Class **Program**ad alanına ekleyin. `AzureEndpoint` ve `CMSubscriptionKey` alanlarını Endpoint URL 'nizin ve abonelik anahtarınızın değerleriyle güncelleştirin. Bunları, Azure portal kaynağınızın **hızlı başlangıç** sekmesinde bulabilirsiniz.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Content Moderator Istemci nesnesi oluştur

Aşağıdaki yöntem tanımını, VideoTranscriptReviews, Class program ad alanına ekleyin.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Video incelemesi oluşturma

**Contentmoderatorclient. İncelemeleri. Createvideoincelemeleri**ile bir video incelemesi oluşturun. Daha fazla bilgi için bkz. [API başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**Createvideoincelemeleri** aşağıdaki gerekli parametrelere sahiptir:
1. "Application/JSON" olması gereken bir MIME türü içeren bir dize. 
1. Content Moderator takım adınız.
1. Bir **ılist\<Createvideobelgesbodyıtem >** nesnesi. Her **Createvideoreview Sbodyıtem** nesnesi bir video incelemesini temsil eder. Bu hızlı başlangıçta tek seferde bir gözden geçirme oluşturulur.

**Createvideo, Sbodyıtem** 'ın birkaç özelliği vardır. En azından, aşağıdaki özellikleri ayarlarsınız:
- **İçerik**. Gözden geçirilecek videonun URL 'SI.
- **ContentID**. Video incelemeye atanacak bir KIMLIK.
- **Durum**. Değeri "yayımdan kaldırıldı" olarak ayarlayın. Bunu yapmazsanız, varsayılan olarak "bekliyor" olarak ayarlanır; Bu, video incelemesinin yayımlandığı ve insan incelemesi bekleyen bir anlamına gelir. Video incelemesi yayımlandıktan sonra artık video çerçeveleri, bir döküm dosyası veya bir döküm denetimi sonucu ekleyemezsiniz.

> [!NOTE]
> **Createvideoincelemeleri** bir IList\<dize > döndürür. Bu dizelerin her biri video incelemesi için bir KIMLIK içerir. Bu kimlikler GUID 'lerdir ve **ContentID** özelliğinin değeriyle aynı değildir.

Aşağıdaki yöntem tanımını ad alanı Videoincelemeleri, sınıf programına ekleyin.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Content Moderator hizmet anahtarınızın saniyede istek sayısı (RPS) hız sınırı vardır. Sınırı aşarsanız, SDK 429 hata koduyla bir özel durum oluşturulur.
>
> Ücretsiz katman anahtarı bir RPS'lik hız sınırına sahiptir.

## <a name="add-transcript-to-video-review"></a>Video incelemesinin dökümünü ekleyin

**Contentmoderatorclient. İncelemeleri. AddVideoTranscript**ile bir video incelemelerine bir TRANSCRIPT eklersiniz. **Addvideotranscript** aşağıdaki gerekli parametrelere sahiptir:
1. Content Moderator takım KIMLIĞINIZ.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.
1. Dökümü içeren bir **Stream** nesnesi.

TRANSCRIPT, WebVTT biçiminde olmalıdır. Daha fazla bilgi için bkz. [WEBVTT: web video metni Izler biçimi](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program, VTT biçiminde bir örnek TRANSCRIPT kullanır. Gerçek dünyada bir çözümde, bir videodan döküm [oluşturmak](https://docs.microsoft.com/azure/media-services/media-services-index-content) için Azure Media Indexer hizmetini kullanırsınız.

Aşağıdaki yöntem tanımını, VideotranscriptReviews, Class program ad alanına ekleyin.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Video incelemeye döküm denetimi sonucu ekleme

Bir video incelemeye el ile döküm eklemenin yanı sıra, bu dökümü moderonuzun sonucunu da eklersiniz. Bunu **Contentmoderatorclient. İncelemeleri. AddVideoTranscriptModerationResult**ile yapabilirsiniz. Daha fazla bilgi için bkz. [API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**Addvideotranscriptmoderationresult** aşağıdaki gerekli parametrelere sahiptir:
1. "Application/JSON" olması gereken bir MIME türü içeren bir dize. 
1. Content Moderator takım adınız.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.
1. Bir IList\<Transcriptmoderationbodyıtem >. **Transcriptmoderationbodyıtem** aşağıdaki özelliklere sahiptir:
1. **Koşullar**. Bir IList\<TranscriptModerationBodyItemTermsItem >. Bir **TranscriptModerationBodyItemTermsItem** aşağıdaki özelliklere sahiptir:
1. **Dizin**. Terimin sıfır tabanlı dizini.
1. **Terim**. Terimi içeren bir dize.
1. **Zaman damgası**. Koşulların bulunduğu döküm içindeki zamanı saniye cinsinden içeren bir dize.

TRANSCRIPT, WebVTT biçiminde olmalıdır. Daha fazla bilgi için bkz. [WEBVTT: web video metni Izler biçimi](https://www.w3.org/TR/webvtt1/).

Aşağıdaki yöntem tanımını, VideoTranscriptReviews, Class program ad alanına ekleyin. Bu yöntem, **contentmoderatorclient. textmoder. screentext** yöntemine bir döküm gönderir. Ayrıca, sonucu bir IList\<Transcriptmoderationbodyıtem > olarak çevirir ve **Addvideotranscriptmoderationresult**' a gönderir.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Video incelemesi Yayımla

**Contentmoderatorclient. İncelemeleri. PublishVideoReview**ile bir video incelemesi yayımlarsınız. **Publishvideoreview** aşağıdaki gerekli parametrelere sahiptir:
1. Content Moderator takım adınız.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.

Aşağıdaki yöntem tanımını ad alanı Videoincelemeleri, sınıf programına ekleyin.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Hepsini bir araya getirme

**Ana** yöntem tanımını, VideoTranscriptReviews, Class program ad alanına ekleyin. Son olarak, program sınıfını ve VideoTranscriptReviews ad alanını kapatın.

> [!NOTE]
> Program, VTT biçiminde bir örnek TRANSCRIPT kullanır. Gerçek dünyada bir çözümde, bir videodan döküm [oluşturmak](https://docs.microsoft.com/azure/media-services/media-services-index-content) için Azure Media Indexer hizmetini kullanırsınız.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Programı çalıştırma ve çıktıyı gözden geçirme

Uygulamayı çalıştırdığınızda, aşağıdaki satırlarda bir çıktı görürsünüz:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Video dökümü gözden geçirme 'nize gidin

>**video**>**dökümünü** **gözden geçir** ekranında Content moderator gözden geçirme aracındayken video dökümü inceleme sayfasına gidin.

Aşağıdaki özellikleri görürsünüz:
- Eklediğiniz iki satır dökümü
- Metin denetleme hizmeti tarafından bulunan ve vurgulanan küfür terimi
- Bir döküm metni seçildiğinde bu zaman damgasından video başlatılır

![İnsan moderatör için video dökümü incelemesi](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Sonraki adımlar

.NET için bu ve diğer Content Moderator hızlı başlangıçlara yönelik [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ve [Visual Studio çözümü](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) alın.

İnceleme aracında [video İncelemeleri](video-reviews-quickstart-dotnet.md) oluşturmayı öğrenin.

[Tüm video denetleme çözümünü](video-transcript-moderation-review-tutorial-dotnet.md)geliştirme hakkında ayrıntılı öğreticiye göz atın.
