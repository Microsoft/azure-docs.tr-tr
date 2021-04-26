---
title: Azure Media Services olaylara yeniden davranıyor
description: Bu makalede, Media Services olaylarına abone olmak için Azure Event Grid nasıl kullanılacağı açıklanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9bed493ca37d21d0c3e5c289bb8c26607975bdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610065"
---
# <a name="handling-event-grid-events"></a>Event Grid olaylarını işleme

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Media Services olaylar, uygulamaların, modern sunucusuz mimariler kullanılarak farklı olaylara (örneğin, iş durumu değiştirme olayı) tepki vermesini sağlar. Bu, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gereksinimini ortadan kaldırmaz. Bunun yerine, olaylar [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)ve hatta kendi web kancasına dahil olmak üzere olay işleyicisine [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir ve yalnızca kullandığınız kadar ödersiniz. Fiyatlandırma hakkında bilgi için bkz. [Event Grid fiyatlandırması](https://azure.microsoft.com/pricing/details/event-grid/).

Media Services olaylarının kullanılabilirliği Event Grid [kullanılabilirliğine](../../../event-grid/overview.md) bağlıdır ve Event Grid olduğu gibi diğer bölgelerde kullanılabilir hale gelir.  

## <a name="media-services-events-and-schemas"></a>Media Services olayları ve şemaları

Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../../event-grid/concepts.md#event-subscriptions) kullanır. Media Services olaylar, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. Media." ile başladığı için bir Media Services olayı tanımlayabilirsiniz.

Daha fazla bilgi için bkz. [Media Services olay şemaları](../media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar

Media Services olaylarını işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:

* Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabildiğiniz için, olayların belirli bir kaynaktan olduğunu varsaymamak, ancak beklediğiniz depolama hesabından geldiğinden emin olmak için iletinin konusunu denetlemek önemlidir.
* Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
* Anladığınızı alanları yoksayın.  Bu uygulama, gelecekte eklenebilecek yeni özelliklere dayanıklı tutmaya yardımcı olur.
* Olayları belirli bir olayla sınırlamak için "konu" önekini ve sonek eşleşmelerini kullanın.

> [!NOTE]
> Olaylar Event Grid [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)konusuna tabidir. API 'Leri kullanarak olay bildirimleri almak istiyorsanız, [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) veya [Java SDK](https://github.com/Azure-Samples/media-services-v3-java)ile olayları kullanma örneklerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Olayları izleme-Portal](../monitor-events-portal-how-to.md)
* [Olayları izleme - CLI](../job-state-events-cli-how-to.md)
