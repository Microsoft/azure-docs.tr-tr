---
title: Azure sanal WAN küresel aktarım ağı mimarisi | Microsoft Docs
description: Sanal WAN için küresel aktarım ağı mimarisi hakkında bilgi edinin
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 9b736e897278c4656df83c30388efc812ac6193d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607361"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Küresel aktarım ağı mimarisi ve sanal WAN

Modern kuruluşlar, bulutta ve Şirket içindeki hiper dağıtılan uygulamalar, veriler ve kullanıcılar arasında ubititous bağlantısı gerektirir. Küresel aktarım ağı mimarisi, kuruluşlar tarafından bulut odaklı modern, küresel kurumsal BT parmak izini birleştirmek, bağlanmak ve denetlemek için benimsenmekte.

Küresel aktarım ağı mimarisi, bulut barındırılan Ağı ' hub ' 'ın farklı türlerde dağıtılan uç noktalar arasında geçişli bağlantı sağladığından, klasik bir hub ve bağlı bileşen bağlantı modelini temel alır.

Bu modelde, bir bağlı bileşen şu olabilir:
* Sanal ağ (VNet)
* Fiziksel şube sitesi
* Uzak Kullanıcı
* Internet

![Hub ve bağlı bileşen](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Şekil 1: genel geçiş merkezi-ve-bağlı ağ**

Şekil 1 ' de, coğrafi olarak dağıtılmış kullanıcıların, fiziksel sitelerin ve VNET 'lerin bulutta barındırılan bir ağ hub 'ı aracılığıyla birbirine bağlanmış olduğu küresel transit ağının mantıksal görünümü gösterilmektedir. Bu mimari, ağ uç noktaları arasında mantıksal bir atlama aktarım bağlantısı sunar.

## <a name="globalnetworktransit"></a>Sanal WAN ile küresel transit ağı

Azure sanal WAN, Microsoft tarafından yönetilen bir bulut ağ hizmetidir. Bu hizmetin tarafından oluşturulan tüm ağ bileşenleri Microsoft tarafından barındırılır ve yönetilir. Sanal WAN hakkında daha fazla bilgi için bkz. [sanal WAN genel bakış](virtual-wan-about.md) makalesi.

Azure sanal WAN, sanal ağlar, şube siteleri, SaaS ve PaaS uygulamaları ve kullanıcılar içindeki bulut iş yüklerinin küresel olarak dağıtılmış kümesi arasında her türlü bağlantıyı etkinleştirerek küresel bir geçiş ağı mimarisine izin verir.

![Azure Sanal WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Şekil 2: genel aktarım ağı ve sanal WAN**

Azure sanal WAN mimarisinde, sanal WAN hub 'ları, Dallarınızı, VNET 'leri ve uzak kullanıcılarınızı bağlamayı seçebileceğiniz Azure bölgelerinde sağlanır. Fiziksel şube siteleri, Premium ExpressRoute veya siteden siteye-VPN 'Ler tarafından hub 'a bağlanır, sanal ağlar VNet bağlantıları tarafından hub 'a bağlanır ve uzak kullanıcılar kullanıcı VPN (Noktadan siteye VPN 'Ler) kullanarak hub 'a doğrudan bağlanabilir. Sanal WAN Ayrıca, bir bölgedeki VNet 'in farklı bir bölgedeki sanal WAN hub 'ına bağlı olduğu bölgeler arası VNet bağlantısını destekler.

En fazla sayıda bağlı bileşen (dal, VNet, Kullanıcı) ve daha sonra diğer bölgelerdeki alt dalları hub 'a bağlayarak bir sanal WAN 'ı oluşturabilirsiniz. Bu, kurumsal bir parmak izi genellikle birkaç uzak bağlı bileşen içeren bir bölgede olduğunda iyi bir seçenektir.  
  
## <a name="hubtohub"></a>Hub 'dan hub 'a bağlantı

Kurumsal bulut alanı, birden fazla bulut bölgesine yayılabilir ve buluta, fiziksel siteleri ve kullanıcılarına en yakın bölgeden erişmek için idealdir (gecikme süresi). Genel aktarım ağı mimarisinin temel prensipinden biri, tüm bulut ve şirket içi ağ uç noktaları arasında bölgeler arası bağlantıyı etkinleştirmektir. Bu, bir bölgedeki buluta bağlı bir daldan gelen trafiğin, [Azure genel ağı](https://azure.microsoft.com/global-infrastructure/global-network/)tarafından etkinleştirilen hub-hub bağlantısını kullanarak farklı bir bölgedeki başka bir dala veya VNET 'e ulaşabileceği anlamına gelir.

![çapraz bölge](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Şekil 3: sanal WAN çapraz bölge bağlantısı**

Tek bir sanal WAN 'da birden çok hub etkinleştirildiğinde, hub 'lar hub-hub bağlantıları aracılığıyla otomatik olarak birbirlerine bağlanır ve bu sayede birden çok bölgede dağıtılan dallar ve sanal ağlar arasında genel bağlantı sağlar. 

Ayrıca, aynı sanal WAN 'ın tüm parçaları olan hub 'lar farklı bölgesel erişim ve güvenlik ilkeleriyle ilişkilendirilebilir. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [güvenlik ve ilke denetimi](#security) bölümüne bakın.

## <a name="anytoany"></a>Her türlü bağlantı

Küresel aktarım ağı mimarisi, sanal WAN hub 'ları aracılığıyla herhangi bir bağlantıyı sağlar. Bu mimari, derleme ve bakım açısından daha karmaşık olan bağlı bağlantılar arasında tam ağ veya kısmi ağ bağlantısı gereksinimini ortadan kaldırır veya azaltır. Ayrıca, hub ve bağlı bileşen ve ağ ağları arasındaki yönlendirme denetimi daha kolay yapılandırılır ve korunur.

Herhangi bir bağlantı (küresel mimari bağlamında), küresel olarak dağıtılmış kullanıcılar, dallar, veri merkezleri, VNET 'ler ve uygulamaların "iletim" hub 'ları aracılığıyla birbirlerine bağlanmasına olanak sağlar. Azure sanal WAN, küresel aktarım sistemi olarak davranır.

![herhangi birine](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Şekil 4: sanal WAN trafiği yolları**

Azure sanal WAN, aşağıdaki genel transit bağlantı yollarını destekler. Parantez içindeki harfler Şekil 4 ' e eşlenir.

* Daldan VNet (a)
* Dala dalı (b)
  * ExpressRoute Global Reach ve sanal WAN
* Uzak Kullanıcı-VNet (c)
* Uzak kullanıcıdan dala (d)
* VNet-VNet (e)
* Daldan hub-hub-dalı (f)
* Daldan hub 'dan VNet 'e (g)
* VNet-hub-hub-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Daldan VNet (a) ve daldan VNet arası çapraz bölge (g)

Daldan VNet, Azure sanal WAN tarafından desteklenen birincil yoldur. Bu yol, dalları Azure sanal ağları 'nda dağıtılan Azure ıAAS kurumsal iş yüklerine bağlamanıza olanak tanır. Dallar, ExpressRoute veya siteden siteye VPN aracılığıyla sanal WAN 'a bağlanabilir. Trafik, sanal WAN hub 'larına VNet bağlantıları aracılığıyla bağlı sanal ağlara geçiş yapılır. Sanal WAN, Şube sitesine otomatik olarak ağ geçidi geçişi sağladığından, sanal WAN için açık [ağ geçidi geçişi](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) gerekli değildir. SD-WAN CPE 'ı sanal WAN 'a bağlama hakkında [sanal WAN Iş ortakları](virtual-wan-configure-automation-providers.md) makalesine bakın.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach ve sanal WAN

ExpressRoute, şirket içi ağlarınızı Microsoft Bulut bağlamak için özel ve dayanıklı bir yoldur. Sanal WAN, Express Route bağlantı hattı bağlantılarını destekler. Bir dal sitesini Express Route ile sanal WAN 'a bağlamak 1) Premium devre 2) devresinin Global Reach etkinleştirilmiş bir konumda olması gerekir.

ExpressRoute Global Reach ExpressRoute için bir eklenti özelliğidir. Global Reach ile, şirket içi ağlarınızla özel bir ağ oluşturmak için ExpressRoute bağlantı devresine bir araya getirebilirsiniz. ExpressRoute kullanarak Azure sanal WAN 'a bağlı dallar, ExpressRoute Global Reach birbirleriyle iletişim kurmasını gerektirir.

Bu modelde, ExpressRoute kullanılarak sanal WAN hub 'ına bağlanan her dal, sanal ağlar arası yolu kullanarak VNet 'lere bağlanabilir. ExpressRoute Global Reach, Azure WAN üzerinde daha iyi bir yol sağladığından, Dalla olan trafik hub 'ını iletimez.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Daldan dala (b) ve dalı arası çapraz bölge (f)

Dallar, ExpressRoute devreleri ve/veya siteden siteye VPN bağlantıları kullanarak bir Azure sanal WAN hub 'ına bağlanabilir. Dalları dala en yakın bölgede bulunan sanal WAN hub 'ına bağlayabilirsiniz.

Bu seçenek, kuruluşların şubelerle bağlantı kurmak için Azure omurgasına yararlanmasını sağlar. Ancak, bu özellik kullanılabilir olsa bile, dalları Azure sanal WAN üzerinden bağlama avantajlarına ve özel bir WAN kullanmaya yönelik avantajlardan yararlanabilirsiniz.  

### <a name="remote-user-to-vnet-c"></a>Uzak Kullanıcı-VNet (c)

Uzak bir Kullanıcı istemcisinden bir sanal WAN 'a Noktadan siteye bağlantı kullanarak Azure 'a doğrudan, güvenli uzaktan erişim sağlayabilirsiniz. Kurumsal uzak kullanıcıların artık bir kurumsal VPN kullanarak buluta daha ince bir şekilde PIN 'i olması gerekmez.

### <a name="remote-user-to-branch-d"></a>Uzak kullanıcıdan dala (d)

Uzak kullanıcıdan dal yolu, Azure 'a Noktadan siteye bağlantı kullanan uzak kullanıcıların, bulut üzerinden geçiş yaparak şirket içi iş yüklerine ve uygulamalarına erişmesini sağlar. Bu yol, uzak kullanıcılara hem Azure 'da hem de şirket içinde dağıtılan iş yüklerine erişme esnekliği sağlar. Kuruluşlar, Azure sanal WAN 'da Merkezi bulut tabanlı güvenli uzaktan erişim hizmetini etkinleştirebilir.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-VNet aktarım (e) ve VNet-VNet arası çapraz bölge (h)

Sanal ağdan sanal ağa aktarma, sanal ağların birden çok sanal ağa uygulanmış çok katmanlı uygulamalar bağlantısı sağlamak için birbirlerine bağlanmasını sağlar. İsteğe bağlı olarak, sanal ağları VNet eşlemesi aracılığıyla birbirlerine bağlayabilirsiniz ve bu, VWAN hub aracılığıyla geçiş yapılması gereken bazı senaryolar için uygun olabilir.

## <a name="security"></a>Güvenlik ve ilke denetimi

Azure sanal WAN hub 'ları karma ağ genelinde tüm ağ uç noktalarını birbirine iletiyor ve tüm aktarım ağı trafiğini görebilir. Sanal WAN hub 'ları, bulut tabanlı güvenlik, erişim ve ilke denetimini etkinleştirmek için VWAN hub 'larına Azure Güvenlik Duvarı dağıtarak güvenli sanal hub 'lara dönüştürülebilir. Azure Güvenlik duvarlarını sanal WAN hub 'larda düzenleme, Azure Güvenlik Duvarı Yöneticisi tarafından gerçekleştirilebilir.

[Azure Güvenlik Duvarı Yöneticisi](https://go.microsoft.com/fwlink/?linkid=2107683) , küresel aktarım ağları için güvenliği yönetme ve ölçeklendirme olanakları sağlar. Azure Güvenlik Duvarı Yöneticisi, Azure Güvenlik Duvarı ile birlikte yönlendirme, genel ilke yönetimi ve gelişmiş Internet güvenliği hizmetlerini üçüncü taraf aracılığıyla merkezi olarak yönetebilme olanağı sağlar.

![Azure Güvenlik Duvarı ile güvenli sanal hub](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Şekil 5: Azure Güvenlik Duvarı ile güvenli sanal hub**

Sanal WAN ile Azure Güvenlik Duvarı, aşağıdaki genel güvenli geçiş bağlantı yollarını destekler. Parantez içindeki harfler Şekil 5 ' e eşlenir.

* VNet-VNet güvenli aktarım (e)
* VNet-Internet veya üçüncü taraf güvenlik hizmeti (ı)
* Daldan Internet veya üçüncü taraf güvenlik hizmeti (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 'ten VNet 'e güvenli geçiş (e)

VNet 'ten VNet 'e güvenli geçiş, sanal ağ hub 'ındaki Azure Güvenlik Duvarı aracılığıyla sanal ağların birbirlerine bağlanmasına olanak sağlar.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-Internet veya üçüncü taraf güvenlik hizmeti (ı)

VNet 'Ten Internet 'e veya üçüncü taraf güvenli geçiş, sanal ağ hub 'ındaki Azure Güvenlik Duvarı üzerinden İnternet 'e veya desteklenen bir üçüncü taraf güvenlik hizmetine bağlanmasını sağlar.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Daldan Internet veya üçüncü taraf güvenlik hizmeti (j)
Daldan Internet veya üçüncü taraf güvenli geçiş, dalların internet 'e veya desteklenen bir üçüncü taraf güvenlik hizmetine sanal WAN hub 'ındaki Azure Güvenlik Duvarı üzerinden bağlanmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN kullanarak bağlantı oluşturun ve Azure Güvenlik duvarını VWAN hub 'ları ile dağıtın.

* [Sanal WAN kullanarak siteden siteye bağlantılar](virtual-wan-site-to-site-portal.md)
* [Sanal WAN kullanarak ExpressRoute bağlantıları](virtual-wan-expressroute-portal.md)
* [VWAN 'da Azure ILT dağıtmak için Azure Güvenlik Duvarı Yöneticisi](https://go.microsoft.com/fwlink/?linkid=2107683)
