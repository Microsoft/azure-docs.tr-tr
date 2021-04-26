---
title: Azure Media Analytics yüzeyleri redaksiyonu Microsoft Docs
description: Azure Media Redactor, bulutta ölçeklenebilir yüz redaksiyon sağlayan bir Azure Media Analytics medya işlemcisidir. Bu makalede, Azure Medya Analizi ile yüzleri nasıl redaksiyonun yapılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d416810f6a39fb36bfa3c5225301fe87fdd128c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013474"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Azure Media Analytics ile yüzeyleri redaksiyonu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

**Azure Media Redactor** , bulutta ölçeklenebilir yüz redaksiyon sağlayan bir [Azure Media Analytics](./legacy-components.md) medya işlemcisidir (MP). Yüz Redaksiyon, seçili kişilerin yüzlerini bulanıklaştırmak için videonuzu değiştirmenize olanak sağlar. Yüz redaksiyon hizmetini genel güvenlik ve haber medya senaryolarında kullanmak isteyebilirsiniz. Birden çok yüz içeren birkaç dakikalık bir çekimi, el ile redaksiyona kadar zaman alabilir, ancak bu hizmetle yüz redaksiyon süreci yalnızca birkaç basit adım gerektirir.

Bu makale, **Azure Media Redactor** ayrıntılarını sağlar ve .net IÇIN Media Services SDK ile nasıl kullanacağınızı gösterir.

## <a name="face-redaction-modes"></a>Yüz redaksiyon modları

Yüz Redaksiyon, her video çerçevesindeki yüzleri algılayarak ve yüz nesnesini aynı anda ileri ve geri doğru şekilde izlerken, aynı bireyin diğer açılardan de bulanıklaşır. Otomatik redaksiyon süreci karmaşıktır ve istenen çıktının her zaman %100 ' unu üretmez. bu nedenle Media Analytics, nihai çıktıyı değiştirmek için birkaç yol sağlar.

Tam otomatik moda ek olarak, bir kimlik listesi aracılığıyla bulunan yüzlerin seçimine/seçilmesine izin veren iki taramalı bir iş akışı vardır. Ayrıca, her çerçeve için rastgele ayarlama yapmak üzere MP, JSON biçiminde bir meta veri dosyası kullanır. Bu iş akışı **analiz** ve **redakct** modlarına bölünür. İki modu, her iki görevi de tek bir işte çalıştıran tek bir geçişte birleştirebilirsiniz; Bu mod **Birleşik** olarak adlandırılır.

   > [!NOTE]
   > Yüz algılayıcısı medya Işlemcisi, Haziran 2020 ' den itibaren [eski Azure Media Services bileşenleri](./legacy-components.md)kullanımdan kaldırılmıştır. Azure Media Services v3 API 'sini kullanmayı düşünün.

### <a name="combined-mode"></a>Birleşik mod

Bu, el ile herhangi bir girdi olmadan redaksiyonu otomatik olarak oluşturur.

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |foo.bar |WMV, har veya MP4 biçimindeki video |
| Giriş yapılandırması |İş yapılandırması önayarı |{' version ': ' 1.0 ', ' Seçenekler ': {' Mode ': ' Birleşik '}} |
| Çıkış varlığı |foo_redacted.mp4 |Bulanıklaştırma uygulanmış video |

### <a name="analyze-mode"></a>Çözümleme modu

İki taramalı iş akışının **Çözümle** geçişi bir video girişi alır ve algılanan her bir yüz IÇIN bir JSON dosyası ve bir dizi görüntü oluşturur.

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |foo.bar |WMV, MPV veya MP4 biçimindeki video |
| Giriş yapılandırması |İş yapılandırması önayarı |{' version ': ' 1.0 ', ' Seçenekler ': {' Mode ': ' Analyze '}} |
| Çıkış varlığı |Üzerinde foo_annotations.js |JSON biçimindeki yüz konumlarının ek açıklama verileri. Bu, bulanıklaştırma sınırlayıcı kutularını değiştirmek için Kullanıcı tarafından düzenlenebilir. Aşağıdaki örneğe bakın. |
| Çıkış varlığı |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Algılanan her bir yüzün kırpılan bir jpg (sayının, yüzün LabelId 'sini gösterdiği) |

#### <a name="output-example"></a>Çıkış örneği

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-mode"></a>Redact modu

İş akışının ikinci geçişi, tek bir varlık içinde birleştirilmesi gereken daha fazla sayıda giriş alır.

Bu, bulanıklaştırmak üzere bir kimlik listesi, özgün video ve ek açıklama JSON 'u içerir. Bu mod, giriş videosunda bulanıklaştırma uygulamak için ek açıklamaları kullanır.

Analiz geçişinin çıktısı özgün videoyu içermez. Videonun, Redakct modu görevi için giriş varlığına yüklenmesi ve birincil dosya olarak seçilmesi gerekir.

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |foo.bar |WMV, MPV veya MP4 biçimindeki video. 1. adımdaki ile aynı video. |
| Giriş varlığı |Üzerinde foo_annotations.js |isteğe bağlı değişiklikler ile birinci aşamadan sonra gelen açıklama meta verileri dosyası. |
| Giriş varlığı |foo_IDList.txt (Isteğe bağlı) |Redaksiyonlar için isteğe bağlı yeni satıra ayrılmış yüz kimlikleri listesi. Boş bırakılırsa bu, tüm yüzleri bulanıklaştırır. |
| Giriş yapılandırması |İş yapılandırması önayarı |{' version ': ' 1.0 ', ' Seçenekler ': {' Mode ': ' Redact '}} |
| Çıkış varlığı |foo_redacted.mp4 |Ek açıklamalar temelinde uygulanan bulanıklık ile video |

#### <a name="example-output"></a>Örnek çıkış

Bu, bir ID seçili olan bir ıdlist öğesinden alınan çıktıdır.

Örnek foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Bulanıklaştırma türleri

**Birleşik** veya **REDAKCT** modunda, JSON giriş yapılandırması aracılığıyla seçebileceğiniz 5 farklı bulanıklaştırma modu vardır: **düşük**, **Med**, **High**, **Box** ve **Black**. Varsayılan olarak **Med** kullanılır.

Aşağıdaki bulanıklaştırma türlerinin örneklerini bulabilirsiniz.

### <a name="example-json"></a>Örnek JSON

```json
{
    'version':'1.0',
    'options': {
        'Mode': 'Combined',
        'BlurType': 'High'
    }
}
```

#### <a name="low"></a>Düşük

![Düşük](./media/media-services-face-redaction/blur1.png)

#### <a name="med"></a>Olduğundan

![Olduğundan](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Yüksek

![Yüksek](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Siyahi

![Siyah](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Çıktı JSON dosyasının öğeleri

Redaksiyon MP, bir video çerçevesinde 64 adede kadar insan yüzü algılayan yüksek duyarlıklı bir konum algılama ve izleme sağlar. Yüz yüze yüzler en iyi sonuçları sağlar, kenar yüzeyleri ve küçük yüzler (24x24 pikselden küçük veya buna eşit) zor olur.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
2. Aşağıdaki JSON ön ayarını içeren bir yapılandırma dosyasını temel alan yüz redaksiyon göreviyle bir iş oluşturun: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Çıktı JSON dosyalarını indirin.

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar

[Azure Media Services Analytics genel bakışı](./legacy-components.md)

[Azure Media Analytics gösterileri](http://amslabs.azurewebsites.net/demos/Analytics.html)
