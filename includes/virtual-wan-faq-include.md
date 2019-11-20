---
title: include dosyası
description: include dosyası
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0ba69715998f110d88015bdba2fbf340a6b64089
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491653"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure sanal ağ geçidi (VPN Gateway) ve Azure sanal WAN VPN Gateway arasındaki fark nedir?

Sanal WAN geniş ölçekli Siteden Siteye bağlantı sağlar; aktarım hızı, ölçeklendirilebilirlik ve kullanım kolaylığı için oluşturulmuştur. Bir siteyi bir sanal WAN VPN Gateway 'e bağladığınızda, ağ geçidi türü ' VPN ' kullanan normal bir sanal ağ geçidinden farklıdır. Benzer şekilde, bir ExpressRoute devresini bir sanal WAN hub 'ına bağladığınızda ExpressRoute ağ geçidi için ' ExpressRoute ' ağ geçidi türünü kullanan normal sanal ağ geçidine göre farklı bir kaynak kullanır. Sanal WAN, hem VPN hem de ExpressRoute için 20 GB/sn toplam işleme destekler. Sanal WAN, bir CPE şube cihaz iş ortaklarının ekosistemiyle bağlantı için de Otomasyon içerir. CPE şube cihazlarında, otomatik olarak sağlanan ve Azure sanal WAN 'a bağlanan yerleşik Otomasyon vardır. Bu cihazlar büyüyen bir SD-WAN ve VPN iş ortakları ekosisteminden sağlanır. [Tercih edilen Iş ortağı listesine](../articles/virtual-wan/virtual-wan-locations-partners.md)bakın.

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Sanal WAN, Azure sanal ağ geçidinden nasıl farklıdır?

Sanal ağ geçidi VPN, 30 tünelle sınırlıdır. Bağlantılar için, büyük ölçekli VPN’lere yönelik Sanal WAN kullanmanız gerekir. Her bölge için en fazla 1.000 şube bağlantısına (sanal hub), hub başına 20 Gbps toplam ile bağlanabilirsiniz. Bağlantı şirket içi VPN cihazından sanal hub’a giden bir etkin-etkin tüneldir. Her bölge için bir hub 'ınız olabilir, bu, hub 'larda 1.000 ' den fazla dalı bağlayabilmeniz anlamına gelir.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Hangi cihaz sağlayıcıları (sanal WAN iş ortakları) destekleniyor?

Şu anda birçok iş ortağı tümüyle otomatik Sanal WAN deneyimini desteklemektedir. Daha fazla bilgi için bkz. [Sanal WAN iş ortakları](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Sanal WAN iş ortağı otomasyonu adımları nedir?

İş ortağı otomasyonu adımları için bkz. [Sanal WAN iş ortağı otomasyonu](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Tercih edilen bir iş ortağı cihazını kullanmam gerekiyor mu?

Hayır. IKEv2/IKEv1 IPsec desteğinin Azure gereksinimlerini karşılayan, VPN özellikli herhangi bir cihaz kullanabilirsiniz.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Sanal WAN iş ortakları Azure Virtual WAN ile bağlantıyı nasıl otomatik hale getirir?

Yazılım tanımlı bağlantı çözümleri normalde dal cihazlarını yönetmek için bir denetleyici veya cihaz sağlama merkezi kullanır. Denetleyici, Azure Sanal WAN'a otomatik bağlantı sağlamak için Azure API'lerini kullanabilir. Daha fazla bilgi için bkz. Sanal WAN iş ortağı otomasyonu.

Yazılım tanımlı bağlantı çözümleri normalde dal cihazlarını yönetmek için bir denetleyici veya cihaz sağlama merkezi kullanır. Denetleyici, Azure Sanal WAN'a otomatik bağlantı sağlamak için Azure API'lerini kullanabilir. Otomasyon, şube bilgilerini karşıya yüklemeyi, Azure yapılandırmasını indirmeyi, Azure tünellerini Azure sanal hub 'lara ayarlamayı ve şube cihazını Azure sanal WAN 'a otomatik olarak kurmayı içerir. Yüzlerce dala sahipseniz, ekleme deneyiminin büyük ölçekli IPSec bağlantısı kurma, yapılandırma ve yönetme ihtiyacını ortadan oluşturduğundan, sanal WAN CPE Iş ortakları kullanarak bağlanmak kolaydır. Daha fazla bilgi için bkz. [Sanal WAN iş ortağı otomasyonu](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Tercih edilen bir iş ortağı cihazını kullanmam gerekiyor mu?

Hayır. IKEv2/IKEv1 IPsec desteğinin Azure gereksinimlerini karşılayan, VPN özellikli herhangi bir cihaz kullanabilirsiniz.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>SD-WAN cihazlarını sanal WAN 'ı destekleme

Sanal WAN ortakları, Azure VPN uç noktalarıyla IPSec bağlantısını otomatik hale getirir. Sanal WAN iş ortağı bir SD-WAN sağlayıcısıysanız, SD-WAN denetleyicisi 'nin Azure VPN uç noktalarıyla otomasyonu ve IPSec bağlantısını yönetmesi yerinde olur. SD-WAN cihazı, herhangi bir özel SD-WAN işlevselliği için Azure VPN yerine kendi uç noktasını gerektiriyorsa, SD-WAN uç noktasını bir Azure VNet 'te dağıtabilir ve Azure sanal WAN ile bir arada bulunabilir.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Sanal WAN mevcut bağlantı özelliklerinden herhangi birini değiştiriyor mu?

Mevcut Azure bağlantı özelliklerinde hiçbir değişiklik yoktur.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sanal WAN için yeni Resource Manager kaynakları sağlanıyor mu?
  
Evet, Sanal WAN yeni Resource Manager kaynakları getirir. Daha fazla bilgi için bkz. [Genel Bakış](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kaç VPN cihazı tek bir hub 'a bağlanabilir?

Sanal hub başına en fazla 1.000 bağlantı desteklenir. Her bağlantı dört bağlantılardan oluşur ve her bağlantı bağlantısı etkin-etkin bir yapılandırmada olan iki tüneli destekler. Tüneller bir Azure sanal hub vpngateway 'de sonlandırılır.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Şirket içi VPN cihazı birden çok Hub’a bağlanabilir mi?

Evet. Trafik akışı, yorum yapıldığında şirket içi cihazdan en yakın Microsoft ağ kenarına ve ardından sanal hub 'a göre yapılır.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Sanal WAN ile tercih ettiğim ağ sanal cihazını (bir NVA sanal ağı içinde) dağıtabilir ve kullanabilir miyim?

Evet, en sevdiğiniz ağ sanal gereci(NVA) VNet’ini Azure Sanal WAN’a bağlayabilirsiniz. İlk olarak ağ sanal gereci VNet’ini Hub Sanal Ağ bağlantısının olduğu hub’a bağlayın. Ardından, sanal gereci işaret eden bir sonraki atlamada bir sanal hub yolu oluşturun. Sanal hub yol tablosuna birden çok yol uygulayabilirsiniz. Uç VNet yollarının şirket içi sistemlere yayıldığından emin olunması için NVA VNet’e bağlı olan tüm uçların ek olarak sanal hub’a bağlı olması gerekmektedir.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Sanal hub içinde bir ağ sanal gereci oluşturabilir miyim?

Bir ağ sanal gereci (NVA), sanal hub içinde dağıtılamaz. Bununla birlikte, sanal hub 'a bağlı bir bağlı ağ VNet 'te oluşturabilir ve hub 'daki bir yolu, NVA IP adresi (NIC 'nin) aracılığıyla hedef VNet için trafiği yönlendirmek üzere etkinleştirebilirsiniz.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Bir bağlı olan VNet bir sanal ağ geçidine sahip olabilir mi?

Hayır. Sanal hub 'a bağlıysa, bağlı olan VNet 'in sanal ağ geçidi olamaz.

### <a name="is-there-support-for-bgp"></a>BGP için destek var mı?

Evet, BGP desteklenir. Bir VPN sitesi oluşturduğunuzda BGP parametrelerini buna sağlayabilirsiniz. Bu, bu site için Azure 'da oluşturulan tüm bağlantıların BGP için etkinleştirileceğini kapsıyor. Ayrıca, bir NVA ile VNet 'iniz varsa ve bu NVA VNet bir sanal WAN hub 'ına eklenmişse, bir NVA VNET 'ten gelen yolların uygun şekilde tanıtıldığından emin olmak için NVA VNET 'e bağlı olan ışınsal 'ler BGP 'yi devre dışı bırakmalıdır. Ayrıca, bağlı olan VNet yollarının şirket içi sistemlere yayıldığından emin olmak için bu bağlı ağ sanal ağlarını sanal hub VNet 'e bağlayın.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Sanal hub'da UDR kullanarak trafiği yönlendirebilir miyim?

Evet, bir sanal hub yol tablosu kullanarak trafiği VNet 'e yönlendirebilirsiniz. Bu, belirli bir IP adresi (genellikle NVA NIC) aracılığıyla Azure 'daki hedef VNET 'ler için yollar ayarlamanıza olanak sağlar.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sanal WAN ile ilgili lisans ve fiyatlandırma bilgileri var mı?

Evet. Bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-wan/) sayfası.

### <a name="how-do-i-calculate-price-of-a-hub"></a>Bir hub 'ın fiyatını hesaplamak Nasıl yaparım? mı?

* Hub 'daki hizmetler için ödeme yaparsınız. Örneğin, Azure sanal WAN 'a bağlanmak için gereken 10 dal veya şirket içi cihazlarınızın, hub 'daki VPN uç noktalarına bağlanıldığını varsayalım. Bunun 1 ölçekli birim = 500 Mbps hızında VPN olduğunu varsayalım. Bu, $0.361/HR üzerinden ücretlendirilir. Her bağlantı, $0,05/saat üzerinden ücretlendirilir. 10 bağlantı için toplam hizmet ücreti/hr $0,361 + $. 5/sa olur. Azure 'dan çıkmadan trafik için veri ücretleri.

* Ek Merkez ücreti vardır. Bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-wan/) sayfası.

* ExpressRoute bağlantı hattı bir sanal hub 'a bağlanırken ExpressRoute Gateway 'e sahipseniz, ölçek birimi fiyatı için ödeme yaparsınız. ER 'daki her ölçek birimi 2 GB/sn 'dir ve her bağlantı birimi VPN bağlantı birimiyle aynı hızda ücretlendirilir.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>İlk iş ortağı listenizde yer almayan yeni iş ortakları nasıl eklenir?

Tüm sanal WAN API 'Leri açık API. Teknik bir bilimme değerlendirmek için belgelere gidebilirsiniz. Sorularınız varsa azurevirtualwan@microsoft.comadresine bir e-posta gönderin. İdeal bir iş ortağı, IKEv1 veya IKEv2 IPsec bağlantısına yönelik sağlanabilen bir cihaza sahip olandır.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Kullandığım bir cihaz sanal WAN iş ortağı listesinde yoksa ne olur? Azure sanal WAN VPN 'e bağlanmak için kullanmaya devam edebilir miyim?

Bu, cihaz IPSec IKEv1 veya Ikev2 'yi desteklediği sürece evet. Sanal WAN iş ortakları cihazdan Azure VPN uç noktalarına bağlantıyı otomatik hale getirir. Bu, ' dal bilgileri yüklemesi ', ' IPSec ve yapılandırma ' ve ' bağlantı ' gibi adımları otomatikleştirmeye yönelik bir ifade gerektirir. Cihazınız bir sanal WAN iş ortağı ekosisteminden olmadığından, Azure yapılandırmasını el ile alma ve cihazınızı IPSec bağlantısı kurmak üzere güncelleştirme konusunda ağır bir kaldırma yapmanız gerekir.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Azure Sanal WAN’ı bir Resource Manager şablonu ile yapılandırmak mümkün mü?

Tek bir hub ve bir vpnsite içeren bir sanal WAN 'ın basit bir yapılandırması, bir [hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)kullanılarak oluşturulabilir. Sanal WAN, birincil olarak bir REST veya Portal temelli hizmettir.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure Sanal WAN ile Genel Sanal Ağ Eşleme destekleniyor mu? 

Sanal WAN 'inizdeki farklı bir bölgedeki VNet 'i bağlayabilirsiniz.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Sanal hub'a bağlı uç sanal ağları birbiriyle iletişim kurabiliyor mu?

Evet. Bağlı olan VNET 'ler, sanal ağ eşlemesi aracılığıyla doğrudan iletişim kurabilir. Daha fazla bilgi için bkz. [Sanal Ağ Eşlemesi](../articles/virtual-network/virtual-network-peering-overview.md). Hub aracılığıyla geçişli iletişim kuran VNET 'leri de destekliyoruz.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure sanal WAN ile dal bağlantısı nedir?

Şube cihazından Azure sanal WAN 'a bağlantı en fazla dört bağlantıyı destekler. Bağlantı, şube konumundaki fiziksel bağlantı bağlantıdır (örneğin: ATT, Verizon vb.). Her bağlantı bağlantısı, iki etkin/etkin IPSec tünellerinden oluşur.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Sanal WAN’da daldan dala bağlantıya izin verilir mi?

Evet, daldan dala bağlantı VPN için Sanal WAN’da ve VPN ile ExpressRoute arasında kullanılabilir.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Azure sanal WAN aracılığıyla daldan dala trafiği çapraz geçiş yapar mi?

Evet.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Sanal WAN her siteden ExpressRoute gerektiriyor mu?

Hayır, Sanal WAN her siteden ExpressRoute alınmasını gerektirmez. Cihazdan bir Azure sanal WAN hub 'ına internet bağlantıları aracılığıyla standart IPSec siteden siteye bağlantısı kullanır. Siteleriniz ExpressRoute bağlantı hattı kullanılarak bir sağlayıcı ağına bağlanabilir. Bir sanal hub 'da ExpressRoute kullanılarak bağlanan sitelerde, siteler VPN ve ExpressRoute arasında şube trafiği akışına dala sahip olabilir.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Azure Sanal WAN kullanırken bir ağ aktarım hızı sınırı var mı?

Dal sayısı hub/bölge başına 1000 bağlantıyla ve hub 'da toplam 20 Gbps ile sınırlıdır. Bölge başına 1 hub olabilir.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Bir sanal WAN hub 'ı kaç VPN bağlantısı destekliyor?

Azure sanal WAN hub, en fazla 1.000 S2S bağlantısı, 10.000 P2S bağlantısı ve 4 ExpressRoute bağlantısını destekleyebilir.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN tünelinin toplam VPN işleme ve bağlantı nedir?

Bir hub 'ın toplam VPN verimlilik, seçilen ölçek birimine bağlı olarak 20 Gbps 'e kadar olur. Aktarım hızı, var olan tüm bağlantılar tarafından paylaşılır. Bir bağlantı içindeki her tünel 1 GB/sn 'ye kadar destek alabilir.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Portalda sanal hub için 20 Gbps ayarı görmüyorum. Nasıl yaparım? yapılandırmak mı istiyorsunuz?

Portalda bir hub 'ın içindeki VPN ağ geçidine gidin ve uygun ayarla değiştirmek için ölçek birimine tıklayın.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Sanal WAN, şirket içi cihazın birden çok ISS 'yi paralel olarak kullanmasına izin veriyor mu veya her zaman tek bir VPN tüneli mi?

Sanal WAN VPN 'ye gelen bir bağlantı, dalda bulunan bir bağlantı kullanılarak her zaman etkin-etkin bir tüneldir (aynı hub/bölge dahilinde esneklik için). Bu bağlantı, şirket içi dalda bir ISS bağlantısı olabilir. ' VPNSite ' sanal WAN 'ı siteye bağlantı bilgilerini ekleme özelliği sağlar. Dalda birden çok ISS varsa ve her bir ISS 'nin bir bağlantı sağladıysanız, bu bilgiler Azure 'daki VPN sitesi bilgilerinde ayarlanabilir. Ancak, daldaki ISS 'lerde yük devretmeyi yönetmek, tamamen şube merkezli bir yönlendirme işlemidir.

### <a name="what-is-global-transit-architecture"></a>Küresel geçiş mimarisi nedir?

Küresel aktarım mimarisi hakkında daha fazla bilgi için bkz. [küresel aktarım ağı mimarisi ve sanal WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure temelinde trafik nasıl yönlendirilir?

Trafik şu şekilde izler: şube cihazı > ISP-> Microsoft ağ Edge-> Microsoft DC (hub VNet)-> Microsoft ağ Edge-> ISS-> Şube cihazı

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Bu modelde her sitede neye ihtiyacınız var? Yalnızca interneti bağlantısına mı?

Evet. Tercihen tümleşik [sanal WAN iş ortaklarımız](../articles/virtual-wan/virtual-wan-locations-partners.md)tarafından IPSec 'i destekleyen bir internet bağlantısı ve fiziksel cihaz. İsteğe bağlı olarak, Azure ile yapılandırmayı ve bağlantıyı tercih ettiğiniz cihazdan el ile yönetebilirsiniz.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Bir bağlantıda (VPN, ExpressRoute veya sanal ağ) varsayılan yolu (0.0.0.0/0) etkinleştirmek Nasıl yaparım?:

Bir sanal hub, bağlantıda ' Enabled ' olduğunda, bir sanal ağ/siteden siteye VPN/ExpressRoute bağlantısına öğrenilen bir varsayılan yol yayabilir. Kullanıcı bir sanal ağ bağlantısını, bir VPN bağlantısını veya ExpressRoute bağlantısını düzenlediğinde bu bayrak görünür. Varsayılan olarak, bir site veya ExpressRoute devresi bir hub 'a bağlıyken bu bayrak devre dışıdır. Sanal ağı bir sanal hub 'a bağlamak için bir sanal ağ bağlantısı eklendiğinde varsayılan olarak etkindir. Varsayılan yol, sanal WAN hub 'ında değil; Varsayılan yol, hub 'da bir güvenlik duvarı dağıtımının bir sonucu olarak sanal WAN hub tarafından zaten öğrenildiği ya da başka bir bağlı sitede Zorlamalı tünel etkinse yayılır.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Sanal WAN türleri (temel ve standart) arasındaki farklar nelerdir?

' Temel ' WAN türü, temel bir hub (SKU = temel) oluşturmanıza imkan tanır. ' Standart ' WAN türü, standart Merkez (SKU = standart) oluşturmanızı sağlar. Temel hub 'lar siteden siteye VPN işlevselliğiyle sınırlıdır. Standart Hub 'lar, hub 'lar üzerinden ExpressRoute, User VPN (P2S), tam ağ hub ve sanal ağdan VNet 'e aktarım yapmanızı sağlar. Standart Hub 'lar için $0,25/SA temel ücretini ve VNet 'ten VNet 'e bağlantı sırasında Hub 'lar üzerinden geçiş yapmak için bir veri işleme ücretini ödeyin ve hub 'a giden trafiğe yönelik veri işleme ücreti ödersiniz. Daha fazla bilgi için bkz. [temel ve standart sanal WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Fiyatlandırma için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-wan/) sayfası.
