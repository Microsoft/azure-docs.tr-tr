---
title: Azure Media Services 'daki LiveEvent düşük gecikme süresi ayarları
description: Bu konu, LiveEvent düşük gecikme ayarlarına genel bir bakış sağlar ve düşük gecikme süresinin nasıl ayarlanacağını gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2b612201a5ce1c661c8d6549f2882f86cfaf0e32
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123510"
---
# <a name="live-event-low-latency-settings"></a>Canlı olay düşük gecikme süresi ayarları

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede, [canlı bir olayda](/rest/api/media/liveevents)düşük gecikme süresinin nasıl ayarlanacağı gösterilmektedir. Ayrıca, çeşitli oynatıcılarda düşük gecikme süresi ayarlarını kullanırken gördüğünüz tipik sonuçları da açıklar. Sonuçlar CDN ve ağ gecikme süresine göre farklılık gösterir.

Yeni **LowLatency** özelliğini kullanmak Için, **Streammoptionsflag** öğesini **liveevent** üzerinde **LowLatency** olarak ayarlarsınız. HLS kayıttan yürütme için [liveoutput](/rest/api/media/liveoutputs) oluştururken [Liveoutput. Hls. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) değerini 1 olarak ayarlayın. Akış çalışır duruma getirildikten sonra, [Azure Media Player](https://ampdemo.azureedge.net/) (amp demo sayfası) kullanabilir ve kayıttan yürütme seçeneklerini "düşük gecikmeli buluşsal yöntemler profilini" kullanacak şekilde ayarlayabilirsiniz.

> [!NOTE]
> Şu anda Azure Media Player içindeki LowLatency HeuristicProfile, MPEG-DASH protokolünde, CSF veya CMAF biçimiyle (örneğin, ya da) birlikte akış çalmak için tasarlanmıştır `format=mdp-time-csf` `format=mdp-time-cmaf` . 

Aşağıdaki .NET örneğinde, **Liveevent** üzerinde **LowLatency** ayarlama gösterilmektedir:

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

Bkz. tam örnek: [DVR Ile canlı olay](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs).

## <a name="live-events-latency"></a>Canlı olay gecikmesi

Aşağıdaki tablolarda, bir Görüntüleyici Player 'da kayıttan yürütmeyi gördüğünde, katkı akışı hizmete ulaştığı zamandan itibaren ölçülen gecikme süresi (LowLatency bayrağı etkinleştirildiğinde Media Services) için tipik sonuçlar gösterilmektedir. Düşük gecikme süresini en iyi şekilde kullanmak için Kodlayıcı ayarlarınızı 1 saniyelik "resim grubu" (GOP) uzunluğu olarak ayarlamanız gerekir. Daha yüksek bir GOP uzunluğu kullanırken, bant genişliği tüketimini en aza indirmiş ve aynı kare hızında bit hızını azaltabilirsiniz. Daha az hareket eden videolarda özellikle faydalıdır.

### <a name="pass-through"></a>Geçiş 

||2s GOP düşük gecikme süresi etkin|1s GOP düşük gecikme süresi etkin|
|---|---|---|
|**AMP 'da DASH**|10s|8S|
|**Yerel iOS yürütücüsü üzerinde HLS**|14s|10s|

### <a name="live-encoding"></a>Live encoding

||2s GOP düşük gecikme süresi etkin|1s GOP düşük gecikme süresi etkin|
|---|---|---|
|**AMP 'da DASH**|14s|10s|
|**Yerel iOS yürütücüsü üzerinde HLS**|18s|13s|

> [!NOTE]
> Uçtan uca gecikme süresi, yerel ağ koşullarına bağlı olarak veya bir CDN önbelleğe alma katmanı sunarak farklılık gösterebilir. Tam yapılandırmalarınızı test etmelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Canlı akışa genel bakış](stream-live-streaming-concept.md)
- [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)
