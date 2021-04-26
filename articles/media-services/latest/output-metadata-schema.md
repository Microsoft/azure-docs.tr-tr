---
title: Azure Media Services çıktı meta verileri şeması
description: Bu makale, Azure Media Services v3 çıkış meta veri şemasına genel bir bakış sunar.
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
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612465"
---
# <a name="output-metadata"></a>Çıkış meta verileri

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bir kodlama işi, üzerinde bazı kodlama görevlerini gerçekleştirmek istediğiniz bir giriş varlığı (veya varlıkları) ile ilişkilendirilir. Örneğin, bir MP4 dosyasını H. küçük resim oluşturma; yer paylaşımları oluştur. Bir görevi tamamladıktan sonra bir çıkış varlığı üretilir.  Çıktı varlığı video, ses, küçük resim ve diğer dosyaları içerir. Çıktı varlığı Ayrıca çıkış varlığı hakkında meta veriler içeren bir dosya içerir. Meta veri JSON dosyasının adı şu biçimdedir: `<source_file_name>_manifest.json` (örneğin, `BigBuckBunny_manifest.json` ). Üzerinde herhangi bir * _metadata.jstarama yapmanız ve kaynak dosya adını (kesilmeksizin) bulmak için içindeki FilePath dizesinde sorgu yapmanız gerekir.

Media Services, meta veri oluşturmak için giriş varlıklarını tarapreemptively. Giriş meta verileri yalnızca bir iş içinde bir giriş varlığı işlendiğinde yapıt olarak üretilir. Bu nedenle, bu yapıt çıkış varlığına yazılır. Farklı araçlar, giriş varlıkları ve çıkış varlıkları için meta veriler oluşturmak için kullanılır. Bu nedenle, giriş meta verilerinin, çıkış meta verileri arasında biraz farklı bir şeması vardır.

Bu makalede, çıkış meta verilerinin ( &lt; source_file_name &gt;_manifest.jsüzerinde) temel aldığı JSON şemasının öğeleri ve türleri açıklanmaktadır. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Tüm şema kodunu ve JSON örneğini Bu makalenin sonunda bulabilirsiniz.  

## <a name="assetfile"></a>AssetFile

Kodlama işi için Assetdosya girişlerinin toplanması.  

| Ad | Açıklama |
| --- | --- |
| **Kaynaklar** |Bu Assetdosyasını üretmek için işlenen giriş/kaynak medya dosyalarının koleksiyonu.<br />Örnek: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **Videoizler**|Her fiziksel Assetdosyası bu dosyada sıfır veya daha fazla video ile ilgili bir kapsayıcı biçiminde araya eklenebilir. <br />Bkz. [Videoiz](#videotracks). |
| **Ses Izleri**|Her fiziksel Assetdosyası bu dosyada sıfır veya daha fazla ses parçası ile uygun bir kapsayıcı biçimiyle bulunabilir. Bu, tüm ses izlemelerinin koleksiyonudur.<br /> Daha fazla bilgi için bkz. [Audioiz](#audiotracks). |
| **Ad**<br />Gerekli |Medya varlık dosya adı. <br /><br />Örnek: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Boyut**<br />Gerekli |Varlık dosyasının bayt cinsinden boyutu. <br /><br />Örnek: `"Size": 32414631`|
| **Süre**<br />Gerekli |İçerik kayıttan yürütme süresi. Daha fazla bilgi için bkz. [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) biçimi. <br /><br />Örnek: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>Videoizler 

Her fiziksel Assetdosyası bu dosyada sıfır veya daha fazla video ile ilgili bir kapsayıcı biçiminde araya eklenebilir. **Videoiz** öğesi tüm video izlemelerinin bir koleksiyonunu temsil eder.  

| Ad | Açıklama |
| --- | --- |
| **Numarasını**<br /> Gerekli |Bu video izlemenin sıfır tabanlı dizini. **Note:**  Bu **kimliğin** bir MP4 dosyasında kullanılan TrackID olması gerekmez. <br /><br />Örnek: `"Id": 1`|
| **FourCC**<br />Gerekli | FFmpeg tarafından raporlanan video codec FourCC kodu.  <br /><br />Örnek: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **Profil** |H264 profili (yalnızca H264 codec için geçerlidir)  <br /><br />Örnek: `"Profile": "High"` |
| **Düzeyde** |H264 düzeyi (yalnızca H264 codec için geçerlidir).  <br /><br />Örnek: `"Level": "3.2"`|
| **Genişlik**<br />Gerekli |Piksel cinsinden kodlanmış video genişliği.  <br /><br />Örnek: `"Width": "1280"`|
| **Yükseklik**<br />Gerekli |Piksel cinsinden kodlanmış video yüksekliği.  <br /><br />Örnek: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Gerekli|Video görüntüleme en boy oranı payı.  <br /><br />Örnek: `"DisplayAspectRatioNumerator": 16.0`|
| **Displayaspectratiopayda**<br />Gerekli |Video görüntüleme en boy oranı paydası.  <br /><br />Örnek: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />Gerekli |.3F biçiminde ölçülen video kare hızı.  <br /><br />Örnek: `"Framerate": 29.970`|
| **Bit hızı**<br />Gerekli |Assetdosyasından hesaplanan şekilde saniye başına bit cinsinden ortalama video bit hızı. Yalnızca öğesel akış yükünü sayar ve paketleme ek yükünü içermez.  <br /><br />Örnek: `"Bitrate": 3551567`|
| **Targetbit hızı**<br />Gerekli |Bu video izinin, saniye başına bit cinsinden kodlama ön ayarı aracılığıyla istenen Ortalama bit hızını hedefleyin. <br /><br />Örnek: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>Ses Izleri 

Her fiziksel Assetdosyası bu dosyada sıfır veya daha fazla ses parçası ile uygun bir kapsayıcı biçimiyle bulunabilir. **Audioizler** öğesi tüm ses izlemelerinin bir koleksiyonunu temsil eder.  

| Ad  | Açıklama |
| --- | --- |
| **Numarasını**<br />Gerekli  |Bu ses parçasının sıfır tabanlı dizini. **Note:**  Bu, bir MP4 dosyasında kullanılan TrackID olması gerekmez.  <br /><br />Örnek: `"Id": 2`|
| **Bileşeni**  |Ses izi codec bileşeni dizesi.  <br /><br />Örnek: `"Codec": "aac"`|
| **Dil**|Örnek: `"Language": "eng"`|
| **Kanallar**<br />Gerekli|Ses kanalı sayısı.  <br /><br />Örnek: `"Channels": 2`|
| **SamplingRate**<br />Gerekli |Örnek/sn veya Hz 'teki ses örnekleme oranı.  <br /><br />Örnek: `"SamplingRate": 48000`|
| **Bit hızı**<br />Gerekli |Assetdosyasından hesaplanan, saniye başına bit cinsinden ortalama ses bit hızı. Yalnızca öğesel akış yükünü sayar ve paketleme ek yükünü içermez.  <br /><br />Örnek: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>JSON şeması örneği

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

[HTTPS URL 'sinden iş girişi oluşturma](job-input-from-http-how-to.md)
