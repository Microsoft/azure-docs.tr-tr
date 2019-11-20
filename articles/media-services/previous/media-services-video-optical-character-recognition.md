---
title: Azure Media Analytics OCR ile metni dijital olarak boyutlandır | Microsoft Docs
description: Azure Media Analytics OCR (optik karakter tanıma), video dosyalarındaki metin içeriğini düzenlenebilir, aranabilir dijital metinlere dönüştürmenize olanak sağlar.  Bu sayede, medyanızın video sinyalinden anlamlı meta verilerin ayıklanmasını otomatik hale getirebilirsiniz.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11f897852ce820e666d7403f42735b2ee3bdd73b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084816"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Video dosyalarındaki metin içeriğini dijital metne dönüştürmek için Azure Media Analytics kullanın  

## <a name="overview"></a>Genel Bakış
Video dosyalarınızda metin içeriğini ayıklamanız ve düzenlenebilir, aranabilir bir dijital metin oluşturmanız gerekiyorsa Azure Media Analytics OCR (optik karakter tanıma) kullanmanız gerekir. Bu Azure Medya Işlemcisi, video dosyalarınızda metin içeriğini algılar ve kullanım için metin dosyaları oluşturur. OCR, medyanızın video sinyalinden anlamlı meta verilerin ayıklanmasını otomatik hale getirmenizi sağlar.

Bir arama altyapısıyla birlikte kullanıldığında, medyanıza metin ile kolayca dizin oluşturabilir ve içeriğinizin bulunabilirliğini geliştirebilirsiniz. Bu, bir slayt gösterisi sunusunun video kaydı veya ekran yakalama gibi yüksek düzeyde metin videosunda son derece yararlıdır. Azure OCR medya Işlemcisi dijital metin için iyileştirilmiştir.

**Azure MEDIA OCR** medya işlemcisi Şu anda önizleme aşamasındadır.

Bu makalede, **Azure MEDIA OCR** hakkında ayrıntılar verilmektedir ve .net IÇIN Media Services SDK ile nasıl kullanılacağı gösterilmektedir. Daha fazla bilgi ve örnek için [Bu bloga](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)bakın.

## <a name="ocr-input-files"></a>OCR giriş dosyaları
Video dosyaları. Şu anda şu biçimler desteklenir: MP4, MOV ve WMV.

## <a name="task-configuration"></a>Görev yapılandırması
Görev yapılandırması (ön ayar). **Azure MEDIA OCR**ile bir görev oluştururken JSON veya XML kullanarak bir yapılandırma ön ayarı belirtmeniz gerekir. 

>[!NOTE]
>OCR altyapısı yalnızca yükseklik/genişlik içinde geçerli bir giriş olarak en az 40 32000 piksel olan bir görüntü bölgesi alır.
>

### <a name="attribute-descriptions"></a>Öznitelik açıklamaları
| Öznitelik adı | Açıklama |
| --- | --- |
|AdvancedOutput| AdvancedOutput değerini true olarak ayarlarsanız, JSON çıktısı her bir sözcüğe ait konumsal verileri (tümceciklere ve bölgelere ek olarak) içerir. Bu ayrıntıları görmek istemiyorsanız, bayrağını false olarak ayarlayın. Varsayılan değer false'tur. Daha fazla bilgi için [bu bloga](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/) bakın.|
| Dil |(isteğe bağlı) aranacak metnin dilini açıklar. Aşağıdakilerden biri: otomatik algıla (varsayılan), Arapça, Chinesebasitleştirilmiş, Chinesetradi, Çekçe Danca, Felemenkçe, Ingilizce, Fince, Fransızca, Almanca, Yunanca, Macarca, Italyanca, Japonca, Korece, Norveççe, Lehçe, Portekizce, Rumence, Rusça, SerbianCyrillic, SerbianLatin, Slovakça, Ispanyolca, Isveççe, Türkçe. |
| TextOrientation |(isteğe bağlı) aranacak metnin yönünü açıklar.  "Sol", tüm harflerin sol tarafına doğru işaret ettiği anlamına gelir.  Varsayılan metin (bir kitapta bulunılabilecek gibi), "yukarı" yönelimli olarak adlandırılır.  Aşağıdakilerden biri: otomatik algıla (varsayılan), yukarı, sağ, aşağı, sol. |
| TimeInterval |(isteğe bağlı) örnekleme oranını açıklar.  Varsayılan değer her 1/2 saniyedir.<br/>JSON biçimi – HH: mm: ss. SSS (varsayılan 00:00:00.500)<br/>XML biçimi – W3C XSD Duration temel (varsayılan PT 0,5) |
| Detectregion |seçim Metin algılayan bir görüntü çerçevesi içindeki bölgeleri belirten bir DetectRegion nesneleri dizisi.<br/>Bir DetectRegion nesnesi aşağıdaki dört tamsayı değerden oluşur:<br/>Sol kenar boşluğundan sol-piksel<br/>Üst-kenar boşluğundan üst-piksel<br/>Width – bölgenin piksel cinsinden genişliği<br/>Yükseklik – bölgenin piksel cinsinden yüksekliği |

#### <a name="json-preset-example"></a>JSON önceden ayarlanmış örneği

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>XML önceden ayarlanmış örneği

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR çıktı dosyaları
OCR medya işlemcisinin çıktısı bir JSON dosyasıdır.

### <a name="elements-of-the-output-json-file"></a>Çıktı JSON dosyasının öğeleri
Video OCR çıktısı, videonuzda bulunan karakterlere göre zamana göre kesimli veriler sağlar.  Çözümlemek istediğiniz sözcüklerin tam olarak odaklanmak için dil veya yönlendirme gibi öznitelikleri kullanabilirsiniz. 

Çıktı aşağıdaki öznitelikleri içerir:

| Öğe | Açıklama |
| --- | --- |
| Timescale |Videonun saniye başına "ticks" |
| Uzaklık |Tarih damgalarının zaman kayması. Video API 'Lerinin 1,0 sürümünde bu her zaman 0 olur. |
| Framerate |Videonun saniye başına kareleri |
| Genişlik |Videonun piksel cinsinden genişliği |
| Yükseklik |Videonun piksel cinsinden yüksekliği |
| Fragments |meta verilerin öbekli olduğu, zaman tabanlı video öbeklerinin dizisi |
| start |"ticks" içindeki bir parçanın başlangıç saati |
| duration |"ticks" içindeki bir parçanın uzunluğu |
| interval |verilen parça içindeki her bir olayın aralığı |
| etkinlikler |bölgeleri içeren dizi |
| bölgesinde kullanılamıyor |algılanan kelimeleri veya tümceleri temsil eden nesne |
| language |bölge içinde algılanan metnin dili |
| mesinden |bölge içinde algılanan metnin yönü |
| satırları |bölge içinde algılanan metin satır dizisi |
| metin |gerçek metin |

### <a name="json-output-example"></a>JSON çıkış örneği
Aşağıdaki çıktı örneği, genel video bilgilerini ve çeşitli video parçalarını içerir. Her video parçasında, dil ve metin yönü ile OCR MP tarafından algılanan her bölge bulunur. Bu bölge, bu bölgedeki metnin her sözcük satırını, çizginin metnini, satırın konumunu ve her sözcük bilgisini (Word içeriği, konumu ve güveni) da içerir. Aşağıda bir örnek verilmiştir ve bazı açıklamaları satır içi yerleştirdim.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. OCR yapılandırması/önceden ayarlanmış dosya içeren bir iş oluşturun.
3. Çıktı JSON dosyalarını indirin. 
   
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Media Services öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](media-services-analytics-overview.md)

