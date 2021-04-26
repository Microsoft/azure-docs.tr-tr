---
title: 'Azure ExpressRoute: bağlantı modelleri'
description: Müşterinin ağı, Microsoft Azure ve Microsoft 365 hizmetleri arasındaki bağlantıyı gözden geçirin. Müşteriler MPLS sağlayıcıları, bulut alışverişleri ve Ethernet kullanabilir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978772"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute bağlantı modelleri
Şirket içi ağınız ile Microsoft bulutu arasında dört farklı şekilde bir bağlantı oluşturabilirsiniz, [Cloudexchange ortak konumu](#CloudExchange), [Noktadan noktaya Ethernet bağlantısı](#Ethernet), [Any-any (ıpvpn) bağlantısı](#IPVPN)ve [ExpressRoute Direct](#Direct). Bağlantı sağlayıcıları bir veya daha fazla bağlantı modeli sunabilir. Sizin için en iyi modeli seçmek için bağlantı sağlayıcınız ile çalışabilirsiniz.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="ExpressRoute bağlantı modeli diyagramı":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Bulut değişiminde birlikte bulunan
Bulut değişimine sahip bir tesiste birlikte bulunuyorsanız, ortak konum sağlayıcının Ethernet değişimi aracılığıyla Microsoft bulutuna sanal çapraz bağlantıları sipariş edebilirsiniz. Ortak yerleşim sağlayıcıları, ortak yerleşim tesisi ve Microsoft bulutu altyapısı arasında Katman 2 çapraz bağlantıları veya yönetilen Katman 3 çapraz bağlantılarını sağlayabilir. 

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Noktadan noktaya Ethernet bağlantıları
Noktadan noktaya Ethernet bağlantıları üzerinden şirket içi veri merkezleri/ofislerinizi Microsoft bulutuna bağlayabilirsiniz. Noktadan noktaya Ethernet sağlayıcıları, siteniz ve Microsoft bulutu arasında Katman 2 bağlantıları veya yönetilen Katman 3 bağlantılarını sağlayabilir.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Herhangi bir ağdan herhangi bir ağa (IPVPN) modelindeki ağlar
WAN’ınızı Microsoft bulutu ile tümleştirebilirsiniz. IPVPN sağlayıcıları (genellikle MPLS VPN) şubeleriniz ve veri merkezleriniz arasında herhangi bir yerden herhangi bir yere bağlantı sunabilir. Herhangi diğer bir şube gibi görünmesi sağlamak için Microsoft bulutu ile WAN’ınız birbirine bağlanabilir.  WAN sağlayıcıları genel olarak yönetilen Katman 3 bağlantısı sağlar. Yukarıdaki tüm bağlantı modellerinde ExpressRoute özellikleri aynıdır.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>ExpressRoute sitelerinden doğrudan
Doğrudan Microsoft 'un küresel ağına, dünya genelinde stratejik olarak dağıtılmış bir eşleme konumunda bağlanabilirsiniz. [ExpressRoute Direct](expressroute-erdirect-about.md) , ölçek üzerinde etkin/etkin bağlantıyı destekleyen çift 100 Gbps veya 10 Gbps bağlantı sağlar.

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute bağlantıları ve yönlendirme etki alanları hakkında bilgi edinin. Bkz. [ExpressRoute bağlantı hatları ve etki alanlarını yönlendirme](expressroute-circuit-peerings.md).
* ExpressRoute özellikleri hakkında bilgi alın. Bkz. [ExpressRoute Teknik Genel Bakış](expressroute-introduction.md)
* Bir hizmet sağlayıcı bulun. Bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* [Yönlendirme](expressroute-routing.md), [NAT](expressroute-nat.md)ve [QoS](expressroute-qos.md)için gereksinimlere bakın.
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-portal-resource-manager.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-portal-resource-manager.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-portal-resource-manager.md)