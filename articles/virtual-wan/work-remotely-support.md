---
title: Azure sanal WAN ve uzaktan çalışma
description: Bu sayfada COVıD-19 pandemıc nedeniyle uzaktan çalışmayı etkinleştirmek için Azure sanal WAN 'ı nasıl kullanabileceğiniz açıklanır.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023508"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure sanal WAN ve uzak çalışmayı destekleme

>[!NOTE]
>Bu makalede, Azure sanal WAN, Azure, Microsoft ağı ve Azure iş ortağı ekosisteminden yararlanarak, birlikte çalışarak, COVı-19 çapraz olduğu için, karşılıklı yaptığınız ağ sorunlarını azaltabilirsiniz.
>

Uzak kullanıcılara bağlantı sağlamak için mi karıştırma?
Daha önce planlandıklarınız ötesinde kullanıcıların bir aşırı gereksinimini desteklemeye yönelik bir ihtiyacı var mı?
Kullanıcıdan evden bağlantı kurabilmesi ve yalnızca buluta erişmesini ve ayrıca şirket içi ağa ulaşabilmesi gerekir mi?
Uzak kullanıcılarınıza özel bir WAN 'ın arkasındaki kaynaklara erişebiliyor musunuz?
Kullanıcıların bölgeler arasında bağlantı kurma gereksinimi olmadan, içi bulut kaynaklarına erişmesine gerek var mı?
Bu küresel pandemik, bizim yerinizdeki değişiklikler oluşturduğundan, bağlantı ihtiyaçlarınızı karşılamak için Azure sanal WAN ekibi burada bulunur.

Azure sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getiren bir ağ hizmetidir. Bu işlevler, dal bağlantısını (SD-WAN veya VPN CPE gibi sanal WAN Iş ortağı cihazlarından bağlantı Otomasyonu aracılığıyla) içerir. siteden siteye VPN bağlantısı, uzak kullanıcı VPN (Noktadan siteye) bağlantısı, özel (ExpressRoute) bağlantısı, bulut Içi bağlantı (sanal ağlar için geçişli bağlantı), VPN ExpressRoute ınterconnectivity, yönlendirme, Azure Güvenlik Duvarı, özel bağlantı için şifreleme vb. Sanal WAN kullanmaya başlamak için bu kullanım örneklerinin tümünün olması gerekmez. Yalnızca bir kullanım örneğini kullanmaya başlayabilir ve ağınızı geliştikçe ayarlayabilirsiniz.

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan1.png)

Artık uzak kullanıcılarla ilgili olarak, ağınızı çalışır durumda ve çalışır duruma getirmek için gerekenleri göz atalım:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Uzak Kullanıcı bağlantısını ayarlama

Azure 'daki kaynaklarınıza bir IPSec/ıKE (Ikev2) veya OpenVPN bağlantısı üzerinden bağlanabilirsiniz. Bu tür bir bağlantı, uzak kullanıcı için bir VPN istemcisinin yapılandırılmasını gerektirir. Bu istemci, [Azure VPN istemcisi](https://go.microsoft.com/fwlink/?linkid=2117554) veya openvpn istemcisi ya da Ikev2 'yi destekleyen herhangi bir istemci olabilir. Daha fazla bilgi için bkz. [Noktadan siteye bağlantı oluşturma](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Uzak kullanıcıdan şirket içine bağlantı

Burada iki seçeneğiniz vardır:

* Var olan herhangi bir VPN aygıtıyla siteden siteye bağlantı kurun. IPSec VPN cihazını Azure sanal WAN hub 'a bağladığınızda, Noktadan siteye kullanıcı VPN (uzak kullanıcı) ve siteden siteye VPN arasındaki karşılıklı bağlantı otomatik olarak yapılır. Şirket içi VPN cihazınızdan Azure sanal WAN 'a siteden siteye VPN ayarlama hakkında daha fazla bilgi için bkz. [sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md).

* ExpressRoute devrenizi sanal WAN hub 'ına bağlayın. ExpressRoute bağlantı hattı bağlantısı, sanal WAN 'da bir ExpressRoute ağ geçidinin dağıtılmasını gerektirir. Bir tane dağıttıktan sonra, Noktadan siteye kullanıcı VPN ve ExpressRoute kullanıcısı arasında birbirine bağlı bağlantı otomatik olur. ExpressRoute bağlantısı oluşturmak için bkz. [sanal WAN kullanarak ExpressRoute bağlantısı oluşturma](virtual-wan-expressroute-portal.md). Azure sanal WAN 'a bağlanmak için mevcut bir ExpressRoute devresini kullanabilirsiniz.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Mevcut temel sanal WAN müşterisi

Temel sanal WAN yalnızca siteden siteye VPN sağlar. Uzak kullanıcıların bağlanabilmesi için, sanal WAN 'ı standart sanal WAN 'a yükseltmeniz gerekir. Bir sanal WAN yükseltme adımları için bkz. [bir sanal WAN 'ı temel 'Ten standart sürümüne yükseltme](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Ek bilgi

Sanal WAN, bölge/konum başına bir hub destekler. Konum bilgileri için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi. Her Hub 10.000 adede kadar uzak kullanıcı bağlantısını, 1.000 dal bağlantısını, dört ExpressRoute devresine ve en fazla 500 sanal ağ bağlantısını destekler. Uzak kullanıcıları ölçeklendirirseniz, sorularınız varsa, ' e bir e-posta göndererek yardım arama hakkında daha fazla araştırma yapmayın azurevirtualwan@microsoft.com . Teknik desteğe ihtiyacınız varsa, Azure portal bir destek bileti açın ve yardım 'ın bu şekilde olacağını unutmayın.

## <a name="faq"></a><a name="faq"></a>SSS

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Sonraki Adımlar

Sanal WAN hakkında daha fazla bilgi için bkz. [sanal WAN genel bakış](virtual-wan-about.md)