---
title: Azure Sanal WAN’ye genel bakış | Microsoft Docs
description: Sanal WAN otomatik ölçeklenebilir dalı-dal bağlantısı, kullanılabilir bölgeler ve iş ortakları hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ab54192480a1c36a27c175254d6d4d275b67c8b7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835335"
---
# <a name="what-is-azure-virtual-wan"></a>Azure Sanal WAN nedir?

Azure sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getiren bir ağ hizmetidir. Bu işlevler, dal bağlantısını (SD-WAN veya VPN CPE gibi sanal WAN Iş ortağı cihazlarından bağlantı Otomasyonu aracılığıyla) içerir. siteden siteye VPN bağlantısı, uzak kullanıcı VPN (Noktadan siteye) bağlantısı, özel (ExpressRoute) bağlantısı, bulut içi bağlantı (sanal ağlar için geçişli bağlantı), VPN ExpressRoute arası bağlantı, yönlendirme, Azure Güvenlik Duvarı ve özel bağlantı için şifreleme. Sanal WAN kullanmaya başlamak için bu kullanım örneklerinin tümünün olması gerekmez. Yalnızca tek bir kullanım durumu ile çalışmaya başlayabilir ve ardından ağınızı geliştikçe ayarlayabilirsiniz.

Sanal WAN mimarisi, dallar (VPN/SD-WAN cihazları), kullanıcılar (Azure VPN/OpenVPN/Ikev2 istemcileri), ExpressRoute devreleri ve sanal ağlar için yerleşik ölçek ve performansa sahip bir hub ve bağlı olan mimaridir. Bulut barındırılan Ağı ' hub ' 'ın, farklı türlerde (' tekerlek) dağıtılabilecek uç noktalar arasında geçişli bağlantı sağladığından, [genel aktarım ağı mimarisine](virtual-wan-global-transit-network-architecture.md)izin verebilir.

Azure bölgeleri, bağlanmayı seçebileceğiniz hub olarak görev yapar. Tüm Hub 'lar standart bir sanal WAN 'da tam ağa bağlanır ve kullanıcının herhangi bir (bağlı olan) bağlantı için Microsoft omurgasını kullanmasını kolaylaştırır. SD-WAN/VPN cihazlarıyla bağlı bileşen bağlantısı için, kullanıcılar Azure sanal WAN 'da el ile ayarlayabilir veya sanal WAN CPE (SD-WAN/VPN) iş ortağı çözümünü kullanarak Azure bağlantısı kurabilirsiniz. Bağlantı otomasyonunu destekleyen iş ortakları listeliyoruz (cihaz bilgilerini Azure 'a dışarı aktarma, Azure yapılandırmasını indirme ve bağlantı kurma özelliği) ile Azure sanal WAN. Daha fazla bilgi için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi.

![Sanal WAN diyagramı](./media/virtual-wan-about/virtualwan1.png)

Bu makalede, Azure sanal WAN 'da ağ bağlantısına hızlı bir bakış sunulmaktadır. Sanal WAN şu avantajları sunar:

* **Hub ve bağlı bileşen 'de tümleşik bağlantı çözümleri:** Siteden siteye yapılandırmayı ve şirket içi siteler ile bir Azure hub 'ı arasındaki bağlantıyı otomatikleştirin.
* **Otomatik uç kurulumu ve yapılandırması:** Sanal ağlarınızı ve iş yüklerinizi Azure hub'ına rahatça bağlayın.
* **Sezgisel sorun giderme:** Azure 'da uçtan uca akışı görebilir ve sonra gerekli eylemleri gerçekleştirmek için bu bilgileri kullanabilirsiniz.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Temel ve standart sanal WAN 'Lar

İki tür sanal WAN vardır: temel ve standart. Aşağıdaki tabloda her tür için kullanılabilir yapılandırma gösterilmektedir.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Bir sanal WAN yükseltme adımları için bkz. [bir sanal WAN 'ı temel 'Ten standart sürümüne yükseltme](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Mimari

Sanal WAN mimarisi ve sanal WAN 'a geçiş hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Sanal WAN mimarisi](migrate-from-hub-spoke-topology.md)
* [Genel geçiş ağ mimarisi](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Sanal WAN kaynakları

Uçtan uca sanal WAN'yi yapılandırmak için şu kaynakları oluşturursunuz:

* **virtualWAN:** virtualWAN kaynağı Azure ağınızdaki sanal bir katmanı temsil eder ve birden fazla kaynağı içeren bir koleksiyondur. Sanal WAN’de bulunmasını istediğiniz tüm sanal hub'larınızın bağlantılarını içerir. Sanal WAN kaynakları birbirinden yalıtılmıştır ve ortak bir hub içeremezler. Sanal WAN genelindeki sanal hub 'lar birbirleriyle iletişim kurmaz.

* **Hub:** Sanal hub Microsoft tarafından yönetilen bir sanal ağdır. Hub, bağlantıyı etkinleştirmek için çeşitli hizmet uç noktaları içerir. Şirket içi ağınızdan (vpnsite), sanal hub 'ın içindeki bir VPN Gateway bağlanabilir, ExpressRoute devrelerini bir sanal hub 'a bağlayabilir veya mobil kullanıcıları sanal hub 'daki Noktadan siteye ağ geçidine bağlayabilirsiniz. Hub, bir bölgedeki ağınızın merkezidir. Her Azure bölgesinde tek bir hub bulunabilir.

  Hub ağ geçidi, ExpressRoute ve VPN Gateway için kullandığınız sanal ağ geçidiyle aynı değildir. Örneğin, sanal WAN kullanırken, şirket içi sitenizden doğrudan VNet 'iniz için siteden siteye bağlantı oluşturmazsınız. Bunun yerine, hub 'a siteden siteye bağlantı oluşturursunuz. Trafik her zaman hub ağ geçidi üzerinden gider. Başka bir deyişle, Sanal Ağlarınızın kendi sanal ağ geçitlerine gerek kalmaz. Sanal WAN, Sanal Ağlarınızın sanal hub ve sanal hub ağ geçidi aracılığıyla ölçeklemeden kolayca yararlanmasına olanak tanır.

* **Hub sanal ağ bağlantısı:** Hub'ı sorunsuzca sanal ağınıza bağlamak için bir hub sanal ağ bağlantı kaynağı kullanılır.

* **Hub-hub bağlantısı:** Hub 'ların hepsi sanal bir WAN 'da birbirlerine bağlanır. Bu, bir yerel hub 'a bağlı bir dalın, kullanıcının veya VNet 'in bağlı hub 'ların tam ağ mimarisini kullanarak başka bir dalla veya VNet ile iletişim kurabildiğini gösterir. Ayrıca, hub 'dan hub 'a bağlı çerçeveyi kullanarak sanal hub 'dan ve hub 'daki VNET 'lerde VNET 'ler arasında VNET 'ler de bağlayabilirsiniz.

* **Hub rota tablosu:** Bir sanal hub rotası oluşturarak rotayı sanal hub rota tablosuna uygulayabilirsiniz. Sanal hub rota tablosuna birden fazla rota uygulayabilirsiniz.

**Ek sanal WAN kaynakları**

* **Site:** Bu kaynak yalnızca siteden siteye bağlantılar için kullanılır. Site kaynağı **vpnsite**. Şirket içi VPN cihazınızı ve ayarlarını temsil eder. Sanal WAN iş ortağıyla çalıştığınızda, bu bilgileri otomatik olarak Azure’a aktarmak için yerleşik bir çözümünüz olur.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Bağlantı türleri

Sanal WAN, aşağıdaki bağlantı türlerine izin verir: siteden siteye VPN, kullanıcı VPN (Noktadan siteye) ve ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Siteden Siteye VPN bağlantıları

Azure 'daki kaynaklarınıza site IPSec/ıKE (Ikev2) bağlantısı üzerinden kaynaklarınıza bağlanabilirsiniz. Daha fazla bilgi için bkz. [sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md). 

Bu tür bir bağlantı, bir VPN cihazı veya bir sanal WAN Iş ortağı cihazı gerektirir. Sanal WAN iş ortakları, cihaz bilgilerini Azure 'a dışarı aktarma, Azure yapılandırmasını indirme ve Azure sanal WAN hub 'ına bağlantı kurma özelliği olan bağlantı için Otomasyon sağlar. Kullanılabilir iş ortaklarının ve konumların bir listesi için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi. VPN/SD-WAN cihaz sağlayıcınız belirtilen bağlantıda listelenmiyorsa, bağlantıyı ayarlamak için [sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md) adımını yalnızca adım adım yönergeler ' i kullanabilirsiniz.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Kullanıcı VPN (Noktadan siteye) bağlantıları

Azure 'daki kaynaklarınıza bir IPSec/ıKE (Ikev2) veya OpenVPN bağlantısı üzerinden bağlanabilirsiniz. Bu tür bir bağlantı, istemci bilgisayarda bir VPN istemcisinin yapılandırılmasını gerektirir. Daha fazla bilgi için bkz. [Noktadan siteye bağlantı oluşturma](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute bağlantıları
ExpressRoute, şirket içi ağı özel bir bağlantı üzerinden Azure 'a bağlamanıza olanak tanır. Bağlantıyı oluşturmak için bkz. [sanal WAN kullanarak ExpressRoute bağlantısı oluşturma](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Hub-VNet bağlantıları

Bir Azure sanal ağını bir sanal hub 'a bağlayabilirsiniz. Daha fazla bilgi için bkz. [sanal ağı bir hub 'A bağlama](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Transit bağlantısı

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>VNET 'ler arasında geçiş bağlantısı

Sanal WAN, VNET 'ler arasında geçiş bağlantısına izin verir. VNET 'ler bir sanal ağ bağlantısı aracılığıyla sanal bir hub 'a bağlanır. **Standart sanal WAN** 'daki VNET 'ler arasındaki aktarım bağlantısı, her sanal hub 'da bir yönlendirici olması nedeniyle etkinleştirilir. Bu yönlendirici, sanal hub ilk oluşturulduğunda oluşturulur.

Yönlendiricide dört yönlendirme durumu olabilir: sağlanan, sağlama, başarısız veya yok. **Yönlendirme durumu** Azure Portal sanal hub sayfasına gidilerek bulunur.

* **Hiçbiri** durum, sanal hub 'ın yönlendirici sağlamadığını gösterir. Bu durum, sanal WAN *temel* tür ise veya sanal hub 'ın kullanılabilir hale getirilmesinden önce dağıtılması durumunda gerçekleşebilir.
* **Hatalı** durum, örnekleme sırasında hata olduğunu gösterir. Yönlendiriciyi başlatmak veya sıfırlamak için Azure portal sanal hub genel bakış sayfasına giderek, **yönlendirici sıfırlama** seçeneğini bulabilirsiniz.

Her sanal hub yönlendiricisi, 50 Gbps 'e kadar toplam üretimi destekler. Sanal ağ bağlantıları arasındaki bağlantı, tek bir sanal hub 'a bağlı tüm VNET 'lerde toplam 2000 VM iş yükünün olduğunu varsayar.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>VPN ve ExpressRoute arasında geçiş bağlantısı

Sanal WAN, VPN ve ExpressRoute arasında geçiş bağlantısına izin verir. Bu, VPN bağlantılı sitelerin veya uzak kullanıcıların ExpressRoute ile bağlantılı sitelerle iletişim kurabildiğini gösterir. Ayrıca, **dalı arası bayrağın** ETKIN ve BGP 'nin VPN ve ExpressRoute bağlantılarında desteklendiği bir kapalı varsayım vardır. Bu bayrak, Azure portal Azure sanal WAN ayarları ' nda bulunabilir. Tüm rota yönetimi, sanal ağlar arasında aktarım bağlantısı sağlayan sanal hub yönlendiricisi tarafından sağlanır.

### <a name="custom-routing"></a><a name="routing"></a>Özel yönlendirme

Sanal WAN, gelişmiş yönlendirme iyileştirmeleri sağlar. Özel yol tabloları ayarlama, yönlendirme ilişkilendirmesi ve yayma ile sanal ağ yönlendirmeyi iyileştirme, etiketleri mantıksal olarak gruplama ve çok sayıda ağ sanal gereci (NVA) veya paylaşılan hizmetleri yönlendirme senaryolarını basitleştirme.

### <a name="global-vnet-peering"></a><a name="global"></a>Küresel VNet eşlemesi

Küresel VNet eşlemesi, farklı bölgelerde iki sanal ağı birbirine bağlamak için bir mekanizma sağlar. Sanal WAN 'da sanal ağ bağlantıları sanal ağları sanal hub 'lara bağlanır. Kullanıcının genel VNet eşlemesini açıkça ayarlaması gerekmez. Aynı bölgedeki sanal hub 'a bağlı sanal ağlar VNet eşleme ücretlerine tabi. Farklı bir bölgedeki sanal hub 'a bağlı sanal ağlar küresel VNet eşleme ücretlerine tabi değildir.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute trafik şifrelemesi

Azure sanal WAN, ExpressRoute trafiğinizi şifreleme olanağı sağlar. Bu teknik, genel İnternet üzerinden veya genel IP adreslerini kullanarak ExpressRoute üzerinden şirket içi ağlar ve Azure sanal ağları arasında şifrelenmiş bir aktarım sağlar. Daha fazla bilgi için bkz. [sanal WAN Için ExpressRoute üzerinden IPSec](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Yerlerini

Konum bilgileri için bkz. [sanal WAN iş ortakları ve konumları](virtual-wan-locations-partners.md) makalesi.

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Temel ve standart sanal WAN 'Lar için rota tabloları

Yol tablolarında ilişkilendirme ve yayma özellikleri artık vardır. Önceden var olan bir yol tablosu, bu özelliklere sahip olmayan bir yol tablosudur. Hub yönlendirmesinde önceden varolan yollar varsa ve yeni özellikleri kullanmak istiyorsanız, aşağıdakileri göz önünde bulundurun:

* **Sanal hub 'da önceden var olan yollara sahip standart sanal WAN müşterileri**: Azure Portal Hub için yönlendirme bölümünde önceden varolan yollar varsa, önce bunları silmeniz ve ardından yeni rota tabloları oluşturmaya (Azure Portal hub Için yol tabloları bölümünde kullanılabilir) denemeniz gerekir. Sanal bir WAN 'daki tüm Hub 'lar için silme adımını yapmanız önemle önerilir.

* **Sanal hub 'da önceden var olan yolların bulunduğu temel sanal WAN müşterileri**: Azure Portal Hub için yönlendirme bölümünde önceden var olan yollar varsa, önce bunları silmeniz ve ardından temel sanal WAN 'ınızı standart sanal WAN 'a **yükseltmeniz** gerekir. Bkz. [bir sanal WAN 'ı temel 'Ten standart sürümüne yükseltme](upgrade-virtual-wan.md). Sanal bir WAN 'daki tüm Hub 'lar için silme adımını yapmanız önemle önerilir.

## <a name="faq"></a><a name="faq"></a>SSS

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Yenilikler nelerdir?

RSS akışına abone olun ve [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN) sayfasında en son sanal WAN özelliği güncelleştirmelerini görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md)
