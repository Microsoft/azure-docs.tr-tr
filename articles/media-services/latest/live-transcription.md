---
title: Canlı döküm Azure Media Services | Microsoft Docs
description: Bu makalede, Azure Media Services canlı dökümünün ne olduğu açıklanmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 98084b9bb6f19d22c7995d3044bb32ceaa947dc5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040430"
---
# <a name="live-transcription-preview"></a>Canlı döküm (Önizleme)

Azure Medya hizmeti, farklı protokollerde video, ses ve artık metin sağlar. MPEG-DASH veya HLS/CMAF kullanarak canlı akışınızı yayımladığınızda ve video ve sesle birlikte hizmetimiz, MPEG-4 Bölüm 30 ' a (ISO/ıEC 14496-30) paketlenmiş ve ıMSC 1.1 uyumlu TTML 'ye paketlenmiş metin teslim eder. HLS/TS aracılığıyla teslim kullanılıyorsa, metin öbekli VTT olarak dağıtılır. 

Bu makalede, Azure Media Services v3 ile canlı bir olay akışı yapılırken canlı bir olay nasıl etkinleştirileceği açıklanır. Devam etmeden önce Media Services v3 REST API 'lerinin kullanımına alışkın olduğunuzdan emin olun (Ayrıntılar için [Bu öğreticiye](stream-files-tutorial-with-rest.md) bakın). Ayrıca, [canlı akış](live-streaming-overview.md) kavramı hakkında bilgi sahibi olmanız gerekir. [Media Services öğreticisi ile canlı akışı](stream-live-tutorial-with-api.md) doldurmanız önerilir. 

> [!NOTE]
> Şu anda, canlı döküm yalnızca Batı ABD 2 bölgesinde önizleme özelliği olarak kullanılabilir. Okunan sözcüklerin Ingilizce olarak metne dökümünü destekler. Bu özellik için API başvurusu bu belgede, önizleme aşamasında olduğundan, REST belgelerimizde Ayrıntılar kullanılamaz. 

## <a name="creating-the-live-event"></a>Canlı etkinlik oluşturma 

Canlı etkinliği oluşturmak için PUT işlemini 2019-05-01 sürümüne gönderirsiniz, örneğin: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

İşlem aşağıdaki gövdeye sahiptir (burada, bir geçiş canlı olayı, alma protokolü olarak RTMP ile oluşturulur). Döküm özelliğinin eklenmesini aklınızda edin. Dil için yalnızca izin verilen değer en-US ' dir. 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

"Çalışıyor" durumuna gelene kadar canlı etkinliğin durumunu yoklamalısınız. Bu, artık bir bir RTMP akışı gönderebileceğini gösterir. Artık bu öğreticide, önizleme akışını denetleme ve canlı çıktılar oluşturma gibi aynı adımları izleyebilirsiniz. 

## <a name="delivery-and-playback"></a>Teslim ve kayıttan yürütme 

Hizmetimizin, farklı protokollerde video, ses ve artık metin sunmak için dinamik paketleme 'yı nasıl kullandığını gösteren [dinamik paketlemeye genel bakış](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) makalesini gözden geçirin. MPEG-DASH veya HLS/CMAF kullanarak canlı akışınızı yayımladığınızda ve video ve sesle birlikte hizmetimiz, MPEG-4 Bölüm 30 ' a (ISO/ıEC 14496-30) paketlenmiş ve ıMSC 1.1 uyumlu TTML 'ye paketlenmiş metin teslim eder. HLS/TS aracılığıyla teslim kullanılıyorsa, metin öbekli VTT olarak dağıtılır. Akışı oynatmak için [Azure Media Player](use-azure-media-player.md) gibi bir Web oynatıcı kullanabilirsiniz.  

> [!NOTE]
>  Azure Media Player kullanıyorsanız, sürüm 2.3.3 veya üstünü kullanın.

## <a name="known-issues"></a>Bilinen sorunlar 

Önizlemede, canlı dökümle ilgili bilinen sorunlar aşağıda verilmiştir 

* Özelliği yalnızca Batı ABD 2 kullanılabilir.
* Uygulamaların [Media Services v3 Openapı belirtim](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) belirtiminde açıklanan önizleme API 'lerini kullanması gerekir.
* Desteklenen tek dil Ingilizce 'dir.
* İçerik korumasına göre yalnızca AES zarf şifrelemesi desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

[Media Services genel bakış](media-services-overview.md)