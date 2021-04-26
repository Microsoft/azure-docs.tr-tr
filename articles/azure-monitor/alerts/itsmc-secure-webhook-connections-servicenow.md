---
title: BT Hizmet Yönetimi Bağlayıcısı-Azure Izleyici 'de güvenli dışarı aktarma-ServiceNow ile yapılandırma
description: Bu makalede, Azure Izleyici 'de güvenli dışarı aktarma hakkında ServiceNow ile ıTSM ürünlerinizi/hizmetlerinizin nasıl bağlanacağı gösterilmektedir.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: cf19911bd8126bfb73f848c086aa817a7d7adb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563700"
---
# <a name="connect-servicenow-to-azure-monitor"></a>ServiceNow 'ı Azure Izleyici 'ye bağlama

Aşağıdaki bölümlerde, Azure 'da ServiceNow ürününüzü bağlama ve güvenli dışarı aktarma işlemlerinin nasıl yapılacağı hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulları karşılatığınızdan emin olun:

* Azure AD kaydedilir.
* ServiceNow olay yönetimi-ıTOM 'un desteklenen sürümüne sahipsiniz (New York veya üzeri sürümü).
* ServiceNow örneğinde yüklü [uygulama](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) .

## <a name="configure-the-servicenow-connection"></a>ServiceNow bağlantısını yapılandırma

1. Https://(örnek adı) bağlantısını kullanın. Service-Now. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor, güvenli dışarı aktarma tanımı için URI.

2. Sürüme göre yönergeleri izleyin:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Tarihleri arasında Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [New Yok](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/concept/azure-integration.html)
