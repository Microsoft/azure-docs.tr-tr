---
title: .NET-Content Moderator kullanarak video incelemeleri oluşturma
titleSuffix: Azure Cognitive Services
description: Bu makalede, video İncelemeleri oluşturmak için ile C# Content moderator SDK 'sını kullanmaya hızlı bir şekilde başlamanıza yardımcı olacak bilgiler ve kod örnekleri sağlanmaktadır.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931668"
---
# <a name="create-video-reviews-using-net"></a>.NET kullanarak video incelemeleri oluşturma

Bu makalede, [ C# ile Content moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 'yı kullanmaya hızlı bir şekilde başlamanıza yardımcı olacak bilgiler ve kod örnekleri sunulmaktadır:

- İnsan moderatör için video incelemesi oluşturma
- Bir incelemeye kareler ekleme
- İnceleme için çerçeveleri al
- İnceleme durumunu ve ayrıntılarını alın
- İncelemeyi Yayımla

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.
- Bu makalede [, videoyu (bkz. hızlı başlangıç)](video-moderation-api.md) dağıttığınız ve yanıt verilerinin bulunduğu varsayılmaktadır. Bu, insan moderatör için çerçeve tabanlı incelemeler oluşturmak için gereklidir.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>API anahtarınızın inceleme oluşturma amacıyla inceleme API'sini çağırabildiğinden emin olun

Önceki adımları tamamladıktan sonra, başlangıcı Azure portaldan yaptıysanız şu anda iki Content Moderator anahtarınız olmalıdır.

SDK örneğinizde Azure tarafından sağlanan API anahtarını kullanmayı planlıyorsanız, uygulamanızın inceleme API’sini çağırmasına ve incelemeler oluşturmasına izin vermek için [inceleme API'siyle Azure anahtarını kullanma](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) bölümünde anlatılan adımları izleyin.

İnceleme aracı tarafından oluşturulan ücretsiz deneme anahtarını kullanırsanız, inceleme aracı hesabınız anahtarı zaten tanıyordur ve bu nedenle ek bir adım gerekli değildir.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Videonuzu ve video çerçevelerini gözden geçirilmek üzere hazırlama

URL 'Lerinin olması gerektiğinden, gözden geçirilmesi gereken video ve örnek video çerçeveleri çevrimiçi olarak yayımlanmalıdır.

> [!NOTE]
> Program, gözden geçirme API 'sinin kullanımını göstermek için rastgele yetişkin/racy puanlarını içeren videodan el ile kaydedilen ekran görüntülerini kullanır. Gerçek dünyada bir durumda, görüntü oluşturmak ve puanları atamak için [video denetleme çıktısını](video-moderation-api.md#run-the-program-and-review-the-output) kullanırsınız. 

Video için, gözden geçirme aracının Player görünümünde videoyu oynaması için bir akış uç noktası gerekir.

![Video tanıtımı küçük resmi](images/ams-video-demo-view.PNG)

- Bildirim URL 'SI için bu [Azure Media Services demo](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) sayfasında **URL 'yi** kopyalayın.

Video çerçeveleri (görüntüler) için aşağıdaki görüntüleri kullanın:

![Video çerçevesi küçük resmi 1](images/ams-video-frame-thumbnails-1.PNG) | ![Video çerçevesi küçük resmi 2](images/ams-video-frame-thumbnails-2.PNG) | ![Video çerçevesi küçük resmi 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Çerçeve 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Çerçeve 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Kare 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio projenizi oluşturun

1. Çözümünüze yeni bir **Konsol uygulaması (.NET Framework)** projesi ekleyin.

1. Projeyi **Videoincelemeleri**olarak adlandırın.

1. Bu projeyi çözümün tekil başlangıç projesi olarak seçin.

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

Aşağıdaki özel özellikleri, ad alanı **Videoincelemeleri**, sınıf **programına**ekleyin. `AzureEndpoint` ve `CMSubscriptionKey` alanlarını Endpoint URL 'nizin ve abonelik anahtarınızın değerleriyle güncelleştirin. Bunları, Azure portal kaynağınızın **hızlı başlangıç** sekmesinde bulabilirsiniz.


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

Aşağıdaki yöntem tanımını ad alanı **Videoincelemeleri**, sınıf **programına**ekleyin.

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
1. Bir **ılist \<CreateVideoReviewsBodyItem >** nesnesi. Her **Createvideoreview Sbodyıtem** nesnesi bir video incelemesini temsil eder. Bu hızlı başlangıçta tek seferde bir gözden geçirme oluşturulur.

**Createvideo, Sbodyıtem** 'ın birkaç özelliği vardır. En azından, aşağıdaki özellikleri ayarlarsınız:
- **İçerik**. Gözden geçirilecek videonun URL 'SI.
- **ContentID**. Video incelemeye atanacak bir KIMLIK.
- **Durum**. Değeri "yayımdan kaldırıldı" olarak ayarlayın. Bunu yapmazsanız, varsayılan olarak "bekliyor" olarak ayarlanır; Bu, video incelemesinin yayımlandığı ve insan incelemesi bekleyen bir anlamına gelir. Video incelemesi yayımlandıktan sonra artık video çerçeveleri, bir döküm dosyası veya bir döküm denetimi sonucu ekleyemezsiniz.

> [!NOTE]
> **Createvideoincelemeleri** bir ılist \<string > döndürür. Bu dizelerin her biri video incelemesi için bir KIMLIK içerir. Bu kimlikler GUID 'lerdir ve **ContentID** özelliğinin değeriyle aynı değildir. 

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
> Content Moderator hizmet anahtarınızın saniye başına istek (RPS) hız limiti vardır ve sınırı aşarsanız SDK 429 hata kodu ile bir özel durum oluşturur.
>
> Ücretsiz katmanı anahtarı bir RPS’lik hız sınırına sahiptir.

## <a name="add-video-frames-to-the-video-review"></a>Video incelemesine video çerçeveleri ekleme

**Contentmoderatorclient. Review. AddVideoFrameUrl** (video çerçeveleriyse çevrimiçi olarak barındırılıyorsa) veya **contentmoderatorclient. Review. AddVideoFrameStream** (video çerçeveleriz yerel olarak barındırılıyorsa) ile bir video incelemesine video çerçeveleri eklersiniz. Bu hızlı başlangıçta, video çerçevelerlerinizin çevrimiçi olarak barındırıldığı varsayılmaktadır ve bu nedenle **Addvideoframeurl 'si**kullanılır. Daha fazla bilgi için bkz. [API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**Addvideoframeurl** aşağıdaki gerekli parametrelere sahiptir:
1. "Application/JSON" olması gereken bir MIME türü içeren bir dize.
1. Content Moderator takım adınız.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.
1. Bir **ılist \<VideoFrameBodyItem >** nesnesi. Her **Videoframebodyıtem** nesnesi bir video çerçevesini temsil eder.

**Videoframebodyıtem** aşağıdaki özelliklere sahiptir:
- **Zaman damgası**. Video çerçevesinin alındığı videodaki süreyi saniye cinsinden içeren bir dize.
- **FrameImage**. Video çerçevesinin URL 'SI.
- **Meta veri**. Bir IList \<VideoFrameBodyItemMetadataItem >. **Videoframebodyıtemmetadataıtem** yalnızca bir anahtar/değer çiftidir. Geçerli anahtarlar şunlardır:
- **gözden geçirmeyi öneririz**. Video çerçevesinin insan incelemesi önerilse doğru.
- **adultScore**. Video çerçevesindeki yetişkinlere yönelik içeriğin önem derecesini derecelendiren, 0 ile 1 arasında bir değer.
- **a**. Video yetişkinlere yönelik içerik içeriyorsa doğru.
- **Oycyscore**. Video çerçevesindeki süslü içeriğin önem derecesini derecelendiren, 0 ile 1 arasında bir değer.
- **r**. Video çerçevesi, süslü içerik içeriyorsa doğru.
- **Reviewerresulttags**. Bir IList \<VideoFrameBodyItemReviewerResultTagsItem >. **VideoFrameBodyItemReviewerResultTagsItem** yalnızca bir anahtar/değer çiftidir. Bir uygulama, video çerçevelerini düzenlemek için bu etiketleri kullanabilir.

> [!NOTE]
> Bu hızlı başlangıç, **adultScore** ve **racyscore** özellikleri için rastgele değerler üretir. Bir üretim uygulamasında, bu değerleri bir Azure Media Service olarak dağıtılan [video denetleme hizmetinden](video-moderation-api.md)elde edersiniz.

Aşağıdaki yöntem tanımlarını ad alanı Videoincelemeleri, sınıf programına ekleyin.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Video incelemesi için video çerçeveleri al

Bir video incelemesi için, **Contentmoderatorclient. Review. GetVideoFrames**ile video çerçevelerini alabilirsiniz. **GetVideoFrames** aşağıdaki gerekli parametrelere sahiptir:
1. Content Moderator takım adınız.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.
1. Alınacak ilk video çerçevesinin sıfır tabanlı dizini.
1. Alınacak video çerçevelerinin sayısı.

Aşağıdaki yöntem tanımını ad alanı Videoincelemeleri, sınıf programına ekleyin.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Video inceleme bilgilerini al

**Contentmoderatorclient. İncelemeleri. GetReview**ile bir video incelemesinin bilgilerini alacaksınız. **Getreview** aşağıdaki gerekli parametrelere sahiptir:
1. Content Moderator takım adınız.
1. **Createvideoincelemeleri**tarafından döndürülen VIDEO İnceleme kimliği.

Aşağıdaki yöntem tanımını ad alanı Videoincelemeleri, sınıf programına ekleyin.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

**Ana** yöntem tanımını ad alanı videoincelemeleri, sınıf programına ekleyin. Son olarak, program sınıfını ve Videoincelemeleri ad alanını kapatın.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

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

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Video incelemeniz için göz atın

Son olarak, **Gözden**geçirme >**video** ekranında Content moderator gözden geçirme araç hesabınızda video incelemesini görürsünüz.

![İnsan moderatör için video incelemesi](images/ams-video-review.PNG)

## <a name="next-steps"></a>Sonraki adımlar

.NET için bu ve diğer Content Moderator hızlı başlangıçlara yönelik [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ve [Visual Studio çözümü](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) alın.

Video incelemesinin [dökümünü](video-transcript-moderation-review-tutorial-dotnet.md) ekleme hakkında bilgi edinin. 

[Tüm video denetleme çözümünü](video-transcript-moderation-review-tutorial-dotnet.md)geliştirme hakkında ayrıntılı öğreticiye göz atın.
