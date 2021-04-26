---
title: 'Azure ExpressRoute: Önkoşullar'
description: Bu sayfada bir Azure ExpressRoute bağlantı hattı sipariş etmeden önce karşılanması gereken gereksinimlerin listesi bulunmaktadır. Bir denetim listesi içerir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204801"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute önkoşulları ve denetim listesi
ExpressRoute kullanarak Microsoft bulut hizmetlerine bağlanmak için, aşağıdaki bölümlerde listelenen gereksinimlerin karşılanmış olduğunu doğrulamanız gerekir.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure hesabı
* Geçerli ve etkin bir Microsoft Azure hesabı. ExpressRoute bağlantı hattı kurmak için bu hesap gereklidir. ExpressRoute bağlantı hatları Azure aboneliklerinin içindeki kaynaklardır. Bağlantı, Microsoft 365 gibi Azure olmayan Microsoft bulut hizmetleriyle sınırlı olsa bile bir Azure aboneliği gereksinimidir.
* Etkin bir Microsoft 365 aboneliği (Microsoft 365 hizmetleri kullanılıyorsa). Daha fazla bilgi için bu makalenin Microsoft 365 belirli gereksinimler bölümüne bakın.

## <a name="connectivity-provider"></a>Bağlantı sağlayıcısı

* Microsoft bulut’a bağlanmak için [ExpressRoute bağlantı ortağı](expressroute-locations.md#partners) ile çalışabilirsiniz. Şirket içi ağınız ve Microsoft arasında bağlantıyı [üç şekilde](expressroute-introduction.md) kurabilirsiniz.
* Sağlayıcınız ExpressRoute bağlantı ortağı değilse bile [bulut değişim sağlayıcısı](expressroute-locations.md#connectivity-through-exchange-providers) yoluyla Microsoft bulut’a bağlanabilirsiniz.

## <a name="network-requirements"></a>Ağ gereksinimleri
* **Her bir eşleme konumunda artıklık**: Microsoft, her ExpressRoute devresi üzerinde Microsoft 'un yönlendiricileri ve eşleme yönlendiricileri arasında ( [bir bulut değişimine yalnızca bir fiziksel bağlantınız](expressroute-faqs.md#onep2plink)olduğunda bile) ayarlanmış olması gerekir.
* **Olağanüstü durum kurtarma Için yedeklilik**: Microsoft, tek bir başarısızlık noktasını önlemek için farklı eşleme konumlarında en az Iki ExpressRoute devrenizin ayarlamanızı kesinlikle önerir.
* **Yönlendirme**: Microsoft bulut nasıl bağlanadığınıza bağlı olarak, siz veya sağlayıcınızın [yönlendirme etkı alanları](expressroute-circuit-peerings.md)için BGP oturumlarını ayarlaması ve yönetmesi gerekir. Bazı Ethernet bağlantı sağlayıcıları veya bulut Exchange sağlayıcıları, değer ekleme hizmeti olarak BGP yönetimi sunabilir.
* **NAT**: Microsoft genel IP adreslerini sadece Microsoft eşleme yoluyla kabul eder. Şirket içi ağınızda özel IP adresleri kullanıyorsanız, siz veya sağlayıcınızın [NAT kullanarak](expressroute-nat.md)özel IP ADRESLERINI genel IP adreslerine çevirmesi gerekir.
* **QoS**: Skype Kurumsal’da farklı QoS davranışları gerektiren çeşitli hizmetler (ör. ses, video, mesaj) vardır. [Qos gereksinimleri](expressroute-qos.md)ne siz ve sağlayıcınız tarafından uyulmalıdır.
* **Ağ Güvenliği**: ExpressRoute ile Microsoft Bulut'a bağlanırken [ağ güvenliğine](/azure/cloud-adoption-framework/reference/networking-vdc) dikkat edin.

## <a name="microsoft-365"></a>Microsoft 365
ExpressRoute üzerinde Microsoft 365 etkinleştirmeyi planlıyorsanız, Microsoft 365 gereksinimler hakkında daha fazla bilgi için aşağıdaki belgeleri gözden geçirin.

* [Microsoft 365 için Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [Microsoft 365 için ExpressRoute ile yönlendirme](/microsoft-365/enterprise/routing-with-expressroute)
* [ExpressRoute ile yüksek kullanılabilirlik ve yük devretme](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 URL 'Leri ve IP adresi aralıkları](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Microsoft 365 için ağ planlama ve performans ayarlaması](/microsoft-365/enterprise/network-planning-and-performance)
* [Microsoft 365 için ağ ve geçiş planlaması](/microsoft-365/enterprise/network-and-migration-planning)
* [Şirket içi ortamlarla Microsoft 365 tümleştirme](/microsoft-365/enterprise/microsoft-365-integration)
* [Office 365'te ExpressRoute ileri düzey eğitim videoları](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
* ExpressRoute bağlantı sağlayıcısı bulun. Bkz. [ExpressRoute ortakları ve eşleme konumları](expressroute-locations.md).
* [Yönlendirme](expressroute-routing.md), [NAT](expressroute-nat.md)ve [QoS](expressroute-qos.md)için gereksinimlere bakın.
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-arm.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)