---
title: Azure sanal ağ eşlemesi
titlesuffix: Azure Virtual Network
description: Azure'daki sanal ağ eşlemesi hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: anavin
ms.openlocfilehash: 728d32ddb63658d24e932e8eeef4a3f50371ccc3
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72265057"
---
# <a name="virtual-network-peering"></a>Sanal ağ eşlemesi

Sanal ağ eşlemesi, Azure [sanal ağlarına](virtual-networks-overview.md)sorunsuz bir şekilde bağlanmanıza olanak sağlar. Eşleme yapıldıktan sonra, bağlantı açısından sanal ağlar tek bir sanal ağ gibi görünür. Eşlenen sanal ağlarda bulunan sanal makineler arasındaki trafik, Microsoft omurga altyapısı aracılığıyla tıpkı sanal ağdaki sanal makineler arasında olduğu gibi yalnızca *özel* IP adresleri üzerinden yönlendirilir. Azure’ın destekledikleri:
* Sanal ağ eşleme - aynı Azure bölgesindeki sanal ağları bağlama
* Genel sanal ağ eşleme - Azure bölgeleri arasında sanal ağları bağlama

Yerel veya genel sanal ağ eşlemesini kullanmanın avantajları şunlardır:

* Eşlenen sanal ağlar arasındaki ağ trafiği gizlidir. Sanal ağlar arasındaki trafik Microsoft omurga ağı üzerinde tutulur. Sanal ağlar arasındaki iletişimde genel İnternet, ağ geçidi veya şifreleme gerekli değildir.
* Farklı sanal ağlardaki kaynaklar arasında düşük gecikme süresi ve yüksek bant genişlikli bağlantı.
* Sanal ağlar eşlendikten sonra Sanal ağların birindeki kaynaklar farklı bir sanal ağdaki kaynaklarla iletişim kurabilir.
* Verilerinizi farklı Azure abonelikleri, dağıtım modelleri ve Azure bölgeleri arasında taşıma imkanı.
* Azure Resource Manager ile oluşturulan sanal ağları eşleyebilme veya Resource Manager ile oluşturulan bir sanal ağı klasik dağıtım modeliyle oluşturulan sanal ağ ile eşleyebilme özelliği. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için bkz. [Azure dağıtım modellerini kavrama](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Eşleme oluştururken veya eşleme oluşturulduktan sonra iki sanal ağdaki kaynaklarda da kesinti süresi yaşanmaz.

## <a name="connectivity"></a>Bağlantı

Sanal ağlar eşlendikten sonra, eşlenen sanal ağlardan herhangi birindeki kaynaklar diğer sanal ağın kaynaklarıyla doğrudan bağlantı kurabilir.

Aynı bölgede yer alan eşlenmiş sanal ağlardaki sanal makineler arasındaki ağ gecikme süresi, tek bir sanal ağdaki gecikme süresiyle aynıdır. Ağ verimi, büyüklüğüne orantılı olarak sanal makine için izin verilen bant genişliğine bağlıdır. Eşleme içindeki bant genişliği ile ilgili herhangi bir ek kısıtlama yoktur.

Eşlenmiş sanal ağlarda bulunan sanal makineler arasındaki trafik bir ağ geçidi veya genel İnternet üzerinden değil, doğrudan Microsoft omurga altyapısı aracılığıyla yönlendirilir.

İstendiğinde, diğer sanal ağlara veya alt ağlara erişimi engellemek için her bir sanal ağda ağ güvenlik grupları uygulanabilir.
Sanal ağ eşlemesi yapılandırırken, sanal ağlar arasındaki ağ güvenlik grubu kurallarını açabilir veya kapatabilirsiniz. Eşlenen sanal ağlar arasında tam bağlantıyı (varsayılan seçenek) açarsanız, belirli erişimleri engellemek ya da reddetmek için belirli alt ağlara veya sanal makinelere ağ güvenlik grupları uygulayabilirsiniz. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek bkz. [Ağ güvenlik gruplarına genel bakış](security-overview.md).

## <a name="service-chaining"></a>Hizmet zinciri

Hizmet zinciri oluşturmayı etkinleştirmek için *sonraki atlama* IP adresi olarak eşlenen sanal ağlardaki sanal makinelere veya sanal makine ağ geçitlerine işaret eden kullanıcı tanımlı yollar yapılandırabilirsiniz. Hizmet zinciri oluşturma, kullanıcı tanımlı yollar aracılığıyla trafiği bir sanal ağdan eşlenmiş bir sanal ağdaki bir sanal gerece veya bir sanal ağ geçidine yönlendirmenize imkan tanır.

Ayrıca, hub ve bağlı bileşen ağlarını da verimli bir şekilde oluşturabilirsiniz. Bu ağlarda hub sanal ağı, ağ sanal gereci veya VPN ağ geçidi gibi altyapı bileşenlerini barındırabilir. Daha sonra, tüm ağlı sanal ağlar merkezi sanal ağla eşlenebilir. Trafik, hub sanal ağındaki ağ sanal gereçleri veya VPN ağ geçitleri üzerinden akabilir. 

Sanal ağ eşlemesi sayesinde, kullanıcı tanımlı yolda bir sonraki atlama, eşlenen sanal ağdaki veya bir VPN ağ geçidindeki bir sanal makinenin IP adresi olabilir. Ancak, sonraki atlama türü olarak ExpressRoute ağ geçidini belirten kullanıcı tanımlı bir yol ile sanal ağlar arasında yönlendirme yapamazsınız. Kullanıcı tanımlı yollar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı yollara genel bakış](virtual-networks-udr-overview.md#user-defined). Merkez ve uç ağ topolojisi oluşturmayı öğrenmek için bkz. [Merkez ve uç ağ topolojisi oluşturma](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Ağ geçitleri ve şirket içi bağlantı

Her sanal ağ başka bir sanal ağ ile eşlenip eşlenmediğine bakılmaksızın kendi ağ geçidine sahip olabilir ve bu sanal ağ geçidini şirket içi bir ağa bağlanmak için kullanabilir. Ayrıca, sanal ağlar eşlenmiş olsa bile ağ geçitlerini kullanarak [sanal ağlar arası bağlantılar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırabilirsiniz.

Sanal ağlar arası bağlantı için her iki seçenek de yapılandırıldığında, sanal ağlar arasındaki trafik, eşleme yapılandırması (Azure omurgası) üzerinden akış gerçekleştirir.

Sanal ağlar eşlendiğinde, eşlenmiş sanal ağdaki ağ geçidini şirket içi bir ağa geçiş noktası olarak da yapılandırabilirsiniz. Bu durumda, uzak ağ geçidi kullanan sanal ağın kendi ağ geçidi olamaz. Bir sanal ağın yalnızca bir ağ geçidi olabilir. Ağ geçidi, aşağıdaki resimde gösterildiği gibi yerel veya uzak bir ağ geçidi (eşlenen sanal ağda) olabilir:

![Sanal ağ eşleme geçişi](./media/virtual-networks-peering-overview/figure04.png)

Ağ Geçidi geçişi hem VNet eşlemesi hem de küresel VNet eşlemesi için desteklenir. Farklı dağıtım modelleri (Kaynak Yöneticisi ve klasik) aracılığıyla oluşturulan sanal ağlar arasındaki ağ geçidi geçişi, yalnızca ağ geçidi sanal ağda (Kaynak Yöneticisi) olduğunda desteklenir. Geçiş için bir ağ geçidi kullanma hakkında daha fazla bilgi için bkz. [Sanal ağ eşlemesinde geçiş için bir VPN ağ geçidi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Tek bir Azure ExpressRoute bağlantısını kullanan sanal ağlar eşlendiğinde, bu iki sanal ağ arasındaki trafik, eşleme ilişkisi (Azure omurga ağı) üzerinden akış gerçekleştirir. Şirket içi devreye bağlanmak için her bir sanal ağ üzerindeki yerel ağ geçitlerini kullanmaya devam edebilirsiniz. Alternatif olarak, paylaşılan bir ağ geçidini kullanıp şirket içi bağlantı için bir geçiş yapılandırabilirsiniz.

## <a name="troubleshoot"></a>Sorun giderme

Bir sanal ağ eşlemesini onaylamak için, bir sanal ağdaki herhangi bir alt ağın ağ arabirimine yönelik [etkili yolları denetleyebilirsiniz](diagnose-network-routing-problem.md). Bir sanal ağ eşlemesi zaten varsa, sanal ağ içindeki tüm alt ağlar, eşlenen her bir sanal ağdaki her bir adres alanı için sonraki atlama türü *VNet eşlemesi* olan yollara sahip olur.

Eşlenmiş sanal ağdaki bir sanal makinenin bağlantı durumuyla ilgili sorunları gidermek için Ağ İzleyicisi'nin [bağlantı denetimini](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de kullanabilirsiniz. Bağlantı denetimi sayesinde trafiğin bir kaynak sanal makinenin ağ arabiriminden hedef sanal makinenin ağ arabirimine nasıl yönlendirildiğini denetleyebilirsiniz.

Ayrıca, [sanal ağ eşleme sorunları Için sorun gidericiyi](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues)deneyebilirsiniz.

## <a name="requirements-and-constraints"></a>Gereksinimler ve kısıtlamalar

Aşağıdaki kısıtlamalar yalnızca sanal ağlar genel olarak eşlendikten sonra geçerlidir:
- Bir sanal ağdaki kaynaklar, genel olarak eşlenmiş bir sanal ağdaki temel bir iç yük dengeleyicinin ön uç IP adresiyle iletişim kuramaz. Temel Load Balancer desteği yalnızca aynı bölgede bulunur. Hem VNet eşlemesi hem de küresel VNet eşlemesi için Standart Load Balancer desteği vardır. Küresel VNet eşlemesi üzerinden çalışmayan temel bir yük dengeleyici kullanan hizmetler [burada belgelenmiştir.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Gereksinimler ve kısıtlamalar hakkında daha fazla bilgi edinmek için bkz. [Sanal ağ eşleme gereksinimleri ve kısıtlamaları](virtual-network-manage-peering.md#requirements-and-constraints). Bir sanal ağ için oluşturabileceğiniz eşleme sayısı sınırları hakkında bilgi edinmek için bkz. [Azure ağ sınırları](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>İzinler

Bir sanal ağ eşlemesi oluşturmak için gereken izinler hakkında bilgi edinmek için bkz. [Sanal ağ eşleme izinleri](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Fiyatlandırma

Sanal ağ eşleme bağlantısı kullanan girdi ve çıkış trafiği için nominal bir ücret uygulanır. Sanal Ağ Eşleme ve Genel Sanal Ağ eşleme fiyatlandırması hakkında daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/virtual-network).

Ağ Geçidi geçişi, bir sanal ağın, şirket içi veya VNet-VNet bağlantısı için eşlenmiş bir sanal ağda VPN/ExpressRoute Gateway kullanmasını sağlayan bir eşleme özelliğidir. Eşlenen VNet 'teki ağ geçidine (giriş veya çıkış) giden trafik, VNet eşleme ücretlerine tabi olacaktır. Daha fazla ayrıntı için [VPN Gateway ücretlerine](https://azure.microsoft.com/pricing/details/vpn-gateway/) veya ExpressRoute ağ geçidi ücretlerine ve [VNET eşleme ücretlerine bakın.](https://azure.microsoft.com/pricing/details/virtual-network)

>[!NOTE]
> Bu belgenin önceki bir sürümü, VNet eşleme ücretlerinin ağ geçidi aktarımına uygulanabileceğini ifade etmez. Bu, fiyatlandırma sayfası başına doğru fiyatlandırmayı yansıtacak şekilde güncelleştirilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

* Aynı veya farklı aboneliklerde, aynı veya farklı dağıtım modelleriyle oluşturulmuş sanal ağlar arasında, bir sanal ağ eşlemesi oluşturulur. Aşağıdaki senaryolardan biri için öğreticiyi tamamlayın:

    |Azure dağıtım modeli             | Abonelik  |
    |---------                          |---------|
    |Her ikisi de Resource Manager              |[Aynı](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Farklı](create-peering-different-subscriptions.md)|
    |Biri Resource Manager, diğeri klasik  |[Aynı](create-peering-different-deployment-models.md)|
    |                                   |[Farklı](create-peering-different-deployment-models-subscriptions.md)|

* [Hub ve bağlı bileşen ağ topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturmayı öğrenin.
* Tüm [sanal ağ eşleme ayarları ve ayarların nasıl değiştirileceği](virtual-network-manage-peering.md) hakkında bilgi edinin.
* Sık sorulan Sanal Ağ Eşleme ve Genel Sanal Ağ Eşleme sorularının yanıtları için bkz. [Sanal Ağ Eşleme Hakkında SSS](virtual-networks-faq.md#vnet-peering)
