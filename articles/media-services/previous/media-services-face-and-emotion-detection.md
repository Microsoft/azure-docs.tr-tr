---
title: Azure Media Analytics ile yüz ve duygu algılama | Microsoft Docs
description: Bu konu, Azure Media Analytics ile yüzleri ve onayları nasıl algılayabileceğinizi gösterir.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: b6cbca454d1463ffe6e73d7021ea563e7121b7be
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084633"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Azure Media Analytics ile yüz ve duygu algılama

## <a name="overview"></a>Genel Bakış

**Azure Media Face Detector** medya IŞLEMCISI (MP), yüz ifadeleriyle hareket etmenizi, hareketleri izlemenizi ve hatta ölçek katılımı ve yeniden eylemini izlemenize olanak sağlar. Bu hizmet iki özellik içerir: 

* **Yüz algılama**
  
    Yüz algılama, video içindeki insan yüzlerini bulur ve izler. Birden çok yüz algılanabilir ve sonra bir JSON dosyasında döndürülen zaman ve konum meta verileri ile birlikte hareket ettikleri sırada izlenir. İzleme sırasında, Kullanıcı ekranda dolaştıklarında veya kısa bir süre sonra bile olsa da, aynı yüz için tutarlı bir KIMLIK bırakmaya çalışır.
  
  > [!NOTE]
  > Bu hizmet yüz tanıma gerçekleştirmiyor. Çerçeveyi atan bir kişiye veya çok uzun süre boyunca geri döntiklerinde yeni bir KIMLIK verilmeyecektir.
  > 
  > 
* **Duygu algılama**
  
    Duygu algılama, anormal, sadyetler, korku, Anger ve daha fazlası dahil olmak üzere, algılanan yüzlerden çok sayıda öznitelik Analizi döndüren Yüz Algılama medya Işlemcisinin isteğe bağlı bir bileşenidir. 

**Azure Media Face Detector** MP Şu anda önizleme aşamasındadır.

Bu makale, **Azure Media Face Detector** ayrıntılarını sağlar ve .net IÇIN Media Services SDK ile nasıl kullanacağınızı gösterir.

## <a name="face-detector-input-files"></a>Yüz algılayıcısı giriş dosyaları
Video dosyaları. Şu anda şu biçimler desteklenir: MP4, MOV ve WMV.

## <a name="face-detector-output-files"></a>Yüz algılayıcısı çıkış dosyaları
Yüz algılama ve izleme API 'SI, bir videoda en fazla 64 insan yüzü algılayan yüksek duyarlıklı bir konum algılama ve izleme sağlar. En iyi yüzler, yüz ve küçük yüzler (24x24 pikselden küçük veya buna eşit) doğru şekilde en iyi sonuçları sağlar.

Algılanan ve izlenen yüzler, görüntüdeki yüzlerin ve bu kişinin izlenmesini belirten yüz KIMLIK numarası ile birlikte koordinatları (sol, üst, genişlik ve yükseklik) ile döndürülür. Yüz KIMLIĞI numaraları, en alt yüz kayıp veya çerçevede örtüşdüğünde, bazı kişilerin birden çok kimliğe atanabileceği durumlar altında sıfırlanır.

## <a id="output_elements"></a>Çıktı JSON dosyasının öğeleri

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Yüz algılayıcısı parçalanma tekniklerini (meta verilerin zaman tabanlı öbeklere bölünebileceği ve yalnızca ihtiyaç duyduğunuz şeyi indirebileceği) ve kesimlemeye (çok büyük bir zaman aldığı durumlarda olayların koptulabileceği) kullanır. Bazı basit hesaplamalar, verileri dönüştürmenize yardımcı olabilir. Örneğin, bir olay 6300 (Ticks) ile başlatıldıysa, zaman ölçeği 2997 (ticks/sn) ve (kare/sn) 29,97 kare hızı ile:

* Başlangıç/Ölçek = 2,1 saniye
* Saniye x kare hızı = 63 kare

## <a name="face-detection-input-and-output-example"></a>Yüz algılama giriş ve çıkış örneği
### <a name="input-video"></a>Video gir
[Video gir](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Görev yapılandırması (önceden ayarlanmış)
**Azure Media Face Detector**bir görev oluştururken, bir yapılandırma ön ayarı belirtmeniz gerekir. Aşağıdaki yapılandırma ön ayarı yalnızca yüz algılama için kullanılır.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Öznitelik açıklamaları
| Öznitelik adı | Açıklama |
| --- | --- |
| Mod |Hızlı işleme hızı, ancak daha az doğru (varsayılan).|

### <a name="json-output"></a>JSON çıkışı
Aşağıdaki JSON çıktısı örneği kesildi.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Duygu algılama giriş ve çıkış örneği
### <a name="input-video"></a>Video gir
[Video gir](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Görev yapılandırması (önceden ayarlanmış)
**Azure Media Face Detector**bir görev oluştururken, bir yapılandırma ön ayarı belirtmeniz gerekir. Aşağıdaki yapılandırma ön ayarı, duygu tanıma algılamasına göre JSON oluşturmayı belirtir.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Öznitelik açıklamaları
| Öznitelik adı | Açıklama |
| --- | --- |
| Mod |Yüzler: yalnızca yüz algılama.<br/>Perceemotion: her yüz algılama için duygu tanıma 'ı bağımsız olarak döndürün.<br/>Aggregateemotion: çerçevedeki tüm yüzler için Ortalama duygu tanıma değeri döndürür. |
| AggregateEmotionWindowMs |AggregateEmotion modunu seçtiyseniz kullanın. Her toplama sonucunu oluşturmak için kullanılan videonun milisaniye cinsinden uzunluğunu belirtir. |
| Aggregateemotionınterlationms |AggregateEmotion modunu seçtiyseniz kullanın. Toplam sonuçların nasıl üretileceğini belirten sıklığı belirtir. |

#### <a name="aggregate-defaults"></a>Toplam varsayılanlar
Toplam pencere ve Aralık ayarları için önerilen değerler aşağıda verilmiştir. AggregateEmotionWindowMs, Aggregateemotionınterlationms 'den daha uzun olmalıdır.

|| Varsayılanlar | En fazla (s) | En az (s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| Aggregateemotionınterlationms |0,5 |1 |0.25|

### <a name="json-output"></a>JSON çıkışı
Toplam duygu tanıma için JSON çıktısı (kesilmiş):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Sınırlamalar
* Desteklenen giriş video biçimleri, MP4, MOV ve WMV 'yi içerir.
* Algılanabilir yüz boyut aralığı 24x24-2048x2048 pikseldir. Bu aralığın dışına çıkan yüzler algılanmayacak.
* Her video için döndürülen en fazla yüz sayısı 64 ' dir.
* Teknik sorunlar nedeniyle bazı yüzler algılanmayabilir; Örneğin, çok büyük yüz açıları (baş poz) ve büyük occlusiyon. En iyi ve yakın yüzlerin en iyi sonuçları vardır.

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. Aşağıdaki JSON ön ayarını içeren bir yapılandırma dosyasını temel alan bir yüz algılama göreviyle iş oluşturun: 

    ```json
            {
                "version": "1.0"
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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Azure Media Analytics gösterileri](https://amslabs.azurewebsites.net/demos/Analytics.html)

