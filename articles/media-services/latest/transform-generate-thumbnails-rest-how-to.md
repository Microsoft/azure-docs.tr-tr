---
title: Media Encoder Standard REST kullanarak küçük resimler oluşturma
description: Bu makalede, Media Encoder Standard kullanarak aynı anda bir varlık kodlamak ve küçük resimler oluşturmak için REST 'in nasıl kullanılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8688f0a477f3fa0a5fced64c288e14132f041299
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492452"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>REST ile kodlayıcı standardını kullanarak küçük resimleri oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Giriş videoınızdan [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)veya [BMP](https://en.wikipedia.org/wiki/BMP_file_format) resim dosyası biçimlerinde bir veya daha fazla küçük resim oluşturmak için Media Encoder Standard kullanabilirsiniz.

## <a name="recommended-reading-and-practice"></a>Önerilen okuma ve uygulama

Özel dönüşümlerle [nasıl kodlanacağını](transform-custom-preset-rest-how-to.md)okuyarak özel dönüştürmeleri tanıdık yapmanız önerilir.

## <a name="thumbnail-parameters"></a>Küçük resim parametreleri

Aşağıdaki parametreleri ayarlamanız gerekir:

- **Başlat** -küçük resim oluşturmaya başlamak için giriş videosunun konumu. Değer ISO 8601 biçiminde (örneğin, 5 saniye içinde başlayacak şekilde PT05S) veya bir çerçeve sayısı (örneğin, 10 ' dan başlamak için 10) ya da akış süresine yönelik göreli bir değer (örneğin, akış süresinin %10 ' u ile başlaması için %10) olabilir. Ayrıca, kodlayıcıya videonun ilk birkaç saniyesinin en iyi küçük resmini seçmesini ve adım ve aralığa yönelik diğer ayarların ne olduğuna bakılmaksızın yalnızca bir küçük resim üretecağını belirten {Best} makrosunu destekler. Varsayılan değer makro {En Iyi} ' dır.
- **adım** -küçük resimlerin oluşturulduğu aralıklar. Değer ISO 8601 biçiminde olabilir (örneğin, her 5 saniyede bir görüntü için PT05S) veya çerçeve sayısı (örneğin, her 30 karede bir görüntü için 30) veya akış süresine yönelik göreli bir değer (örneğin, her %10 akış süresi için bir görüntü için %10) olabilir. Adım değeri, ilk oluşturulan küçük resmi etkiler ve bu, yalnızca dönüştürme önceden ayarlanmış başlangıç saatinde belirtilen bir değer olmayabilir. Bunun nedeni, ilk çıkış olarak başlangıç saatinden başlangıç saati ve adım konumu arasındaki en iyi küçük resmi seçmesini sağlayan kodlayıcıdır. Varsayılan değer %10 olduğundan, akışın uzun süresi varsa, ilk oluşturulan küçük resim başlangıç saatinde belirtilen bir değerden uzakta olabilir. İlk küçük resmin başlangıç saatine yakın olması bekleniyorsa, adım için makul bir değer seçmeyi deneyin veya Başlangıç zamanında yalnızca bir küçük resim gerekliyse Aralık değerini 1 olarak ayarlayın.
- **Aralık** -küçük resim oluşturmanın durdurulması için giriş videosunda önceden ayarlanmış bir başlangıç zamanına göre konum. Değer ISO 8601 biçiminde olabilir (örneğin, başlangıç zamanından önce 5 dakika ve 30 saniye içinde PT5M30S için) veya bir çerçeve sayısı (örneğin, Başlangıç zamanında çerçevenin 300th çerçevesini durdurmak için 300). Bu değer 1 ise, Başlangıç zamanında yalnızca bir küçük resim üretildiğini belirtir) veya akış süresine göreli bir değer (örneğin, başlangıç zamanından itibaren akış süresinin yarısı kadar durmak üzere %50). Varsayılan değer %100 ' dir, bu da akışın sonunda durmak anlamına gelir.
- **Katmanlar** -kodlayıcı tarafından üretilecek çıkış görüntüsü katmanlarından oluşan bir koleksiyon.

## <a name="example-of-a-single-png-file-preset"></a>"Tek PNG dosyası" önayarı örneği

Aşağıdaki JSON ön ayarı, giriş videosunun ilk birkaç saniyesindeki tek bir çıkış PNG dosyası oluşturmak için kullanılabilir ve bu, kodlayıcının "ilgi çekici" bir çerçeve bulmada en iyi çaba denemesi yapar. Çıkış resmi boyutlarının %100 olarak ayarlandığını, yani bu, giriş videosunun boyutlarıyla eşleştiğini unutmayın. Ayrıca "çıktılar" içindeki "biçim" ayarının "bir" codec bileşenleri "bölümünde" Pngkatmanları "kullanımıyla eşleşmesi için nasıl gerekli olduğunu göz önünde bulabilirsiniz.  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>"Bir dizi JPEG görüntüsü" ön ayarı örneği

Aşağıdaki JSON ön ayarı, zaman damgalarının %5 ' i, %15 ' i,..., giriş zaman çizelgesinin %95 ' inde, görüntü boyutunun giriş videosunun bir çeyreği olacak şekilde belirtildiği bir dizi 10 görüntü oluşturmak için kullanılabilir.

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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>"Belirli bir zaman damgasında bir resim" önceden ayarlanmış örneği

Aşağıdaki JSON önayarı, giriş videosunun 30 saniyelik işaretinde tek bir JPEG görüntüsü oluşturmak için kullanılabilir. Bu önayar, giriş videosunun süre içinde 30 saniyeden uzun olmasını bekler (Aksi takdirde iş başarısız olur).

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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>"Farklı çözünürlüklerden küçük resimler" önceden ayarlanmış örneği

Aşağıdaki önayar, bir görevde farklı çözünürlüklere küçük resimler oluşturmak için kullanılabilir. Örnekte, konumlarda %5, %15%,..., giriş zaman çizelgesinin %95 ' unda, kodlayıcı iki görüntü oluşturur: biri, giriş video çözünürlüğünün %100 ' i ve diğeri de %50.

Dosya adında {Resolution} makrosunun kullanımını göz önünde edin; Bu, kodlayıcının, çıkış görüntülerinin dosya adını oluştururken önceden ayarlanmış kodlama bölümünde belirttiğiniz genişlik ve yüksekliği kullanacağını gösterir. Bu Ayrıca, farklı görüntüleri kolayca ayırt etmenize de yardımcı olur.

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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Kodlama sırasında küçük resim oluşturma örneği

Yukarıdaki örneklerin hepsi yalnızca görüntü üreten bir kodlama görevini nasıl gönderebileceğinizi ele alırken video/ses kodlamasını küçük resim oluşturma ile de birleştirebilirsiniz. Aşağıdaki JSON önayarı, kodlayıcı standardına kodlama sırasında küçük resim oluşturmasını söyler.

### <a name="json-preset"></a>JSON önceden ayarı

Şema hakkında daha fazla bilgi için [Bu](../previous/media-services-mes-schema.md) makaleye bakın.

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

## <a name="next-steps"></a>Sonraki adımlar
[.NET kullanarak küçük resimler oluşturma](transform-generate-thumbnails-dotnet-how-to.md)