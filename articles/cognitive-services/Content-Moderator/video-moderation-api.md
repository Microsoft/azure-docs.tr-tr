---
title: Content Moderator, C# sakıncalı malzeme için video içeriğini analiz edin.
titleSuffix: Azure Cognitive Services
description: .NET için Content Moderator SDK kullanarak çeşitli sakıncalı malzemeler için video içeriğini çözümleme
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755230"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>' Deki sakıncalı malzeme için video içeriğini çözümleC#

Bu makalede, [.NET için Content moderator SDK 'yı](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) kullanmaya başlamanıza yardımcı olacak bilgiler ve kod örnekleri, yetişkinlere yönelik içerik ve video içeriğini taramak

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar
- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü

## <a name="set-up-azure-resources"></a>Azure kaynakları ayarlama

Content Moderator video denetleme özelliği, Azure Media Services (AMS) içinde ücretsiz bir genel önizleme **medyası işlemcisi** olarak sunulmaktadır. Azure Media Services, video içeriğini depolamaya ve akışa yönelik özel bir Azure hizmetidir. 

### <a name="create-an-azure-media-services-account"></a>Azure Media Services hesabı oluşturma

AMS 'ye abone olmak ve ilişkili bir Azure depolama hesabı oluşturmak için [Azure Media Services hesabı oluşturma](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) bölümündeki yönergeleri izleyin. Bu depolama hesabında, yeni bir BLOB depolama kapsayıcısı oluşturun.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory uygulaması oluşturma

Azure portal yeni AMS aboneliğinize gidin ve Yan menüden **API erişimi** ' ni seçin. **Hizmet sorumlusu ile Azure Media Services Bağlan**' ı seçin. **REST API uç noktası** alanındaki değeri aklınızda edin; Buna daha sonra ihtiyacınız olacak.

**Azure AD uygulaması** bölümünde **Yeni oluştur** ' u SEÇIN ve yeni Azure AD uygulama kaydınızı adlandırın (örneğin, "VideoModADApp"). **Kaydet** ' e tıklayın ve uygulama yapılandırıldıktan sonra birkaç dakika bekleyin. Ardından, yeni uygulama kaydınızı sayfanın **Azure AD uygulaması** bölümünde görmeniz gerekir.

Uygulama kaydınızı seçin ve altındaki **Uygulamayı Yönet** düğmesine tıklayın. **Uygulama kimliği** alanındaki değeri aklınızda edin; Buna daha sonra ihtiyacınız olacak. **Ayarlar**  > **anahtarlar**' ı seçin ve yeni anahtar için bir açıklama girin (örneğin, "videomodkey"). **Kaydet**' e tıklayın ve ardından yeni anahtar değerine dikkat edin. Bu dizeyi kopyalayın ve güvenli bir yere kaydedin.

Yukarıdaki işlemin daha kapsamlı bir yolu için bkz. [Azure AD kimlik doğrulamasıyla çalışmaya başlama](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Bunu yaptıktan sonra, video denetlemesi medya işlemcisini iki farklı yolla kullanabilirsiniz.

## <a name="use-azure-media-services-explorer"></a>Azure Media Services Gezginini Kullanma

Azure Media Services gezgin, AMS için Kullanıcı dostu bir ön uçta bulunur. AMS hesabınıza gözatıp videoları karşıya yüklemeyi ve Content Moderator medya işlemcisi ile içerik taramayı sağlamak için bu uygulamayı kullanın. Dosyayı [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)'dan indirip yükleyin veya daha fazla bilgi Için [Azure Media Services Gezgini blog](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) gönderisine bakın.

![Content Moderator ile gezgin Azure Media Services](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Visual Studio projesini oluşturma

1. Visual Studio 'da yeni bir **konsol uygulaması (.NET Framework)** projesi oluşturun ve bunu **videodenetlemesi**olarak adlandırın. 
1. Çözümünüzde başka projeler de varsa, tek başlangıç projesi olarak bunu seçin.
1. Gereken NuGet paketlerini alın. Çözüm Gezgini'nde projenize sağ tıklayın ve **NuGet Paketlerini Yönet**'i seçin; ardından aşağıdaki projeleri bulun ve yükleyin:
    - windowsazure. mediaservices
    - windowsazure. mediaservices. Extensions

## <a name="add-video-moderation-code"></a>Video denetleme kodu ekle

Ardından, temel bir içerik moderasyonu senaryosu uygulamak için kodu bu kılavuzdan kopyalayıp projenize yapıştıracaksınız.

### <a name="update-the-programs-using-statements"></a>Programı deyimler kullanarak güncelleştirme

Aşağıdaki `using` deyimlerini _Program.cs_ dosyanızın en üstüne ekleyin.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Kaynak başvurularını ayarlama

Aşağıdaki statik alanları _Program.cs_ dosyasındaki **Program** sınıfına ekleyin. Bu alanlar, AMS aboneliğinize bağlanmak için gereken bilgileri tutar. Bunları yukarıdaki adımlarda aldığınız değerlerle birlikte doldurabilirsiniz. @No__t_0 Azure AD uygulamanızın **uygulama kimliği** değeri olduğunu ve `CLIENT_SECRET` bu uygulama için oluşturduğunuz "VideoModKey" değerinin olduğunu unutmayın.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Yerel bir video dosyası (en basit durum) kullanmak istiyorsanız, projeye ekleyin ve yolunu `INPUT_FILE` değeri olarak girin (göreli yollar yürütme dizinine göre değişir).

Ayrıca, geçerli dizinde _önceden ayarlanmış. JSON_ dosyasını oluşturmanız ve bir sürüm numarası belirtmek için kullanmanız gerekir. Örnek:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Giriş video (ler) i Yükle

**Program** sınıfının **Main** yöntemi bir Azure Medya bağlamı ve ardından bir Azure depolama bağlamı oluşturur (videolarınızın BLOB depolama alanında olması durumunda). Kalan kod, bir Azure depolama kapsayıcısı içindeki yerel bir klasörden, Blobun veya birden çok blobdan bir videoyu tarar. Diğer kod satırlarına açıklama ekleyerek tüm seçenekleri deneyebilirsiniz.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Azure Medya bağlamı oluşturma

**Program** sınıfına aşağıdaki yöntemi ekleyin. Bu, AMS ile iletişime izin vermek için AMS kimlik bilgilerinizi kullanır.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Azure depolama bağlamı oluşturmak için kod ekleme

**Program** sınıfına aşağıdaki yöntemi ekleyin. Blob depolamaya erişmek için depolama kimlik bilgilerinizle oluşturulan depolama bağlamını kullanırsınız.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Yerel dosya ve BLOB 'dan Azure Medya varlıkları oluşturmak için kodu ekleyin

Content Moderator medya işlemcisi, işleri Azure Media Services platformu içindeki **varlıklar** üzerinde çalıştırır.
Bu yöntemler, varlıkları yerel bir dosyadan veya ilişkili bir Blobun oluşturur.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Bir kapsayıcı içindeki bir video koleksiyonunu (blob olarak) taramak için kodu ekleyin

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Content Moderator Işini çalıştırmak için yöntemini ekleyin

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Yardımcı işlevler ekleme

Bu yöntemler, Azure Media Services varlıklarından Content Moderator çıkış dosyasını (JSON) indirir ve programın çalışan bir durumu konsola kaydetmesini sağlamak için denetleme işinin durumunu izlemeye yardımcı olur.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Programı çalıştırma ve çıktıyı gözden geçirme

Içerik denetleme işi tamamlandıktan sonra JSON yanıtını çözümleyin. Şu öğelerden oluşur:

- Video bilgileri Özeti
- "**Parçalar**" olarak **anlık görüntüleri**
- " **(= True veya false)"** adlı "**Olaylar**" olarak, **yetişkin** ve **kcy** puanlarını temel alan **ana çerçeveler**
- **Başlangıç**, **süre**, **TotalDuration**ve **zaman damgası** "ticks" dir. Sayıyı saniye cinsinden almak için **zaman ölçeğinde** ayırın.
 
> [!NOTE]
> - `adultScore`, belirli durumlarda cinsel açık veya yetişkin olarak kabul edilebilir içeriğin olası varlığını ve tahmin Puanını temsil eder.
> - `racyScore`, belirli durumlarda cinsel veya kötü bir şekilde düşünüldüksel olabilecek içeriğin olası varlığını ve tahmin Puanını temsil eder.
> - `adultScore` ve `racyScore` 0 ile 1 arasındadır. Puan arttıkça, modelin daha yüksek olması kategorinin uygulanabilir olabileceğini tahmin edilir. Bu önizleme el ile kodlanmış sonuçlar yerine istatistiksel bir modeli kullanır. Her kategorinin gereksinimlerinize göre nasıl hizalanacağını öğrenmek için kendi içeriklerinizi test etmenizi öneririz.
> - `reviewRecommended`, iç puan eşiklerine bağlı olarak doğru ya da yanlış olur. Müşteriler, bu değerin kullanılıp kullanılmayacağını ya da içerik ilkelerine bağlı olarak özel eşiklere karar vermesini değerlendirmelidir.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Denetleme çıktıınızdan [video İncelemeleri](video-reviews-quickstart-dotnet.md) oluşturmayı öğrenin.

Video incelemelerinizin [dökümünü](video-transcript-moderation-review-tutorial-dotnet.md) ekleyin.

[Kapsamlı bir video ve](video-transcript-moderation-review-tutorial-dotnet.md)döküm denetimi çözümü oluşturma hakkında ayrıntılı öğreticiye göz atın.

.NET için bu ve diğer Content Moderator hızlı başlangıçlara yönelik [Visual Studio çözümünü indirin](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) .
