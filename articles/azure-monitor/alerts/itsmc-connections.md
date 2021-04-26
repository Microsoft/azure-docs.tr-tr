---
title: Azure Izleyici 'de BT Hizmet Yönetimi Bağlayıcısı
description: Bu makalede, ITSM iş öğelerini merkezi olarak izlemek ve yönetmek için ITSM ürünlerinizi/hizmetlerinizi Azure Izleyici 'de BT Hizmet Yönetimi Bağlayıcısı (ITSMC) ile bağlama hakkında bilgi sağlanır.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009326"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>BT Hizmet Yönetimi Bağlayıcısı ile ITSM ürünlerine/hizmetlerine bağlanma
Bu makalede, çalışma öğelerinizi merkezi olarak yönetmek için ıTSM ürününüz/hizmetiniz ile Log Analytics BT Hizmet Yönetimi Bağlayıcısı (ITSMC) arasındaki bağlantının nasıl yapılandırılacağı hakkında bilgi sağlanır. ISMC hakkında daha fazla bilgi için bkz. [genel bakış](./itsmc-overview.md).

Aşağıdaki ıTSM ürünleri/hizmetleri desteklenir. Ürünün ıSMC 'a nasıl bağlanacağı hakkında ayrıntılı bilgileri görüntülemek için ürünü seçin.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Çeriyi ve Provance müşterilerinin, tümleştirmede başka bir çözüm olarak, Cherwell ve Provance uç noktasına [Web kancası eylemi](./action-groups.md#webhook) kullanmasını sağlıyoruz.

## <a name="ip-ranges-for-itsm-partners-connections"></a>ITSM iş ortakları bağlantıları için IP aralıkları
ITSM IP adreslerini iş ortakları ITSM araçlarından gelen bağlantılara izin verecek şekilde listelemek için, Azure bölgesinin tüm genel IP aralığını, LogAnalytics çalışma alanının ait olduğu yere listeetmenizi öneririz. [Ayrıntılar burada](https://www.microsoft.com/en-us/download/details.aspx?id=56519) EUS/WEU/EUS2/WUS2/ABD Orta Güney bölgeleri için müşteri yalnızca ActionGroup ağ etiketini listeleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [ITSM Bağlayıcısındaki sorunları giderme](./itsmc-resync-servicenow.md)
