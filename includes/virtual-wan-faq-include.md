---
title: include dosyası
description: include dosyası
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1cf6e1d47f9bb78349e0846f624e1d6a484669
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386612"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Azure sanal WAN, GA 'de mi?

Evet, Azure sanal WAN genel kullanıma sunuldu (GA). Ancak, sanal WAN çeşitli özelliklerden ve senaryolardan oluşur. Sanal WAN içinde, Microsoft 'un önizleme etiketini uyguladığı özellik veya senaryolar vardır. Bu durumlarda, belirli özellik veya senaryonun kendisi önizlemededir. Belirli bir önizleme özelliği kullanmıyorsanız, normal GA desteği uygulanır. Önizleme desteği hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Azure sanal WAN 'ı kullanmak için kullanıcının SD-WAN/VPN cihazlarıyla hub ve bağlı olması gerekir mi?

Sanal WAN, site/siteden siteye VPN bağlantısı, User/P2S bağlantısı, ExpressRoute bağlantısı, sanal ağ bağlantısı, VPN ExpressRoute ınterconnectivity, VNet-VNet geçişli bağlantı, merkezi yönlendirme, Azure Güvenlik Duvarı ve güvenlik duvarı Yöneticisi güvenlik, Izleme, ExpressRoute şifreleme ve diğer birçok özellik gibi tek bir cam bölmeden yerleşik olarak bulunan birçok işlevi sunar. Sanal WAN kullanmaya başlamak için bu kullanım örneklerinin tümünün olması gerekmez. Yalnızca bir kullanım durumu ile çalışmaya başlayın.

Sanal WAN mimarisi, dalların (VPN/SD-WAN cihazları), kullanıcıların (Azure VPN Istemcileri, openVPN veya Ikev2 Istemcileri), ExpressRoute devreleri, sanal ağların sanal hub 'lar için bağlı bileşenler olarak çalıştığı, ölçek ve performansa sahip bir hub ve bağlı bileşen mimarisidir. Tüm Hub 'lar standart bir sanal WAN 'da tam ağa bağlanır ve kullanıcının herhangi bir (bağlı olan) bağlantı için Microsoft omurgasını kullanmasını kolaylaştırır. SD-WAN/VPN cihazlarıyla hub ve bağlı bileşen için, kullanıcılar Azure sanal WAN portalında el ile ayarlayabilir veya sanal WAN Iş ortağı (SD-WAN/VPN) kullanarak Azure bağlantısı kurabilirsiniz.

Sanal WAN iş ortakları, cihaz bilgilerini Azure 'a dışarı aktarma özelliği olan bağlantı için Otomasyon sağlar, Azure yapılandırmasını indirebilir ve Azure sanal WAN hub 'ına bağlantı kurabilir. Noktadan siteye/kullanıcı VPN bağlantısı için, [Azure VPN istemcisi](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN veya Ikev2 istemcisini destekliyoruz.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Bir sanal WAN 'da tam olarak gelen hub 'ları devre dışı bırakabilirsiniz mi?

Sanal WAN iki şekilde gelir: temel ve standart. Temel sanal WAN 'da, hub 'lar değildir. Standart bir sanal WAN 'da, hub 'lar gönderilir ve sanal WAN ilk kez ayarlandığında otomatik olarak bağlanır. Kullanıcının belirli bir şeyi yapması gerekmez. Kullanıcı ayrıca, örgü hub 'ları elde etmek için işlevselliği devre dışı bırakmak veya etkinleştirmek zorunda değildir. Sanal WAN, herhangi bir bağlı bileşen (VNet, VPN veya ExpressRoute) arasında trafiği çok fazla yönlendirme seçeneği sunar. Tam olarak oluşan hub 'lar ve ayrıca gereksinimlerinize göre trafiği yönlendirme esnekliği sağlar.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Sanal WAN 'da Kullanılabilirlik Alanları ve dayanıklılık nasıl işlenir?

Sanal WAN, hub 'ın içinde kullanıma sunulan bir hub ve hizmet koleksiyonudur. Kullanıcı, ihtiyacı olan çok sayıda sanal WAN olabilir. Bir sanal WAN hub 'ında VPN, ExpressRoute vb. gibi birden çok hizmet vardır. Bu hizmetlerin her biri (Azure Güvenlik Duvarı dışında) bir Kullanılabilirlik Alanları bölgesinde dağıtılır, yani bölge Kullanılabilirlik Alanları destekler. Bir bölge, hub 'daki ilk dağıtımdan sonra bir kullanılabilirlik bölgesi haline gelirse, Kullanıcı ağ geçitlerini yeniden oluşturabilir ve bu da bir kullanılabilirlik alanı dağıtımı tetikleyecektir. Tüm ağ geçitleri bir hub 'da etkin-etkin olarak sağlanır ve bir hub içinde yerleşik olarak bulunan dayanıklılık vardır. Kullanıcılar bölgeler arasında dayanıklılık istiyorlarsa birden çok hub 'a bağlanabilir.

Sanal WAN kavramı küresel olsa da, gerçek sanal WAN kaynağı Kaynak Yöneticisi tabanlıdır ve bölgesel olarak dağıtılır. Sanal WAN bölgesinin kendisi bir sorun oluşturverilirse, bu sanal WAN 'daki tüm Hub 'lar olduğu gibi çalışmaya devam eder, ancak sanal WAN bölgesi kullanılabilir olana kadar Kullanıcı yeni hub 'lar oluşturamayacak.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure sanal WAN Kullanıcı VPN (Noktadan siteye) hangi istemciyi destekler?

Sanal WAN, [Azure VPN istemcisini](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN istemcisini veya herhangi bir Ikev2 istemcisini destekler. Azure AD kimlik doğrulaması, Azure VPN Istemcisi ile desteklenir. en az Windows 10 istemci işletim sistemi sürümü 17763,0 veya üzeri gereklidir.  OpenVPN istemcileri, sertifika tabanlı kimlik doğrulamasını destekleyebilir. Ağ geçidinde sertifika tabanlı kimlik doğrulaması seçildikten sonra cihazınıza indirmek için. ovpn * dosyasını görürsünüz. Ikev2 hem sertifika hem de RADIUS kimlik doğrulamasını destekler. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Kullanıcı VPN (Noktadan siteye) için, P2S istemci havuzu neden iki rotaya bölünür?

Her ağ geçidinin iki örneği vardır; bölünmüş durum, her bir ağ geçidi örneğinin bağlı istemciler için istemci IP 'lerini bağımsız olarak ayırabilmesi ve sanal ağdan gelen trafiğin, ağ geçidi örnek atlamasından kaçınmak için doğru ağ geçidi örneğine geri yönlendirilmesidir.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>P2S istemcileri için DNS sunucuları Nasıl yaparım? eklensin mi?

P2S istemcileri için DNS sunucuları eklemenin iki seçeneği vardır. İlk yöntem, özel DNS sunucularını istemci yerine ağ geçidine eklediği için tercih edilir.

1. Özel DNS sunucularını eklemek için aşağıdaki PowerShell betiğini kullanın. Ortamınız için değerleri değiştirin.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Ya da Windows 10 için Azure VPN istemcisini kullanıyorsanız, indirilen profil XML dosyasını değiştirebilir ve içeri aktarmadan önce **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** etiketleri ekleyebilirsiniz.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Kullanıcı VPN için (Noktadan siteye)-kaç istemci destekleniyor?

Her kullanıcı VPN P2S ağ geçidi iki örneğe sahiptir. Her örnek, ölçek birimi değiştikçe belirli sayıda bağlantıyı destekler. Ölçek birimi 1-3, 500 bağlantıları destekler, birim 4-6 ölçeği 1000 bağlantıları destekler, ölçek birimi 7-12 5000 bağlantıları destekler ve ölçek birimi 13-18 en fazla 10.000 bağlantıyı destekler.

Örneğin, kullanıcının 1 ölçek birimi seçtiği bir bakalım. Her ölçek birimi etkin-etkin bir ağ geçidinin dağıtıldığını ve örneklerin her biri (Bu durumda 2) en fazla 500 bağlantıyı destekleyeceği anlamına gelmez. Ağ Geçidi başına 500 bağlantı alabilmeniz için bu ölçek birimi için 500 yerine 1000 planlaması yapmanız anlamına gelmez. Önerilen bağlantı sayısını geçirirseniz, ek 500 bağlantısının kesintiye uğraması durumunda örneklere bakım yapılması gerekebilir. Ayrıca, ölçek biriminde ölçeği büyütme veya küçültme kararı verirken veya VPN ağ geçidinde Noktadan siteye yapılandırmayı değiştirmek için kapalı kalma süresi planlandığınızdan emin olun.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure sanal ağ geçidi (VPN Gateway) ve Azure sanal WAN VPN Gateway arasındaki fark nedir?

Sanal WAN geniş ölçekli Siteden Siteye bağlantı sağlar; aktarım hızı, ölçeklendirilebilirlik ve kullanım kolaylığı için oluşturulmuştur. Bir siteyi bir sanal WAN VPN Gateway 'e bağladığınızda, ağ geçidi türü ' VPN ' kullanan normal bir sanal ağ geçidinden farklıdır. Benzer şekilde, bir ExpressRoute devresini bir sanal WAN hub 'ına bağladığınızda ExpressRoute ağ geçidi için ' ExpressRoute ' ağ geçidi türünü kullanan normal sanal ağ geçidine göre farklı bir kaynak kullanır. 

Sanal WAN, hem VPN hem de ExpressRoute için 20 GB/sn toplam işleme destekler. Sanal WAN, bir CPE şube cihaz iş ortaklarının ekosistemiyle bağlantı için de Otomasyon içerir. CPE şube cihazlarında, otomatik olarak sağlanan ve Azure sanal WAN 'a bağlanan yerleşik Otomasyon vardır. Bu cihazlar büyüyen bir SD-WAN ve VPN iş ortakları ekosisteminden sağlanır. [Tercih edilen Iş ortağı listesine](../articles/virtual-wan/virtual-wan-locations-partners.md)bakın.

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Sanal WAN, Azure sanal ağ geçidinden nasıl farklıdır?

Sanal ağ geçidi VPN, 30 tünelle sınırlıdır. Bağlantılar için, büyük ölçekli VPN’lere yönelik Sanal WAN kullanmanız gerekir. Her bölge için en fazla 1.000 şube bağlantısına (sanal hub), hub başına 20 Gbps toplam ile bağlanabilirsiniz. Bağlantı şirket içi VPN cihazından sanal hub’a giden bir etkin-etkin tüneldir. Her bölge için bir hub 'ınız olabilir, bu, hub 'larda 1.000 ' den fazla dalı bağlayabilmeniz anlamına gelir.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Sanal WAN ağ geçidi ölçek birimi nedir?

Ölçek birimi, sanal hub 'da bir ağ geçidinin toplam verimini seçmek için tanımlanan bir birimdir. 1 ölçek VPN = 500 Mbps birim. 1 ExpressRoute = 2 Gbps ölçek birimi. Örnek: 10 ölçekli VPN birimi 500 Mbps * 10 = 5 Gbps sayısını kapsıyor

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Hangi cihaz sağlayıcıları (sanal WAN iş ortakları) destekleniyor?

Şu anda birçok iş ortağı tümüyle otomatik Sanal WAN deneyimini desteklemektedir. Daha fazla bilgi için bkz. [Sanal WAN iş ortakları](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Sanal WAN iş ortağı otomasyonu adımları nedir?

İş ortağı otomasyonu adımları için bkz. [Sanal WAN iş ortağı otomasyonu](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Tercih edilen bir iş ortağı cihazını kullanmam gerekiyor mu?

Hayır. IKEv2/IKEv1 IPsec desteğinin Azure gereksinimlerini karşılayan, VPN özellikli herhangi bir cihaz kullanabilirsiniz. Sanal WAN Ayrıca, Azure sanal WAN bağlantısını otomatik hale getiren ve uygun ölçekte IPSec VPN bağlantıları ayarlamayı kolaylaştıran bir CPE iş ortağı çözümlerine sahiptir.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Sanal WAN iş ortakları Azure Virtual WAN ile bağlantıyı nasıl otomatik hale getirir?

Yazılım tanımlı bağlantı çözümleri normalde dal cihazlarını yönetmek için bir denetleyici veya cihaz sağlama merkezi kullanır. Denetleyici, Azure Sanal WAN'a otomatik bağlantı sağlamak için Azure API'lerini kullanabilir. Otomasyon, şube bilgilerini karşıya yüklemeyi, Azure yapılandırmasını indirmeyi, Azure tünellerini Azure sanal hub 'lara ayarlamayı ve şube cihazını Azure sanal WAN 'a otomatik olarak kurmayı içerir. Yüzlerce dala sahipseniz, ekleme deneyiminin büyük ölçekli IPSec bağlantısı kurma, yapılandırma ve yönetme ihtiyacını ortadan oluşturduğundan, sanal WAN CPE Iş ortakları kullanarak bağlanmak kolaydır. Daha fazla bilgi için bkz. [sanal WAN iş ortağı Otomasyonu](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Kullandığım bir cihaz sanal WAN iş ortağı listesinde yoksa ne olur? Azure sanal WAN VPN 'e bağlanmak için kullanmaya devam edebilir miyim?

Bu, cihaz IPSec IKEv1 veya Ikev2 'yi desteklediği sürece evet. Sanal WAN iş ortakları cihazdan Azure VPN uç noktalarına bağlantıyı otomatik hale getirir. Bu, ' dal bilgileri yüklemesi ', ' IPSec ve yapılandırma ' ve ' bağlantı ' gibi adımları otomatikleştirmeye yönelik bir ifade gerektirir. Cihazınız bir sanal WAN iş ortağı ekosisteminden olmadığından, Azure yapılandırmasını el ile alma ve cihazınızı IPSec bağlantısı kurmak üzere güncelleştirme konusunda ağır bir kaldırma yapmanız gerekir.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>İlk iş ortağı listenizde yer almayan yeni iş ortakları nasıl eklenir?

Tüm sanal WAN API 'Leri açık API. Teknik bir bilimme değerlendirmek için belgeler [sanal WAN iş ortağı Otomasyonu](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) ' na gidebilirsiniz. İdeal bir iş ortağı, IKEv1 veya IKEv2 IPsec bağlantısına yönelik sağlanabilen bir cihaza sahip olandır. Şirket, yukarıda verilen Otomasyon yönergelerine bağlı olarak, CPE cihazları için Otomasyon işini tamamladıktan sonra, azurevirtualwan@microsoft.com [iş ortakları aracılığıyla burada bağlantı]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners)altına almak için öğesine ulaşabilirsiniz. Belirli bir şirket çözümünün sanal bir WAN iş ortağı olarak listelenmesini sağlayan bir müşteriyseniz, şirketinin adresine bir e-posta göndererek sanal WAN ile iletişim kurun azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>SD-WAN cihazlarını sanal WAN 'ı destekleme

Sanal WAN ortakları, Azure VPN uç noktalarıyla IPSec bağlantısını otomatik hale getirir. Sanal WAN iş ortağı bir SD-WAN sağlayıcısıysanız, SD-WAN denetleyicisi 'nin Azure VPN uç noktalarıyla otomasyonu ve IPSec bağlantısını yönetmesi yerinde olur. SD-WAN cihazı, herhangi bir özel SD-WAN işlevselliği için Azure VPN yerine kendi uç noktasını gerektiriyorsa, SD-WAN uç noktasını bir Azure VNet 'te dağıtabilir ve Azure sanal WAN ile bir arada bulunabilir.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kaç VPN cihazı tek bir hub 'a bağlanabilir?

Sanal hub başına en fazla 1.000 bağlantı desteklenir. Her bağlantı dört bağlantılardan oluşur ve her bağlantı bağlantısı etkin-etkin bir yapılandırmada olan iki tüneli destekler. Tüneller bir Azure sanal hub VPN Gateway 'de sonlandırılır. Bağlantılar, şube/VPN cihazında fiziksel ISS bağlantısını temsil eder.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure sanal WAN ile dal bağlantısı nedir?

Bir daldan veya VPN cihazından Azure sanal WAN 'a bağlantı, sanal bir hub 'da neredeyse VPN sitesini ve Azure VPN Gateway bağlayan bir VPN bağlantısıdır.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Şirket içi VPN cihazının yalnızca bir Azure sanal WAN VPN Gateway 'e 1 tüneli varsa ne olur?

Bir Azure sanal WAN bağlantısı 2 tünelden oluşur. Sanal bir WAN VPN Gateway, etkin-etkin modda bir sanal hub 'da dağıtılır ve bu, şirket içi cihazlardan ayrı bir örnek üzerinde sonlandıran ayrı tüneller olduğunu gösterir. Bu, tüm kullanıcıların önerisine yöneliktir. Ancak, Kullanıcı sanal WAN VPN Gateway örneklerinden birine yalnızca 1 tünele sahip olacağını seçerse, herhangi bir nedenle (bakım, yayama vb.) ağ geçidi örneği çevrimdışı duruma getirildiğinde, tünel ikincil etkin örneğe taşınır ve Kullanıcı bir yeniden bağlantı yaşayabilir. BGP oturumları, örnekleri arasında hareket etmez.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Şirket içi VPN cihazı birden çok hub 'a bağlanabilir mi?

Evet. Trafik akışı, yorum yapıldığında şirket içi cihazdan en yakın Microsoft ağ kenarına ve ardından sanal hub 'a göre yapılır.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sanal WAN için yeni Resource Manager kaynakları sağlanıyor mu?
  
Evet, sanal WAN yeni Kaynak Yöneticisi kaynaklarına sahiptir. Daha fazla bilgi için bkz. [Genel Bakış](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Sanal WAN ile tercih ettiğim ağ sanal cihazını (bir NVA sanal ağı içinde) dağıtabilir ve kullanabilir miyim?

Evet, en sevdiğiniz ağ sanal gereci(NVA) VNet’ini Azure Sanal WAN’a bağlayabilirsiniz.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Sanal hub içinde bir ağ sanal gereci oluşturabilir miyim?

Bir ağ sanal gereci (NVA), sanal hub içinde dağıtılamaz. Bununla birlikte, sanal hub 'a bağlı bir bağlı olan VNet 'te oluşturabilir ve gereksinimlerinize göre trafiği yönlendirmek için uygun yönlendirmeyi etkinleştirebilirsiniz.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Bir bağlı olan VNet bir sanal ağ geçidine sahip olabilir mi?

Hayır. Sanal hub 'a bağlıysa, bağlı olan VNet 'in sanal ağ geçidi olamaz.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN bağlantısında BGP desteği var mı?

Evet, BGP desteklenir. Bir VPN sitesi oluşturduğunuzda BGP parametrelerini buna sağlayabilirsiniz. Bu, bu site için Azure 'da oluşturulan tüm bağlantıların BGP için etkinleştirileceğini kapsıyor.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sanal WAN ile ilgili lisans ve fiyatlandırma bilgileri var mı?

Evet. Bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-wan/) sayfası.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Azure Sanal WAN’ı bir Resource Manager şablonu ile yapılandırmak mümkün mü?

Tek bir hub ve bir vpnsite içeren bir sanal WAN 'ın basit bir yapılandırması, bir [hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)kullanılarak oluşturulabilir. Sanal WAN, birincil olarak bir REST veya Portal temelli hizmettir.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Bir sanal hub 'a bağlı olan VNET 'ler birbirleriyle (V2V transit) iletişim kurabilir mi?

Evet. Standart sanal WAN, sanal ağların bağlandığı sanal WAN hub 'ı aracılığıyla VNet 'ten VNet 'e geçişli bağlantıyı destekler. Sanal WAN terimlerinde bu yollara, tek bir bölgedeki bir sanal WAN hub 'ına bağlı sanal ağlar için "yerel sanal WAN VNet aktarım" ve iki veya daha fazla bölgede birden çok sanal WAN hub 'ı ile bağlı sanal ağlar için "küresel sanal WAN VNet geçişi" olarak adlandırdık.

Bazı senaryolarda, bağlı olan VNET 'ler yerel veya genel sanal WAN VNet aktarımına ek olarak [sanal ağ eşlemesi](../articles/virtual-network/virtual-network-peering-overview.md) kullanılarak birbirleriyle doğrudan eşlenebilir. Bu durumda, VNet eşlemesi sanal WAN hub 'ı aracılığıyla geçişli bağlantıdan önceliklidir.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Sanal WAN’da daldan dala bağlantıya izin verilir mi?

Evet, daldan dala bağlantı Sanal WAN’da kullanılabilir. Dal, VPN sitesi, ExpressRoute devreleri veya Noktadan siteye/kullanıcı VPN kullanıcıları için kavramsal olarak uygulanabilir. Daldan dala etkinleştirilmesi, varsayılan olarak etkindir ve WAN **yapılandırma** ayarları ' nda bulunabilir. Bu, VPN dallarının/kullanıcılarının diğer VPN dallarına bağlanmasına ve ayrıca VPN ve ExpressRoute kullanıcıları arasında geçiş bağlantısının de etkinleştirilmesini sağlar.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Azure sanal WAN aracılığıyla daldan dala trafiği çapraz geçiş yapar mi?

Evet. Daldan dala trafiği Azure sanal WAN aracılığıyla geçer.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Sanal WAN her siteden ExpressRoute gerektiriyor mu?

Hayır. Sanal WAN, her siteden ExpressRoute gerektirmez. Siteleriniz ExpressRoute bağlantı hattı kullanılarak bir sağlayıcı ağına bağlanabilir. Aynı hub 'da bir sanal hub 'a ve IPSec VPN 'ye ExpressRoute kullanılarak bağlanan sitelerde, sanal hub VPN ve ExpressRoute kullanıcısı arasında geçiş bağlantısı sağlar.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Azure sanal WAN kullanılırken bir ağ performansı veya bağlantı sınırı var mı?

Ağ aktarım hızı, sanal bir WAN hub 'ında hizmet başına. İstediğiniz sayıda sanal WAN 'ınız olabilir, ancak her sanal WAN, bölge başına 1 hub 'a izin verir. Her hub 'da, VPN toplam aktarım hızı 20 Gbps 'e kadar, ExpressRoute toplam aktarım hızı 20 Gbps 'e kadar, kullanıcı VPN/Noktadan siteye VPN toplam verimlilik ise 20 Gbps 'ye kadar olur. Sanal hub 'daki yönlendirici, VNet-VNet trafik akışları için en fazla 50 Gbps destekler ve tek bir sanal hub 'a bağlı tüm VNET 'lerde toplam 2000 VM iş yükünü varsayar.

VPN siteleri bir hub 'a bağlandığında bağlantıları bu şekilde görür. Sanal WAN, sanal hub başına en fazla 1000 bağlantıyı veya 2000 IPSec Tünelini destekler. Uzak kullanıcılar sanal hub 'a bağlandıklarında, sanal hub 'da P2S VPN ağ geçidi için seçilen ölçek birimine (bant genişliği) bağlı olarak en fazla 10.000 kullanıcıyı destekleyen P2S VPN ağ geçidine bağlanır.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN tünelinin toplam VPN işleme ve bağlantı nedir?

Bir hub 'ın toplam VPN verimlilik, VPN ağ geçidinin seçilen ölçek birimine göre 20 Gbps 'ye kadar olur. Aktarım hızı, var olan tüm bağlantılar tarafından paylaşılır. Bir bağlantı içindeki her tünel 1 GB/sn 'ye kadar destek alabilir.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>VPN bağlantımda NAT-T kullanabilir miyim?

Evet, NAT geçişi (NAT-T) desteklenir. Sanal WAN VPN Gateway, IPSec tünellerine/bu paketlerin iç paketlerinde NAT benzeri işlevsellik gerçekleştirmez. Bu yapılandırmada, şirket içi cihazın IPSec Tünelini başlattığınızdan emin olun.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Portalda sanal hub için 20 Gbps ayarı görmüyorum. Nasıl yaparım? yapılandırmak mı istiyorsunuz?

Portalda bir hub 'ın içindeki VPN ağ geçidine gidin ve uygun ayarla değiştirmek için ölçek birimine tıklayın.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Sanal WAN, şirket içi cihazın birden çok ISS 'yi paralel olarak kullanmasına izin veriyor mu veya her zaman tek bir VPN tüneli mi?

Şirket içi cihaz çözümleri, birden çok tüneldeki trafiği Azure sanal WAN hub 'ına (sanal hub 'da VPN Gateway) eklemek için trafik ilkeleri uygulayabilir.

### <a name="what-is-global-transit-architecture"></a>Küresel geçiş mimarisi nedir?

Küresel aktarım mimarisi hakkında daha fazla bilgi için bkz. [küresel aktarım ağı mimarisi ve sanal WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure temelinde trafik nasıl yönlendirilir?

Trafik şu şekilde izler: şube cihazı >ISP->Microsoft ağ Edge->Microsoft DC (hub VNet)->Microsoft ağ Edge->ISS->şube cihazı

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Bu modelde her sitede neye ihtiyacınız var? Yalnızca interneti bağlantısına mı?

Evet. Tercihen tümleşik [sanal WAN iş ortaklarımız](../articles/virtual-wan/virtual-wan-locations-partners.md)tarafından IPSec 'i destekleyen bir internet bağlantısı ve fiziksel cihaz. İsteğe bağlı olarak, Azure ile yapılandırmayı ve bağlantıyı tercih ettiğiniz cihazdan el ile yönetebilirsiniz.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Bağlantı (VPN, ExpressRoute veya sanal ağ) için varsayılan yolu (0.0.0.0/0) etkinleştirmek Nasıl yaparım??

Bir sanal hub, bağlantıda ' Enabled ' olduğunda, bir sanal ağ/siteden siteye VPN/ExpressRoute bağlantısına öğrenilen bir varsayılan yol yayabilir. Kullanıcı bir sanal ağ bağlantısını, bir VPN bağlantısını veya ExpressRoute bağlantısını düzenlediğinde bu bayrak görünür. Varsayılan olarak, bir site veya ExpressRoute devresi bir hub 'a bağlıyken bu bayrak devre dışıdır. Sanal ağı bir sanal hub 'a bağlamak için bir sanal ağ bağlantısı eklendiğinde varsayılan olarak etkindir.

Varsayılan yol, sanal WAN hub 'ında değil; Varsayılan yol, hub 'da bir güvenlik duvarı dağıtımının bir sonucu olarak sanal WAN hub tarafından zaten öğrenildiği ya da başka bir bağlı sitede Zorlamalı tünel etkinse yayılır. Varsayılan yol, hub 'lar (Inter-hub) arasında yayılmaz.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Sanal bir WAN 'daki sanal hub, birden çok hub 'dan bir yol için en iyi yolu nasıl seçer?

Bir sanal hub birden fazla uzak hub 'dan aynı rotayı öğrenirse, karar verdiği sıra aşağıdaki gibidir:

1. En uzun önek eşleşmesi.
1. Interhub üzerinden yerel yollar (sanal hub, ınterhub için 65520-65520 atar).
1. BGP üzerinden statik yollar: Bu, sanal hub yönlendiricisinin yaptığı kararların bağlamıdır. Ancak, karar Oluşturucu bir sitenin BGP aracılığıyla yollar duyurduğu veya statik adres önekleri sağladığı VPN ağ geçidse, BGP yollarının üzerinde statik yollar tercih edilebilir.
1. VPN üzerinden ExpressRoute (ER): bağlam yerel bir hub olduğunda VPN üzerinden de tercih edilir. ExpressRoute devreleri arasındaki aktarım bağlantısı yalnızca Global Reach aracılığıyla kullanılabilir. Bu nedenle, ExpressRoute bağlantı hattının bir hub 'a bağlandığı ve VPN bağlantısıyla farklı bir hub 'a bağlı başka bir ExpressRoute bağlantı hattının bulunduğu senaryolarda, hub 'lar arası senaryolar için VPN tercih edilebilir.
1. Yol uzunluğu olarak.

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Sanal WAN hub 'ı ExpressRoute devreleri arasında bağlantıya izin veriyor mu?

ER ile er arasında geçiş, her zaman küresel erişim aracılığıyla yapılır. Sanal hub ağ geçitleri DC veya Azure bölgelerinde dağıtılır. İki ExpressRoute bağlantı hattı genel erişim aracılığıyla bağlandığında, trafiğin uç yönlendiricilerden sanal hub DC 'ye kadar tüm yollarla gelmesi gerekmez.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Azure sanal WAN ExpressRoute devreleri veya VPN bağlantılarında bir ağırlık kavramı bulunur

Bir sanal hub 'a birden çok ExpressRoute bağlantı hattı bağlandığında, bağlantıda yönlendirme ağırlığı, sanal hub 'daki ExpressRoute için bir devreyi diğer üzerinde tercih etmek üzere bir mekanizma sağlar. VPN bağlantısında ağırlık ayarlama mekanizması yoktur. Azure, tek bir hub içindeki bir VPN bağlantısı üzerinden her zaman bir ExpressRoute bağlantısı tercih eder.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Sanal WAN, Azure trafiği için VPN üzerinden ExpressRoute 'u tercih ediyor

Evet. Sanal WAN, Azure trafiği için bir VPN üzerinden ExpressRoute 'u tercih ediyor.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Bir sanal WAN hub 'ının bir ExpressRoute bağlantı hattı ve bu ağa bağlı bir VPN sitesi olduğunda, ExpressRoute üzerinden bir VPN bağlantı yolunun tercih edildiği ne olur?

ExpressRoute bağlantı hattı sanal hub 'a bağlıyken, Microsoft Edge yönlendiricileri, şirket içi ve Azure arasındaki iletişimin ilk düğümüdür. Bu uç yönlendiriciler, sanal WAN ExpressRoute ağ geçitleriyle iletişim kurar, bu da sanal WAN 'daki tüm ağ geçitleri arasındaki tüm yolları denetleyen sanal hub yönlendiricisinden yollar öğrenmelidir. Microsoft Edge yönlendiricileri, sanal hub ExpressRoute yollarını, şirket içinde öğrenilmiş yollar üzerinde daha yüksek bir tercihe göre işler.

Herhangi bir nedenle, VPN bağlantısı, yol uzunluğu (örneğin, ExpressRoute ve VPN arasındaki yük devretme senaryoları) öğrenilmesi için sanal hub 'ın birincil orta haline gelirse, VPN sitesinin yol uzunluğu daha uzun olmadığı sürece sanal hub, VPN öğrenilen yollarını ExpressRoute ağ geçidiyle paylaşmaya devam edecektir. Bu, Microsoft Edge yönlendiricilerinin şirket içi yollarla VPN yollarını tercih etmesine neden olur.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>İki hub (hub 1 ve 2) bağlı olduğunda ve hub 'lara bir fiyona yay olarak bağlı bir ExpressRoute bağlantı hattı varsa, Merkez 2 ' de bağlı olan VNet 'e ulaşmak için hub 1 ' e bağlı VNet 'in yolu nedir?

Geçerli davranış, VNet-VNet bağlantısı için hub-hub üzerinden ExpressRoute bağlantı hattı üzerinden tercih edilir. Ancak, bu bir sanal WAN kurulumunda önerilir. Sanal WAN ekibi, ExpressRoute yolu üzerinden hub-hub için tercihi etkinleştirmek üzere bir düzeltmeyle çalışır. Bu öneri, birden fazla ExpressRoute devresine (farklı sağlayıcılar) bir hub 'a bağlanmasını ve bölgeler arası trafik akışları için sanal WAN tarafından sunulan hub-hub bağlantısını kullanmayı kullanmaktır.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Hub 'lar sanal WAN 'da farklı bir kaynak grubunda oluşturulabilir mi?

Evet. Bu seçenek şu anda yalnızca PowerShell aracılığıyla kullanılabilir. Sanal WAN portalı, hub 'ların sanal WAN kaynağı ile aynı kaynak grubunda olmasını gerektirir.

### <a name="what-is-the-recommended-hub-address-space-during-hub-creation"></a>Merkez oluşturma sırasında önerilen hub adres alanı nedir?

Önerilen sanal WAN hub 'ı adres alanı/23 ' dir. Sanal WAN hub 'ı çeşitli ağ geçitlerine (ExpressRoute, siteden siteye VPN, Noktadan siteye VPN, Azure Güvenlik Duvarı, sanal hub yönlendiricisi) alt ağlar atar. NVA 'lar 'in bir sanal hub içinde dağıtıldığı senaryolarda, genellikle NVA örnekleri için bir/28 kullanılır. Ancak, Kullanıcı birden çok NVA 'lar sağladıysanız bir/27 alt ağı atanabilir. Bu nedenle gelecekteki bir mimarinin aklınızda tutulması, sanal WAN hub 'ları en az/24 boyutuyla dağıtıldığında, Kullanıcı girişi için oluşturulma zamanında önerilen hub adres alanı/23 olur.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Sanal WAN 'da IPv6 desteği var mı?

IPv6, sanal WAN hub 'ında ve ağ geçitlerinde desteklenmez. IPv4 ve IPv6 desteği olan bir VNet 'iniz varsa ve VNet 'i sanal WAN 'a bağlamak istiyorsanız, bu senaryo şu anda desteklenmemektedir.

Azure Güvenlik Duvarı aracılığıyla Internet 'ten gelen Noktadan siteye kullanıcı VPN senaryosunda, sanal WAN hub 'ına trafiği zorlamak için istemci cihazınızda IPv6 bağlantısını kapatmanız gerekecektir. Bunun nedeni modern cihazların varsayılan olarak IPv6 adresleri kullanmasına yöneliktir.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Çeşitli sanal WAN işlevlerini otomatikleştirerek betikler tarafından kullanılacak önerilen API sürümü nedir?

Minimum 05-01-2020 sürümü (Mayıs 1 2020) gereklidir.

### <a name="are-there-any-virtual-wan-limits"></a>Herhangi bir sanal WAN sınırı var mı?

Abonelik ve hizmet limitleri sayfasındaki [sanal WAN sınırları](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) bölümüne bakın.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Sanal WAN türleri (temel ve standart) arasındaki farklar nelerdir?

Bkz. [temel ve standart sanal WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Fiyatlandırma için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-wan/) sayfası.

### <a name="does-virtual-wan-store-customer-data"></a>Sanal WAN mağazası müşteri verileri mi?

Hayır. Sanal WAN, hiçbir müşteri verisi depolamaz.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Kullanıcılar için sanal WAN 'ı bir hizmet olarak yönetebilen yönetilen hizmet sağlayıcıları var mı?

Evet. Azure Marketi aracılığıyla etkinleştirilen yönetilen hizmet sağlayıcısı (MSP) çözümlerinin listesi için bkz. [Azure ağ oluşturma iş ortakları tarafından sunulan Azure Marketi teklifleri](../articles/networking/networking-partners-msp.md#msp).

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Sanal WAN hub 'ı yönlendirme, VNet 'teki Azure yol sunucusundan nasıl farklıdır?

Azure Route Server, bir DIY hub VNet 'teki yol sunucusundan yolları öğrenmek için NVA 'lar (ağ sanal gereci) tarafından kullanılabilecek bir Sınır Ağ Geçidi Protokolü (BGP) eşleme hizmeti sağlar. Sanal WAN yönlendirmesi, VNet-VNet aktarma yönlendirmesi, özel yönlendirme, özel yol ilişkilendirmesi ve yayma gibi birden çok yetenek ve ExpressRoute, site VPN, uzak kullanıcı/büyük ölçekli P2S VPN ve güvenli Merkez (Azure Güvenlik Duvarı) özellikleri bağlantı hizmetleriyle birlikte sıfır dokunuşla tam bir hub hizmeti sağlar. NVA ve Azure Route sunucunuz arasında bir BGP eşlemesi oluşturduğunuzda, NVA 'ınızdan IP adreslerini sanal ağınıza tanıtabilirsiniz. Geçiş yönlendirmesi, özel yönlendirme vb. gibi tüm gelişmiş yönlendirme özellikleri için sanal WAN yönlendirmeyi kullanabilirsiniz.
