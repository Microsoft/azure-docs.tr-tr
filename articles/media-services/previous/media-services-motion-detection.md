---
title: Azure Media Analytics ile Motions Algıla | Microsoft Docs
description: Azure Media Motion Detector medya işlemcisi (MP), diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, önemli olmayan video
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: fd31528325ddbe913333bc228fc3847242abcd24
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083747"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Azure Media Analytics ile Motions Algıla
 
## <a name="overview"></a>Genel Bakış

**Azure Media Motion Detector** medya IŞLEMCISI (MP), diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, önemli olmayan video Hareket algılama, videonun, hareketin gerçekleştiği yerleri belirlemek için statik kamera çekimleri üzerinde kullanılabilir. Zaman damgalarına ve olayın gerçekleştiği sınırlayıcı bölgeye sahip bir meta veri içeren bir JSON dosyası oluşturur.

Güvenlik video akışlarına hedeflenmiş bu teknoloji, ilgili olaylara yönelik hareketi ve gölge ve aydınlatma değişiklikleri gibi hatalı pozitif sonuçları kategorilere ayırmanıza olanak sağlar. Bu, uzun sürme videolarından ilgi çekici bir şekilde istenmeyen olaylar elde etmeksizin kamera akışından güvenlik uyarıları oluşturmanıza olanak sağlar.

**Azure Media Motion Detector** MP Şu anda önizleme aşamasındadır.

Bu makale, **Azure Media Motion Detector** ayrıntılarını sağlar ve .net IÇIN Media Services SDK ile nasıl kullanacağınızı gösterir

## <a name="motion-detector-input-files"></a>Hareket algılayıcısı giriş dosyaları
Video dosyaları. Şu anda şu biçimler desteklenir: MP4, MOV ve WMV.

## <a name="task-configuration-preset"></a>Görev yapılandırması (önceden ayarlanmış)
**Azure Media Motion Detector**bir görev oluştururken, bir yapılandırma ön ayarı belirtmeniz gerekir. 

### <a name="parameters"></a>Parametreler
Aşağıdaki parametreleri kullanabilirsiniz:

| Ad | Seçenekler | Açıklama | Varsayılan |
| --- | --- | --- | --- |
| sensitivityLevel |String: 'low', 'medium', 'high' |Hareketlerin 'in bildirildiği duyarlılık düzeyini ayarlar. Hatalı pozitif sonuç sayısını ayarlamak için bunu ayarlayın. |'medium' |
| frameSamplingValue |Pozitif tamsayı |Algoritmanın çalışacağı sıklığı ayarlar. 1 her çerçeveye eşittir, 2 her ikinci kare de vb. anlamına gelir. |1 |
| detectLightChange |Boolean: 'true', 'false' |Sonuçlarda açık değişikliklerin raporlanıp raporlanmadığını ayarlar |'False' |
| mergeTimeThreshold |XS-Time: ss: DD: ss<br/>Örnek: 00:00:03 |2 olayın birleştirileceği ve 1 olarak bildirildiği hareket olayları arasındaki zaman penceresini belirtir. |00:00:00 |
| detectionZones |Bir algılama bölgesi dizisi:<br/>-Algılama bölgesi 3 veya daha fazla noktadan oluşan bir dizidir<br/>-Point, 0 ile 1 arasında bir x ve y koordinatı. |Kullanılacak Çokgen algılama bölgelerinin listesini açıklar.<br/>Sonuçlar bir KIMLIK olarak, ilki ' id ' olan bölge ile bildirilir: 0 |Tüm çerçeveyi kapsayan tek bölge. |

### <a name="json-example"></a>JSON örneği

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Hareket algılayıcısı çıkış dosyaları
Bir hareket algılama işi, video içinde hareket uyarılarını ve bunların kategorilerini açıklayan çıkış varlığı içinde bir JSON dosyası döndürür. Dosya videoda algılanan hareket saati ve süresi hakkındaki bilgileri içerir.

Hareket algılayıcısı API 'SI, sabit bir arka plan videosunda (örneğin, bir izleme videosu) hareket halinde nesneler olduğunda göstergeler sağlar. Hareket algılayıcısı, aydınlatma ve gölge değişiklikleri gibi yanlış alarmları azaltmak için eğitilir. Algoritmaların geçerli sınırlamaları, gece Vision videoları, yarı saydam nesneler ve küçük nesneler içerir.

### <a id="output_elements"></a>Çıktı JSON dosyasının öğeleri
> [!NOTE]
> En son sürümde, çıkış JSON biçimi değişmiştir ve bazı müşterilere yönelik bir son değişikliği temsil edebilir.
> 
> 

Aşağıdaki tabloda, çıkış JSON dosyasının öğeleri açıklanmaktadır.

| Öğe | Açıklama |
| --- | --- |
| version |Bu, video API 'sinin sürümünü ifade eder. Geçerli sürüm 2 ' dir. |
| timescale |Videonun saniye başına "ticks" değeri. |
| offset |"Ticks" içindeki zaman damgalarının zaman değeri. Video API 'Lerinin 1,0 sürümünde bu her zaman 0 olur. Daha sonra destekduğumuz senaryolarda bu değer değişebilir. |
| framerate |Videodaki saniye başına kare hızı. |
| width, height |Videonun piksel cinsinden genişlik ve yüksekliğini ifade eder. |
| start |"Ticks" içinde başlangıç zaman damgası. |
| duration |Olayın "ticks" cinsinden uzunluğu. |
| interval |Olaydaki her girdinin "ticks" cinsinden aralığı. |
| etkinlikler |Her olay parçası bu süre içinde algılanan hareketi içerir. |
| type |Geçerli sürümde, bu her zaman genel hareket için ' 2 '. Bu etiket, video API 'Lerine gelecekteki sürümlerde hareketi kategorilere ayırma esnekliği verir. |
| regionId |Yukarıda açıklandığı gibi, bu sürümde her zaman 0 olur. Bu etiket, video API 'SI ile gelecekteki sürümlerde çeşitli bölgelerde hareket bulma esnekliği sağlar. |
| regions |Videonuzdaki hareket hakkında bilgi verdiğiniz alanı ifade eder. <br/><br/>-"ID", bu sürümde yalnızca bir, ID 0 olan bölge alanını temsil eder. <br/>-"tür" hareket için ilgilendiğiniz bölgenin şeklini temsil eder. Şu anda, "dikdörtgen" ve "Çokgen" desteklenir.<br/> "Dikdörtgen" belirttiyseniz, bölgenin boyutları X, Y, genişlik ve yükseklik olarak belirlenmiştir. X ve Y koordinatları, bölgenin sol üst XY koordinatlarını 0,0 ile 1,0 arasında normalleştirilmiş bir ölçekte temsil eder. Genişlik ve yükseklik, 0,0 ile 1,0 arasındaki normalleştirilmiş ölçekte bölgenin boyutunu temsil eder. Geçerli sürümde X, Y, genişlik ve yükseklik her zaman 0, 0 ve 1, 1 ' de sabittir. <br/>"Çokgen" belirttiyseniz, bölgede boyutlar vardır. <br/> |
| fragments |Meta veriler parçalar adlı farklı kesimlere bölünmüştür. Her parçada başlangıç, süre, aralık sayısı ve olaylar vardır. Olayları olmayan bir parça, bu başlangıç saati ve süresi boyunca hiçbir hareketin algılanmadığı anlamına gelir. |
| brackets [] |Her köşeli ayraç, olaydaki bir aralığı temsil eder. Bu Aralık için boş köşeli ayraçlar hiçbir hareket algılanmadığı anlamına gelir. |
| locations |Olaylar altındaki bu yeni giriş, hareketin gerçekleştiği konumu listeler. Bu, algılama bölgelerinden daha özgüdür. |

Aşağıdaki JSON örneği çıktıyı gösterir:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Sınırlamalar
* Desteklenen giriş video biçimleri, MP4, MOV ve WMV 'yi içerir.
* Hareket algılama, sabit arka plan videoları için iyileştirilmiştir. Algoritma, aydınlatma değişiklikleri ve gölgeleri gibi yanlış alarmları azaltmaya odaklanır.
* Teknik sorunlar nedeniyle bazı hareketler algılanmayabilir; Örneğin, gece Vision videoları, yarı saydam nesneler ve küçük nesneler.

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. Aşağıdaki JSON ön ayarını içeren bir yapılandırma dosyasını temel alan video hareket algılama göreviyle bir iş oluşturun: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
            }
    ```

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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Azure Media Services hareket algılayıcısı blogu](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics genel bakışı](media-services-analytics-overview.md)

[Azure Media Analytics gösterileri](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

