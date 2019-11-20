---
title: Azure sanal ağlarını planlayın | Microsoft Docs
description: Yalıtım, bağlantı ve konum gereksinimleriniz temelinde sanal ağları planlamayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: 47da2524f719e53edcbd89686a1a0b76fa6e79cd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802718"
---
# <a name="plan-virtual-networks"></a>Sanal ağları planlayın

Denemek için bir sanal ağ oluşturmak oldukça kolaydır, ancak olasılığınızı, kuruluşunuzun üretim ihtiyaçlarını desteklemek için zaman içinde birden çok sanal ağı dağıtacaksınız. Bazı planlamayla, sanal ağlar dağıtabilir ve ihtiyacınız olan kaynakları daha verimli bir şekilde bağlayabilirsiniz. Bu makaledeki bilgiler, sanal ağları zaten biliyorsanız ve bunlarla çalışan bir deneyim varsa en çok yardımcı olur. Sanal ağlar hakkında bilgi sahibi değilseniz, [sanal ağa genel bakış](virtual-networks-overview.md)' ı okumanız önerilir.

## <a name="naming"></a>Adlandırma

Tüm Azure kaynakları bir ada sahiptir. Ad, her kaynak türü için değişebilen bir kapsam içinde benzersiz olmalıdır. Örneğin, bir sanal ağın adı bir [kaynak grubu](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)içinde benzersiz olmalıdır, ancak bir [abonelik](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) veya Azure [bölgesi](https://azure.microsoft.com/regions/#services)içinde yinelenebilir. Zaman içinde birkaç ağ kaynağını yönetirken, kaynak adlandırma yararlı olduğunda tutarlı bir şekilde kullanabileceğiniz bir adlandırma kuralı tanımlama. Öneriler için bkz. [adlandırma kuralları](/azure/architecture/best-practices/resource-naming#networking).

## <a name="regions"></a>Bölgeler

Tüm Azure kaynakları bir Azure bölgesinde ve aboneliğinde oluşturulur. Kaynak, kaynak ile aynı bölgede ve abonelikte bulunan bir sanal ağda oluşturulabilir. Bununla birlikte, farklı aboneliklerde ve bölgelerde bulunan sanal ağları bağlayabilirsiniz. Daha fazla bilgi için bkz. [bağlantı](#connectivity). İçindeki kaynakları dağıtmak için hangi bölgelere karar verirken, kaynakların tüketicilerinin fiziksel olarak bulunduğu yeri göz önünde bulundurun:

- Kaynak tüketicileri genellikle kaynakları için en düşük ağ gecikmesini ister. Belirtilen bir konum ile Azure bölgesi arasındaki göreli gecikmeleri öğrenmek için bkz. [göreli gecikme sürelerini görüntüleme](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Veri yerleşimi, egemenlik, uyumluluk veya dayanıklılık gereksinimleriniz var mı? Bu durumda, gereksinimlere göre hizalanan bölgenin seçilmesi kritik öneme sahiptir. Daha fazla bilgi için bkz. [Azure coğrafi lıkları](https://azure.microsoft.com/global-infrastructure/geographies/).
- Dağıttığınız kaynaklar için aynı Azure bölgesindeki Azure Kullanılabilirlik Alanları dayanıklılık gerektirsin mi? Sanal makineler (VM) gibi kaynakları, aynı sanal ağ içindeki farklı kullanılabilirlik bölgelerine dağıtabilirsiniz. Ancak tüm Azure bölgeleri kullanılabilirlik bölgelerini desteklemez. Kullanılabilirlik alanları ve bunları destekleyen bölgeler hakkında daha fazla bilgi edinmek için bkz. [kullanılabilirlik alanları](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Abonelikler

Her abonelik için gereken sayıda sanal ağı en fazla [sınıra](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)kadar dağıtabilirsiniz. Bazı kuruluşların, örneğin farklı departmanlar için farklı abonelikleri vardır. Abonelikler hakkında daha fazla bilgi ve ilgili konular için bkz. [abonelik](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy)İdaresi.

## <a name="segmentation"></a>Ayrılmasını

Her bir abonelik ve bölge başına birden çok sanal ağ oluşturabilirsiniz. Her bir sanal ağ içinde birden çok alt ağ oluşturabilirsiniz. Aşağıdaki önemli noktalar, kaç sanal ağın ve alt ağın gerekli olduğunu belirlemenize yardımcı olur:

### <a name="virtual-networks"></a>Sanal ağlar

Sanal ağ, Azure genel ağının sanal, yalıtılmış bir parçasıdır. Her sanal ağ aboneliğinize ayrılmıştır. Bir abonelikte bir sanal ağ veya birden çok sanal ağ oluşturma konusunda karar verirken dikkate almanız gerekenler:

- Trafiği ayrı sanal ağlara yalıtmak için herhangi bir kuruluş güvenlik gereksinimi var mı? Sanal ağları bağlamayı tercih edebilirsiniz. Sanal ağları bağladığınızda, sanal ağlar arasındaki trafik akışını denetlemek için güvenlik duvarı gibi bir ağ sanal gereci uygulayabilirsiniz. Daha fazla bilgi için bkz. [güvenlik](#security) ve [bağlantı](#connectivity).
- Sanal ağları ayrı [aboneliklerde](#subscriptions) veya [bölgelerde](#regions)yalıtmak için herhangi bir kuruluş gereksinimi var mı?
- Bir [ağ arabirimi](virtual-network-network-interface.md) , BIR VM 'nin diğer kaynaklarla iletişim kurmasını sağlar. Her ağ arabirimine atanmış bir veya daha fazla özel IP adresi vardır. Bir sanal ağda kaç ağ arabirimi ve [özel IP adresi](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) gerekir? Bir sanal ağ içinde sahip olabilirsiniz ağ arabirimlerinin ve özel IP adreslerinin sayısı için [sınırlar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) vardır.
- Sanal ağı başka bir sanal ağa veya şirket içi ağa bağlamak istiyor musunuz? Bazı sanal ağları birbirlerine veya şirket içi ağlara bağlamayı tercih edebilirsiniz, ancak diğerlerini kullanamazsınız. Daha fazla bilgi için bkz. [bağlantı](#connectivity). Başka bir sanal ağa veya şirket içi ağa bağlandığınız her sanal ağ, benzersiz bir adres alanına sahip olmalıdır. Her sanal ağın adres alanına atanan bir veya daha fazla ortak veya özel adres aralığı vardır. Bir adres aralığı, sınıfsız Internet etki alanı yönlendirme (CıDR) biçiminde belirtilir, örneğin 10.0.0.0/16. Sanal ağların [adres aralıkları](manage-virtual-network.md#add-or-remove-an-address-range) hakkında daha fazla bilgi edinin.
- Farklı sanal ağlardaki kaynaklar için herhangi bir kuruluş yönetim gereksinimleriniz var mı? Bu durumda, kuruluşunuzdaki bireylere [izin atamasını](#permissions) kolaylaştırmak veya farklı sanal ağlara farklı ilkeler atamak için kaynakları ayrı sanal ağa ayırabilirsiniz.
- Bazı Azure hizmet kaynaklarını bir sanal ağa dağıttığınızda, kendi sanal ağını oluşturamazlar. Bir Azure hizmetinin kendi sanal ağını oluşturup oluşturmadığını öğrenmek için, [bir sanal ağa dağıtılabilecek her bir Azure hizmeti](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)için bilgiler bölümüne bakın.

### <a name="subnets"></a>Alt ağlar

Bir sanal ağ, [sınırlara](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)kadar bir veya daha fazla alt ağa ayrılabilir. Bir abonelikte tek bir alt ağ veya birden çok sanal ağ oluşturma konusunda karar verirken dikkate almanız gerekenler:

- Her alt ağ, sanal ağın adres alanı içinde CıDR biçiminde belirtilen benzersiz bir adres aralığına sahip olmalıdır. Adres aralığı, sanal ağdaki diğer alt ağlarla çakışamaz.
- Bazı Azure hizmet kaynaklarını bir sanal ağa dağıtmayı planlıyorsanız, kendi alt ağını gerektirebilir veya oluşturabilir, bu nedenle bunlar için yeterli ayrılmamış alan olması gerekir. Bir Azure hizmetinin kendi alt ağını oluşturup oluşturmadığını öğrenmek için, [bir sanal ağa dağıtılabilecek her bir Azure hizmeti](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)için bilgiler bölümüne bakın. Örneğin, bir sanal ağı Azure VPN Gateway kullanarak şirket içi bir ağa bağladığınızda, sanal ağın ağ geçidi için ayrılmış bir alt ağı olmalıdır. [Ağ geçidi alt ağları](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)hakkında daha fazla bilgi edinin.
- Azure, varsayılan olarak bir sanal ağdaki tüm alt ağlar arasında ağ trafiğini yönlendirir. Alt ağlar arasında Azure yönlendirmeyi engellemek veya örneğin bir ağ sanal gereci aracılığıyla trafiği alt ağlar arasında yönlendirmek için Azure 'un varsayılan yönlendirmesini geçersiz kılabilirsiniz. Bir ağ sanal gereci (NVA) üzerinden aynı sanal ağ akışındaki kaynaklar arasında bu trafiğe ihtiyaç duyuyorsanız, kaynakları farklı alt ağlara dağıtın. [Güvenlik](#security)bölümünde daha fazla bilgi edinin.
- Bir Azure depolama hesabı veya Azure SQL veritabanı gibi Azure kaynaklarına erişimi, sanal ağ hizmeti uç noktası olan belirli alt ağlara sınırlayabilirsiniz. Ayrıca, internet 'ten kaynaklara erişimi reddedebilirsiniz. Birden çok alt ağ oluşturabilir ve bazı alt ağlar için bir hizmet uç noktası etkinleştirebilirsiniz, ancak diğerlerini kullanamazsınız. [Hizmet uç noktaları](virtual-network-service-endpoints-overview.md)ve bunları Için etkinleştirebileceğiniz Azure kaynakları hakkında daha fazla bilgi edinin.
- Sıfır veya bir ağ güvenlik grubunu bir sanal ağ içindeki her alt ağ ile ilişkilendirebilirsiniz. Aynı veya farklı bir ağ güvenlik grubunu her alt ağ ile ilişkilendirebilirsiniz. Her ağ güvenlik grubu, kaynak ve hedeflere giden ve giden trafiğe izin veren ya da reddeden kurallar içerir. [Ağ güvenlik grupları](#traffic-filtering)hakkında daha fazla bilgi edinin.

## <a name="security"></a>Güvenlik

Ağ güvenlik gruplarını ve ağ sanal gereçlerini kullanarak bir sanal ağdaki kaynaklardan gelen ve giden ağ trafiğini filtreleyebilirsiniz. Azure 'un alt ağlardan gelen trafiği nasıl yönlendirdiğini denetleyebilirsiniz. Ayrıca, kuruluşunuzdaki kimlerin sanal ağlardaki kaynaklarla çalışmasını sınırlayabilirsiniz.

### <a name="traffic-filtering"></a>Trafik filtreleme

- Bir ağ güvenlik grubu, ağ trafiğini filtreleyen bir NVA veya her ikisini kullanarak bir sanal ağdaki kaynaklar arasındaki ağ trafiğini filtreleyebilirsiniz. Ağ trafiğini filtrelemek için güvenlik duvarı gibi bir NVA dağıtmak için bkz. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Bir NVA kullanırken, trafiği alt ağlardan NVA 'ya yönlendirmek için özel yollar da oluşturursunuz. [Trafik yönlendirme](#traffic-routing)hakkında daha fazla bilgi edinin.
- Bir ağ güvenlik grubu, kaynaklara ya da kaynaklardan gelen trafiğe izin veren veya reddeden çeşitli varsayılan güvenlik kuralları içerir. Bir ağ güvenlik grubu bir ağ arabirimiyle, ağ arabiriminin içinde bulunduğu alt ağ veya her ikisi ile ilişkilendirilebilir. Güvenlik kurallarının yönetimini basitleştirmek için, ağ güvenlik grubunu alt ağ içindeki bireysel ağ arabirimleri yerine ayrı alt ağlarla ilişkilendirmeniz önerilir.
- Bir alt ağ içindeki farklı VM 'lere farklı güvenlik kuralları uygulanmışsa, VM 'deki ağ arabirimini bir veya daha fazla uygulama güvenlik grubu ile ilişkilendirebilirsiniz. Bir güvenlik kuralı, bir uygulama güvenlik grubunu kaynağında, hedefinde veya her ikisinde belirtebilir. Bu kural daha sonra yalnızca uygulama güvenlik grubunun üyesi olan ağ arabirimleri için geçerlidir. [Ağ güvenlik grupları](security-overview.md) ve [uygulama güvenlik grupları](security-overview.md#application-security-groups)hakkında daha fazla bilgi edinin.
- Azure, her ağ güvenlik grubu içinde çeşitli varsayılan güvenlik kuralları oluşturur. Bir varsayılan kural, tüm trafiğin bir sanal ağdaki tüm kaynaklar arasında akışa almasına izin verir. Bu davranışı geçersiz kılmak için ağ güvenlik grupları, bir NVA 'ya trafiği yönlendirmek için özel yönlendirme veya her ikisini de kullanın. Tüm Azure [varsayılan güvenlik kurallarını](security-overview.md#default-security-rules) öğrenmeniz ve ağ güvenlik grubu kurallarının bir kaynağa nasıl uygulandığını anlamanız önerilir.

Bir [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)kullanarak Azure ile internet arasında bir çevre ağı (DMZ olarak da bilinir) uygulamaya yönelik örnek tasarımlar görüntüleyebilirsiniz.

### <a name="traffic-routing"></a>Trafik yönlendirme

Azure, bir alt ağdan giden trafik için birkaç varsayılan yol oluşturur. Azure 'un varsayılan yönlendirmeyi, bir rota tablosu oluşturup bir alt ağla ilişkilendirerek geçersiz kılabilirsiniz. Azure 'un varsayılan yönlendirmeyi geçersiz kılan yaygın nedenler şunlardır:
- Alt ağlar arasındaki trafiğin bir NVA üzerinden akmasını istiyorsanız. [BIR NVA üzerinden trafiği zorlamak için yol tablolarını yapılandırma](tutorial-create-route-table-portal.md)hakkında daha fazla bilgi edinmek için.
- İnternet 'e bağlı tüm trafiği bir NVA veya şirket içinde, bir Azure VPN ağ geçidi aracılığıyla zorlamak istiyorsanız. Şirket içi internet trafiğini İnceleme ve günlüğe kaydetme için zorlamak, genellikle Zorlamalı tünel oluşturma olarak adlandırılır. [Zorlamalı tüneli](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)yapılandırma hakkında daha fazla bilgi edinin.

Özel yönlendirme uygulamanız gerekiyorsa, [Azure 'da yönlendirmeyi](virtual-networks-udr-overview.md)öğrenmeniz önerilir.

## <a name="connectivity"></a>Bağlantı

Sanal ağ eşlemesi veya şirket içi ağınızda bir Azure VPN ağ geçidi kullanarak sanal bir ağı diğer sanal ağlara bağlayabilirsiniz.

### <a name="peering"></a>Eşleme

Sanal [ağ eşlemesi](virtual-network-peering-overview.md)kullanılırken, sanal ağlar aynı veya desteklenen farklı Azure bölgelerinde olabilir. Sanal ağlar aynı veya farklı Azure aboneliklerinde (hatta farklı Azure Active Directory kiracılara ait abonelikler) olabilir. Eşleme oluşturmadan önce, tüm eşleme [gereksinimlerini ve kısıtlamalarını](virtual-network-manage-peering.md#requirements-and-constraints)öğrenmeniz önerilir. Aynı bölgedeki sanal ağlarda bulunan kaynaklar arasındaki bant genişliği, kaynakların aynı sanal ağ içinde olup olmadığı ile aynıdır.

### <a name="vpn-gateway"></a>VPN ağ geçidi

Bir sanal ağı, [siteden sıteye VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)kullanarak şirket içi ağınıza bağlamak veya Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ile adanmış bir bağlantı kullanmak için Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanabilirsiniz.

Hub [ve bağlı bileşen ağları](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)oluşturmak için eşleme ve VPN Gateway 'i birleştirebilirsiniz. Bu, bağlı olan sanal ağların bir hub sanal ağına bağlanmasını ve hub 'ın bir şirket içi ağa bağlanmasını sağlar.

### <a name="name-resolution"></a>Ad çözümlemesi

Bir sanal ağdaki kaynaklar, Azure 'un [YERLEŞIK DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)'sini kullanarak eşlenmiş bir sanal ağdaki kaynakların adlarını çözemez. Eşlenen bir sanal ağdaki adları çözümlemek için [kendı DNS sunucunuzu dağıtın](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)veya Azure DNS [özel etki alanlarını](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)kullanın. Bir sanal ağdaki ve şirket içi ağlardaki kaynaklar arasındaki adların çözümlenmesi, kendi DNS sunucunuzu dağıtmanıza da gerek duyar.

## <a name="permissions"></a>İzinler

Azure, kaynaklara [göre rol tabanlı erişim denetimi](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) kullanır. İzinler şu hiyerarşide bir [kapsama](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) atandı: yönetim grubu, abonelik, kaynak grubu ve tek kaynak. Hiyerarşi hakkında daha fazla bilgi edinmek için bkz. [kaynaklarınızı düzenleme](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Azure sanal ağları ve eşleme, ağ güvenlik grupları, hizmet uç noktaları ve yol tabloları gibi ilgili tüm özellikleri ile çalışmak için kuruluşunuzun üyelerini yerleşik [Sahibe](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [katkıda](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)buluna 'ya atayabilir veya [ Ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolleri ve ardından rolü uygun kapsama atayın. Bir sanal ağ özellikleri alt kümesi için belirli izinler atamak istiyorsanız, [özel bir rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturun ve [sanal ağlar](manage-virtual-network.md#permissions), [alt ağlar ve hizmet uç noktaları](virtual-network-manage-subnet.md#permissions), ağ arabirimleri için gereken belirli izinleri atayın [ ](virtual-network-network-interface.md#permissions), [eşleme](virtual-network-manage-peering.md#permissions), [Ağ ve uygulama güvenlik grupları](manage-network-security-group.md#permissions)ya da rol için [Rota tabloları](manage-route-table.md#permissions) .

## <a name="policy"></a>İlke

Azure Ilkesi, ilke tanımları oluşturmanıza, atamanıza ve yönetmenize olanak sağlar. İlke tanımları kaynaklarınızın üzerinde farklı kurallar uygular, bu nedenle kaynaklar kurumsal standartlarınızla ve hizmet düzeyi sözleşmelerle uyumlu kalır. Azure Ilkesi, kaynaklarınızın bir değerlendirmesini çalıştırır ve sahip olduğunuz ilke tanımlarıyla uyumlu olmayan kaynaklar için tarama yapar. Örneğin, yalnızca belirli bir kaynak grubunda veya bölgede sanal ağların oluşturulmasına izin veren bir ilke tanımlayabilir ve uygulayabilirsiniz. Başka bir ilke, her alt ağın kendisiyle ilişkilendirilmiş bir ağ güvenlik grubu olmasını gerektirebilir. Daha sonra, kaynaklar oluşturma ve güncelleştirme sırasında ilkeler değerlendirilir.

İlkeler şu hiyerarşiye uygulandı: yönetim grubu, abonelik ve kaynak grubu. [Azure ilkesi](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hakkında daha fazla bilgi edinin veya bazı sanal ağ [ilkesi şablonu](policy-samples.md) örneklerini dağıtın.

## <a name="next-steps"></a>Sonraki adımlar

Bir [sanal ağ](manage-virtual-network.md), [alt ağ ve hizmet uç noktası](virtual-network-manage-subnet.md), [ağ arabirimi](virtual-network-network-interface.md), [eşleme](virtual-network-manage-peering.md), [Ağ ve uygulama güvenlik grubu](manage-network-security-group.md)ya da [yol tablosu](manage-route-table.md)için tüm görevler, ayarlar ve seçenekler hakkında bilgi edinin.
