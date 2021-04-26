---
title: "Azure 'da birlikte çalışabilirlik: test kurulumu | Microsoft Docs"
description: Bu makalede, Azure 'da ExpressRoute, siteden siteye VPN ve sanal ağ eşlemesi arasındaki birlikte çalışabilirliği çözümlemek için kullanabileceğiniz bir test kurulumu açıklanmaktadır.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234146"
---
# <a name="interoperability-in-azure--test-setup"></a>Azure 'da birlikte çalışabilirlik: test kurulumu

Bu makalede, Azure ağ hizmetlerinin denetim düzlemi düzeyinde ve veri düzlemi düzeyinde nasıl birlikte çalıştığını analiz etmek için kullanabileceğiniz bir test kurulumu açıklanır. Azure ağ bileşenlerine kısaca göz atalım:

-   **Azure ExpressRoute**: şirket içi AĞıNıZDAKI özel IP alanlarını Azure sanal ağ dağıtımlarınıza doğrudan bağlamak Için Azure ExpressRoute 'da özel eşleme kullanın. ExpressRoute, daha yüksek bant genişliğine ve özel bir bağlantıya ulaşmanıza yardımcı olabilir. Birçok ExpressRoute ekonomik iş ortakları SLA 'larla ExpressRoute bağlantısı sunar. ExpressRoute hakkında daha fazla bilgi edinmek ve ExpressRoute 'u nasıl yapılandıracağınızı öğrenmek için bkz. [ExpressRoute 'A giriş][ExpressRoute].
-   **Siteden sıteye VPN**: şirket içi bir ağı Internet üzerinden Azure 'A veya ExpressRoute 'u kullanarak güvenli bir şekilde bağlamak için bir sıteden siteye VPN olarak Azure VPN Gateway kullanabilirsiniz. Azure 'a bağlanmak için siteden siteye VPN yapılandırma hakkında bilgi edinmek için bkz. [configure VPN Gateway][VPN].
-   **VNET eşlemesi**: Azure sanal ağındaki VNET 'ler arasında bağlantı kurmak için sanal ağ (VNet) eşlemesi kullanın. VNet eşlemesi hakkında daha fazla bilgi edinmek için, [VNET eşlemesi hakkında öğreticiye][VNet]bakın.

## <a name="test-setup"></a>Test kurulumu

Aşağıdaki şekil, test kurulumunu göstermektedir:

![1][1]

Test kurulumunun centerof, Azure bölge 1 ' deki merkez VNet ' dir. Hub VNet, farklı ağlara aşağıdaki yollarla bağlanır:

-   Hub VNet, VNet eşlemesi kullanılarak bağlı olan VNet 'e bağlanır. Bağlı olan VNet 'in hub VNet 'teki her iki ağ geçidine uzaktan erişimi vardır.
-   Hub VNet, siteden siteye VPN kullanılarak şube VNet 'e bağlanır. Bağlantı, yolları Exchange için eBGP kullanır.
-   Hub VNet, birincil yol olarak ExpressRoute özel eşlemesini kullanarak şirket içi konum 1 ağına bağlanır. Yedekleme yolu olarak siteden siteye VPN bağlantısı kullanır. Bu makalenin geri kalanında ExpressRoute 1 olarak bu ExpressRoute devresine başvurduk. ExpressRoute devreleri, varsayılan olarak yüksek kullanılabilirlik için yedekli bağlantı sağlar. ExpressRoute 1 ' de, ikincil Microsoft kurumsal sınır yönlendiricisi (MSEE) sunan ikincil müşteri kenarı (CE) yönlendiricisinin alt arabirimi devre dışıdır. Yukarıdaki şekilde çift satırlık ok üzerinde kırmızı bir çizgi devre dışı olan CE yönlendirici alt hattını temsil eder.
-   Hub VNet, başka bir ExpressRoute özel eşlemesi kullanarak şirket içi konum 2 ağına bağlanır. Bu makalenin geri kalanında, ExpressRoute 2 olarak bu ikinci ExpressRoute devresine başvurduk.
-   ExpressRoute 1 hem hub VNet 'i hem de şirket içi konum 1 ağını Azure bölge 2 ' deki uzak VNet 'e bağlar.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Kademeli olarak ExpressRoute ve siteden siteye VPN bağlantısı

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute üzerinden siteden siteye VPN

Şirket içi ağınız ve Azure sanal ağlarınız arasında özel olarak veri alışverişi yapmak için ExpressRoute Microsoft eşlemesi kullanarak siteden siteye VPN yapılandırabilirsiniz. Bu yapılandırmayla, verileri gizlilik, özgünlük ve bütünlük ile değiş tokuş edebilirsiniz. Veri değişimi de yeniden oynama olur. ExpressRoute Microsoft eşlemesi kullanarak bir siteden siteye IPSec VPN 'yi tünel modunda yapılandırma hakkında daha fazla bilgi için bkz. [ExpressRoute üzerinden siteden sıteye VPN Microsoft eşlemesi][S2S-Over-ExR]. 

Microsoft eşlemesi kullanan bir siteden siteye VPN yapılandırmanın birincil sınırlaması aktarım hızına sahiptir. IPSec tüneli üzerinden aktarım hızı VPN Gateway kapasitesinden sınırlıdır. VPN ağ geçidi verimlilik ExpressRoute aktarım hızına göre daha düşüktür. Bu senaryoda, yüksek oranda güvenli trafik için IPSec tüneli kullanılması ve diğer tüm trafik için özel eşleme kullanılması, ExpressRoute bant genişliği kullanımının iyileştirmenize yardımcı olur.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute için güvenli bir yük devretme yolu olarak siteden siteye VPN

ExpressRoute, yüksek kullanılabilirlik sağlamak için yedekli bir devre çifti işlevi görür. Coğrafi olarak yedekli ExpressRoute bağlantısını, farklı Azure bölgelerinde yapılandırabilirsiniz. Ayrıca, test kurulumumuzda gösterildiği gibi, bir Azure bölgesi içinde, ExpressRoute bağlantınızın yük devretme yolunu oluşturmak için siteden siteye VPN kullanabilirsiniz. Aynı ön ekler hem ExpressRoute hem de siteden siteye VPN üzerinden tanıtıldığında Azure, ExpressRoute 'u önceliklendirir. ExpressRoute ve siteden siteye VPN arasındaki asimetrik yönlendirmeyi önlemek için, şirket içi ağ yapılandırması, siteden siteye VPN bağlantısı kullanmadan önce ExpressRoute bağlantısı kullanılarak da devrik olmalıdır.

ExpressRoute ve siteden siteye VPN için birlikte var olan bağlantıları yapılandırma hakkında daha fazla bilgi için bkz. [ExpressRoute ve siteden siteye birlikte bulunma][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Arka uç bağlantısını bağlı olan sanal ağlara ve dal konumlarına Genişlet

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet eşlemesi kullanarak bağlı olan VNet bağlantısı

Hub ve bağlı ağ VNet mimarisi yaygın olarak kullanılır. Hub, bağlı olan sanal ağlarınız ve şirket içi ağınız arasında merkezi bir bağlantı noktası görevi gören Azure 'da bulunan bir VNet ' dir. Bağlı bileşen, hub ile eş olan ve iş yüklerini yalıtmak için kullanabileceğiniz sanal ağlardır. ExpressRoute veya VPN bağlantısı aracılığıyla şirket içi veri merkezi ile hub arasındaki trafik akışları. Mimari hakkında daha fazla bilgi için bkz. [Azure 'da Merkez-uç ağ topolojisi uygulama][Hub-n-Spoke].

Bir bölgedeki VNet eşlemesi içinde, bağlı olan sanal ağlar, uzak ağlarla iletişim kurmak için hub VNet ağ geçitlerini (VPN ve ExpressRoute ağ geçitleri) kullanabilir.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Siteden siteye VPN kullanarak dal VNet bağlantısı

Farklı bölgelerde olan dal VNET 'leri ve şirket içi ağları bir hub VNet aracılığıyla birbirleriyle iletişim kurmak isteyebilirsiniz. Bu yapılandırma için yerel Azure çözümü, VPN kullanarak siteden siteye VPN bağlantısı olur. Alternatif, hub 'da yönlendirme için bir ağ sanal gereci (NVA) kullanmaktır.

Daha fazla bilgi için bkz. [ne VPN Gateway?][VPN] nedir ve [yüksek oranda kullanılabilir NVA dağıtma][Deploy-NVA].

## <a name="next-steps"></a>Sonraki adımlar

Test topolojisi için [yapılandırma ayrıntıları][Configuration] hakkında bilgi edinin.

Test kurulumunun [Denetim düzlemi analizini][Control-Analysis] ve topolojideki farklı VNET veya VLAN 'ların görünümlerini öğrenin.

Test kurulumunun ve Azure ağ izleme özelliği görünümlerinin [veri düzlemi analizini][Data-Analysis] öğrenin.

Bkz. [ExpressRoute SSS][ExR-FAQ] :
-   ExpressRoute ağ geçidine kaç ExpressRoute bağlantı hattı bağlayabileceğinizi öğrenin.
-   Bir ExpressRoute devresine kaç ExpressRoute ağ geçidi bağlayabileceğinizi öğrenin.
-   ExpressRoute 'un diğer ölçek sınırları hakkında bilgi edinin.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Test topolojisi diyagramı"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha