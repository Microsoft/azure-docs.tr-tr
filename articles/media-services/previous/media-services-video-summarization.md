---
title: Video özeti oluşturmak için Azure Media Video Thumbnails kullanma | Microsoft Docs
description: Video özetleme, kaynak videodan ilgi çekici parçacıkları otomatik olarak seçerek uzun videoların özetlerini oluşturmanıza yardımcı olabilir. Bu, uzun bir videoda beklendiklere ilişkin hızlı bir genel bakış sağlamak istediğinizde yararlıdır.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: da397c180b6ccaf91084a0ec22210c7bcc88a5dc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084811"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Video özeti oluşturmak için Azure Media Video Thumbnails kullanma  
 
## <a name="overview"></a>Genel Bakış

**Azure Media video Thumbnails** medya IŞLEMCISI (MP), yalnızca uzun bir videonun özetini önizlemek isteyen müşteriler için yararlı olan bir videonun özetini oluşturmanızı sağlar. Örneğin, müşteriler bir küçük resmin üzerine gelindiğinde kısa bir "Özet videosu" görmek isteyebilir. Bir yapılandırma ön ayarı aracılığıyla **Azure Media video Thumbnails** parametrelerini ayırarak, açıklayıcı bir alt klip oluşturmak ALGORITHMICALLY için MP 'nin güçlü görüntü algılama ve birleştirme teknolojisini kullanabilirsiniz.  

**Azure Media video küçük resim** MP, şu anda önizleme aşamasındadır.

Bu makale, **Azure Media video küçük resmindeki** ayrıntıları sağlar ve .net IÇIN Media Services SDK ile nasıl kullanacağınızı gösterir.

## <a name="limitations"></a>Sınırlamalar

Bazı durumlarda, videonuzun farklı sahnelerin olmaması durumunda çıkış yalnızca tek bir görüntü olacaktır.

## <a name="video-summary-example"></a>Video Özeti örneği
Azure Media Video Thumbnails medya işlemcisinin yapabilecekleri bazı örnekler aşağıda verilmiştir:

### <a name="original-video"></a>Özgün video
[Özgün video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Video küçük resmi sonucu
[Video küçük resmi sonucu](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Görev yapılandırması (önceden ayarlanmış)
**Azure Media video Thumbnails**bir video küçük resim görevi oluştururken, bir yapılandırma önayarı belirtmeniz gerekir. Yukarıdaki küçük resim örneği aşağıdaki temel JSON yapılandırmasıyla oluşturulmuştur:

```json
    {
        "version":"1.0"
    }
```

Şu anda aşağıdaki parametreleri değiştirebilirsiniz:

| param | Açıklama |
| --- | --- |
| outputAudio |Sonuç videonun herhangi bir ses içerip içermediğini belirtir. <br/>İzin verilen değerler: true veya false. Varsayılan değer true 'dur. |
| fadeInFadeOut |Ayrı hareket küçük resimleri arasında Soldur geçişlerinin kullanılıp kullanılmayacağını belirtir.  <br/>İzin verilen değerler: true veya false.  Varsayılan değer true 'dur. |
| maxMotionThumbnailDurationInSecs |Elde edilen videonun tamamının ne kadar süreyle olması gerektiğini belirten tamsayı.  Varsayılan değer özgün video süresine bağlıdır. |

Aşağıdaki tabloda, **maxMotionThumbnailInSecs** kullanılmazsa varsayılan süre açıklanmaktadır.

|  |  |  |
| --- | --- | --- |
| Video süresi |d < 3 dk |3 dk < d < 15 dk |
| Küçük resim süresi |15 sn (2-3 sahne) |30 sn (3-5 sahne) |

Aşağıdaki JSON, kullanılabilir parametreleri ayarlar.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. Aşağıdaki JSON ön ayarını içeren bir yapılandırma dosyasını temel alan video küçük resmi görevi ile bir iş oluşturur: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Çıkış dosyalarını indirir. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 

#### <a name="example"></a>Örnek

```csharp
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

        }
    }
```

### <a name="video-thumbnail-output"></a>Video küçük resmi çıkışı
[Video küçük resmi çıkışı](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](media-services-analytics-overview.md)

[Azure Media Analytics gösterileri](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

