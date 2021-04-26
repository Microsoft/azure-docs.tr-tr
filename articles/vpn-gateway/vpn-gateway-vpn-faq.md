---
title: Azure VPN Gateway SSS
description: VPN Ağ Geçidi SSS. Microsoft Azure Sanal Ağ şirket içi ve dışı bağlantılar, karma yapılandırma bağlantıları ve VPN Ağ Geçitleri hakkında SSS.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yushwang
ms.openlocfilehash: 3d29e99f3b539fdbea2a19df7ffc25d4e41a5376
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731267"
---
# <a name="vpn-gateway-faq"></a>VPN Gateway SSS

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>Sanal ağlara bağlanma

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Farklı Azure bölgelerinde sanal ağlara bağlanabilir miyim?

Evet. Aslında, hiçbir bölge kısıtlaması yoktur. Bir sanal ağ aynı bölgedeki veya farklı bir Azure bölgesindeki başka bir sanal ağa bağlanabilir.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Farklı aboneliklerle sanal ağlara bağlanabilir miyim?

Evet.

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>VPN Gateway yapılandırırken VNet 'imde özel DNS sunucuları belirtebilir miyim?

VNet dosyanızı oluştururken bir DNS sunucusu veya sunucuları belirttiyseniz, VPN Gateway belirttiğiniz DNS sunucularını kullanır. Bir DNS sunucusu belirtirseniz, DNS sunucunuzun Azure için gereken etki alanı adlarını çözümleyebildiğini doğrulayın.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Tek bir sanal ağdan birden çok siteye bağlanabilir miyim?

Windows PowerShell ve Azure REST API'lerini kullanarak birden çok siteye bağlanabilirsiniz. [Çok siteli ve VNet - VNet Bağlantı](#V2VMulti) SSS bölümüne bakın.

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>VPN ağ geçidini etkin-etkin olarak ayarlamak için ek bir maliyet var mı?

Hayır.

### <a name="what-are-my-cross-premises-connection-options"></a>Şirket içi ve dışı bağlantı seçeneklerim nelerdir?

Aşağıdaki şirket içi ve dışı bağlantılar desteklenmektedir:

* Siteden Siteye – IPsec üzerinden (IKE v1 ve IKE v2) VPN bağlantısı. Bu bağlantı türüne şirket içi bir VPN cihazı ya da RRAS gerekir. Daha fazla bilgi için bkz. [Siteden Siteye](./tutorial-site-to-site-portal.md).
* Noktadan Siteye – SSTP (Güvenli Yuva Tünel Protokolü) veya IKE v2 üzerinden VPN bağlantısı. Bu bağlantıya VPN cihazı gerekmez. Daha fazla bilgi için bkz. [Noktadan Siteye](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Sanal Ağdan Sanal Ağa – Bu bağlantı türü, Siteden Siteye yapılandırmasıyla aynıdır. VNet - VNet, IPsec üzerinden (IKE v1 ve IKE v2) bir VPN bağlantısıdır. VPN cihazı gerekmez. Daha fazla bilgi için bkz. [Sanal Ağdan Sanal Ağa](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Çok Siteli – Birden çok şirket içi sitenin bir sanal ağa bağlanmasına olanak sağlayan Siteden Siteye yapılandırmanın bir çeşididir. Daha fazla bilgi için bkz. [Çok Siteli](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – ExpressRoute, genel Internet üzerinden VPN bağlantısıyla değil, WAN 'ınızdan Azure 'a özel bir bağlantıdır. Daha fazla bilgi için bkz. [ExpressRoute’a Teknik Genel Bakış](../expressroute/expressroute-introduction.md) ve [ExpressRoute SSS](../expressroute/expressroute-faqs.md).

VPN ağ geçidi bağlantıları hakkında daha fazla bilgi için bkz. [VPN Gateway Hakkında](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Siteden Siteye bağlantı ve Noktadan Siteye bağlantı arasındaki fark nelerdir?

**Siteden Siteye** (IPsec/IKE VPN tüneli) yapılandırmalar, şirket içi konumunuz ile Azure arasında yapılır. Diğer bir deyişle, şirket içinde yer alan bilgisayarlarla sanal makineler arasında ya da yönlendirme ve izin yapılandırmayı nasıl seçtiğinize bağlı olarak sanal ağınızdaki rol örneği ile bağlantı kurabilirsiniz. Her zaman kullanılabilir şirket içi bağlantı için harika bir seçenektir ve karma yapılandırmalara uygun hale gelir. Bu tür bir bağlantı; ağınıza ucuna dağıtılmış olması gereken IPsec VPN uygulamasına bağlıdır (donanım cihazı veya yazılım aracı). Bu tür bir bağlantı oluşturmak için, dışarıdan yönelik bir IPv4 adresine sahip olmanız gerekir.

**Noktadan Siteye** (SSTP üzerinden VPN) yapılandırmalar, sanal ağınızda bulunan her yerden her şeye tek bir bilgisayardan bağlanmanızı sağlar. Windows yerleşik VPN istemcisi kullanır. Noktadan Siteye yapılandırmasının bir parçası olarak, bir sertifika ve bir VPN istemci yapılandırma paketi yüklerseniz; bu pakette, bilgisayarınızın sanal ağda herhangi bir sanal makineye veya rol örneğine bağlanmasını sağlayan ayarlar bulunur. Şirket içi olmayan sanal ağa bağlanmak istediğinizde çok yararlıdır. Her ikisi de Siteden Siteye bağlantısına gereken VPN donanımına veya dışarıya dönük IPv4 adresine erişiminiz yoksa bu da iyi bir seçenektir.

Siteden Siteye bağlantınızı ağ geçidinizi için rota tabanlı VPN türü kullanarak oluşturmanız kaydıyla, sanal ağınızı aynı anda hem Siteden Siteye, hem de Noktadan Siteye bağlanacak şekilde yapılandırabilirsiniz. Rota tabanlı VPN türlerine klasik dağıtım modelinde dinamik ağ geçitleri adı verilir.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>Sanal ağ geçitleri

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>VPN ağ geçidi bir sanal ağ geçidi midir?

VPN ağ geçidi bir sanal ağ geçidi türüdür. VPN ağ geçidi, şifrelenmiş trafiği bir genel bağlantı üzerinden sanal ağınız ile şirket içi konumunuz arasında gönderir. Sanal ağlar arasında trafik göndermek için de VPN ağ geçidi kullanabilirsiniz. Bir VPN ağ geçidi oluştururken 'Vpn' -GatewayType değerini kullanın. Daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>İlke tabanlı (statik yönlendirme) ağ geçidi nedir?

İlke tabanlı ağ geçitleri, ilke tabanlı VPN'leri uygular. İlke temelli VPN'ler, şirket içi ağınızla Azure VNet'iniz arasında adres öneklerinin birleşimleri temelindeki IPsec tüneller üzerinden paketleri şifreler ve yönlendirirler. İlke (veya Trafik Seçici) çoğunlukla VPN yapılandırmasında bir erişim listesi olarak tanımlanır.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Rota tabanlı (dinamik yönlendirme) ağ geçidi nedir?

Rota tabanlı ağ geçitleri yol tabanlı VPN'leri uygular. Rota temelli VPN'ler, paketleri kendi ilgili arabirimlerine yönlendirmek için IP iletme veya yönlendirme tablosunda "yolları" seçeneğini kullanır. Bundan sonra tünel arabirimleri, paketleri tünellerin içinde veya dışında şifreler veya şifrelerini çözer. Rota tabanlı VPN 'Ler için ilke veya trafik seçicileri, herhangi bir (veya joker karakter) olarak yapılandırılır.

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>İlke tabanlı VPN ağ Geçidimi rota temelinde güncelleştirebilir miyim?

Hayır. Ağ Geçidi türü, ilke tabanlı ya da yol tabanlı olarak, ilke tabanlı olarak değiştirilemez. Bir ağ geçidi türünü değiştirmek için, ağ geçidinin silinip yeniden oluşturulması gerekir. Bu işlem yaklaşık 60 dakika sürer. Yeni ağ geçidini oluşturduğunuzda, özgün ağ geçidinin IP adresini tutamaz.

1. Ağ geçidiyle ilişkili tüm bağlantıları silin.

1. Aşağıdaki makalelerden birini kullanarak ağ geçidini silin:

   * [Azure portalındaki](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell-klasik](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. İstediğiniz ağ geçidi türünü kullanarak yeni bir ağ geçidi oluşturun ve ardından VPN kurulumunu doldurun. Adımlar için, [siteden siteye öğreticiye](./tutorial-site-to-site-portal.md#VNetGateway)bakın.

### <a name="do-i-need-a-gatewaysubnet"></a>'GatewaySubnet' gerekli mi?

Evet. Ağ geçidi alt ağı, sanal ağ geçidi hizmetlerinin kullandığı IP adreslerini içerir. Bir sanal ağ geçidi yapılandırmak için sanal ağınıza yönelik bir ağ geçidi alt ağı oluşturmanız gerekir. Tüm ağ geçidi alt ağlarının düzgün çalışması için GatewaySubnet şeklinde adlandırılması gerekir. Ağ geçidi alt ağını başka şekilde adlandırmayın. VM’leri veya herhangi başka bir şeyi de ağ geçidi alt ağına dağıtmayın.

Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Ağ geçidi alt ağındaki IP adresleri, ağ geçidi hizmetine ayrılır. Bazı yapılandırmalar, ağ geçidi hizmetlerine daha fazla IP adresi ayrılmasını gerektirir. Gelecekteki büyümeye ve meydana gelebilecek diğer yeni bağlantı yapılandırmalarına uyum sağlaması için ağ geçidi alt ağınızın yeterince IP adresi içerdiğinden emin olmanız gerekir. Bu nedenle, /29 kadar küçük bir ağ geçidi alt ağı oluşturmanız mümkün olsa da /27 veya daha büyük bir (/27, /26, /25 vb.) ağ geçidi alt ağı oluşturmanız önerilir. Oluşturmak istediğiniz yapılandırmanın gereksinimlerine bakın ve sahip olduğunuz ağ geçidi alt ağının bu gereksinimleri karşıladığından emin olun.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Sanal Makineleri veya rol örneklerini ağ geçidi alt ağıma dağıtabilir miyim?

Hayır.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Oluşturmadan önce VPN ağ geçidi IP adresimi alabilir miyim?

Bölgesel olarak yedekli ve zikzak ağ geçitleri (adı _az_ olan ağ geçidi SKU 'ları), her Ikisi de _Standart SKU_ Azure genel IP kaynağını kullanır. Azure Standart SKU genel IP kaynakları bir statik ayırma yöntemi kullanmalıdır. Bu nedenle, sizin için kullanmayı düşündüğünüz standart SKU genel IP kaynağını oluşturduktan hemen sonra VPN ağ geçidinizin genel IP adresine sahip olursunuz.

Bölgesel olmayan ve olmayan ağ geçitleri (adında _az_ _olmayan ağ_ geçidi SKU 'ları) IÇIN, OLUŞTURULMADAN önce VPN Gateway IP adresini alamaz. IP adresi yalnızca VPN ağ geçidinizi silip yeniden oluşturursanız değişir.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>VPN ağ geçidim için bir Statik Genel IP adresi isteğinde bulunabilir miyim?

Yukarıda belirtildiği gibi, bölgesel olarak yedekli ve zikzak ağ geçitleri (adında _az_ olan ağ geçidi SKU 'ları), her Ikisi de _Standart SKU_ Azure genel IP kaynağını kullanır. Azure Standart SKU genel IP kaynakları bir statik ayırma yöntemi kullanmalıdır.

Bölgesel olmayan ve olmayan ağ geçitleri (adında _az_ _olmayan ağ_ geçidi SKU 'ları) için yalnızca dinamik IP adresi ataması desteklenir. Ancak bu, IP adresinin VPN ağ geçidinize atandıktan sonra değiştiği anlamına gelmez. VPN ağ geçidi IP adresi, ağ geçidinin silindiği ve yeniden oluşturulduğu zamana göre değişir. VPN Gateway 'in diğer iç bakımını ve yükseltmelerini yeniden boyutlandırdığınızda, sıfırladığınızda veya tamamladıktan sonra VPN Gateway genel IP adresi değişmez.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>VPN tünelimin kimliği nasıl doğrulanır?

Azure VPN PSK (Önceden Paylaşılan Anahtar) kimlik doğrulamasını kullanır. VPN tüneli oluşturduğumuzda önceden paylaşılan anahtar (PSK) oluştururuz. Önceden paylaşılan bir anahtar PowerShell cmdlet 'i veya REST API olarak, otomatik olarak oluşturulmuş bir PSK 'yi değiştirebilirsiniz.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Önceden Paylaşılan Anahtar API’sini Ayarla’yı ilke tabanlı (statik yönlendirme) ağ geçidi VPN’mi yapılandırmak için kullanabilir miyim?

Evet, Önceden Paylaşılan Anahtar API’sini ve PowerShell cmdlet’ini Ayarla, hem Azure ilke tabanlı (statik) VPN'ler, hem de rota tabanlı (dinamik) yönlendirme VPN'leri yapılandırmak için kullanılabilir.

### <a name="can-i-use-other-authentication-options"></a>Diğer kimlik doğrulama seçeneklerini kullanabilir miyim?

Önceden paylaşılan anahtarı (PSK) kimlik doğrulaması için sınırladık.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>VPN ağ geçidime hangi trafiğin gideceğini nasıl belirtirim?

#### <a name="resource-manager-deployment-model"></a>Resource Manager dağıtım modeli

* PowerShell: yerel ağ geçidinize ait trafiği belirtmek için "AddressPrefix" kullanın.
* Azure portalı: Yerel ağ geçidi > Yapılandırma > Adres alanı'na gidin.

#### <a name="classic-deployment-model"></a>Klasik dağıtım modeli

* Azure portalı: Klasik sanal ağ > VPN bağlantıları > Siteden siteye VPN bağlantıları > Yerel site adı > Yerel site > İstemci adres alanı yolunu izleyin.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>VPN bağlantımda NAT-T kullanabilir miyim?

Evet, NAT geçişi (NAT-T) desteklenir. Azure VPN Gateway, IPSec tünellerine/bu paketlerin iç paketlerinde NAT benzeri işlevsellik gerçekleştirmez.  Bu yapılandırmada, lütfen şirket içi cihazın IPSec Tünelini başlattığınızdan emin olun.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Azure'da kendi VPN sunucumu kurup bu sunucuyu şirket içi ağıma bağlanmak üzere kullanabilir miyim?

Evet, Azure’de kendi VPN ağ geçitlerinizi veya sunucularınızı ister Azure Market’ten, ister kendi VPN yönlendiricilerinizi oluşturarak dağıtabilirsiniz. Şirket içi ağlarınız ve sanal ağ alt ağları arasında trafiğin düzgün yönlendirilmesini sağlamak amacıyla sanal ağınızda kullanıcı tanımlı yolları yapılandırmanız gerekir.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>Sanal ağ geçidimde neden belirli bağlantı noktaları açıldı?

Azure altyapı iletişimi için gereklidirler. Bunlar Azure sertifikaları tarafından korunur (kilitlenir). Uygun sertifikaları olmadan, bu ağ geçitlerinin müşterileri de dahil dış varlıklar, bu uç noktalarında etkiye neden olamazlar.

Bir sanal ağ geçidi, bir NIC 'nin müşterinin özel ağına dokunduktan ve bir NIC ortak ağa bakan bir NIC 'e sahip olan çok bağlantılı bir cihaza sahiptir. Azure altyapı varlıkları uyum nedenleriyle müşterinin özel ağlarına dokunamazlar; bu nedenle altyapı iletişimi için ortak uç noktaları kullanmaları gerekir. Ortak uç noktalar düzenli aralıklarla Azure güvenlik denetimi tarafından taranır.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Ağ geçidi türleri, gereksinimleri ve verimliliği hakkında daha fazla bilgi

Daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>Siteden Siteye bağlantılar ve VPN cihazları

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>VPN cihazını seçerken nelere dikkat etmeliyim?

Cihaz satıcılarıyla işbirliğiyle bir dizi standart Siteden Siteye VPN cihazını doğruladık. Bilinen uyumlu VPN cihazlarının listesi, ilgili yapılandırma yönergeleri veya örnekleri ve cihaz özellikleri listesi [VPN cihazları hakkında](vpn-gateway-about-vpn-devices.md) makalesinde bulunabilir. Bilinen uyumlu olarak listelenen cihaz ailelerindeki tüm cihazlar Virtual Network ile çalışmalıdır. VPN cihazınızı yapılandırmaya yardım için uygun cihaz ailesine karşılık gelen cihaz yapılandırma örneğine veya bağlantılara bakın.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>VPN cihazı yapılandırma ayarlarını nerede bulabilirim?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>VPN cihazı yapılandırma örneklerini nasıl düzenleyebilirim?

Cihaz yapılandırma örneklerini düzenleme hakkında daha fazla bilgi için bkz. [Örnekleri düzenleme](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>IPsec ve IKE parametrelerini nerede bulabilirim?

IPsec/IKE parametreleri için bkz. [Parametreler](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Trafik boştayken neden ilke tabanlı VPN tünelim kayboluyor?

İlke tabanlı (statik rota olarak da bilinir) VPN Ağ geçitleri için bu beklenen bir davranıştır. tünel üzerindeki trafik 5 dakikadan fazla boşta kalırsa tünel bozulur. Herhangi bir yönde trafik akışı başladığında tünel hemen yeniden başlatılır.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Azure'e bağlanmak için VPN'ler yazılımını kullanabilir miyim?

Siteden Siteyi şirket içi ve dışı yapılandırması için Windows Server 2012 Yönlendirme ve Uzaktan Erişim (RRAS) sunucularını destekliyoruz.

Endüstri standardı IPsec uygulamalarıyla uyumlu olana kadar diğer yazılım VPN çözümleri bizim ağ geçidimizle çalışmalıdır. Yapılandırma ve destek hakkında yönergeler için yazılım satıcısına başvurun.

## <a name="how-do-i-change-the-authentication-type-for-my-point-to-site-connections"></a>Nasıl yaparım? Noktadan siteye bağlantılarım için kimlik doğrulaması türünü değiştirmek mi istiyorsunuz?

Noktadan siteye bağlantılarınızın kimlik doğrulama yöntemini, VPN Gateway altındaki **Noktadan siteye yapılandırma** bölümüne giderek ve istenen radyo düğmesini denetleyerek değiştirebilirsiniz. Geçerli seçenekler **Azure sertifikası, RADIUS kimlik doğrulaması ve Azure Active Directory**. Yeni profil indirildikten ve istemciye yapılandırılana kadar, geçerli istemcilerin değişiklikten sonra **bağlanamayacağını** lütfen unutmayın.

## <a name="point-to-site-using-native-azure-certificate-authentication"></a><a name="P2S"></a>Yerel Azure sertifika doğrulamasını kullanarak Noktadan Siteye

Bu bölüm Resource Manager dağıtım modeli için geçerlidir.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site-using-radius-authentication"></a><a name="P2SRADIUS"></a>RADIUS kimlik doğrulamasını kullanarak Noktadan Siteye

Bu bölüm Resource Manager dağıtım modeli için geçerlidir.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>Sanal Ağdan Sanal Ağa ve Çok Siteli bağlantılar

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Siteden siteye VPN bağlantısı ile ExpressRoute arasında yönlendirmeyi etkinleştirmek Nasıl yaparım?.

ExpressRoute 'a bağlanmış dalınız ile bir siteden siteye VPN bağlantısına bağlı dalınız arasında yönlendirmeyi etkinleştirmek istiyorsanız, [Azure Route Server](../route-server/expressroute-vpn-support.md)'ı ayarlamanız gerekir.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Şirket içi sitelerim arasında veya başka bir sanal ağa trafiği geçirmek için Azure VPN ağ geçidini kullanabilir miyim?

**Resource Manager dağıtım modeli**<br>
Evet. Daha fazla bilgi için [BGP](#bgp) bölümüne bakın.

**Klasik dağıtım modeli**<br>
Klasik dağıtım modeli kullanılarak Azure VPN ağ geçidi üzerinden trafik geçirilebilse de, bu geçiş, ağ yapılandırma dosyasında statik olarak tanımlanan adres alanlarına bağlıdır. Klasik dağıtım modeli kullanan Azure Sanal Ağlar ve VPN ağ geçitleri ile BGP henüz desteklenmemektedir. BGP olmadan, geçiş adres alanlarının el ile tanımlanması çok hata eğilimindedir ve önerilmez.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure, IPsec/IKE önceden paylaşılan anahtarı tüm VPN bağlantılarımla aynı sanal ağ için mi üretiyor?

Hayır, varsayılan olarak Azure farklı VPN bağlantıları için farklı önceden paylaşılan anahtarlar oluşturur. Ancak, isterseniz anahtar değeri ayarlamak için VPN Ağ Geçidi Anahtarı REST API veya PowerShell cmdlet'ini kullanabilirsiniz. Anahtar yazdırılabilir ASCII karakterleri OLMALıDıR.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Daha fazla Siteden Siteye VPN ile tek bir sanal ağa göre daha fazla bant genişliği elde edebilir miyim?

Hayır, Noktadan Siteye VPN’lerde dahil tüm VPN tünelleri aynı Azure VPN ağ geçidini ve kullanılabilir bant genişliğini paylaşır.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Çok siteli VPN kullanarak sanal ağlarım ve şirket içi sitem arasında birden fazla tünel yapılandırabilir miyim?

Evet, ancak iki tünelde de aynı konum için BGP yapılandırması gerçekleştirmeniz gerekir.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Birden çok VPN tüneline sahip sanal ağımla birlikte Noktadan Siteye VPN’lerini kullanabilir miyim?

Evet, Noktadan Siteye (P2S) VPN’ler şirket içi sitelere ve başka sanal ağlara VPN ağ geçitleriyle kullanılabilir.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>IPsec VPN’lerle sanal ağı ExpressRoute devreme bağlayabilir miyim?

Evet, bu desteklenir. Daha fazla bilgi için bkz. [Bir arada var olan ExpressRoute ve Siteden Siteye VPN bağlantıları yapılandırma](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/IKE ilkesi

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP ve yönlendirme

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>Zorlamalı tüneli yapılandırabilir miyim?

Evet. Bkz. [Zorlamalı tüneli yapılandırma](vpn-gateway-about-forced-tunneling.md).

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>Şirket içi ve dışı bağlantısı ve VM'ler

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Sanal makinem sanal bir ağdaysa, şirket içi ve dışı bağlantım varsa VM’ye nasıl bağlanmalıyım?

Birkaç seçeneğiniz vardır. Sanal makineniz için RDP’yi etkinleştirdiyseniz, özel IP adresini kullanarak sanal makinenize bağlanabilirsiniz. Bu durumda, özel IP adresini ve bağlanmak istediğiniz bağlantı noktasını (genellikle 3389) belirtmeniz gerekir. Sanal makinenizde trafik için bağlantı noktası yapılandırmanız gerekir.

Ayrıca, aynı sanal ağda bulunan başka bir sanal makineden sanal makinenize özel IP adresi ile bağlanabilirsiniz. Sanal ağınızın dışında bir konumdan bağlanıyorsanız özel IP adresi kullanarak sanal makinenizde RDP gerçekleştiremezsiniz. Örneğin, yapılandırılmış bir Noktadan Siteye sanal ağınız varsa ve bilgisayarınızdan bağlantı kurmuyorsanız, sanal makineyi özel IP adresi ile bağlayamazsınız.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Sanal makinem şirket içi ve dışı bağlantılı bir sanal ağdaysa, VM’me ait trafiğin tümü bu bağlantıdan geçer mi?

Hayır. Bir tek, belirttiğiniz sanal ağ Yerel Ağ Ip adresi aralıklarında bulunan hedef IP’si olan trafik sanal ağ geçidinden geçer. Sanal ağ içinde yer alan bir hedef IP'ye sahip olan trafik, sanal ağ içinde kalır. Diğer trafik ortak ağlara yük dengeleyiciyle gönderilir veya zorlamalı tünel kullanılırsa, Azure VPN ağ geçidi üzerinden gönderilir.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>VM ile RDP bağlantısı sorunlarını nasıl giderebilirim?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>Sanal ağ hakkında SSS

Ek sanal ağ ek bilgilerini [Virtual Network SSS](../virtual-network/virtual-networks-faq.md) bölümünde görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

* VPN Gateway hakkında daha fazla bilgi için bkz. [VPN Gateway Hakkında](vpn-gateway-about-vpngateways.md).
* VPN Gateway yapılandırma ayarları hakkında daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

**"OpenVPN", OpenVPN Inc 'nin ticari markasıdır.**
