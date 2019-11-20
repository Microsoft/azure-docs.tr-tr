---
title: Azure Sanal WAN’ye genel bakış | Microsoft Docs
description: Sanal WAN otomatik ölçeklenebilir dalı-dal bağlantısı, kullanılabilir bölgeler ve iş ortakları hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 3b33015b3a69372f6783d59b28a111ec279dd860
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048483"
---
# <a name="about-azure-virtual-wan"></a>Azure sanal WAN hakkında

Azure sanal WAN, Azure ile ve arasında iyileştirilmiş ve otomatik şube bağlantısı sağlayan bir ağ hizmetidir. Azure bölgeleri, Dallarınızı bağlamak için seçebileceğiniz hub olarak görev yapar. Aynı zamanda dalları bağlamak ve şube-VNet bağlantısının avantajlarından yararlanmak için Azure omurgasına sahip olabilirsiniz. Azure sanal WAN VPN ile bağlantı Otomasyonu 'nu destekleyen iş ortaklarının listesi sunuyoruz. Daha fazla bilgi için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi.

Azure sanal WAN, siteden siteye VPN, kullanıcı VPN (Noktadan siteye) ve ExpressRoute gibi birçok Azure bulut bağlantısı hizmetini tek bir işletim arabirimine getirir. Azure VNet bağlantısı, sanal ağ bağlantıları kullanılarak oluşturulur. Bulut barındırılan Ağı ' hub ' 'ın farklı türleri arasında dağıtılabilecek uç noktalar arasında geçişli bağlantı sağladığından, klasik bir hub ve bağlı bileşen bağlantı modelini temel alan [genel aktarım ağı mimarisine](virtual-wan-global-transit-network-architecture.md) izin verebilir.

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan1.png)

Bu makalede, Azure sanal WAN 'da ağ bağlantısına hızlı bir bakış sunulmaktadır. Sanal WAN şu avantajları sunar:

* **Hub ve bağlı bileşen 'de tümleşik bağlantı çözümleri:** Siteden siteye yapılandırmayı ve şirket içi siteler ile bir Azure hub 'ı arasındaki bağlantıyı otomatikleştirin.
* **Otomatik uç kurulumu ve yapılandırması:** Sanal ağlarınızı ve iş yüklerinizi Azure hub'ına rahatça bağlayın.
* **Sezgisel sorun giderme:** Azure 'da uçtan uca akışı görebilir ve sonra gerekli eylemleri gerçekleştirmek için bu bilgileri kullanabilirsiniz.

## <a name="basicstandard"></a>Temel ve standart sanal WAN 'Lar

İki tür sanal WAN vardır: temel ve standart. Aşağıdaki tabloda her tür için kullanılabilir yapılandırma gösterilmektedir.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Bir sanal WAN yükseltme adımları için bkz. [bir sanal WAN 'ı temel 'Ten standart sürümüne yükseltme](upgrade-virtual-wan.md).

## <a name="resources"></a>Sanal WAN kaynakları

Uçtan uca sanal WAN'yi yapılandırmak için şu kaynakları oluşturursunuz:

* **virtualWAN:** virtualWAN kaynağı Azure ağınızdaki sanal bir katmanı temsil eder ve birden fazla kaynağı içeren bir koleksiyondur. Sanal WAN’de bulunmasını istediğiniz tüm sanal hub'larınızın bağlantılarını içerir. Sanal WAN kaynakları birbirinden yalıtılmıştır ve ortak bir hub içeremezler. Sanal WAN genelindeki sanal hub 'lar birbirleriyle iletişim kurmaz.

* **Hub:** Sanal hub Microsoft tarafından yönetilen bir sanal ağdır. Hub, bağlantıyı etkinleştirmek için çeşitli hizmet uç noktaları içerir. Şirket içi ağınızdan (vpnsite), sanal hub 'ın içindeki bir VPN Gateway bağlanabilir, ExpressRoute devrelerini bir sanal hub 'a bağlayabilir veya mobil kullanıcıları sanal hub 'daki Noktadan siteye ağ geçidine bağlayabilirsiniz. Hub, bir bölgedeki ağınızın merkezidir. Her Azure bölgesinde tek bir hub bulunabilir.

  Hub ağ geçidi, ExpressRoute ve VPN Gateway için kullandığınız sanal ağ geçidiyle aynı değildir. Örneğin, sanal WAN kullanırken, şirket içi sitenizden doğrudan VNet 'iniz için siteden siteye bağlantı oluşturmazsınız. Bunun yerine, hub 'a siteden siteye bağlantı oluşturursunuz. Trafik her zaman hub ağ geçidi üzerinden gider. Başka bir deyişle, Sanal Ağlarınızın kendi sanal ağ geçitlerine gerek kalmaz. Sanal WAN, Sanal Ağlarınızın sanal hub ve sanal hub ağ geçidi aracılığıyla ölçeklemeden kolayca yararlanmasına olanak tanır.

* **Hub sanal ağ bağlantısı:** Hub'ı sorunsuzca sanal ağınıza bağlamak için bir hub sanal ağ bağlantı kaynağı kullanılır.

* **(Önizleme) hub 'Dan hub 'a bağlantı** -hub 'ların hepsi bır sanal WAN 'da birbirlerine bağlanır. Bu, bir yerel hub 'a bağlı bir dalın, kullanıcının veya VNet 'in bağlı hub 'ların tam ağ mimarisini kullanarak başka bir dalla veya VNet ile iletişim kurabildiğini gösterir. Ayrıca, hub 'dan hub 'a bağlı çerçeveyi kullanarak sanal hub 'dan ve hub 'daki VNET 'lerde VNET 'ler arasında VNET 'ler de bağlayabilirsiniz.

* **Hub rota tablosu:** Bir sanal hub rotası oluşturarak rotayı sanal hub rota tablosuna uygulayabilirsiniz. Sanal hub rota tablosuna birden fazla rota uygulayabilirsiniz.

**Ek sanal WAN kaynakları**

  * **Site:** Bu kaynak yalnızca siteden siteye bağlantılar için kullanılır. Site kaynağı **vpnsite**. Şirket içi VPN cihazınızı ve ayarlarını temsil eder. Sanal WAN iş ortağıyla çalıştığınızda, bu bilgileri otomatik olarak Azure’a aktarmak için yerleşik bir çözümünüz olur.

## <a name="connectivity"></a>Bilirlik

Sanal WAN, aşağıdaki bağlantı türlerine izin verir: siteden siteye VPN, kullanıcı VPN (Noktadan siteye) ve ExpressRoute.

### <a name="s2s"></a>Siteden siteye VPN bağlantıları

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan.png)

Bir sanal WAN siteden siteye bağlantı oluşturduğunuzda, kullanılabilir bir iş ortağıyla çalışabilirsiniz. Bir iş ortağı kullanmak istemiyorsanız bağlantıyı el ile yapılandırabilirsiniz. Daha fazla bilgi için bkz. [sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Sanal WAN iş akışı

Bir sanal WAN ortağıyla çalışırken iş akışı şu şekilde olur:

1. Dal cihazı (VPN/SDWAN) denetleyicisinin, [Azure Hizmet Sorumlusu](../active-directory/develop/howto-create-service-principal-portal.md) kullanarak site merkezli bilgileri Azure’a aktarmak için kimliği doğrulanır.
2. Dal cihazı (VPN/SDWAN) denetleyicisi Azure bağlantı yapılandırmasını alır ve yerel cihazı güncelleştirir. Bu, şirket içi VPN cihazının yapılandırma indirme, düzenleme ve güncelleştirme işlemlerini otomatikleştirir.
3. Cihazda doğru Azure yapılandırması olduktan sonra, Azure WAN’a bir siteden siteye bağlantısı (iki etkin tünel) kurulur. Azure hem IKEv1’i hem de IKEv2'yi destekler. BGP isteğe bağlıdır.

#### <a name="partners"></a>Siteden siteye sanal WAN bağlantıları için iş ortakları

Kullanılabilir iş ortaklarının ve konumların bir listesi için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi.

### <a name="uservpn"></a>Kullanıcı VPN (Noktadan siteye) bağlantıları

Azure 'daki kaynaklarınıza bir IPSec/ıKE (Ikev2) veya OpenVPN bağlantısı üzerinden bağlanabilirsiniz. Bu tür bir bağlantı, istemci bilgisayarda bir VPN istemcisinin yapılandırılmasını gerektirir. Daha fazla bilgi için bkz. [Noktadan siteye bağlantı oluşturma](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute bağlantıları
ExpressRoute, şirket içi ağı özel bir bağlantı üzerinden Azure 'a bağlamanıza olanak tanır. Bağlantıyı oluşturmak için bkz. [sanal WAN kullanarak ExpressRoute bağlantısı oluşturma](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Yerlerini

Konum bilgileri için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi.

## <a name="faq"></a>SSS

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md)
