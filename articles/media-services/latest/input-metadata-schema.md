---
title: Azure Media Services v3 giriş meta verileri şeması
description: Bu makale, Azure Media Services v3 giriş meta verisi şemasına genel bir bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634694"
---
# <a name="input-metadata"></a>Giriş meta verileri

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bir kodlama işi, üzerinde bazı kodlama görevlerini gerçekleştirmek istediğiniz bir giriş varlığı (veya varlıkları) ile ilişkilendirilir.  Bir görevi tamamladıktan sonra bir çıkış varlığı üretilir. Çıktı varlığı video, ses, küçük resim, bildirim ve diğer dosyaları içerir. 

Çıktı varlığı, giriş varlığı hakkında meta veriler içeren bir dosya da içerir. Meta veri JSON dosyasının adı rastgele bir KIMLIĞE sahiptir, çıkış varlığının ait olduğu giriş varlığını belirlemek için bunu kullanmayın. Ait olduğu giriş varlığını belirlemek için `Uri` alanını kullanın (daha fazla bilgi için bkz. [diğer alt öğeler](#other-child-elements)).  

Media Services, meta veri oluşturmak için giriş varlıklarını tarapreemptively. Giriş meta verileri yalnızca bir Iş içinde bir giriş varlığı işlendiğinde yapıt olarak üretilir. Bu nedenle, bu yapıt çıkış varlığına yazılır. Farklı araçlar, giriş varlıkları ve çıkış varlıkları için meta veriler oluşturmak için kullanılır. Bu nedenle, giriş meta verilerinin, çıkış meta verileri arasında biraz farklı bir şeması vardır.

Bu makalede, giriş metada ( &lt; asset_id &gt;_metadata.jsüzerinde) temel aldığı JSON şemasının öğeleri ve türleri açıklanmaktadır. Çıktı varlığı hakkında meta veriler içeren dosya hakkında daha fazla bilgi için bkz. [Çıkış meta verileri](output-metadata-schema.md).  

JSON şeması örneğini Bu makalenin sonunda bulabilirsiniz.  

## <a name="assetfile"></a>AssetFile  

Kodlama işi için Assetdosya öğelerinin bir koleksiyonunu içerir.  

> [!NOTE]
> Aşağıdaki dört alt öğe bir dizide yer almalıdır.

| Ad  | Açıklama |
| --- | --- | 
| **Videoizler**|Her fiziksel varlık dosyası sıfır veya daha fazla video, araya eklemeli bir şekilde uygun bir kapsayıcı biçimine sahip olabilir. Daha fazla bilgi için bkz. [Videoiz](#videotracks). |
| **Ses Izleri**|Her fiziksel varlık dosyası, uygun bir kapsayıcı biçiminde araya eklemeli sıfır veya daha fazla ses parçası içerebilir. Daha fazla bilgi için bkz. [ses izleri](#audiotracks) |
| **Meta veri**  |Varlık dosyasının meta verileri key\value dizeleri olarak temsil edilir. <br />Örnek: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Diğer alt öğeler

| Ad | Açıklama |
| --- | --- |
| **Ad**<br />Gerekli |Varlık dosya adı. <br /><br />Örnek: `"Name": "Ignite-short.mp4"` |
| **Kullanılmamışsa**<br />Gerekli |Giriş varlığının bulunduğu URL. Çıkış varlığının ait olduğu giriş varlığını belirlemek için, `Uri` kimlik yerine alanını kullanın.|
| **Boyut**<br />Gerekli |Varlık dosyasının bayt cinsinden boyutu.  <br /><br />Örnek: `"Size": 75739259`|
| **Süre**<br />Gerekli |İçerik kayıttan yürütme süresi. <br /><br />Örnek: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Gerekli |Varlık dosyasındaki akış sayısı.  <br /><br />Örnek: `"NumberOfStreams": 2`|
| **FormatNames**<br />Gerekli |Biçim adları.  <br /><br />Örnek: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Gerekli |Ayrıntı adlarını biçimlendirin. <br /><br />Örnek: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |İçerik başlangıç saati.  <br /><br />Örnek: `"StartTime": "PT0S"` |
| **Overallbit hızı** |Saniye başına bit cinsinden varlık dosyasının Ortalama bit hızı.  <br /><br />Örnek: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>Videoizler

| Ad | Açıklama |
| --- | --- |
| **FourCC**<br />Gerekli |FFmpeg tarafından raporlanan video codec FourCC kodu.<br /><br />Örnek: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Profil** |Video izleme profili. <br /><br />Örnek: `"Profile": "Main"`|
| **Düzeyde** |Video izlemenin düzeyi. <br /><br />Örnek: `"Level": "3.2"`|
| **PixelFormat 'a dönüştürme** |Video izlemenin piksel biçimi. <br /><br />Örnek: `"PixelFormat": "yuv420p"`|
| **Genişlik**<br />Gerekli |Piksel cinsinden kodlanmış video genişliği. <br /><br />Örnek: `"Width": "1280"`|
| **Yükseklik**<br />Gerekli |Piksel cinsinden kodlanmış video yüksekliği.<br /><br />Örnek: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Gerekli |Video görüntüleme en boy oranı payı.<br /><br />Örnek: `"DisplayAspectRatioNumerator": 16.0` |
| **Displayaspectratiopayda**<br />Gerekli |Video görüntüleme en boy oranı paydası. <br /><br />Örnek: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Video örneği en boy oranı payı. <br /><br />Örnek: `"SampleAspectRatioNumerator": 1.0`|
| **Sampleaspectratiopayda**|Örnek: `"SampleAspectRatioDenominator": 1.0`|
| **Kare hızı değerini**<br />Gerekli |.3F biçiminde ölçülen video kare hızı. <br /><br />Örnek: `"FrameRate": 29.970`|
| **Bit hızı** |Varlık dosyasından hesaplanan bit/saniye cinsinden ortalama video bit hızı. Yalnızca öğesel akış yükü sayılır ve paketleme ek yükü dahil değildir. <br /><br />Örnek: `"Bitrate": 8421583`|
| **HasBFrames** |Video parça sayısı B kare. <br /><br />Örnek: `"HasBFrames": 2`|
| **Meta veri** |Çeşitli bilgileri tutmak için kullanılabilen genel anahtar/değer dizeleri. <br />Makalenin sonundaki tam örneğe bakın. |
| **Numarasını**<br />Gerekli |Bu sesin veya video izlemenin sıfır tabanlı dizini.<br /><br /> Bu **kimliğin** bir MP4 dosyasında kullanılan TrackID olması gerekmez. <br /><br />Örnek: `"Id": 2`|
| **Bileşeni** |Video izleme codec bileşeni dizesi. <br /><br />Örnek: `"Codec": "h264 | hev1"`|
| **CodecLongName** |Ses veya video izleme codec bileşeni uzun adı. <br /><br />Örnek: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Bileşeni** |Video izleme codec bileşeni dizesi. <br /><br />Örnek: `"Codec": "h264 | hev1"`|
| **Temeli**<br />Gerekli |Zaman tabanı.<br /><br />Örnek: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Çerçeve sayısı (video parçaları için mevcut). <br /><br />Örnek: `"NumberOfFrames": 2107`|
| **StartTime** |Başlangıç saatini izleyin.<br /><br />Örnek: `"StartTime": "PT0.033S"` |
| **Süre** |Süreyi izleyin. <br /><br />Örnek: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>Ses Izleri

| Ad  | Açıklama |
| --- | --- | 
| **SampleFormat** |Örnek biçim. <br /><br />Örnek: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Kanal düzeni. <br /><br />Örnek: `"ChannelLayout": "stereo"`|
| **Kanallar**<br />Gerekli |Ses kanalı sayısı (0 veya daha fazla). <br /><br />Örnek: `"Channels": 2`|
| **SamplingRate**<br />Gerekli |Örnek/sn veya Hz 'teki ses örnekleme oranı. <br /><br />Örnek: `"SamplingRate": 48000`|
| **Bit hızı** |Varlık dosyasından hesaplanan bit/saniye cinsinden ortalama ses bit hızı. Yalnızca öğesel akış yükü sayılır ve paketleme ek yükü bu sayıma dahil değildir. <br /><br />Örnek: `"Bitrate": 192080`|
| **Meta veri** |Çeşitli bilgileri tutmak için kullanılabilen genel anahtar/değer dizeleri.  <br />Makalenin sonundaki tam örneğe bakın. |
| **Numarasını**<br />Gerekli |Bu sesin veya video izlemenin sıfır tabanlı dizini.<br /><br /> Bu, bir MP4 dosyasında kullanılan TrackID olması gerekmez. <br /><br />Örnek: `"Id": 1`|
| **Bileşeni** |Video izleme codec bileşeni dizesi. <br /><br />Örnek: `"Codec": "aac"`|
| **CodecLongName** |Ses veya video izleme codec bileşeni uzun adı. <br /><br />Örnek: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Temeli**<br />Gerekli |Zaman tabanı.<br /><br />Örnek: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Çerçeve sayısı (video parçaları için mevcut). <br /><br />Örnek: `"NumberOfFrames": 3294`|
| **StartTime** |Başlangıç saatini izleyin. Daha fazla bilgi için bkz. [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Örnek: `"StartTime": "PT0S"` |
| **Süre** |Süreyi izleyin. <br /><br />Örnek: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Meta veri

| Ad | Açıklama |
| --- | --- |
| **anahtar**<br />Gerekli |Anahtar/değer çiftindeki anahtar. |
| **değer**<br /> Gerekli |Anahtar/değer çiftindeki değer. |

## <a name="schema-example"></a>Şema örneği

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Çıkış meta verileri](output-metadata-schema.md)
