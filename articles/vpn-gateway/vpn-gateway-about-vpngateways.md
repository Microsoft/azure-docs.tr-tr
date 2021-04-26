---
title: Azure VPN Gateway hakkında
description: VPN Gateway ne olduğunu ve IPSec ıKE siteden siteye, VNet-VNet ve Noktadan siteye VPN sanal ağlarına bağlanmak için VPN Gateway nasıl kullanacağınızı öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom:
- contperf-fy21q1
- e2e-hybrid
ms.openlocfilehash: b4a2e1b80fe62c5e8f74e35a1a7f61bdddf89c53
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066543"
---
# <a name="what-is-vpn-gateway"></a>VPN Ağ Geçidi nedir?

VPN ağ geçidi, genel İnternet üzerinden bir Azure sanal ağı ile şirket içi konum arasında şifrelenmiş trafik göndermek için kullanılan belirli bir sanal ağ geçidi türüdür. Ayrıca VPN ağ geçidini Microsoft ağı üzerinden Azure sanal ağları arasında şifrelenmiş trafik göndermek için de kullanabilirsiniz. Her sanal ağın yalnızca bir VPN ağ geçidi olabilir. Ancak, aynı VPN ağ geçidi ile birden fazla bağlantı oluşturabilirsiniz. Aynı VPN ağ geçidiyle birden fazla bağlantı oluşturduğunuzda, tüm VPN tünelleri kullanılabilir ağ geçidi bant genişliğini paylaşır.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Sanal ağ geçidi nedir?

Sanal ağ geçidi, *ağ geçidi alt ağı* olarak adlandırılan, oluşturduğunuz belirli bir alt ağa dağıtılan iki veya daha fazla VM 'den oluşur. Sanal ağ geçidi VM 'Leri, yönlendirme tabloları içerir ve belirli ağ geçidi hizmetlerini çalıştırır. Bu VM 'Ler, sanal ağ geçidini oluşturduğunuzda oluşturulur. Sanal ağ geçidinin parçası olan VM 'Leri doğrudan yapılandıramazsınız.

Bir sanal ağ geçidi yapılandırdığınızda, ağ geçidi türünü belirten bir ayar yapılandırırsınız. Ağ Geçidi türü, sanal ağ geçidinin nasıl kullanılacağını ve ağ geçidinin gerçekleştireceği eylemleri belirler. ' VPN ' ağ geçidi türü, oluşturulan sanal ağ geçidi türünün bir ' VPN Gateway ' olduğunu belirtir. Bu, farklı bir ağ geçidi türü kullanan bir ExpressRoute Gateway 'ten ayırt eder. Bir sanal ağda iki sanal ağ geçidi olabilir; bir VPN Gateway ve bir ExpressRoute ağ geçidi. Daha fazla bilgi için bkz. [ağ geçidi türleri](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

Bir sanal ağ geçidinin oluşturulması 45 dakika sürebilir. Bir sanal ağ geçidi oluşturduğunuzda ağ geçidi VM’leri ağ geçidi alt ağına dağıtılır ve belirttiğiniz ayarlarla yapılandırılır. Bir VPN ağ geçidi oluşturduktan sonra bu VPN ağ geçidi ile başka bir VPN ağ geçidi arasında bir IPsec/IKE VPN tüneli bağlantısı (Sanal Ağlar arası) oluşturabilir veya VPN ağ geçidi ile bir şirket içi VPN cihazı (Siteden Siteye) arasında IPsec/IKE VPN tünel bağlantısı oluşturabilirsiniz. Ayrıca, sanal ağınıza bir konferans veya evden gibi uzak bir konumdan bağlanmanızı sağlayan bir noktadan siteye VPN bağlantısı (OpenVPN, Ikev2 veya SSTP üzerinden VPN) oluşturabilirsiniz.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>VPN Gateway yapılandırma

VPN ağ geçidi bağlantısı belirli ayarlarla yapılandırılmış birden fazla kaynağı kullanır. Kaynakların birçoğu ayrı ayrı yapılandırılabilir, ancak bazı kaynakların belirli bir sırayla yapılandırılması gerekir.

### <a name="design"></a><a name="diagrams"></a>Tasarım

VPN ağ geçidi bağlantıları için kullanılabilecek farklı yapılandırmalar vardır. Gereksinimlerinize en uygun yapılandırmayı belirlemeniz gerekir. Örneğin, Noktadan siteye, siteden siteye ve birlikte var olan ExpressRoute/siteden siteye bağlantıların hepsi farklı yönergeler ve yapılandırma gereksinimlerine sahiptir. Tasarım ve bağlantı topolojisi diyagramlarını görüntüleme hakkında daha fazla bilgi için bkz. [Tasarım](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Planlama tablosu

Aşağıdaki tablo çözümünüz için en iyi bağlantı seçeneğine karar vermenize yardımcı olabilir.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Ayarlar

Her kaynak için seçtiğiniz ayarlar başarılı bir bağlantı oluşturmak için önemlidir. VPN Gateway’e ilişkin kaynaklar ve ayarlar hakkında bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md). Makale; ağ geçidi türleri, ağ geçidi SKU’ları, VPN türleri, bağlantı türleri, ağ geçidi alt ağları, yerel ağ geçitleri ve kullanmayı düşünebileceğiniz diğer çeşitli ayarlar hakkında bilgi içerir.

### <a name="deployment-tools"></a><a name="tools"></a>Dağıtım araçları

Azure portalı gibi bir yapılandırma aracını kullanarak kaynakları oluşturmaya ve yapılandırmaya başlayabilirsiniz. Daha sonra ek kaynaklar yapılandırmak ya da uygun olduğunda var olan kaynakları değiştirmek için PowerShell gibi başka bir araca geçmeye karar verebilirsiniz. Şu anda Azure portalında her kaynağı ve kaynak ayarını yapılandıramazsınız. Her bağlantı topolojisine ilişkin makalelerdeki yönergelerde, belirli bir aracının ne zaman gerekli olduğu belirtilmektedir.

## <a name="gateway-skus"></a><a name="gwsku"></a>Ağ Geçidi SKU’ları

Bir sanal ağ geçidi oluşturduğunuzda, kullanmak istediğiniz ağ geçidi SKU’sunu belirtmelisiniz. İş yükü, aktarım hızı, özellik ve SLA türlerine bağlı olarak gereksinimlerinize uyan SKU’ları seçin.

* Desteklenen özellikler, üretim ve geliştirme-test ve yapılandırma adımları dahil ağ geçidi SKU 'Ları hakkında daha fazla bilgi için [VPN Gateway ayarları-ağ geçidi SKU 'ları](vpn-gateway-about-vpn-gateway-settings.md#gwsku) makalesine bakın.
* Eski SKU bilgileri için bkz. [eski SKU 'lar Ile çalışma](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Tünele, bağlantıya ve performansa göre Ağ Geçidi SKU’ları

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Kullanılabilirlik Alanları

VPN ağ geçitleri Azure Kullanılabilirlik Alanları ' de dağıtılabilir. Bu seçenek, sanal ağ geçitlerine dayanıklılık, ölçeklenebilirlik ve daha yüksek kullanılabilirlik getirir. Ağ geçitlerini Azure Kullanılabilirlik Alanları içinde dağıtmak, bir bölge içindeki ağ geçitlerini fiziksel ve mantıksal olarak birbirinden ayırırken, Azure ile şirket içi ağ bağlantınızı alan düzeyindeki hatalardan korur. [Azure kullanılabilirlik alanları ' de bölgesel olarak yedekli sanal ağ geçitleri hakkında](about-zone-redundant-vnet-gateways.md)bölümüne bakın.

## <a name="pricing"></a><a name="pricing"></a>Fiyatlandırma

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

VPN Gateway’e yönelik ağ geçidi SKU’ları hakkında bilgi için bkz. [Ağ geçidi SKU’ları](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>SSS

VPN Gateway hakkında sık sorulan sorular için bkz. [VPN Gateway hakkında SSS](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Yenilikler nelerdir?

RSS akışına abone olun ve [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) sayfasında en son VPN Gateway Özellik güncelleştirmelerini görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi edinmek için [VPN Gateway ile ilgili SSS](vpn-gateway-vpn-faq.md) makalesini görüntüleyin.
- [Abonelik ve hizmet sınırlamaları](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini görüntüleyin.
- Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
