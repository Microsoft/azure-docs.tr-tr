---
title: "Azure Sanal Ağ eşlemesi | Microsoft Belgeleri"
description: "Azure&quot;daki sanal ağ eşlemesi hakkında bilgi edinin."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Sanal ağ eşleme
Sanal ağ eşlemesi, Azure omurga ağı aracılığıyla aynı bölgedeki iki sanal ağı birbirine bağlamanızı sağlar. Eşleme yapıldıktan sonra iki sanal ağ, bağlantı amacıyla tek bir sanal ağ gibi görünür. İki sanal ağ ayrı kaynaklar olarak yönetilmeye devam eder, ancak eşlenmiş sanal ağlardaki sanal makineler (VM) özel IP adresleri kullanarak birbirleriyle doğrudan iletişim kurabilir.

Eşlenen sanal ağlardaki sanal makineler arasındaki trafik, Azure altyapısı aracılığıyla, aynı sanal ağdaki sanal makineler arasında olduğu gibi yönlendirilir. VNet eşlemesini kullanmanın bazı avantajları şunlardır:

* Farklı sanal ağlardaki kaynaklar arasında düşük gecikme süreli, yüksek bant genişliği bağlantısı.
* VPN ağ geçitleri ve ağ sanal gereçleri gibi kaynakları, eşlenmiş VNet içinde geçiş noktaları olarak kullanabilme.
* Azure Resource Manager dağıtım modeliyle oluşturulan iki sanal ağı eşleyebilme veya Resource Manager ile oluşturulan bir sanal ağı klasik dağıtım modeliyle oluşturulan sanal ağ ile eşleyebilme özelliği. İki Azure dağıtım modeli arasındaki fark hakkında daha fazla bilgi almak için [Azure dağıtım modellerini anlama](../azure-resource-manager/resource-manager-deployment-model.md) makalesini okuyun.

VNet eşlemesi ile ilgili gereksinimler ve önemli noktalar:

* Eşlenmiş sanal ağlar aynı Azure bölgesinde bulunmalıdır.
* Eşlenmiş sanal ağların IP adresi alanları çakışmamalıdır.
* Sanal ağ eşlemesi iki sanal ağ arasında gerçekleşir ve eşlemeler arasında türetilmiş geçişli bir ilişki yoktur. Örneğin, SanalAğA ile SanalAğB eşlenir ve SanalAğB ile SanalAğC eşlenirse, SanalAğA ile SanalAğC *eşlenmez*.
* Eşlemenin her iki aboneliğin de ayrıcalıklı bir kullanıcısı tarafından yetkilendirilmiş olması ve aboneliklerin aynı Active Directory kiracısı ile ilişkilendirilmesi şartıyla iki farklı abonelikte mevcut olan sanal ağları eşleyebilirsiniz.
* Her iki sanal ağ da Resource Manager dağıtım modeliyle oluşturulursa veya biri Resource Manager dağıtım modeliyle, diğeri klasik dağıtım modeliyle oluşturulursa sanal ağlar eşlenebilir. Ancak, klasik dağıtım modeliyle oluşturulan iki sanal ağ birbiriyle eşlenemez. Farklı dağıtım modelleriyle oluşturulan sanal ağlar eşlenirken, sanal ağların her ikisi de *aynı* abonelikte olmalıdır. *Farklı* aboneliklerde mevcut olan farklı dağıtım modelleriyle oluşturulmuş sanal ağları eşleyebilme özelliği **önizleme** sürümündedir. Daha fazla bilgi için [Powershell kullanarak sanal ağ eşlemesi oluşturma](virtual-networks-create-vnetpeering-arm-ps.md) makalesini okuyun.
* Eşlenmiş sanal ağlardaki sanal makineler arasında kurulan iletişim için ek bant genişliği kısıtlamaları olmasa da, sanal makine boyutuna bağlı olarak hala geçerli olan bir ağ bant genişliği üst sınırı vardır. Farklı sanal makine boyutlarına yönelik ağ bant genişliği üst sınırları hakkında daha fazla bilgi almak için [Windows](../virtual-machines/windows/sizes.md) veya [Linux](../virtual-machines/linux/sizes.md) VM boyutları makalelerini okuyun.

![Temel VNet eşlemesi](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Bağlantı
İki sanal ağ eşlendikten sonra, sanal ağ içindeki sanal makineler veya Cloud Services rolleri eşlenmiş sanal ağa bağlı diğer kaynaklara doğrudan bağlanabilir. İki sanal ağ tam IP düzeyinde bağlantıya sahip olur.

Eşlenmiş sanal ağlardaki iki sanal makine arasında gidiş dönüş ağ gecikmesi, tek bir sanal ağdaki gidiş dönüş ağ gecikme süresi ile aynıdır. Ağ aktarım hızı, büyüklüğüyle orantılı olarak VM için izin verilen bant genişliğine bağlıdır. Eşleme içindeki bant genişliği ile ilgili herhangi bir ek kısıtlama yoktur.

Eşlenmiş sanal ağlarda bulunan sanal makineler arasındaki trafik bir ağ geçidi üzerinden değil, doğrudan Azure arka uç altyapısı aracılığıyla yönlendirilir.

Bir sanal ağa bağlı sanal makineler, eşlenmiş sanal ağdaki iç yük dengeli (ILB) uç noktalara erişebilir. İstenirse diğer sanal ağ veya alt ağlara erişimi engellemek için herhangi bir sanal ağa Ağ güvenlik grupları (NSG) uygulanabilir.

Eşlemeyi yapılandırırken, sanal ağlar arasındaki NSG kurallarını açıp kapatabilirsiniz. Eşlenmiş sanal ağlar arasında tam bağlantıyı açarsanız (varsayılan seçenek), belirli bir erişimi engellemek ya da reddetmek için NSG’leri belirli alt ağlara veya sanal makinelere uygulayabilirsiniz. NSG’ler hakkında daha fazla bilgi almak için [Ağ güvenlik grupları](virtual-networks-nsg.md) makalesini okuyun.

Azure tarafından sanal makineler için sağlanan iç DNS ad çözünürlüğü, eşlenmiş sanal ağlarda çalışmaz. Sanal makinelerin yalnızca yerel sanal ağ üzerinde çözümlenebilen iç DNS adları vardır. Ancak, eşlenmiş sanal ağlara bağlı sanal makineleri bir sanal ağın DNS sunucuları olarak yapılandırabilirsiniz. Daha fazla bilgi için [Kendi DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) makalesini okuyun.

## <a name="service-chaining"></a>Hizmet zinciri
Eşlenmiş sanal ağlardaki sanal makineleri işaret eden kullanıcı tanımlı yolları (UDR), bu makalenin sonraki bölümlerinde yer alan diyagramda gösterildiği gibi "sonraki atlama" IP adresi olarak yapılandırabilirsiniz. Bunun yapılması, bir sanal ağdan eşlenmiş sanal ağda çalışan sanal bir cihaza UDR’ler aracılığıyla trafik yönlendirmenize olanak tanıyan hizmet zincirlemesini sağlar.

Ayrıca, hub ve bağlı bileşen türündeki ortamları da verimli bir şekilde oluşturabilirsiniz. Bu ortamlarda hub, ağ sanal gereci gibi altyapı bileşenlerini barındırabilir. Böylece tüm bağlı sanal ağlar, bunun gibi altyapı bileşenlerinin yanı sıra hub sanal ağında çalışan gereçlere giden trafiğin bir alt ağıyla da eşlenebilir. Kısacası, sanal ağ eşlemesi sayesinde UDR’deki sonraki atlama IP adresi, eşlenen sanal ağ üzerindeki bir sanal makinenin IP adresi olabilir. UDR’ler hakkında ek bilgi için [kullanıcı tanımlı yollar](virtual-networks-udr-overview.md) makalesini okuyun.

## <a name="gateways-and-on-premises-connectivity"></a>Ağ geçitleri ve şirket içi bağlantı
Başka bir sanal ağ ile eşlenip eşlenmediğine bakılmaksızın her sanal ağ kendi ağ geçidine sahip olabilir ve bu sanal ağ geçidini bir şirket içi ağına bağlanmak için kullanılabilir. Ayrıca kullanıcılar, sanal ağlar eşlenmiş olsa bile ağ geçitlerini kullanarak [sanal ağlar arası bağlantıları](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) yapılandırabilir.

Sanallar arası bağlantı için her iki seçenek de yapılandırıldığında sanal ağlar arasındaki trafik, eşleme yapılandırması (Azure omurgası) aracılığıyla akar.

Sanal ağlar eşlendiğinde kullanıcılar eşlenmiş sanal ağdaki ağ geçidini şirket içi ağına bir geçiş noktası olarak kullanılacak şekilde de yapılandırabilir. Bu durumda, uzak ağ geçidi kullanan sanal ağın kendi ağ geçidi olamaz. Bir sanal ağda yalnızca bir ağ geçidi olabilir. Ağ geçidi aşağıdaki resimde gösterildiği gibi yerel veya uzak bir ağ geçidi (eşlenen sanal ağ üzerinde) olabilir:

![VNet eşleme geçişi](./media/virtual-networks-peering-overview/figure02.png)

Farklı dağıtım modelleriyle oluşturulmuş sanal ağlar arasındaki eşleme ilişkisinde ağ geçidi geçişi desteklenmez. Bir ağ geçidi geçişinin çalışması için eşleme ilişkisindeki her iki sanal ağ da Resource Manager ile oluşturulmuş olmalıdır.

Tek bir Azure ExpressRoute bağlantısını kullanan sanal ağlar eşlendiğinde, bu iki sanal ağ arasındaki trafik, eşleme ilişkisi (Azure omurga ağı) üzerinden akış gerçekleştirir. Şirket içi devreye bağlanmak için her bir sanal ağ üzerindeki yerel ağ geçitlerini kullanmaya devam edebilirsiniz. Alternatif olarak, paylaşılan bir ağ geçidini kullanıp şirket içi bağlantı için bir geçiş yapılandırabilirsiniz.

## <a name="provisioning"></a>Sağlama
VNet eşlemesi ayrıcalıklı bir işlemdir. VirtualNetworks ad alanı altında yer alan ayrı bir işlevdir. Bir kullanıcıya eşlemeyi yetkilendirmesi için belirli haklar verilebilir. Sanal ağa yönelik okuma/yazma erişimi olan bir kullanıcı bu haklara otomatik olarak sahip olur.

Eşleme özelliğinin yönetici ya da ayrıcalıklı kullanıcısı olan bir kullanıcı, başka bir VNet üzerinde eşleme işlemi başlatabilir. Diğer tarafta eşleme için eşleşen bir istek varsa ve diğer gereksinimler karşılanırsa eşleme gerçekleştirilir.

İki sanal ağ arasında sanal ağ eşlemesi oluşturma hakkında bilgi almak için bu makalenin [Sonraki adımlar](#next-steps) bölümüne bakın.

## <a name="limits"></a>Sınırlar
Tek bir sanal ağ için izin verilen eşleme sayısı sınırlıdır. Daha fazla bilgi edinmek için bkz. [Azure ağ sınırları](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Fiyatlandırma
Sanal ağ eşlemesi kullanan giriş ve çıkış trafiği için nominal bir ücret uygulanır. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdakileri kullanarak sanal ağ eşlemesi oluşturma hakkında bilgi alın:

* [Azure portalı](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Azure Resource Manager şablonu](virtual-networks-create-vnetpeering-arm-template-click.md)

