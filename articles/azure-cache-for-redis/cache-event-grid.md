---
title: Redsıs için Azure önbelleği Event Grid genel bakış
description: Redsıs olayları için Azure önbelleğini yayımlamak üzere Azure Event Grid kullanın.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056307"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Redsıs için Azure önbelleği Event Grid genel bakış 

Düzeltme eki uygulama, ölçeklendirme, içeri/dışarı aktarma (RDB) olayları gibi Redsıs olayları için Azure önbelleği, Azure Işlevleri, Azure Logic Apps, hatta kendi http dinleyiciniz gibi abonelere [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) kullanılarak gönderilir. Event Grid zengin yeniden deneme ilkeleri ve atılacak uygulamalar aracılığıyla uygulamalarınıza güvenilir olay teslimi sağlar.

Redsıs için Azure önbelleğinin desteklediği olayların tam listesini görüntülemek için [redsıs olayları şeması Için Azure önbelleği](../event-grid/event-schema-azure-cache.md) makalesine bakın.

Redsıs olayları için Azure önbelleği 'ni denemek istiyorsanız, bu hızlı başlangıçlardan herhangi birine bakın:

|Bu aracı kullanmak istiyorsanız:    |Bu hızlı başlangıcı inceleyin: |
|--|-|
|Azure portalı    |[Hızlı başlangıç: Azure portal ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Hızlı başlangıç: PowerShell ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](cache-event-grid-quickstart-powershell.md)|
|Azure CLI’si    |[Hızlı başlangıç: Azure CLı ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Olay modeli

Event Grid, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Bu görüntüde olay yayımcıları, olay abonelikleri ve olay işleyicileri arasındaki ilişki gösterilmektedir.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event Grid modeli.":::

İlk olarak, bir uç noktayı olaya abone olun. Ardından, bir olay tetiklendiğinde Event Grid hizmeti bu olayla ilgili verileri uç noktaya gönderir.

Şunları görmek için [redsıs olayları şema Için Azure önbelleği](../event-grid/event-schema-azure-cache.md) 'ne bakın:

> [!div class="checklist"]
> * Redsıs olayları için Azure önbelleğinin ve her olayın nasıl tetiklendiğine ilişkin kapsamlı bir liste.
> * Event Grid bu olayların her biri için gönderebilecek verilere bir örnek.
> * Verilerde görüntülenen her anahtar değer çiftinin amacı.


## <a name="best-practices-for-consuming-events"></a>Olayları tüketen en iyi uygulamalar

Redin olayları için Azure önbelleğini işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:
> [!div class="checklist"]
> * Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabildiğiniz için, olayların belirli bir kaynaktan olduğunu varsaymamak önemlidir, ancak beklediğiniz Redsıs örneği için Azure önbelleğinden geldiğinden emin olmak için iletinin konusunu kontrol edin.
> * Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
> * Redsıs olayları için Azure önbelleği, tüm iletilerin kaydedilmesini sağlayan abonelere en az bir kez gönderim sağlar. Ancak, yeniden denemeler veya aboneliklerin kullanılabilirliği nedeniyle, yinelenen iletiler zaman zaman oluşabilir. İleti teslimi ve yeniden deneme hakkında daha fazla bilgi edinmek için bkz. [Event Grid ileti teslimi ve yeniden deneme](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve TRY olayları için Azure önbelleği 'ne bir deneme verin:

- [Event Grid Hakkında](../event-grid/overview.md)
- [Redsıs olayları şeması için Azure önbelleği](../event-grid/event-schema-azure-cache.md)
- [Azure CLı ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](cache-event-grid-quickstart-cli.md)
- [Redsıs olayları için Azure önbelleğini Azure portal web uç noktasına yönlendirin](cache-event-grid-quickstart-portal.md)
- [PowerShell ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirin](cache-event-grid-quickstart-powershell.md)
