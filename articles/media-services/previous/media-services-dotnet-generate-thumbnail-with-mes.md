---
title: Media Encoder Standard ve .NET kullanarak küçük resim oluşturma
description: Bu konuda, Media Encoder Standard kullanarak aynı anda bir varlık kodlamak ve küçük resimler oluşturmak için .NET kullanımı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0159a706bd5f5229a12ab52bc92d240f01a2ed11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014052"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Media Encoder Standard ve .NET kullanarak küçük resim oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Giriş videoınızdan [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)veya [BMP](https://en.wikipedia.org/wiki/BMP_file_format) resim dosyası biçimlerinde bir veya daha fazla küçük resim oluşturmak için Media Encoder Standard kullanabilirsiniz. Yalnızca görüntü üreten görevler gönderebilir veya küçük resim oluşturmayı kodlama ile birleştirebilirsiniz. Bu makale, bu tür senaryolar için birkaç örnek XML ve JSON küçük ayarı sağlar. Makalenin sonunda, kodlama görevini gerçekleştirmek için Media Services .NET SDK ' nın nasıl kullanılacağını gösteren [örnek bir kod](#code_sample) vardır.

Örnek ön ayarlarda kullanılan öğeler hakkında daha fazla bilgi için [Media Encoder Standard şemayı](media-services-mes-schema.md)gözden geçirmeniz gerekir.

[Konular](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) bölümünü gözden geçirdiğinizden emin olun.
    
## <a name="example-of-a-single-png-file-preset"></a>"Tek PNG dosyası" önayarı örneği

Aşağıdaki JSON ve XML önayarı, giriş videosunun ilk birkaç saniyesindeki tek bir çıkış PNG dosyası oluşturmak için kullanılabilir ve bu, kodlayıcının "ilginç" bir çerçeve bulmak için en iyi çaba denemesi yapar. Çıkış resmi boyutlarının %100 olarak ayarlandığını, yani bu, giriş videosunun boyutlarıyla eşleştiğini unutmayın. Ayrıca "çıktılar" içindeki "biçim" ayarının "bir" codec bileşenleri "bölümünde" Pngkatmanları "kullanımıyla eşleşmesi için nasıl gerekli olduğunu göz önünde bulabilirsiniz. 

### <a name="json-preset"></a>JSON önceden ayarı

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML önayarı

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>"Bir dizi JPEG görüntüsü" ön ayarı örneği

Aşağıdaki JSON ve XML önayarı, zaman damgalarının %5 ' i, %15 ' i,..., %95, giriş zaman çizelgesinin, görüntü boyutunun giriş videosunun bir çeyrekte olduğu bir üç aylık dönem olarak belirtildiği bir dizi 10 görüntü oluşturmak için kullanılabilir.

### <a name="json-preset"></a>JSON önceden ayarı

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML önayarı
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>"Belirli bir zaman damgasında bir resim" önceden ayarlanmış örneği

Aşağıdaki JSON ve XML önayarı, giriş videosunun 30 saniyelik işaretinde tek bir JPEG görüntüsü oluşturmak için kullanılabilir. Bu önayar, giriş videosunun süre içinde 30 saniyeden uzun olmasını bekler (Aksi takdirde iş başarısız olur).

### <a name="json-preset"></a>JSON önceden ayarı

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML önayarı
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>"Farklı çözünürlüklerden küçük resimler" önceden ayarlanmış örneği

Aşağıdaki önayar, bir görevde farklı çözünürlüklere küçük resimler oluşturmak için kullanılabilir. Örnekte, konumlarda %5, %15%,..., giriş zaman çizelgesinin %95 ' unda, kodlayıcı iki görüntü oluşturur: biri, giriş video çözünürlüğünün %100 ' i ve diğeri de %50.

Dosya adında {Resolution} makrosunun kullanımını göz önünde edin; Bu, kodlayıcının, çıkış görüntülerinin dosya adını oluştururken önceden ayarlanmış kodlama bölümünde belirttiğiniz genişlik ve yüksekliği kullanacağını gösterir. Bu Ayrıca, farklı görüntüleri kolayca ayırt etmenize de yardımcı olur

### <a name="json-preset"></a>JSON önceden ayarı

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML önayarı
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Kodlama sırasında küçük resim oluşturma örneği

Yukarıdaki örneklerin hepsi yalnızca görüntü üreten bir kodlama görevini nasıl gönderebileceğinizi ele alırken video/ses kodlamasını küçük resim oluşturma ile de birleştirebilirsiniz. Aşağıdaki JSON ve XML önayarı kodlama sırasında küçük resim oluşturmak **Media Encoder Standard** söyler.

### <a name="json-preset"></a><a id="json"></a>JSON önceden ayarı
Şema hakkında daha fazla bilgi için [Bu](./media-services-mes-schema.md) makaleye bakın.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML önayarı
Şema hakkında daha fazla bilgi için [Bu](./media-services-mes-schema.md) makaleye bakın.

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>.NET ile video kodlama ve küçük resim oluşturma

Aşağıdaki kod örneği aşağıdaki görevleri gerçekleştirmek için Media Services .NET SDK kullanır:

* Bir kodlama işi oluşturun.
* Media Encoder Standard Kodlayıcısı için bir başvuru alın.
* Kodlama ön ayarını ve küçük resim oluşturmak için gereken bilgileri içeren [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) veya [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) 'yi yükleyin. Bu  [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) veya [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) dosyasını bir dosyaya kaydedebilir ve dosyayı yüklemek için aşağıdaki kodu kullanabilirsiniz.

    ```csharp
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

* İşe tek bir kodlama görevi ekleyin. 
* Kodlanacak giriş varlığını belirtin.
* Kodlanmış varlığı içeren bir çıkış varlığı oluşturun.
* İşin ilerlemesini denetlemek için bir olay işleyicisi ekleyin.
* İşi gönder.

Geliştirme ortamınızı ayarlama hakkında yönergeler için bkz. [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) makalesi.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Dikkat edilmesi gerekenler
Aşağıdaki noktalara dikkat edilmelidir:

* Başlangıç/adım/Aralık için açık zaman damgalarının kullanımı, giriş kaynağının en az 1 dakika uzunluğunda olduğunu varsayar.
* JPG/PNG/Bmpımage öğelerinde başlangıç, adım ve Aralık dizesi öznitelikleri vardır; bunlar şöyle yorumlanabilecek:
  
  * Negatif olmayan tamsayılarla çerçeve numarası, örneğin "Başlat": "120",
  * %-Sonfixed olarak ifade edilen kaynak süresine göreli, örneğin "Başlat": "15%" veya
  * SS: DD: SS olarak ifade edilen zaman damgası... formatını. Örneğin, "Başlat": "00:01:00"
    
    Gösterimleri sizin için karıştırabilir ve eşleştirebilirsiniz.
    
    Ayrıca, Başlat, içerik notunun ilk "ilginç" çerçevesini belirlemeyi deneyen {Best} özel makrosunu da destekler: (başlatma {En Iyi} olarak ayarlandığında adım ve Aralık yok sayılır)
  * Varsayılanlar: başlangıç: {En Iyi}
* Her görüntü biçimi için çıkış biçiminin açıkça sağlanması gerekir: jpg/png/BmpFormat. Mevcut olduğunda, JpgVideo ile JpgFormat arasında devam eder. OutputFormat, görüntü çıkış biçimleri için mevcut olması gereken (bir kez ve yalnızca bir kez) yeni bir görüntü codec bileşeni ({Index}) sunuyor.

## <a name="next-steps"></a>Sonraki adımlar

Kodlama işi beklenirken [işin ilerlemesini](media-services-check-job-progress.md) kontrol edebilirsiniz.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Media Services kodlamaya genel bakış](media-services-encode-asset.md)