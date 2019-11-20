---
title: Media Encoder Standard-Azure ile videoları kırpma | Microsoft Docs
description: Bu makalede videoların Media Encoder Standard nasıl kırpılacağını gösterir.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 03d68cc3a60abba8b7189a9d03fbc21d7606f736
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "69016620"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Media Encoder Standard ile videoları kırpma  

Giriş videonuzu kırpmak için Media Encoder Standard (MES) kullanabilirsiniz. Kırpma, video çerçevesi içinde dikdörtgen bir pencere seçme ve yalnızca bu penceredeki pikselleri kodlama işlemidir. Aşağıdaki diyagram, işlemi göstermeye yardımcı olur.

![Videoyu kırpın](./media/media-services-crop-video/media-services-crop-video01.png)

1920x1080 piksel (16:9 en boy oranı) çözümüne sahip bir video girişi olduğunu varsayalım, ancak sol ve sağ tarafta siyah çubuklar (ekran kutuları) vardır; böylece yalnızca bir 4:3 penceresi veya 1440x1080 piksel etkin video içerir. Siyah çubukları kırpmak veya düzenlemek ve 1440x1080 bölgesini kodlamak için MES ' i kullanabilirsiniz.

MES 'in kırpılması bir ön işleme aşamasıdır, bu nedenle kodlama ön ayarlamadaki kırpma parametreleri özgün giriş videosu için geçerlidir. Kodlama sonraki bir aşamadır ve genişlik/yükseklik ayarları orijinal videoya değil, *önceden işlenmiş* videoya uygulanır. Önceden ayarlarınızı tasarlarken şunları yapmanız gerekir: (a) orijinal giriş videosunu temel alan kırpma parametrelerini seçin ve (b) kırpılan videoya göre kodlama ayarlarınızı seçin. Kırpılan video ile kodlama ayarlarınızı eşleşmiyorsa, çıkış istediğiniz gibi olmayacaktır.

[Aşağıdaki](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) konuda, bir kodlama işinin nasıl oluşturulduğu ve kodlama görevi için özel bir hazır ayarın nasıl ayarlanacağı gösterilmektedir. 

## <a name="creating-a-custom-preset"></a>Özel önayar oluşturma
Diyagramda gösterilen örnekte:

1. Özgün giriş 1920x1080
2. Giriş çerçevesinde ortalanan, 1440x1080 çıkışındaki bir çıktıya kırpılmaya gerekir
3. Bu, (1920 – 1440)/2 = 240 X ve Y değeri sıfır olan X sapmasını gösterir
4. Kırpma dikdörtgeninin genişliği ve yüksekliği sırasıyla 1440 ve 1080 ' dir
5. Kodlama aşamasında, ask üç katman oluşturmak, sırasıyla çözünürlük 1440x1080, 960x720 ve 480x360 şeklindedir

### <a name="json-preset"></a>JSON önceden ayarı
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


## <a name="restrictions-on-cropping"></a>Kırpmadan kısıtlamalar
Kırpma özelliği el ile olacak şekilde tasarlanmıştır. Giriş videonuzu, ilgi çekici çerçeveler seçmenizi sağlayan uygun bir araç aracına yüklemeniz, imleci, kırpma dikdörtgeninin kaydırmalarını belirlemek için işaretçiyi, söz konusu video için ayarlanmış kodlama ön ayarını belirlemek üzere konumlandırmanızı (vb.) yüklemeniz gerekir. Bu özellik, giriş Videonuzdaki siyah harf kutusu/pillarbox kenarlıklarının otomatik algılanması ve kaldırılması gibi şeyleri etkinleştirmek için tasarlanmamıştır.

Aşağıdaki kısıtlamalar kırpma özelliği için geçerlidir. Bunlar karşılanmazsa, kodlama görevi başarısız olabilir veya beklenmeyen bir çıkış üretebilir.

1. Kırpma dikdörtgeninin birlikte bulunan ve boyutunun, giriş videosunun içine sığması gerekir
2. Yukarıda belirtildiği gibi, kodlama ayarlarındaki genişlik & yüksekliğinin kırpılan videoya karşılık gelmesi gerekir
3. Kırpma, yatay modda yakalanan videolar için geçerlidir (örn. dikey olarak veya dikey modda tutulan bir akıllı telefonlarla Kaydedilen videoların geçerli olmadığı)
4. Kare pikselleriyle yakalanan ilerleyen videolar ile en iyi şekilde geçerlidir

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Sonraki adım
AMS tarafından sunulan harika özellikler hakkında bilgi edinmenize yardımcı olması için bkz. Azure Media Services öğrenme yolları.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
