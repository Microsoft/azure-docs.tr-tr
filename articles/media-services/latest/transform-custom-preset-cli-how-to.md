---
title: Özel bir dönüşüm CLı kodlaması
description: Bu konu, Azure CLı kullanarak özel bir dönüşüm kodlamak için Azure Media Services v3 'in nasıl kullanılacağını gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d3977b02bffd49762bdcb69fa4940052518c9d25
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499043"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Özel bir dönüşümle kodlama-Azure CLı

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services ile kodlarken, [akış dosyaları](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) hızlı başlangıç bölümünde gösterildiği gibi, sektörde en iyi uygulamaları temel alan önerilen yerleşik ön ayarlardan biriyle hızlı bir başlangıç yapabilirsiniz. Ayrıca, belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir ön ayar oluşturabilirsiniz.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Özel ön ayarlar oluşturulurken aşağıdaki noktalar geçerlidir:

* AVC içeriğinde yükseklik ve genişlik değerlerinin tümü 4 ' ün katı olmalıdır.
* Azure Media Services v3 'de, tüm kodlama bit fiyatları bit/saniye cinsinden. Bu, birim olarak kilobit/saniye kullanan v2 API 'lerimiz olan ön ayarlardan farklıdır. Örneğin, v2 'deki bit hızı 128 (kilobit/saniye) olarak belirtilmişse, v3 'de 128000 (bit/saniye) olarak ayarlanır.

## <a name="prerequisites"></a>Önkoşullar

[Media Services hesabı oluşturun](./account-create-how-to.md).

Kaynak grubu adını ve Media Services hesap adını hatırlayacağınızdan emin olun.

## <a name="define-a-custom-preset"></a>Özel bir önayar tanımlama

Aşağıdaki örnek, yeni bir dönüşümün istek gövdesini tanımlar. Bu dönüşüm kullanıldığında oluşturulmasını istediğimiz bir çıktı kümesi tanımladık.

Bu örnekte, önce ses kodlaması ve video kodlaması için iki H264Video katmanı için bir AacAudio katmanı ekleyeceğiz. Video katmanlarında, çıkış dosyası adlarında kullanılabilmesi için Etiketler atacağız. Ardından, çıktının küçük resimleri de içermesini istiyoruz. Aşağıdaki örnekte, resimleri, giriş videonun çözümlenme %50 ' de ve üç zaman 75 50 damgalarına (örneğin, giriş videosunun uzunluğuna) göre, PNG biçiminde belirttik. Son olarak, çıkış dosyalarının biçimini, video + ses için, diğeri ise küçük resimler için belirttik. Birden çok H264Layers olduğundan, her katman için benzersiz adlar üreten makrolar kullandık. Bir `{Label}` veya `{Bitrate}` makrosunu kullanabilir, örnek eski ' yi gösterir.

Bu dönüşümü bir dosyaya kaydedecekiz. Bu örnekte, dosyayı adı veririz `customPreset.json` .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
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
```

## <a name="create-a-new-transform"></a>Yeni dönüşüm oluştur  

Bu örnekte, daha önce tanımladığımız özel hazır ayarı temel alan bir **dönüşüm** oluşturacağız. Bir dönüşüm oluştururken, önce bir tane olup olmadığını denetlemeniz gerekir. Dönüşüm varsa, yeniden kullanın. Aşağıdaki komut, varsa `show` `customTransformName` dönüşümü döndürür:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Aşağıdaki Azure CLı komutu özel önayar (daha önce tanımlanan) temel alarak dönüşümü oluşturur.

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Media Services belirtilen videoya veya sese dönüştürmeyi uygulamak için, o dönüşüm altında bir Iş göndermeniz gerekir. Bir dönüştürme altında iş göndermeyi gösteren tam bir örnek için bkz. [hızlı başlangıç: Stream video Files-Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Ayrıca bkz.

[Azure CLI](/cli/azure/ams)
