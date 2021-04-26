---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77211434"
---
Eski VPN ağ geçidi SKU'ları şunlardır:

* Varsayılan (temel)
* Standart
* HighPerformance

VPN Gateway, UltraPerformance ağ geçidi SKU’sunu kullanmaz. UltraPerformance SKU’su hakkında bilgi edinmek için [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) belgelerine bakın.

Eski SKU'lar ile çalışırken aşağıdakileri göz önünde bulundurun:

* PolicyBased VPN türünü kullanmak istiyorsanız Temel SKU'yu kullanmanız gerekir. PolicyBased VPN'ler (daha önce Statik Yönlendirme olarak biliniyordu), diğer SKU’larda desteklenmez.
* BGP, Temel SKU’da desteklenmez.
* ExpressRoute-VPN Gateway ağ geçidi bir arada var olabilen yapılandırmaları, temel SKU'da desteklenmez.
* Etkin-etkin S2S VPN Gateway bağlantıları, yalnızca HighPerformance değerine sahip SKU'larda yapılandırılabilir.