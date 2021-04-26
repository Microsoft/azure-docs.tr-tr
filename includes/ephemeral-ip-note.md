---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529578"
---
> [!NOTE]
> Azure, genel IP adresi atanmamış veya iç temel Azure Load Balancer arka uç havuzunda yer almayan Azure sanal makineleri için kısa ömürlü bir IP sağlar. Kısa ömürlü IP mekanizması, yapılandırılabilir olmayan bir giden IP adresi sağlar. 
>
>Sanal makineye genel bir IP adresi atandığında veya sanal makine, giden kuralları olan veya olmayan bir Standart Load Balancer arka uç havuzunda yerleştirildiğinde kısa ömürlü IP devre dışı bırakılır. Sanal makinenin alt ağına bir [Azure sanal ağ NAT](../articles/virtual-network/nat-overview.md) ağ geçidi kaynağı atanırsa, kısa ömürlü IP devre dışı bırakılır.
>
> Azure 'daki giden bağlantılar hakkında daha fazla bilgi için bkz. [giden bağlantılar Için kaynak ağ adresi çevirisini (SNAT) kullanma](../articles/load-balancer/load-balancer-outbound-connections.md).