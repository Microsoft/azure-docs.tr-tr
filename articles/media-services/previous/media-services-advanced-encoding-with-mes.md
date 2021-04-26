---
title: MES ön ayarlarını özelleştirerek gelişmiş kodlama gerçekleştirme | Microsoft Docs
description: Bu konu, Media Encoder Standard görev önayarlarını özelleştirerek gelişmiş kodlamanın nasıl gerçekleştirileceğini gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 345fd56e5ff069ea222661a4820e75d96a9e2395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014511"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>MES ön ayarlarını özelleştirerek gelişmiş kodlama gerçekleştirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

Bu konuda Media Encoder Standard önayarlarının nasıl özelleştirileceği gösterilmektedir. [Özel ön ayarları kullanarak Media Encoder Standard kodlama](media-services-custom-mes-presets-with-dotnet.md) , bir kodlama görevi ve bu görevi yürüten bir iş oluşturmak için .net 'in nasıl kullanılacağını gösterir. Bir önayarı özelleştirdikten sonra kodlama görevine özel ön ayarları sağlayın. 

XML önayarı kullanıyorsanız, aşağıdaki XML örneklerinde gösterildiği gibi öğelerin sırasını koruduğunuzdan emin olun (örneğin, keyFrameInterval, manzara 'nin önüne gelmelidir).

> [!NOTE] 
> Media Encoder Standard Gelişmiş Media Services V2 özelliklerinin birçoğu şu anda v3 'de kullanılamaz. Daha fazla bilgi için [geçiş kılavuzuna](../latest/migrate-v-2-v-3-migration-introduction.md)bakın.

## <a name="support-for-relative-sizes"></a>Göreli boyutlar için destek

Küçük resim oluştururken, her zaman piksel cinsinden çıkış genişliğini ve yüksekliğini belirtmeniz gerekmez. Bunları yüzde cinsinden, [1%,..., %100] aralığında belirtebilirsiniz.

### <a name="json-preset"></a>JSON önceden ayarı

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML önayarı

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Küçük resim oluşturma

Bu bölümde, küçük resim üreten bir önayarın nasıl özelleştirileceği gösterilmektedir. Aşağıda tanımlanan önceden tanımlı ayar, dosyanızın nasıl kodlanacağını ve küçük resim oluşturmak için gereken bilgileri içerir. [Bu](media-services-mes-presets-overview.md) bölümde belgelenen mes ön ayarlarından herhangi birini alabilir ve küçük resimler üreten kodlar ekleyebilirsiniz.  

> [!NOTE]
> Aşağıdaki ön ayarda bulunan **manzara** , tek bir bit hızlı videoya kodlarken yalnızca true olarak ayarlanabilir. Çoklu bit hızına sahip bir videoya kodlarken ve **manzara** ' i true olarak ayarlarsanız, kodlayıcı bir hata döndürür.  
>
>

Şema hakkında daha fazla bilgi için [Bu](media-services-mes-schema.md) konuya bakın.

[Konular](#considerations) bölümünü gözden geçirdiğinizden emin olun.

### <a name="json-preset"></a>JSON önceden ayarı

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
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
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Aşağıdaki noktalara dikkat edilmelidir:

* Başlangıç/adım/Aralık için açık zaman damgalarının kullanımı, giriş kaynağının en az 1 dakika uzunluğunda olduğunu varsayar.
* JPG/PNG/Bmpımage öğelerinde başlangıç, adım ve Aralık dizesi öznitelikleri vardır; bunlar şöyle yorumlanabilecek:

  * Negatif olmayan tamsayılarla çerçeve numarası, örneğin "Başlat": "120",
  * %-Sonfixed olarak ifade edilen kaynak süresine göreli, örneğin "Başlat": "15%" veya
  * SS: DD: SS olarak ifade edilen zaman damgası... biçim, örneğin "Başlat": "00:01:00"

    Gösterimleri sizin için karıştırabilir ve eşleştirebilirsiniz.

    Ayrıca, Başlat, içerik notunun ilk "ilginç" çerçevesini belirlemeyi deneyen {Best} özel makrosunu da destekler: (başlatma {En Iyi} olarak ayarlandığında adım ve Aralık yok sayılır)
  * Varsayılanlar: başlangıç: {En Iyi}
* Her görüntü biçimi için çıkış biçiminin açıkça sağlanması gerekir: jpg/png/BmpFormat. Mevcut olduğunda, JpgVideo ile JpgFormat arasında devam eder. OutputFormat, görüntü çıkış biçimleri için mevcut olması gereken (bir kez ve yalnızca bir kez) yeni bir görüntü codec bileşeni ({Index}) sunuyor.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Video kırpma (kırpma)
Bu bölümde, girişin bir so dosyası veya isteğe bağlı dosya olduğu giriş videosunu kırpmak veya kırpmak için Kodlayıcı ön ayarlarını değiştirme hakkında bilgi verilir. Kodlayıcı, canlı bir akıştan yakalanan veya arşivlenen bir varlığı kırpmak veya kırpmak için de kullanılabilir. bunun ayrıntıları [Bu blogda](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)alınabilir.

Videolarınızı kırpmak için, [Bu](media-services-mes-presets-overview.md) bölümü belgelenen mes ön ayarlarından herhangi birini alabilir ve **kaynaklar** öğesini (aşağıda gösterildiği gibi) değiştirebilirsiniz. StartTime değerinin, giriş videosunun mutlak zaman damgalarına uyması gerekir. Örneğin, giriş videosunun ilk karesinde 12:00:10.000 zaman damgası varsa StartTime, en az 12:00:10.000 ve daha büyük olmalıdır. Aşağıdaki örnekte, giriş videosunun, sıfır başlangıç zaman damgasına sahip olduğunu varsayalım. **Kaynaklar** , önayarın başlangıcına yerleştirilmelidir.

### <a name="json-preset"></a><a id="json"></a>JSON önceden ayarı

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
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

### <a name="xml-preset"></a>XML önayarı
Videolarınızı kırpmak için [burada](media-services-mes-presets-overview.md) belgelenen mes ön ayarlarından herhangi birini alabilir ve **kaynaklar** öğesini (aşağıda gösterildiği gibi) değiştirebilirsiniz.

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
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
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Bir kaplama oluşturma

Media Encoder Standard, görüntünün var olan bir videonun üzerinde yer almasına olanak tanır. Şu anda şu biçimler desteklenir: PNG, jpg, GIF ve BMP. Aşağıda tanımlanan önceden tanımlı, video kaplamasıyla temel bir örnektir.

Önceden ayarlanmış bir dosya tanımlamaya ek olarak, varlık içindeki hangi dosyanın yer kaplayan görüntü olduğunu ve görüntünün üzerine eklemek istediğiniz kaynak video olduğunu Media Services de bilmeniz gerekir. Video dosyası, **birincil** dosya olmalıdır.

.NET kullanıyorsanız, [Bu](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) konuda açıklanan .net örneğine aşağıdaki iki işlevi ekleyin. **Uploadmediafilesfromfolder** işlevi bir klasörden dosya yükler (örneğin, BigBuckBunny.mp4 ve Image001.png) ve MP4 dosyasını varlıktaki birincil dosya olacak şekilde ayarlar. **EncodeWithOverlay** işlevi, kodlama görevini oluşturmak için kendisine geçirilen özel önceden ayarlanmış dosyayı (örneğin, izleyen ön ayar) kullanır.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Geçerli sınırlamalar:
>
> Kaplama geçirgenliği ayarı desteklenmiyor.
>
> Kaynak video dosyanızın ve kaplama görüntü dosyasının aynı varlık içinde olması gerekir ve video dosyasının bu varlık içinde birincil dosya olarak ayarlanması gerekir.
>
>

### <a name="json-preset"></a>JSON önceden ayarı

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML önayarı

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Girişte ses olmadığında sessiz bir ses izi ekleyin
Varsayılan olarak, yalnızca video içeren ve ses içermeyen kodlayıcıya bir giriş gönderirseniz, çıkış varlığı yalnızca video verilerini içeren dosyaları içerir. Bazı oyuncular bu tür çıkış akışlarını işleyemeyebilir. Bu ayarı, kodlayıcının Bu senaryodaki çıkışa sessiz ses izi eklemesini zorlamak için kullanabilirsiniz.

Bir giriş sesi olmadığında, kodlayıcının sessiz ses izi içeren bir varlık üretmesine zorlamak için "ınsertsilenceifnoaudio" değerini belirtin.

[Bu](media-services-mes-presets-overview.md) bölümde belgelenen mes ön ayarlarından herhangi birini alabilir ve aşağıdaki değişikliği yapabilirsiniz:

### <a name="json-preset"></a>JSON önceden ayarı

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML önayarı

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Otomatik taramayı devre dışı bırak
Taramasız içerik otomatik olarak titreşimsiz olmasını istiyorsanız müşterilerin hiçbir şey yapması gerekmez. Otomatik tarama açık olduğunda (varsayılan), aralıklı çerçevelerin otomatik olarak algılanmasını ve yalnızca titreşimli interlaces çerçevelerini aralıklı olarak işaretlenir.

Otomatik taramayı devre dışı bırakabilirsiniz. Bu seçenek önerilmez.

### <a name="json-preset"></a>JSON önceden ayarı

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML önayarı

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Yalnızca ses önayarları
Bu bölümde, yalnızca iki sesli MES önayarları gösterilmektedir: AAC audio ve AAC Iyi kalitede ses.

### <a name="aac-audio"></a>AAC ses

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>AAC Iyi kalitede ses

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>İki veya daha fazla video dosyasını birleştirme

Aşağıdaki örnek, iki veya daha fazla video dosyasını birleştirmek için nasıl önayar oluşturabileceğiniz gösterilmektedir. En yaygın senaryo, ana videoya bir üst bilgi veya bir artbilgisi eklemek istemektir. Tasarlanan kullanım, video dosyalarının birlikte düzenlenme özelliği (video çözünürlüğü, çerçeve oranı, ses izleme sayısı vb.) ile düzenlenme amaçlıdır. Farklı kare hızlarındaki videoları veya farklı sayıda ses parçasını karıştırmamak için dikkatli olmanız gerekir.

>[!NOTE]
>Birleştirme özelliğinin geçerli tasarımı, giriş video kliplerinin çözümleme, kare hızı vb. bakımından tutarlı olmasını bekler. 

### <a name="requirements-and-considerations"></a>Gereksinimler ve önemli noktalar

* Giriş videolarının yalnızca bir ses izi olmalıdır.
* Giriş videolarının hepsi aynı kare hızına sahip olmalıdır.
* Videolarınızı ayrı varlıklara yüklemeniz ve videoları her bir varlığın birincil dosyası olarak ayarlamanız gerekir.
* Videolarınızın süresini bilmeniz gerekir.
* Aşağıdaki önceden ayarlanmış örneklerde, tüm giriş videolarının sıfır zaman damgasıyla başlayacağı varsayılır. Videolarda farklı başlangıç zaman damgası varsa, genellikle canlı Arşivlerle ilgili olduğu gibi StartTime değerlerini değiştirmeniz gerekir.
* JSON önceden ayarı, giriş varlıklarının AssetID değerlerine açık başvurular yapar.
* Örnek kod, JSON önayarının "C:\supportFiles\preset.json" gibi yerel bir dosyaya kaydedildiğini varsayar. Ayrıca iki video dosyası karşıya yüklendiğinde iki varlık oluşturulduğunu ve sonuç AssetID değerlerini bildiğinizi varsayar.
* Kod parçacığı ve JSON önceden kümesi, iki video dosyasını birleştirerek bir örnek gösterir. Bunu iki videoya göre genişletebilirsiniz:

  1. Görev çağrılıyor. Inputassets. daha fazla video eklemek için art arda. Add ().
  2. Aynı sırada daha fazla girdi ekleyerek JSON 'daki "Sources" öğesinde karşılık gelen düzenlemeler yapılıyor.

### <a name="net-code"></a>.NET kodu

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON önceden ayarı

Özel ön ayarlarınızı, birleştirmek istediğiniz varlıkların kimlikleriyle ve her video için uygun zaman segmentiyle güncelleştirin.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Media Encoder Standard ile videoları kırpma
[Media Encoder Standard ile videoları kırpın](media-services-crop-video.md) konusuna bakın.

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Girişte video yoksa video izlemesi ekleyin

Varsayılan olarak, yalnızca ses içeren ve video içermeyen kodlayıcıya bir giriş gönderirseniz, çıkış varlığı yalnızca ses verileri içeren dosyaları içerir. Azure Media Player dahil bazı oyuncular (bkz. [Bu) bu](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)akışları işleyemeyebilir. Bu ayarı, kodlayıcının Bu senaryodaki çıktıya tek renkli bir video izlemesi eklemesini zorlamak için kullanabilirsiniz.

> [!NOTE]
> Bir çıktı eklemek için kodlayıcıyı zorlamak, çıkış varlığının boyutunu artırır ve bu nedenle kodlama görevi için ücret uygulanır. Bu sonuçta elde edilen artışın yalnızca aylık ücretlerinizde bir VAST etkisi olduğunu doğrulamak için testler çalıştırmalısınız.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Videoyu yalnızca en düşük bit hızında ekleme

Video dosyalarının ve yalnızca ses dosyalarının bir karışımını içeren, akış için tüm giriş kataloğunuzun kodlanmasını sağlamak için ["H264 çoklu bit hızı 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) gibi birden çok bit hızı kodlamalı bir ön ayar kullandığınızı varsayalım. Bu senaryoda, girişte video yoksa, kodlayıcının her çıkış bit hızında video ekleme yerine yalnızca en düşük bit hızında bir tek renkli video izlemesi eklemesini zorlamak isteyebilirsiniz. Bunu başarmak için, **ınsertblackıce** ' ı kullanın.

[Bu](media-services-mes-presets-overview.md) bölümde belgelenen mes ön ayarlarından herhangi birini alabilir ve aşağıdaki değişikliği yapabilirsiniz:

#### <a name="json-preset"></a>JSON önceden ayarı

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML önayarı

XML kullanırken, **H264Video** öğesi ve Condition = "ınsertsilenceifnoses" özniteliği olarak bir Attribute olarak "ınsertblackıce ıfnovideobottomlayeronly" **kullanın.**

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Tüm çıkış bittarifelerinde video ekleme
Video dosyalarının ve yalnızca ses dosyalarının bir karışımını içeren akış için tüm giriş kataloğunuzu kodlamak amacıyla ["H264 çoklu bit hızı 720p"](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) gibi birden çok bit hızı kodlamalı bir ön ayar kullandığınızı varsayalım. Bu senaryoda, girişte hiçbir video yoksa, kodlayıcının tüm çıkış bittarifelerinde tek renkli bir video izlemesi eklemesini zorlamak isteyebilirsiniz. Bu, çıkış varlıklarınızın video izlemelerinin ve ses izlemelerinin sayısına göre tüm hogenlerin olmasını sağlar. Bunu başarmak için, "ınsertblackıce Ifnovideo" bayrağını belirtmeniz gerekir.

[Bu](media-services-mes-presets-overview.md) bölümde belgelenen mes ön ayarlarından herhangi birini alabilir ve aşağıdaki değişikliği yapabilirsiniz:

#### <a name="json-preset"></a>JSON önceden ayarı

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML önayarı

XML kullanırken, "ınsertblackıce Ifnovideo" öğesini **H264Video** öğesi için bir öznitelik olarak ve Condition = "ınsertsilenceifnoses" öğesini **aacaudio** öğesine bir öznitelik olarak kullanın.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Videoyu döndürme
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) , 0/90/180/270 açılara dönüşü destekler. Varsayılan davranış, gelen video dosyasındaki döndürme meta verilerini algılamaya ve bunu dengedenemeye çalıştığı "Auto" dır. Aşağıdaki **kaynaklar** öğesini [Bu](media-services-mes-presets-overview.md) bölümde tanımlanan önayarlardan birine ekleyin:

### <a name="json-preset"></a>JSON önceden ayarı

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML önayarı

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Ayrıca, kodlayıcının, döndürme tazminatı tetiklendiğinde ön ayarda genişlik ve yükseklik ayarlarını nasıl yorumlayacağına ilişkin daha fazla bilgi için [Bu](media-services-mes-schema.md#PreserveResolutionAfterRotation) konuya bakın.

"0" değerini, giriş videosunda, varsa döndürme meta verilerini yok saymaya işaret etmek için kullanabilirsiniz.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Media Services kodlamaya genel bakış](media-services-encode-asset.md)
