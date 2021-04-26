---
title: Media Encoder Standard önayarlarını özelleştirme | Microsoft Docs
description: Bu konu, Media Encoder Standard görev önayarlarını özelleştirerek gelişmiş kodlamanın nasıl gerçekleştirileceğini gösterir. Bu konuda, bir kodlama görevi ve işi oluşturmak için .NET SDK Media Services nasıl kullanılacağı gösterilmektedir. Ayrıca, kodlama işine özel ön ayarların nasıl alınacağını gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 758a2fc607217f38306f8d50273c580daf523982
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492703"
---
# <a name="customizing-media-encoder-standard-presets"></a>Media Encoder Standard önayarlarını özelleştirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Genel Bakış

Bu makalede, özel bir ön ayar kullanılarak Media Encoder Standard (MES) ile gelişmiş kodlama gerçekleştirme işlemi gösterilmektedir. Bu makalede, bir kodlama görevi ve bu görevi yürüten bir iş oluşturmak için .NET kullanılır.  

Bu makalede, [H264 çoklu bit hızı 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) önayarını gerçekleştirerek ve katman sayısını azaltarak bir önayarı nasıl özelleştireceğiniz gösterilmektedir. [Media Encoder Standard özelleştirmeyi özelleştirme](media-services-advanced-encoding-with-mes.md) makalesinde, gelişmiş kodlama görevlerini gerçekleştirmek için kullanılabilecek özel önayarlar gösterilir.

> [!NOTE]
> Bu makalede açıklanan özel ön ayarlar [Media Services v3](../latest/index.yml) dönüştürmelerde veya CLI komutlarında kullanılamaz. Daha fazla ayrıntı için bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md) .

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> MES ön ayarını özelleştirme

### <a name="original-preset"></a>Özgün önayar

. JSON uzantılı bazı dosyadaki [H264 çoklu bit hızı 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) MAKALESINDE tanımlanan JSON 'ı kaydedin. Örneğin, **üzerindeCustomPreset_JSON.js**.

### <a name="customized-preset"></a>Özelleştirilmiş önayar

Dosyasında **CustomPreset_JSON.js** açın ve **H264Layers** 'den ilk üç katmanı kaldırarak dosyanızı şöyle bir şekilde görünür.

```json 
  {  
    "Version": 1.0,  
    "Codecs": [  
      {  
        "KeyFrameInterval": "00:00:02",  
        "H264Layers": [  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 1000,  
            "MaxBitrate": 1000,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 650,  
            "MaxBitrate": 650,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 400,  
            "MaxBitrate": 400,  
            "BufferWindow": "00:00:05",  
            "Width": 320,  
            "Height": 180,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          }  
        ],  
        "Type": "H264Video"  
      },  
      {  
        "Profile": "AACLC",  
        "Channels": 2,  
        "SamplingRate": 48000,  
        "Bitrate": 128,  
        "Type": "AACAudio"  
      }  
    ],  
    "Outputs": [  
      {  
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
        "Format": {  
          "Type": "MP4Format"  
        }  
      }  
    ]  
  }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Media Services .NET SDK ile kodlama

Aşağıdaki kod örneği aşağıdaki görevleri gerçekleştirmek için Media Services .NET SDK kullanır:

- Bir kodlama işi oluşturun.
- Media Encoder Standard Kodlayıcısı için bir başvuru alın.
- Önceki bölümde oluşturduğunuz özel JSON ön ayarını yükleyin. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- İşe bir kodlama görevi ekleyin. 
- Kodlanacak giriş varlığını belirtin.
- Kodlanmış varlığı içeren bir çıkış varlığı oluşturun.
- İşin ilerlemesini denetlemek için bir olay işleyicisi ekleyin.
- İşi gönder.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
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

    }
}
```

## <a name="see-also"></a>Ayrıca bkz.

- [CLı kullanarak özel dönüşümle kodlama](../latest/transform-custom-preset-cli-how-to.md)
- [Media Services v3 ile kodlama](../latest/encode-concept.md)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
