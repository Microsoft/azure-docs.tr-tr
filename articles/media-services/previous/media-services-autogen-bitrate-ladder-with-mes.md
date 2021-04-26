---
title: Bit hızı el merdivenini otomatik oluşturmak için Media Encoder Standard kullanın-Azure | Microsoft Docs
description: Bu konuda, giriş çözünürlüğü ve bit hızı temelinde bit hızı bir el ile oluşturma için Media Encoder Standard (MES) kullanımı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6ea28d61cc142c3191d591721b92e08d651c7ed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014409"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Bit hızı merdiveni otomatik olarak oluşturmak için Media Encoder Standard kullanın

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Genel Bakış

Bu makalede, giriş çözümüne ve bit hızına göre bit hızı bir el ile (bit hızı çözme çiftleri) otomatik olarak oluşturmak için Media Encoder Standard (MES) nasıl kullanılacağı gösterilmektedir. Otomatik olarak oluşturulan önayar hiçbir şekilde giriş çözünürlüğünü ve bit hızını aşmaz. Örneğin, giriş 3 Mbps hızında olursa çıktı en iyi 720p kalır ve 3 Mbps 'den düşük oranlarda başlatılır.

### <a name="encoding-for-streaming-only"></a>Yalnızca akış için kodlama

Amaç, kaynak videonuzu yalnızca akış için kodlayıp, bir kodlama görevi oluştururken "uyarlamalı akış" ön ayarını kullanmanız gerekir. **Uyarlamalı akış** önayarı KULLANıLıRKEN, mes Kodlayıcısı bit hızı bir el ile büyük bir şekilde çalışır. Ancak, hizmet kaç katmanın kullanılacağını ve hangi çözünürlükte çözümlendiğinden, kodlama maliyetlerini denetleyemeyeceksiniz. Bu makalenin sonundaki **uyarlamalı akış** ön ayarıyla kodlama sonucu olarak, mes tarafından üretilen çıkış katmanlarının örneklerini görebilirsiniz. Çıkış varlığı, ses ve videonun araya eklemeli olduğu MP4 dosyaları içerir.

### <a name="encoding-for-streaming-and-progressive-download"></a>Akış ve aşamalı Indirme için kodlama

Amaç, kaynak videonuzu akış için kodlayıp ve aşamalı indirme için MP4 dosyaları oluşturmak istiyorsanız, bir kodlama görevi oluştururken "Content Uyarlamalı çoklu bit hızı MP4" ön ayarını kullanmanız gerekir. **Content Uyarlamalı çoklu bit hızı MP4** ön ayarını KULLANıRKEN, mes Kodlayıcısı yukarıdaki gibi aynı kodlama mantığını uygular, ancak artık çıkış varlığı ses ve videonun ARALANMıŞ olduğu MP4 dosyalarını içerecektir. Bu MP4 dosyalarından birini (örneğin, en yüksek bit hızı sürümü) aşamalı indirme dosyası olarak kullanabilirsiniz.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Media Services .NET SDK ile kodlama

Aşağıdaki kod örneği aşağıdaki görevleri gerçekleştirmek için Media Services .NET SDK kullanır:

- Bir kodlama işi oluşturun.
- Media Encoder Standard Kodlayıcısı için bir başvuru alın.
- İşe bir kodlama görevi ekleyin ve **uyarlamalı akış** ön ayarını kullanmayı belirtin. 
- Kodlanmış varlığı içeren bir çıkış varlığı oluşturun.
- İşin ilerlemesini denetlemek için bir olay işleyicisi ekleyin.
- İşi gönder.

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 

#### <a name="example"></a>Örnek

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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

## <a name="output"></a><a id="output"></a>Çıktı

Bu bölümde, **uyarlamalı akış** ön ayarıyla kodlama sonucu olarak, mes tarafından üretilen çıkış katmanlarının üç örneği gösterilmektedir. 

### <a name="example-1"></a>Örnek 1
"1080" yüksekliğinde ve "29,970" kare hızına sahip kaynak 6 video katmanı üretir:

|Katman|Height|Width|Bit hızı (Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Örnek 2
"720" yüksekliğinde ve "23,970" kare hızına sahip kaynak 5 video katmanı üretir:

|Katman|Height|Width|Bit hızı (Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Örnek 3
"360" yüksekliğinde ve "29,970" kare hızına sahip kaynak 3 video katmanı üretir:

|Katman|Height|Width|Bit hızı (Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Media Services kodlamaya genel bakış](media-services-encode-asset.md)

