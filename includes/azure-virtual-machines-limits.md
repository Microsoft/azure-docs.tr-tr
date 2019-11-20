---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188729"
---
| Resource | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Bulut hizmeti başına [sanal makine](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) sayısı<sup>1</sup> |50 |50 |
| Bulut hizmeti başına giriş uç noktaları<sup>2</sup> |150 |150 |

<sup>1</sup> Azure Resource Manager yerine klasik dağıtım modeli kullanılarak oluşturulan sanal makineler otomatik olarak bir bulut hizmetinde depolanır. Yük Dengeleme ve kullanılabilirlik için bu bulut hizmetine daha fazla sanal makine ekleyebilirsiniz. 

<sup>2</sup> Giriş uç noktaları, sanal makinenin bulut hizmeti dışından bir sanal makineye yönelik iletişimlere izin verir. Aynı bulut hizmeti veya sanal ağ içindeki sanal makineler birbirleriyle otomatik olarak iletişim kurabilir. Daha fazla bilgi için bkz. [sanal makineye uç noktaları ayarlama](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
