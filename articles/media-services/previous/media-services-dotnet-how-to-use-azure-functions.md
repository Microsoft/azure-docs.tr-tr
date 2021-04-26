---
title: Media Services ile Azure Işlevleri geliştirme
description: Bu konuda, Azure portal kullanarak Media Services ile Azure Işlevleri geliştirmeye nasıl başlayayapılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ef8e3a2e733247f713fe0240cb8ef6069803fcb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013967"
---
# <a name="develop-azure-functions-with-media-services"></a>Media Services ile Azure Işlevleri geliştirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bu makalede, Media Services kullanan Azure Işlevleri oluşturmaya nasıl başlacağınız gösterilmektedir. Bu makalede tanımlanan Azure Işlevi, yeni MP4 dosyaları için **giriş** adlı bir depolama hesabı kapsayıcısını izler. Depolama kapsayıcısına bir dosya bırakıldıktan sonra blob tetikleyicisi işlevi yürütür. Azure Işlevlerini gözden geçirmek için bkz. **Azure işlevleri** bölümündeki [genel bakış](../../azure-functions/functions-overview.md) ve diğer konular.

Azure Media Services kullanan mevcut Azure Işlevlerini araştırmak ve dağıtmak istiyorsanız [Media Services Azure işlevleri](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)' ne bakın. Bu depo, doğrudan BLOB depolama, kodlama ve BLOB depolamaya geri içerik yazma ile ilgili içerik akışını göstermek için Media Services kullanan örnekleri içerir. Ayrıca, Web kancaları ve Azure Kuyrukları aracılığıyla iş bildirimlerinin nasıl izleneceği hakkında örnekler de içerir. Ayrıca, [Media Services Azure işlevleri](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) deposundaki örneklere göre işlevlerinizi geliştirebilirsiniz. İşlevleri dağıtmak için **Azure 'A dağıt** düğmesine basın.

## <a name="prerequisites"></a>Önkoşullar

- İlk işlevinizin oluşturmadan önce etkin bir Azure hesabınız olması gerekir. Bir Azure hesabınız yoksa [ücretsiz hesaplar kullanılabilir](https://azure.microsoft.com/free/).
- Azure Media Services (AMS) hesabınızda eylem gerçekleştiren veya Media Services tarafından gönderilen olayları dinlemek için Azure Işlevleri oluşturacaksanız, [burada](media-services-portal-create-account.md)açıklandığı gibi bir AMS hesabı oluşturmanız gerekir.
    
## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

1. [Azure portalına](https://portal.azure.com) gidip Azure hesabınızla oturum açın.
2. [Burada](../../azure-functions/functions-create-function-app-portal.md)açıklandığı gibi bir işlev uygulaması oluşturun.

>[!NOTE]
> **Storageconnection** ortam değişkeninde (bkz. sonraki adıma bakın) belirttiğiniz bir depolama hesabı, uygulamanızla aynı bölgede olmalıdır.

## <a name="configure-function-app-settings"></a>İşlev uygulaması ayarlarını yapılandırma

Media Services işlevleri geliştirirken, işlevlerinizin tamamında kullanılacak ortam değişkenlerini eklemek kullanışlıdır. Uygulama ayarlarını yapılandırmak için uygulama ayarlarını yapılandır bağlantısına tıklayın. Daha fazla bilgi için bkz.  [Azure işlev uygulaması ayarlarını yapılandırma](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Bu makalede tanımlanan işlevi, uygulama ayarlarınızda aşağıdaki ortam değişkenlerine sahip olduğunuzu varsayar:

**Amsaadtenantdomain**: Azure AD kiracı uç noktası. AMS API 'sine bağlanma hakkında daha fazla bilgi için [Bu](media-services-use-aad-auth-to-access-ams-api.md) makaleye bakın.

**AMSRESTAPIEndpoint**: REST API uç noktasını temsil eden URI. 

**Amsclitıd**: Azure AD uygulama istemci kimliği.

**Amsclientsecret**: Azure AD uygulama istemci parolası.

**Storageconnection**: Media Services hesabıyla ilişkili hesabın depolama bağlantısı. Bu değer, **function.js** File ve **Run. CSX** dosyasında (aşağıda açıklanmıştır) kullanılır.

## <a name="create-a-function"></a>İşlev oluşturma

İşlev Uygulamanız dağıtıldıktan sonra, **App Services** Azure işlevleri arasında bulabilirsiniz.

1. İşlev uygulamanızı seçin ve **yeni işlev**' e tıklayın.
2. **C#** dilini ve **veri işleme** senaryosunu seçin.
3. **Blobtrigger** şablonunu seçin. Bu işlev, **giriş** kapsayıcısına bir blob yüklendiğinde tetiklenir. **Giriş** adı, bir sonraki adımda **yolunda** belirtilir.

    ![Ekran görüntüsü, BlobTrigger seçiliyken şablon seç iletişim kutusunu gösterir.](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. **Blobtrigger** öğesini seçtiğinizde sayfada daha fazla denetim görüntülenir.

    ![Ekran görüntüsünde, işlevinizin adı iletişim kutusu gösterilir.](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. **Oluştur**’a tıklayın. 

## <a name="files"></a>Dosyalar

Azure işleviniz, bu bölümde açıklanan kod dosyaları ve diğer dosyalarla ilişkilidir. Bir işlev oluşturmak için Azure portal kullandığınızda, **function.js** ve **Run. CSX** sizin için oluşturulur. Dosyaya bir **project.js** eklemeniz veya karşıya yüklemeniz gerekir. Bu bölümün geri kalanı her bir dosyanın kısa bir açıklamasını verir ve tanımlarını gösterir.

![Ekran görüntüsü projenizdeki JSON dosyalarını gösterir.](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Dosyadaki function.js, işlev bağlamalarını ve diğer yapılandırma ayarlarını tanımlar. Çalışma zamanı, izlenecek olayları ve işlev yürütmeden verilerin nasıl geçirileceğini ve geri döneceğini öğrenmek için bu dosyayı kullanır. Daha fazla bilgi için bkz. [Azure İşlevleri HTTP ve web kancası bağlamaları](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>İşlevin yürütülmesini engellemek için **devre dışı** özelliğini **true** olarak ayarlayın. 

Dosyadaki mevcut function.jsiçeriğini aşağıdaki kodla değiştirin:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

project.jsdosya bağımlılıkları içerir. NuGet 'den gerekli .NET Azure Media Services paketlerini içeren dosya **project.js** bir örnektir. Sürüm numaralarının paketlerin en son güncelleştirmeleriyle değişdiğine ve bu nedenle en son sürümleri onaylamanız gerekir. 

project.jsiçin aşağıdaki tanımı ekleyin. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>Run. CSX

Bu, işleviniz için C# kodudur.  Aşağıda tanımlanan işlev, yeni MP4 dosyaları için **Input** (yolda belirtilen) adlı bir depolama hesabı kapsayıcısını izler. Depolama kapsayıcısına bir dosya bırakıldıktan sonra blob tetikleyicisi işlevi yürütür.
    
Bu bölümde tanımlanan örnek şunları gösterir 

1. bir varlığı bir Media Services hesabına alma (bir blobu bir AMS varlığına kopyalayarak) ve 
2. Media Encoder Standard "uyarlamalı akış" ön ayarını kullanan bir kodlama işi gönderme.

Gerçek yaşam senaryosunda, büyük ihtimalle iş ilerlemesini izlemek ve sonra kodlanmış varlığınızı yayımlamak isteyeceksiniz. Daha fazla bilgi için bkz. [Media Services iş bildirimlerini izlemek Için Azure Web kancalarını kullanma](media-services-dotnet-check-job-progress-with-webhooks.md). Daha fazla örnek için bkz. [Azure işlevleri Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Mevcut Run. CSX dosyasının içeriğini şu kodla değiştirin: işlevinizi tanımlamayı tamamladıktan sonra **Kaydet ve Çalıştır**' a tıklayın.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>İşlevinizi sınama

İşlevinizi test etmek için, bağlantı dizesinde belirttiğiniz depolama hesabının **giriş** KAPSAYıCıSıNA bir MP4 dosyası yüklemeniz gerekir.  

1. **Storageconnection** ortam değişkeninde belirttiğiniz depolama hesabını seçin.
2. **Bloblar**' a tıklayın.
3. **+ Kapsayıcı**’ya tıklayın. Kapsayıcı **girişini** adlandırın.
4. Karşıya **Yükle** ' ye basın ve karşıya yüklemek istediğiniz bir. MP4 dosyasına gidin.

>[!NOTE]
> Bir tüketim planında blob tetikleyicisi kullanırken, bir işlev uygulaması boşta kaldıktan sonra yeni Blobları işlerken 10 dakikalık bir gecikme olabilir. İşlev uygulaması çalışmaya başladıktan sonra Bloblar hemen işlenir. Daha fazla bilgi için bkz. [BLOB depolama Tetikleyicileri ve bağlamaları](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu noktada, bir Media Services uygulaması geliştirmeye başlamaya başlayabilirsiniz. 
 
Özel içerik oluşturma iş akışları oluşturmak için Azure Işlevleri 'ni kullanma ve Azure Media Services ile Logic Apps örnekleri/çözümleri hakkında daha ayrıntılı bilgi edinmek için bkz. [GitHub 'da Media Services .net Işlevleri tümleştirme örneği](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Ayrıca bkz. [.NET ile Media Services iş bildirimlerini izlemek Için Azure Web kancalarını kullanma](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
