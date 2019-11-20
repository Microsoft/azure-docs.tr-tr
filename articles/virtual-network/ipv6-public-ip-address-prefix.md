---
title: Azure sanal ağı 'nda genel IPv6 adresi ön eki
titlesuffix: Azure Virtual Network
description: Azure sanal ağ 'da genel IPv6 adresi ön eki hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965166"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Ayrılmış genel IPv6 adresi ön eki (Önizleme)

Azure 'da, Internet bağlantısı olmadığından, ikili yığın (IPv4 + IPv6) sanal ağları (VNet) ve sanal makineler (VM 'Ler) varsayılan olarak güvenlidir. Azure Yük dengeleyicileri ve sanal makinelerinize, Azure 'dan edindiğiniz genel IPv6 adresleriyle kolayca bir IPv6 Internet bağlantısı ekleyebilirsiniz.

Ayrılan genel IP 'Ler, seçtiğiniz bir Azure bölgesiyle ve Azure aboneliğiniz ile ilişkilendirilir. Bir ayrılmış (statik) IPv6 genel IP 'sini aboneliğinizdeki herhangi bir Azure yük dengeleyiciden veya VM arasında taşıyabilirsiniz. IPv6 genel IP 'nin ilişkilendirmesini tamamen çözebilir ve siz de kullanıma aldığınızda kullanım için bu IP 'nin tutulması gerekir.

> [!WARNING]
> Genel IP adreslerinizi yanlışlıkla silmemek için dikkatli kullanın. Genel bir IP silindiğinde aboneliğinizden kaldırılır ve bunu kurtaramazsınız (Azure desteğinin yardımıyla bile olmasa da).

Ayrı IPv6 adreslerini ayırmaya ek olarak, kullanım için Azure IPv6 adreslerinin (IP öneki olarak bilinir) bitişik aralıklarını ayırabilirsiniz.  Ayrı IP adreslerine benzer şekilde, ayrılmış ön ekler tercih ettiğiniz bir Azure bölgesiyle ve Azure aboneliğiniz ile ilişkilendirilir. Öngörülebilir ve bitişik bir adres aralığının birçok kullanımı vardır. Örneğin, şirketiniz tarafından Azure 'da barındırılan uygulamalarınızın IP *beyaz listesini* büyük ölçüde basitleştirerek, statik IP aralıkları şirket içi güvenlik duvarlarıyla kolayca programlanabilir hale getirebilirsiniz.  Gerektiğinde IP önekinizden bağımsız genel IP 'Ler oluşturabilir ve bunları daha sonra yeniden kullanabilmeniz için bu genel IP 'Leri ayrılmış aralığa *geri döndürürsünüz* . IP Önekinizdeki tüm IP adresleri, ön ekine sildiğiniz zaman kadar özel kullanım için ayrılmıştır.

> [!Important]
> Azure sanal ağ için IPv6 Şu anda genel önizleme aşamasındadır. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>IPv6 ön ek boyutları
Aşağıdaki genel IP öneki boyutları kullanılabilir:

-  En düşük IPv6 ön ek boyutu:/127 = 2 Adres
-  En fazla IPv6 ön ek boyutu:/124 = 16 adres

Ön ek boyutu, sınıfsız etki alanları arası yönlendirme (CıDR) Maske boyutu olarak belirtilir. Örneğin,/128 maskesi, IPv6 adresleri 128 bitten oluşan tek bir IPv6 adresini temsil eder.

## <a name="pricing"></a>Fiyatlandırma
 
Tek tek IP adresleri ve IP aralıkları olmak üzere Azure genel IP 'Leri kullanımıyla ilişkili maliyetler için bkz. [genel IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Sınırlamalar
IPv6, Azure 'daki uygulamanızı (VM 'ler veya yük dengeleyiciler) silip yeniden dağıtıyorsanız IPv6 adresinin değiştirileceği anlamına gelen temel genel IP 'lerde yalnızca "dinamik" ayırma ile desteklenir. Standart IPv6 genel IP 'si yalnızca statik (ayrılmış) ayırmayı destekler, ancak standart Iç yük dengeleyiciler, atandıkları alt ağ içinden dinamik ayırmayı de destekleyebilir.  

En iyi uygulama olarak, IPv6 uygulamalarınız için standart genel IP 'Leri ve standart yük dengeleyicileri kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar
- Genel bir [IPv6 adresi ön eki](ipv6-reserve-public-ip-address-prefix.md)ayırın.
- [IPv6 adresleri](ipv6-overview.md)hakkında daha fazla bilgi edinin.
- Azure 'da [genel IP 'leri](virtual-network-public-ip-address.md) (IPv4 ve IPv6) oluşturma ve kullanma hakkında bilgi edinin.
