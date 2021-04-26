---
title: Azure VMware Çözüm ağı ve bağlantısı
description: Azure VMware Çözüm ağı ve bağlantı açıklaması.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462600"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware çözümü, şirket içi ve Azure tabanlı kaynaklardan erişilebilen özel bir bulut ortamı sunar. Azure ExpressRoute, VPN bağlantıları veya Azure sanal WAN gibi hizmetler bağlantı sağlar. Bu hizmetler, hizmetleri etkinleştirmek için belirli ağ adresi aralıkları ve güvenlik duvarı bağlantı noktaları gerektirir.

Özel bir bulut dağıtımında, yönetim, sağlama ve vMotion için özel ağlar oluşturulur. VCenter ve NSX-T Manager ve sanal makine vMotion veya dağıtımına erişmek için bu özel ağları kullanın.  

ExpressRoute Global Reach, özel bulutları şirket içi ortamlara bağlamak için kullanılır. Bağlantı, aboneliğinizde şirket içi bir ExpressRoute devresine sahip bir sanal ağ gerektirir.

Özel buluta dağıtılan sanal makinelere (VM), Azure sanal WAN genel IP işlevselliği aracılığıyla internet üzerinden erişilebilir.  Varsayılan olarak, internet erişimi yeni özel bulutlar için devre dışıdır. Daha fazla bilgi için bkz. [Azure VMware çözümünde genel IP işlevselliğini kullanma](../public-ip-usage.md).

