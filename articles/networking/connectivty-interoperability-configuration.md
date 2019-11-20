---
title: 'Azure arka uç bağlantı özelliklerinde birlikte çalışabilirlik: Yapılandırma ayrıntıları | Microsoft Docs'
description: Bu makalede, Azure 'da ExpressRoute, siteden siteye VPN ve sanal ağ eşlemesi arasındaki birlikte çalışabilirliği çözümlemek için kullanabileceğiniz test kurulumunun yapılandırma ayrıntıları açıklanmaktadır.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68335936"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Azure arka uç bağlantı özelliklerinde birlikte çalışabilirlik: Test yapılandırması ayrıntıları

Bu makalede, [Test kurulumunun][Setup]yapılandırma ayrıntıları açıklanmaktadır. Test kurulumu, Azure ağ hizmetlerinin denetim düzlemi düzeyinde ve veri düzlemi düzeyinde nasıl birlikte çalıştığını çözümlemenize yardımcı olur.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet eşlemesi kullanarak bağlı olan VNet bağlantısı

Aşağıdaki şekilde, bir bağlı bileşen sanal ağının (VNet) Azure sanal ağ eşleme ayrıntıları gösterilmektedir. İki sanal ağ arasında eşleme ayarlamayı öğrenmek için bkz. [VNET eşlemesini yönetme][VNet-Config]. Bağlı olan VNet 'in hub VNet 'e bağlı ağ geçitlerini kullanmasını istiyorsanız **uzak ağ geçitlerini kullan**' ı seçin.

[![1]][1]

Aşağıdaki şekilde, hub VNet 'in VNet eşleme ayrıntıları gösterilmektedir. Hub VNet 'in bağlı olan VNet 'in hub ağ geçitlerini kullanmasına izin vermek istiyorsanız, **ağ geçidi aktarımına Izin ver**' i seçin.

[![iki]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Siteden siteye VPN kullanarak dal VNet bağlantısı

Azure VPN Gateway 'de VPN ağ geçitlerini kullanarak Merkez ve dal sanal ağları arasında siteden siteye VPN bağlantısı kurun. Varsayılan olarak, VPN ağ geçitleri ve Azure ExpressRoute ağ geçitleri, **65515**özel bir otonom sistem numarası (ASN) değeri kullanır. VPN Gateway ASN değerini değiştirebilirsiniz. Test kurulumunda, şube VNet VPN ağ geçidinin ASN değeri, hub ve dal sanal ağları arasındaki eBGP yönlendirmeyi desteklemek için **65516** olarak değiştirilir.


[![03]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>ExpressRoute ve siteden siteye VPN kullanarak şirket içi konum 1 bağlantısı

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 yapılandırma ayrıntıları

Aşağıdaki şekilde, şirket içi konum 1 müşteri kenarı (CE) yönlendiricilerine yönelik Azure bölge 1 ExpressRoute bağlantı hattı yapılandırması gösterilmektedir:

[![4]][4]

Aşağıdaki şekilde, ExpressRoute 1 devresi ve hub VNet arasındaki bağlantı yapılandırması gösterilmektedir:

[![e]][5]

Aşağıdaki listede, ExpressRoute özel eşleme bağlantısı için birincil CE yönlendirici yapılandırması gösterilmektedir. (Cisco ASR1000 yönlendiricileri, test kurulumunda CE yönlendiricileri olarak kullanılır.) Siteden siteye VPN ve ExpressRoute devreleri, şirket içi bir ağı Azure 'a bağlamak için paralel olarak yapılandırıldığında, Azure, ExpressRoute devresini varsayılan olarak önceliklendirir. Asymmetrik yönlendirmeyi önlemek için, şirket içi ağ aynı zamanda siteden siteye VPN bağlantısı üzerinden ExpressRoute bağlantısına öncelik de sağlamalıdır. Aşağıdaki yapılandırma BGP **yerel tercih** özniteliğini kullanarak önceliklendirmesini belirler:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Siteden siteye VPN yapılandırma ayrıntıları

Aşağıdaki listede, siteden siteye VPN bağlantısı için birincil CE yönlendirici yapılandırması gösterilmektedir:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>ExpressRoute kullanarak şirket içi konum 2 bağlantısı

İkinci bir ExpressRoute devresi, şirket içi konum 2 ' ye daha yakından yaklaşarak, şirket içi konum 2 ' yi hub VNet 'e bağlar. Aşağıdaki şekilde, ikinci ExpressRoute yapılandırması gösterilmektedir:

[![inç]][6]

Aşağıdaki şekilde, ikinci ExpressRoute devresi ve hub VNet arasındaki bağlantı yapılandırması gösterilmektedir:

[![7@@]][7]

ExpressRoute 1 hem hub VNet hem de şirket içi konum 1 ' i farklı bir Azure bölgesindeki uzak VNet 'e bağlar:

[![240]][8]

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

Test kurulumunun [Denetim düzlemi analizini][Control-Analysis] ve topolojideki farklı VNET veya VLAN 'ların görünümlerini öğrenin.

Test kurulumu ve Azure ağ izleme özelliği görünümlerinin [veri düzlemi Analizi][Data-Analysis] hakkında bilgi edinin.

Bkz. [ExpressRoute SSS][ExR-FAQ] :
-   ExpressRoute ağ geçidine kaç ExpressRoute bağlantı hattı bağlayabileceğinizi öğrenin.
-   Bir ExpressRoute devresine kaç ExpressRoute ağ geçidi bağlayabileceğinizi öğrenin.
-   ExpressRoute 'un diğer ölçek sınırları hakkında bilgi edinin.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png  "Bağlı olan VNET 'In VNET eşlemesi"
[2]: ./media/backend-interoperability/HubVNet-peering.png  "Hub VNET 'In VNET eşlemesi"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png  "Şube VNet VPN Gateway yapılandırması"
[4]: ./media/backend-interoperability/ExR1.png  "ExpressRoute 1 yapılandırması"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png  "ExpressRoute 1 ' in bir hub VNET ExR Gateway 'e bağlantısı yapılandırması"
[6]: ./media/backend-interoperability/ExR2.png  "ExpressRoute 2 yapılandırması"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png  "ExpressRoute 2 ' nin merkez VNET ExR ağ geçidine bağlantı yapılandırması"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png  "ExpressRoute 2 ' nin uzak VNET ExR ağ geçidine bağlantı yapılandırması"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


