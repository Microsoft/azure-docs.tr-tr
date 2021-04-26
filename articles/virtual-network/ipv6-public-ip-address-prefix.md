---
title: Azure sanal ağı 'nda genel IPv6 adresi ön eki
titlesuffix: Azure Virtual Network
description: Azure sanal ağ 'da genel IPv6 adresi ön eki hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538215"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Ayrılmış genel IPv6 adresi ön eki

Azure 'da, Internet bağlantısı olmadığından, ikili yığın (IPv4 + IPv6) sanal ağları (VNet) ve sanal makineler (VM 'Ler) varsayılan olarak güvenlidir. Azure Yük dengeleyicileri ve sanal makinelerinize, Azure 'dan edindiğiniz genel IPv6 adresleriyle kolayca bir IPv6 Internet bağlantısı ekleyebilirsiniz.

Ayrılan genel IP 'Ler, seçtiğiniz bir Azure bölgesiyle ve Azure aboneliğiniz ile ilişkilendirilir. Bir ayrılmış (statik) IPv6 genel IP 'sini aboneliğinizdeki herhangi bir Azure yük dengeleyiciden veya VM arasında taşıyabilirsiniz. IPv6 genel IP 'nin ilişkilendirmesini tamamen çözebilir ve siz de kullanıma aldığınızda kullanım için bu IP 'nin tutulması gerekir.

> [!WARNING]
> Genel IP adreslerinizi yanlışlıkla silmemek için dikkatli kullanın. Genel bir IP silindiğinde aboneliğinizden kaldırılır ve bunu kurtaramazsınız (Azure desteğinin yardımıyla bile olmasa da).

Ayrı IPv6 adreslerini ayırmaya ek olarak, kullanım için Azure IPv6 adreslerinin (IP öneki olarak bilinir) bitişik aralıklarını ayırabilirsiniz.  Ayrı IP adreslerine benzer şekilde, ayrılmış ön ekler tercih ettiğiniz bir Azure bölgesiyle ve Azure aboneliğiniz ile ilişkilendirilir. Öngörülebilir ve bitişik bir adres aralığının birçok kullanımı vardır. Örneğin, şirketiniz tarafından Azure 'da barındırılan uygulamalarınızın IP *filtrelemesini* büyük ölçüde basitleştirebilir ve statik IP aralıklarınız şirket içi güvenlik duvarlarıyla kolayca programlanabilir hale getirebilirsiniz.  Gerektiğinde IP önekinizden bağımsız genel IP 'Ler oluşturabilir ve bunları daha sonra yeniden kullanabilmeniz için bu genel IP 'Leri ayrılmış aralığa *geri döndürürsünüz* . IP Önekinizdeki tüm IP adresleri, ön ekine sildiğiniz zaman kadar özel kullanım için ayrılmıştır.



## <a name="ipv6-prefix-sizes"></a>IPv6 ön ek boyutları
Aşağıdaki genel IP öneki boyutları kullanılabilir:

-  En düşük IPv6 ön ek boyutu:/127 = 2 Adres
-  En fazla IPv6 ön ek boyutu:/124 = 16 adres

Ön ek boyutu bir sınıfsız Inter-Domain yönlendirme (CıDR) Maske boyutu olarak belirtilir. Örneğin,/128 maskesi, IPv6 adresleri 128 bitten oluşan tek bir IPv6 adresini temsil eder.

## <a name="pricing"></a>Fiyatlandırma
 
Tek tek IP adresleri ve IP aralıkları olmak üzere Azure genel IP 'Leri kullanımıyla ilişkili maliyetler için bkz. [genel IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Sınırlamalar
IPv6, Azure 'daki uygulamanızı (VM 'ler veya yük dengeleyiciler) silip yeniden dağıtıyorsanız IPv6 adresinin değiştirileceği anlamına gelen temel genel IP 'lerde yalnızca "dinamik" ayırma ile desteklenir. Standart IPv6 genel IP 'si yalnızca statik (ayrılmış) ayırmayı destekler, ancak standart Iç yük dengeleyiciler, atandıkları alt ağ içinden dinamik ayırmayı de destekleyebilir.  

En iyi uygulama olarak, IPv6 uygulamalarınız için standart genel IP 'Leri ve standart yük dengeleyicileri kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar
- Genel bir [IPv6 adresi ön eki](ipv6-reserve-public-ip-address-prefix.md)ayırın.
- [IPv6 adresleri](ipv6-overview.md)hakkında daha fazla bilgi edinin.
- Azure 'da [genel IP 'leri](virtual-network-public-ip-address.md) (IPv4 ve IPv6) oluşturma ve kullanma hakkında bilgi edinin.
