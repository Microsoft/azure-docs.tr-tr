---
title: Media Services .NET ile video dosyalarını kırpma | Microsoft Docs
description: Kırpma, video çerçevesi içinde dikdörtgen bir pencere seçme ve yalnızca bu penceredeki pikselleri kodlama işlemidir. Bu konu başlığında, .NET kullanarak video dosyalarının Media Services nasıl kırpılacağını gösterilmektedir.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572442"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Media Services .NET ile video dosyalarını kırpma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Giriş videosunu kırpmak için Media Services kullanabilirsiniz. Kırpma, video çerçevesi içinde dikdörtgen bir pencere seçme ve yalnızca bu penceredeki pikselleri kodlama işlemidir. Aşağıdaki diyagram, işlemi göstermeye yardımcı olur.

## <a name="pre-processing-stage"></a>Ön işleme aşaması

Kırpma bir ön işleme aşamasıdır, bu nedenle kodlama ön ayarlamadaki *kırpma parametreleri* *giriş* videosu için geçerlidir. Kodlama sonraki bir aşamadır ve genişlik/yükseklik ayarları orijinal videoya değil, *önceden işlenmiş* videoya uygulanır. Önceden ayarlarınızı tasarlarken şunları yapın:

1. Özgün giriş videosunu temel alarak kırpma parametrelerini seçin
1. Kırpılan videoya göre kodlama ayarlarınızı seçin.

> [!WARNING]
> Kırpılan video ile kodlama ayarlarınızı eşleşmiyorsa, çıkış istediğiniz gibi olmayacaktır.

Örneğin, giriş videonuzda bir 1920x1080 piksel çözünürlüğü vardır (16:9 en boy oranı), ancak sol ve sağ tarafta siyah çubuklar (ekran kutuları) bulunur, böylece yalnızca bir 4:3 penceresi veya 1440x1080 piksel etkin video içerir. Siyah çubukları kırpabilir ve 1440x1080 alanını kodlayabilirsiniz.

## <a name="transform-code"></a>Kodu Dönüştür

Aşağıdaki kod parçacığı videoları kırpmak için .NET ' te bir dönüşümün nasıl yazılacağını gösterir.  Kod, birlikte çalışmak için yerel bir dosyanız olduğunu varsayar.

- Sol, kırpın en sol konumudur.
- Üstten kırpın en üst konumudur.
- Genişlik, kırpmaın son genişliğidir.
- Yükseklik, kırpmaın son yüksekliğidir.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
