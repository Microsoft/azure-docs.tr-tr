---
title: Azure sanal ağ eşlemesi
titlesuffix: Azure Virtual Network
description: Azure sanal ağ 'da ağları nasıl bağlayabileceğinizi de kapsayan Azure 'da sanal ağ eşlemesi hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 1356c896abff8c525df283251240d027123405fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778298"
---
# <a name="virtual-network-peering"></a>Sanal ağ eşleme

Sanal ağ eşlemesi, Azure 'da iki veya daha fazla [sanal ağı](virtual-networks-overview.md) sorunsuzca bağlamanıza olanak sağlar. Sanal ağlar, bağlantı amaçlarıyla bir tane olarak görünür. Eşlenen sanal ağlardaki sanal makineler arasındaki trafik, Microsoft omurga altyapısını kullanır. Aynı ağdaki sanal makineler arasındaki trafik gibi, trafik yalnızca Microsoft 'un *özel* ağı aracılığıyla yönlendirilir.

Azure aşağıdaki eşleme türlerini destekler:

* **Sanal ağ eşlemesi**: aynı Azure bölgesindeki sanal ağları bağlayın.
* **Küresel sanal ağ eşlemesi**: Azure bölgeleri arasında sanal ağları bağlama.

Yerel veya genel sanal ağ eşlemesini kullanmanın avantajları şunlardır:

* Farklı sanal ağlardaki kaynaklar arasında düşük gecikme süresi ve yüksek bant genişlikli bağlantı.
* Bir sanal ağdaki kaynakların farklı bir sanal ağdaki kaynaklarla iletişim kurmasına olanak tanır.
* Azure abonelikleri, Azure Active Directory kiracılar, dağıtım modelleri ve Azure bölgeleri arasında sanal ağlar arasında veri aktarma özelliği.
* Azure Resource Manager aracılığıyla oluşturulmuş sanal ağları eşleyebilme özelliği.
* Kaynak Yöneticisi aracılığıyla oluşturulan bir sanal ağı, klasik dağıtım modeliyle oluşturulan bir ağa eşleyebilme özelliği. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için bkz. [Azure dağıtım modellerini kavrama](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Eşleme oluştururken veya eşleme oluşturulduktan sonra iki sanal ağdaki kaynaklarda da kesinti süresi yaşanmaz.

Eşlenen sanal ağlar arasındaki ağ trafiği gizlidir. Sanal ağlar arasındaki trafik Microsoft omurga ağı üzerinde tutulur. Sanal ağlar arasındaki iletişimde genel İnternet, ağ geçidi veya şifreleme gerekli değildir.

## <a name="connectivity"></a>Bağlantı

Eşlenen sanal ağlar için, her iki sanal ağdaki kaynaklar eşlenen sanal ağdaki kaynaklarla doğrudan bağlantı sağlayabilir.

Aynı bölgede yer alan eşlenmiş sanal ağlardaki sanal makineler arasındaki ağ gecikme süresi, tek bir sanal ağdaki gecikme süresiyle aynıdır. Ağ verimi, büyüklüğüne orantılı olarak sanal makine için izin verilen bant genişliğine bağlıdır. Eşleme içindeki bant genişliği ile ilgili herhangi bir ek kısıtlama yoktur.

Eşlenmiş sanal ağlarda bulunan sanal makineler arasındaki trafik bir ağ geçidi veya genel İnternet üzerinden değil, doğrudan Microsoft omurga altyapısı aracılığıyla yönlendirilir.

Diğer sanal ağlara veya alt ağlara erişimi engellemek için, her iki sanal ağ üzerinde ağ güvenlik grupları uygulayabilirsiniz.
Sanal Ağ eşlemesini yapılandırırken, sanal ağlar arasındaki ağ güvenlik grubu kurallarını açın veya kapatın. Eşlenen sanal ağlar arasında tam bağlantı açarsanız, belirli erişimi engellemek veya reddetmek için ağ güvenlik grupları uygulayabilirsiniz. Tam bağlantı varsayılan seçenektir. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek için bkz. [güvenlik grupları](./network-security-groups-overview.md).

## <a name="service-chaining"></a>Hizmet zinciri

Hizmet zincirleme, bir sanal ağdan gelen trafiği, Kullanıcı tanımlı yollar aracılığıyla eşlenmiş bir ağdaki sanal gereç veya ağ geçidine yönlendirmenizi sağlar.

Hizmet zincirlemesini etkinleştirmek için, eşlenmiş sanal ağlardaki sanal makineleri *sonraki atlama* IP adresi olarak işaret eden Kullanıcı tanımlı yolları yapılandırın. Kullanıcı tanımlı yollar Ayrıca, hizmet zincirlemesini etkinleştirmek için sanal ağ geçitlerine işaret verebilir.

Hub sanal ağının ağ sanal gereci veya VPN Gateway gibi altyapı bileşenlerini barındırdığı merkez ve bağlı ağ ağlarını dağıtabilirsiniz. Daha sonra, tüm ağlı sanal ağlar merkezi sanal ağla eşlenebilir. Trafik, hub sanal ağındaki ağ sanal gereçleri veya VPN ağ geçitleri aracılığıyla akar.

Sanal ağ eşlemesi sayesinde, kullanıcı tanımlı yolda bir sonraki atlama, eşlenen sanal ağdaki veya bir VPN ağ geçidindeki bir sanal makinenin IP adresi olabilir. Sonraki atlama türü olarak bir Azure ExpressRoute ağ geçidini belirten Kullanıcı tanımlı bir yol ile sanal ağlar arasında yönlendirileyemiyorum. Kullanıcı tanımlı yollar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı yollara genel bakış](virtual-networks-udr-overview.md#user-defined). Hub ve bağlı bileşen ağ topolojisi oluşturmayı öğrenmek için bkz. [Azure 'Da Merkez-uç ağ topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Ağ geçitleri ve şirket içi bağlantı

Eşlenen bir sanal ağ da dahil olmak üzere her sanal ağ kendi ağ geçidine sahip olabilir. Bir sanal ağ, ağ geçidini şirket içi bir ağa bağlanmak için kullanabilir. Ayrıca, eşlenen sanal ağlar için bile ağ geçitlerini kullanarak [sanal ağdan sanal ağ bağlantılarını](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırabilirsiniz.

Sanal ağ bağlantısı için her iki seçeneği de yapılandırdığınızda, sanal ağlar arasındaki trafik, eşleme yapılandırması üzerinden akar. Trafik Azure omurgasını kullanır.

Eşlenen sanal ağdaki ağ geçidini şirket içi bir ağa geçiş noktası olarak da yapılandırabilirsiniz. Bu durumda, uzak ağ geçidi kullanan sanal ağın kendi ağ geçidi olamaz. Bir sanal ağın yalnızca bir ağ geçidi vardır. Ağ Geçidi, aşağıdaki diyagramda gösterildiği gibi, eşlenen sanal ağdaki yerel veya uzak bir ağ geçididir:

![Sanal ağ eşleme geçişi](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Hem sanal ağ eşlemesi hem de küresel sanal ağ eşlemesi, ağ geçidi geçişi destekler.

Farklı dağıtım modelleriyle oluşturulan sanal ağlar arasındaki ağ geçidi geçişi desteklenir. Ağ Geçidi, Kaynak Yöneticisi modelinde sanal ağda olmalıdır. Geçiş için bir ağ geçidi kullanma hakkında daha fazla bilgi için bkz. [Sanal ağ eşlemesinde geçiş için bir VPN ağ geçidi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Tek bir Azure ExpressRoute bağlantısını paylaşan sanal ağları eşaktardığınızda, aralarındaki trafik eşleme ilişkisine gider. Bu trafik Azure omurga ağını kullanır. Şirket içi devreye bağlanmak için her bir sanal ağ üzerindeki yerel ağ geçitlerini kullanmaya devam edebilirsiniz. Aksi takdirde, paylaşılan bir ağ geçidini kullanabilir ve şirket içi bağlantı için geçişi yapılandırabilirsiniz.

## <a name="troubleshoot"></a>Sorun giderme

Sanal ağların eşlendiğini onaylamak için, etkin yolları kontrol edebilirsiniz. Bir sanal ağ içindeki herhangi bir alt ağda bulunan bir ağ arabirimine yönelik yolları denetleyin. Bir sanal ağ eşlemesi zaten varsa, sanal ağ içindeki tüm alt ağlar, eşlenen her bir sanal ağdaki her bir adres alanı için sonraki atlama türü *VNet eşlemesi* olan yollara sahip olur. Daha fazla bilgi için bkz. [sanal makine yönlendirme sorununu tanılama](diagnose-network-routing-problem.md).

Ayrıca, Azure ağ Izleyicisi 'ni kullanarak eşlenmiş bir sanal ağdaki sanal makineye yönelik bağlantı sorunlarını giderebilirsiniz. Bağlantı denetimi, trafiğin bir kaynak sanal makinenin ağ arabiriminden hedef sanal makinenin ağ arabirimine nasıl yönlendirildiğini görmenizi sağlar. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure Ağ İzleyicisi ile bağlantı sorunlarını giderme](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Ayrıca, [sanal ağ eşleme sorunlarını giderin](virtual-network-troubleshoot-peering-issues.md)' i deneyebilirsiniz.

## <a name="constraints-for-peered-virtual-networks"></a>Eşlenen sanal ağlar için kısıtlamalar<a name="requirements-and-constraints"></a>

Aşağıdaki kısıtlamalar yalnızca sanal ağlar genel olarak eşlendikten sonra geçerlidir:

* Bir sanal ağdaki kaynaklar, genel olarak eşlenmiş bir sanal ağdaki temel bir Iç Load Balancer (ıLB) ön uç IP adresiyle iletişim kuramaz.
* Temel yük dengeleyici kullanan bazı hizmetler genel sanal ağ eşlemesi üzerinde çalışmaz. Daha fazla bilgi için bkz. [genel VNET eşlemesi ve yük dengeleyiciler ile ilgili kısıtlamalar nelerdir?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Daha fazla bilgi için bkz. [gereksinimler ve kısıtlamalar](virtual-network-manage-peering.md#requirements-and-constraints). Desteklenen eşleme sayısı hakkında daha fazla bilgi edinmek için bkz. [ağ sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>İzinler

Sanal ağ eşlemesi oluşturmak için gereken izinler hakkında bilgi edinmek için, bkz. [izinler](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Fiyatlandırma

Sanal ağ eşleme bağlantısı kullanan giriş ve çıkış trafiği için nominal bir ücret uygulanır. Daha fazla bilgi için bkz. [sanal ağ fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network).

Ağ Geçidi geçişi, bir sanal ağın eşlenen bir sanal ağda VPN/ExpressRoute Gateway kullanmasını sağlayan bir eşleme özelliğidir. Ağ Geçidi geçişi, hem şirket içi hem de ağdan ağa bağlantı için kullanılır. Eşlenen sanal ağdaki ağ geçidine (giriş veya çıkış) giden trafik, bağlı olan VNet (veya ağ geçidi olmayan VNet) üzerinde sanal ağ eşleme ücretleri doğurur. Daha fazla bilgi için bkz. ExpressRoute ağ geçidi ücretleri için VPN Gateway ücretleri için fiyatlandırma ve ExpressRoute ağ geçidi fiyatlandırması [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) .

>[!NOTE]
> Bu belgenin önceki bir sürümü, ağ geçidi aktarımı ile bağlı olan VNet (veya ağ geçidi olmayan VNet) üzerinde sanal ağ eşleme ücretlerinin uygulanabileceğini ifade etmez. Artık fiyatlandırma sayfasına göre doğru fiyatlandırmayı yansıtır.

## <a name="next-steps"></a>Sonraki adımlar

* İki sanal ağ arasında bir eşleme oluşturabilirsiniz. Ağlar aynı aboneliğe, aynı abonelikte farklı dağıtım modellerine veya farklı aboneliklere ait olabilir. Aşağıdaki senaryolardan biri için öğreticiyi tamamlayın:

    |Azure dağıtım modeli             | Abonelik  |
    |---------                          |---------|
    |Her ikisi de Resource Manager              |[Aynı](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Farklı](create-peering-different-subscriptions.md)|
    |Biri Resource Manager, diğeri klasik  |[Aynı](create-peering-different-deployment-models.md)|
    |                                   |[Farklı](create-peering-different-deployment-models-subscriptions.md)|

* Hub ve bağlı bileşen ağ topolojisi oluşturmayı öğrenmek için bkz. [Azure 'Da Merkez-uç ağ topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Tüm sanal ağ eşleme ayarları hakkında bilgi edinmek için bkz. [sanal ağ eşlemesi oluşturma, değiştirme veya silme](virtual-network-manage-peering.md).
* Ortak sanal ağ eşlemesi ve küresel sanal ağ eşlemesi sorularına yanıt almak için bkz. [VNET eşlemesi](virtual-networks-faq.md#vnet-peering).
