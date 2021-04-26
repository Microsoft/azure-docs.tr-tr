---
title: Azure VPN Gateway tasarımı hakkında
description: Azure sanal ağlarına bağlanmak için bir VPN Gateway topolojisi tasarlama yolları hakkında bilgi edinin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99b8cdbb1528caefd30fb11c9702dff1e817c0ab
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061817"
---
# <a name="vpn-gateway-design"></a>VPN Gateway tasarımı

VPN ağ geçidi bağlantıları için kullanılabilecek farklı yapılandırmalar vardır. Gereksinimlerinize en uygun yapılandırmayı belirlemeniz gerekir. Aşağıdaki bölümlerde, aşağıdaki VPN Gateway bağlantılarıyla ilgili tasarım bilgilerini ve topoloji diyagramlarını görüntüleyebilirsiniz. Gereksinimlerinize uygun bağlantı topolojisini seçmenize yardımcı olması için diyagramları ve açıklamaları kullanabilirsiniz. Diyagramlar ana temel topolojilerini gösterir, ancak diyagramları kılavuz olarak kullanarak daha karmaşık yapılandırmaların oluşturulması mümkündür.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Siteden Siteye ve Çok Siteli (IPsec/IKE VPN tüneli)

### <a name="site-to-site"></a><a name="S2S"></a>Siteden siteye

Siteden Siteye (S2S) VPN ağ geçidi bağlantısı, IPSec/IKE (IKEv1 veya IKEv2) VPN tüneli üzerinden kurulan bir bağlantıdır. S2S bağlantıları, şirket içi ve dışı yapılandırmalar ile birlikte karma yapılandırmalar için kullanılabilir. Bir S2S bağlantısı, bir genel IP adresi atanmış Şirket içinde bulunan bir VPN cihazı gerektirir. VPN cihazı seçme hakkında daha fazla bilgi için bkz. [VPN Gateway SSS - VPN cihazları](vpn-gateway-vpn-faq.md#s2s).

![Azure VPN Gateway Siteden Siteye bağlantı örneği](./media/design/vpngateway-site-to-site-connection-diagram.png)

VPN Gateway, tek bir genel IP kullanarak etkin bekleme modunda veya iki genel IP kullanan etkin-etkin modda yapılandırılabilir. Etkin bekleme modunda, bir IPSec tüneli etkin ve diğer tünel bekleme durumunda. Bu kurulumda trafik etkin tünelden akar ve bu tünelle ilgili bir sorun oluşursa trafik bekleme tünelinden geçer. Etkin-etkin modda VPN Gateway ayarlamak, her iki tünelde aynı anda veri akışı ile her iki IPSec tünellerinin de aynı anda etkin olması *önerilir* . Etkin-etkin moddan daha fazla avantaj, müşterilerin daha yüksek bir deneyime sahip olması.

### <a name="multi-site"></a><a name="Multi"></a>Çok siteli

Bu türden bir bağlantı, Siteden Siteye bağlantının bir çeşididir. Sanal ağ geçidinizden genellikle birden fazla şirket içi siteye bağlanan birden fazla VPN bağlantısı oluşturursunuz. Birden fazla bağlantıyla çalışırken Yol Tabanlı VPN türü (klasik sanal ağlar ile çalışırken “dinamik ağ geçidi” adıyla kullanılır) kullanmanız gerekir. Her sanal ağın yalnızca bir VPN ağ geçidi olabileceğinden, ağ geçidi boyunca tüm bağlantılar mevcut bant genişliğini paylaşır. Bu bağlantı türü genellikle "çok siteli" bağlantı olarak adlandırılır.

![Azure VPN Gateway Çok Siteli bağlantı örneği](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Siteden Siteye ve Çok Siteli için dağıtım modelleri ve yöntemleri

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Noktadan siteye VPN

Noktadan Siteye (P2S) VPN ağ geçidi bağlantısı, ayrı bir istemci bilgisayardan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. P2S bağlantısı, istemci bilgisayardan başlatılarak oluşturulur. Bu çözüm, Azure sanal ağlarına uzak bir konumdan (örneğin, evden veya bir konferanstan) bağlanmak isteyen uzaktan çalışan kişiler için kullanışlıdır. P2S VPN ayrıca, bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda S2S VPN yerine kullanabileceğiniz yararlı bir çözümüdür.

S2S bağlantılarının aksine, P2S bağlantıları için şirket içi genel kullanıma yönelik IP adresi veya VPN cihazı gerekmez. Her iki bağlantı için tüm yapılandırma gereksinimlerinin uyumlu olması şartıyla P2S bağlantıları aynı VPN ağ geçidi üzerinden S2S bağlantılarıyla birlikte kullanılabilir. Noktadan Siteye bağlantılar hakkında daha fazla bilgi edinmek için bkz. [Noktadan Siteye VPN hakkında](point-to-site-about.md).

![Azure VPN Gateway Noktadan Siteye bağlantı örneği](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>P2S için dağıtım modelleri ve yöntemleri

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Sanal Ağdan Sanal Ağa bağlantılar (IPsec/IKE VPN tüneli)

Bir sanal ağı başka bir sanal ağa bağlamak (VNet'ten VNet'e), bir VNet'i şirket içi site konumuna bağlamakla aynıdır. Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır. Hatta Sanal Ağdan Sanal Ağa iletişimini çok siteli bağlantı yapılandırmalarıyla bile birleştirebilirsiniz. Bu özellik şirket içi ve şirket dışı bağlantıyla ağ içi bağlantıyı birleştiren ağ topolojileri kurabilmenize olanak sağlar.

Bağladığınız VNet'ler,

* aynı veya farklı bölgelerde olabilir
* aynı veya farklı aboneliklerde olabilir 
* aynı veya farklı dağıtım modellerinde olabilir

![Azure VPN Gateway Sanal Ağdan Sanal Ağa bağlantı örneği](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Dağıtım modelleri arasındaki bağlantılar

Azure'ın şu anda iki dağıtım modeli vardır: Klasik ve Resource Manager. Azure'ı bir süredir kullanıyorsanız klasik VNet'te çalışan Azure VM'leriniz ve örnek rollerinizin olması olasıdır. Daha yeni VM'leriniz ve rol örnekleriniz Resource Manager'da oluşturulan bir VNet'te çalışıyor olabilir. Bir VNet'teki kaynakların bir diğerindeki kaynaklarla doğrudan iletişim kurabilmesini sağlamak üzere VNet'ler arasında bir bağlantı oluşturabilirsiniz.

### <a name="vnet-peering"></a>VNet eşlemesi

Sanal ağınız belirli gereksinimleri karşılıyorsa bağlantınızı oluşturmak için VNet eşlemesini kullanabilirsiniz. VNet eşlemesi sanal ağ geçidini kullanmaz. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Sanal Ağdan Sanal Ağa dağıtım modelleri ve yöntemleri

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (özel bağlantı)

ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute ile Microsoft Azure, Microsoft 365 ve CRM Online gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz. Bağlantının kaynağı herhangi iki ağ (IP VPN), noktadan noktaya Ethernet ağı veya ortak barındırma tesisindeki bağlantı sağlayıcısı aracılığıyla sanal çapraz bağlantı olabilir.

ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. Bu, ExpressRoute bağlantılarına İnternet üzerindeki sıradan bağlantılara göre daha fazla güvenilirlik, yüksek hız, düşük gecikme ve normal bağlantılardan daha yüksek güvenlik sağlar.

ExpressRoute bağlantısı, zorunlu yapılandırmasının bir parçası olarak sanal ağ geçidi kullanır. Bir ExpressRoute bağlantısında sanal ağ geçidi 'Vpn' yerine 'ExpressRoute' ile yapılandırılır. Bir ExpressRoute devresi üzerinden geçen trafik varsayılan olarak şifrelenmiş olmasa da, bir ExpressRoute devresi üzerinden şifrelenmiş trafik göndermenize olanak tanıyan bir çözüm oluşturmanız mümkündür. ExpressRoute hakkında daha fazla bilgi için bkz. [ExpressRoute teknik genel bakış](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Siteden Siteye ve ExpressRoute eşzamanlı bağlantıları

ExpressRoute, WAN bağlantınızdan (genel İnternet üzerinden değil) Azure dahil olmak üzere Microsoft Hizmetlerine doğrudan, özel olarak gerçekleştirilen bir bağlantıdır. Siteden Siteye VPN trafiği genel İnternet üzerinden şifrelenmiş olarak hareket eder. Aynı sanal ağ için Siteden Siteye VPN ve ExpressRoute bağlantıları yapılandırabiliyor olmanın çeşitli avantajları vardır.

ExpressRoute için güvenli bir yük devretme yolu olarak Siteden Siteye VPN yapılandırabilir veya ağınızın parçası olmayıp ExpressRoute üzerinden bağlanılan sitelere bağlanmak için Siteden Siteye VPN'ler kullanabilirsiniz. Bu yapılandırma, aynı sanal ağ için biri ‘Vpn’, diğeri ‘ExpressRoute’ ağ geçidi türünü kullanan iki sanal ağ geçidinin kullanılmasını gerektirir.

![ExpressRoute ve VPN Gateway eşzamanlı bağlantı örneği](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>S2S ve ExpressRoute dağıtım modelleri ve yöntemleri aynı anda mevcut

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Yüksek oranda kullanılabilir bağlantılar

Yüksek oranda kullanılabilir bağlantılara yönelik planlama ve tasarım için bkz. [yüksek oranda kullanılabilir bağlantılar](vpn-gateway-highlyavailable.md).

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilgi edinmek için [VPN Gateway ile ilgili SSS](vpn-gateway-vpn-faq.md) makalesini görüntüleyin.

* [VPN Gateway yapılandırma ayarları](vpn-gateway-about-vpn-gateway-settings.md)hakkında daha fazla bilgi edinin.

* VPN Gateway BGP konuları için bkz. [BGP hakkında](vpn-gateway-bgp-overview.md).

* [Abonelik ve hizmet sınırlamaları](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini görüntüleyin.

* Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
