---
title: 'Azure ExpressRoute: bağlantı modelleri'
description: Bu makalede, müşterinin ağı ile Microsoft Azure ve Office 365 hizmetleri arasındaki farklı bağlantı modları açıklanmaktadır. Müşteriler MPLS sağlayıcılarını, bulut değişimlerini ve Ethernet sağlayıcılarını kullanabilir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080706"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute bağlantı modelleri
Şirket içi ağınız ile Microsoft bulut arasında üç yöntemle bağlantı oluşturabilirsiniz: [CloudExchange Ortak Yerleşim](#CloudExchange), [Noktadan Noktaya Ethernet Bağlantısı](#Ethernet) ve [Herhangi bir ağdan herhangi bir ağa (IPVPN) Bağlantı](#IPVPN). Bağlantı sağlayıcıları bir veya daha fazla bağlantı modeli sunabilir. Sizin için en iyi modeli seçmek için bağlantı sağlayıcınız ile çalışabilirsiniz.
<br><br>

![ExpressRoute bağlantı modeli diyagramı](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Bulut değişiminde ortak yerleşim
Bulut değişimine sahip bir tesiste ortak yerleşime sahipseniz ortak yerleşim sağlayıcının Ethernet değişimi üzerinden sanal çapraz bağlantıları Microsoft ağına sıralayabilirsiniz. Ortak yerleşim sağlayıcıları, ortak yerleşim tesisi ve Microsoft bulutu altyapısı arasında Katman 2 çapraz bağlantıları veya yönetilen Katman 3 çapraz bağlantılarını sağlayabilir.

## <a name="Ethernet"></a>Noktadan noktaya Ethernet bağlantıları
Noktadan noktaya Ethernet bağlantıları üzerinden şirket içi veri merkezleri/ofislerinizi Microsoft bulutuna bağlayabilirsiniz. Noktadan noktaya Ethernet sağlayıcıları, siteniz ve Microsoft bulutu arasında Katman 2 bağlantıları veya yönetilen Katman 3 bağlantılarını sağlayabilir.

## <a name="IPVPN"></a>Herhangi bir ağdan herhangi bir (IPVPN) ağa
WAN’ınızı Microsoft bulutu ile tümleştirebilirsiniz. IPVPN sağlayıcıları (genellikle MPLS VPN) şubeleriniz ve veri merkezleriniz arasında herhangi bir yerden herhangi bir yere bağlantı sunabilir. Herhangi diğer bir şube gibi görünmesi sağlamak için Microsoft bulutu ile WAN’ınız birbirine bağlanabilir. WAN sağlayıcıları genel olarak yönetilen Katman 3 bağlantısı sağlar. ExpressRoute yetenekleri ve özellikleri yukarıdaki tüm bağlantı modelleri arasında aynıdır. 

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute bağlantıları ve yönlendirme etki alanları hakkında bilgi edinin. Bkz. [ExpressRoute bağlantı hatları ve etki alanlarını yönlendirme](expressroute-circuit-peerings.md).
* ExpressRoute özellikleri hakkında bilgi alın. Bkz. [ExpressRoute Teknik Genel Bakış](expressroute-introduction.md)
* Bir hizmet sağlayıcı bulun. Bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* [Yönlendirme](expressroute-routing.md), [NAT](expressroute-nat.md) ve [QoS](expressroute-qos.md) için gereksinimlere bakın.
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-portal-resource-manager.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-portal-resource-manager.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-portal-resource-manager.md)