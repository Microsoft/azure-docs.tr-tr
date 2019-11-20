---
title: Azure Media Indexer 2 Preview ile medya dosyalarını dizine alma | Microsoft Docs
description: Azure Media Indexer, medya dosyalarınızın içeriğini aranabilir hale getirmenizi ve kapalı açıklamalı alt yazı ve anahtar sözcükler için tam metin dökümü oluşturmanıza olanak sağlar. Bu konuda, Media Indexer 2 Preview kullanımı gösterilmektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: d03c3a15d9bccf93b73d36302f986dffd95c6428
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309240"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Azure Media Indexer 2 Preview ile medya dosyalarını dizine alma

> [!NOTE]
> [Azure Media Indexer 2](media-services-process-content-with-indexer2.md) medya Işlemcisi 1 Ocak 2020 tarihinde kullanımdan kaldırılacaktır. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) , bu eski medya işlemcisinin yerini alır. Daha fazla bilgi için [Azure Media Indexer ve Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](migrate-indexer-v1-v2.md)konusuna bakın.

**Azure Media Indexer 2 Preview** medya IŞLEMCISI (MP), medya dosyalarını ve içeriği aranabilir yapmanızı ve kapalı açıklamalı altyazı parçaları oluşturmanızı sağlar. Önceki [Azure Media Indexer](media-services-index-content.md)sürümüyle karşılaştırıldığında, **Azure Media Indexer 2 önizlemesi** daha hızlı Dizin oluşturma gerçekleştirir ve daha geniş bir dil desteği sunar. Desteklenen diller arasında Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Çince (Mandarin, Basitleştirilmiş), Portekizce, Arapça, Rusça ve Japonca bulunur.

**Azure Media Indexer 2 Preview** MP, şu anda önizleme aşamasındadır.

Bu makalede **Azure Media Indexer 2 Preview**ile dizin oluşturma işlerinin nasıl oluşturulacağı gösterilmektedir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Aşağıdaki noktalar geçerlidir:
 
* Dizin Oluşturucu 2, Azure Çin 21Vianet ve Azure Kamu 'da desteklenmez.
* İçerik dizin oluştururken, çok temiz konuşmaya sahip medya dosyalarını (arka plan müziği, gürültü, efekt veya mikrofon hisleri olmadan) kullandığınızdan emin olun. Uygun içeriğe örnek olarak şunlar verilebilir: kayıtlı toplantılar, seminerler veya sunular. Şu içerikler dizin oluşturmak için uygun olmayabilir: Filmler, TV programları, karışık ses ve ses efektleriyle herhangi bir şey, arka plan gürültüsü olan kötü kaydedilmiş içerik (hisler).
 
## <a name="input-and-output-files"></a>Giriş ve çıkış dosyaları
### <a name="input-files"></a>Giriş dosyaları
Ses veya video dosyaları

### <a name="output-files"></a>Çıkış dosyaları
Bir dizin oluşturma işi, aşağıdaki biçimlerde kapalı açıklamalı altyazı dosyaları oluşturabilir:  

* **SAMI**
* **TTML**
* **WebVTT**

Bu biçimlerdeki kapalı açıklamalı altyazı (CC) dosyaları, ses ve video dosyalarını işitme engelli kişiler için erişilebilir hale getirmek için kullanılabilir.

## <a name="task-configuration-preset"></a>Görev yapılandırması (önceden ayarlanmış)
**Azure Media Indexer 2 Preview**ile bir dizin oluşturma görevi oluştururken, bir yapılandırma ön ayarı belirtmeniz gerekir.

Aşağıdaki JSON, kullanılabilir parametreleri ayarlar.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Desteklenen diller
Azure Media Indexer 2 Preview, aşağıdaki diller için konuşmayı metne (görev yapılandırmasında dil adını belirtirken aşağıda gösterildiği gibi, parantez içinde 4 karakterlik kod kullanın) destekler:

* İngilizce [EnUs]
* İspanyolca [EsEs]
* Çince (Mandarin, Basitleştirilmiş) [ZhCn]
* Fransızca [FrFr]
* Almanca [DeDe]
* İtalyanca [ItIt]
* Portekizce [PtBr]
* Arapça (Mısır) [ArEg]
* Japonca [JaJp]
* Rusça [RuRu]
* İngiliz Ingilizcesi [EnGb]
* İspanyolca (Meksika) [EsMx] 

## <a name="supported-file-types"></a>Desteklenen dosya türleri

Desteklenen dosya türleri hakkında daha fazla bilgi için [Desteklenen codec bileşenleri/biçimler](media-services-media-encoder-standard-formats.md#input-containerfile-formats) bölümüne bakın.

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. Aşağıdaki JSON ön ayarını içeren bir yapılandırma dosyasını temel alan bir dizin oluşturma göreviyle iş oluşturun:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Çıkış dosyalarını indirin. 
   
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

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](media-services-analytics-overview.md)

[Azure Media Analytics gösterileri](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

