---
title: Azure VPN Gateway SSS
description: VPN Ağ Geçidi SSS. Microsoft Azure Sanal Ağ şirket içi ve dışı bağlantılar, karma yapılandırma bağlantıları ve VPN Ağ Geçitleri hakkında SSS.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: yushwang
ms.openlocfilehash: fa4e403733c418a4ceff417dd0cf7b54f9409a49
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151670"
---
# <a name="vpn-gateway-faq"></a>VPN Gateway SSS

## <a name="connecting"></a>Sanal ağlara bağlanma

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Farklı Azure bölgelerinde sanal ağlara bağlanabilir miyim?

Evet. Aslında, hiçbir bölge kısıtlaması yoktur. Bir sanal ağ aynı bölgedeki veya farklı bir Azure bölgesindeki başka bir sanal ağa bağlanabilir. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Farklı aboneliklerle sanal ağlara bağlanabilir miyim?

Evet.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Tek bir sanal ağdan birden çok siteye bağlanabilir miyim?

Windows PowerShell ve Azure REST API'lerini kullanarak birden çok siteye bağlanabilirsiniz. [Çok siteli ve VNet - VNet Bağlantı](#V2VMulti) SSS bölümüne bakın.

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>VPN ağ geçidini etkin-etkin olarak ayarlamak için ek bir maliyet var mı?

Hayır. 

### <a name="what-are-my-cross-premises-connection-options"></a>Şirket içi ve dışı karışık bağlantı seçeneklerim nelerdir?

Aşağıdaki şirket içi ve dışı bağlantılar desteklenmektedir:

* Siteden Siteye – IPsec üzerinden (IKE v1 ve IKE v2) VPN bağlantısı. Bu bağlantı türüne şirket içi bir VPN cihazı ya da RRAS gerekir. Daha fazla bilgi için bkz. [Siteden Siteye](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Noktadan Siteye – SSTP (Güvenli Yuva Tünel Protokolü) veya IKE v2 üzerinden VPN bağlantısı. Bu bağlantıya VPN cihazı gerekmez. Daha fazla bilgi için bkz. [Noktadan Siteye](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Sanal Ağdan Sanal Ağa – Bu bağlantı türü, Siteden Siteye yapılandırmasıyla aynıdır. VNet - VNet, IPsec üzerinden (IKE v1 ve IKE v2) bir VPN bağlantısıdır. VPN cihazı gerekmez. Daha fazla bilgi için bkz. [Sanal Ağdan Sanal Ağa](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Çok Siteli – Birden çok şirket içi sitenin bir sanal ağa bağlanmasına olanak sağlayan Siteden Siteye yapılandırmanın bir çeşididir. Daha fazla bilgi için bkz. [Çok Siteli](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – ExpressRoute, genel Internet üzerinden VPN bağlantısıyla değil, WAN 'ınızdan Azure 'a özel bir bağlantıdır. Daha fazla bilgi için bkz. [ExpressRoute’a Teknik Genel Bakış](../expressroute/expressroute-introduction.md) ve [ExpressRoute SSS](../expressroute/expressroute-faqs.md).

VPN ağ geçidi bağlantıları hakkında daha fazla bilgi için bkz. [VPN Gateway Hakkında](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Siteden Siteye bağlantı ve Noktadan Siteye bağlantı arasındaki fark nelerdir?

**Siteden Siteye** (IPsec/IKE VPN tüneli) yapılandırmalar, şirket içi konumunuz ile Azure arasında yapılır. Diğer bir deyişle, şirket içinde yer alan bilgisayarlarla sanal makineler arasında ya da yönlendirme ve izin yapılandırmayı nasıl seçtiğinize bağlı olarak sanal ağınızdaki rol örneği ile bağlantı kurabilirsiniz. Her zaman kullanıma uygun şirket içi ve dışı bağlantı için müthiş bir seçenek; karma yapılandırmalar için de çok uygundur. Bu tür bir bağlantı; ağınıza ucuna dağıtılmış olması gereken IPsec VPN uygulamasına bağlıdır (donanım cihazı veya yazılım aracı). Bu tür bir bağlantı oluşturmak için, dışarıdan yönelik bir IPv4 adresine sahip olmanız gerekir.

**Noktadan Siteye** (SSTP üzerinden VPN) yapılandırmalar, sanal ağınızda bulunan her yerden her şeye tek bir bilgisayardan bağlanmanızı sağlar. Windows yerleşik VPN istemcisi kullanır. Noktadan Siteye yapılandırmasının bir parçası olarak, bir sertifika ve bir VPN istemci yapılandırma paketi yüklerseniz; bu pakette, bilgisayarınızın sanal ağda herhangi bir sanal makineye veya rol örneğine bağlanmasını sağlayan ayarlar bulunur. Şirket içi olmayan sanal ağa bağlanmak istediğinizde çok yararlıdır. Her ikisi de Siteden Siteye bağlantısına gereken VPN donanımına veya dışarıya dönük IPv4 adresine erişiminiz yoksa bu da iyi bir seçenektir.

Siteden Siteye bağlantınızı ağ geçidinizi için rota tabanlı VPN türü kullanarak oluşturmanız kaydıyla, sanal ağınızı aynı anda hem Siteden Siteye, hem de Noktadan Siteye bağlanacak şekilde yapılandırabilirsiniz. Rota tabanlı VPN türlerine klasik dağıtım modelinde dinamik ağ geçitleri adı verilir.

## <a name="gateways"></a>Sanal ağ geçitleri

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>VPN ağ geçidi bir sanal ağ geçidi midir?

VPN ağ geçidi bir sanal ağ geçidi türüdür. VPN ağ geçidi, şifrelenmiş trafiği bir genel bağlantı üzerinden sanal ağınız ile şirket içi konumunuz arasında gönderir. Sanal ağlar arasında trafik göndermek için de VPN ağ geçidi kullanabilirsiniz. Bir VPN ağ geçidi oluştururken 'Vpn' -GatewayType değerini kullanın. Daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>İlke tabanlı (statik yönlendirme) ağ geçidi nedir?

İlke tabanlı ağ geçitleri, ilke tabanlı VPN'leri uygular. İlke temelli VPN'ler, şirket içi ağınızla Azure VNet'iniz arasında adres öneklerinin birleşimleri temelindeki IPsec tüneller üzerinden paketleri şifreler ve yönlendirirler. İlke (veya Trafik Seçici) çoğunlukla VPN yapılandırmasında bir erişim listesi olarak tanımlanır.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Rota tabanlı (dinamik yönlendirme) ağ geçidi nedir?

Rota tabanlı ağ geçitleri yol tabanlı VPN'leri uygular. Rota temelli VPN'ler, paketleri kendi ilgili arabirimlerine yönlendirmek için IP iletme veya yönlendirme tablosunda "yolları" seçeneğini kullanır. Bundan sonra tünel arabirimleri, paketleri tünellerin içinde veya dışında şifreler veya şifrelerini çözer. Rota temelli VPN’lerle ilgili ilke veya trafik seçici herhangi birinden herhangi birine (veya joker karakterler) olarak yapılandırılır.

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>İlke temelli VPN ağ geçidimi Rota temelli olarak güncelleştirebilir miyim?
Hayır. Azure VNET ağ geçidi türü, ilke tabanlı olarak yol tabanlı veya başka bir şekilde değiştirilemez. Ağ geçidinin silinip yeniden oluşturulması gerekir ve bu işlem yaklaşık 60 dakika sürer. Ağ geçidinin IP adresi veya Önceden Paylaşılan Anahtar (PSK) korunmaz.
1. Silinecek ağ geçidiyle ilişkilendirilmiş bağlantıları silin.
1. Ağ geçidini silin:
1. [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
1. [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
1. [Azure Powershell - klasik](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. [İstenen türde yeni bir ağ geçidi oluşturun ve VPN kurulumunu tamamlayın](vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)

### <a name="do-i-need-a-gatewaysubnet"></a>'GatewaySubnet' gerekli mi?

Evet. Ağ geçidi alt ağı, sanal ağ geçidi hizmetlerinin kullandığı IP adreslerini içerir. Bir sanal ağ geçidi yapılandırmak için sanal ağınıza yönelik bir ağ geçidi alt ağı oluşturmanız gerekir. Tüm ağ geçidi alt ağlarının düzgün çalışması için GatewaySubnet şeklinde adlandırılması gerekir. Ağ geçidi alt ağını başka şekilde adlandırmayın. VM’leri veya herhangi başka bir şeyi de ağ geçidi alt ağına dağıtmayın.

Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Ağ geçidi alt ağındaki IP adresleri, ağ geçidi hizmetine ayrılır. Bazı yapılandırmalar, ağ geçidi hizmetlerine daha fazla IP adresi ayrılmasını gerektirir. Gelecekteki büyümeye ve meydana gelebilecek diğer yeni bağlantı yapılandırmalarına uyum sağlaması için ağ geçidi alt ağınızın yeterince IP adresi içerdiğinden emin olmanız gerekir. Bu nedenle, /29 kadar küçük bir ağ geçidi alt ağı oluşturmanız mümkün olsa da /27 veya daha büyük bir (/27, /26, /25 vb.) ağ geçidi alt ağı oluşturmanız önerilir. Oluşturmak istediğiniz yapılandırmanın gereksinimlerine bakın ve sahip olduğunuz ağ geçidi alt ağının bu gereksinimleri karşıladığından emin olun.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Sanal Makineleri veya rol örneklerini ağ geçidi alt ağıma dağıtabilir miyim?

Hayır.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Oluşturmadan önce VPN ağ geçidi IP adresimi alabilir miyim?

Hayır. IP adresini almak için önce ağ geçidi oluşturmanız gerekir. VPN ağ geçidinizi silip yeniden oluşturursanız IP adresi değişir.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>VPN ağ geçidim için bir Statik Genel IP adresi isteğinde bulunabilir miyim?

Hayır. Yalnızca Dinamik IP adresi ataması desteklenir. Ancak, bu durum IP adresinin VPN ağ geçidinize atandıktan sonra değiştiği anlamına gelmez. VPN ağ geçidi IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidi genel IP adresi, VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez. 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>VPN tünelimin kimliği nasıl doğrulanır?

Azure VPN PSK (Önceden Paylaşılan Anahtar) kimlik doğrulamasını kullanır. VPN tüneli oluşturduğumuzda önceden paylaşılan anahtar (PSK) oluştururuz. Otomatik olarak oluşturulan PSK’yi, Önceden Paylaştırılan Anahtar PowerShell cmdlet'ini veya REST API’sini Ayarla ile kendi istediğiniz gibi değiştirebilirsiniz.

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

### <a name="can-i-configure-force-tunneling"></a>Zorlamalı Tüneli yapılandırabilir miyim?

Evet. Bkz. [Zorlamalı tüneli yapılandırma](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Azure'da kendi VPN sunucumu kurup bu sunucuyu şirket içi ağıma bağlanmak üzere kullanabilir miyim?

Evet, Azure’de kendi VPN ağ geçitlerinizi veya sunucularınızı ister Azure Market’ten, ister kendi VPN yönlendiricilerinizi oluşturarak dağıtabilirsiniz. Şirket içi ağlarınız ve sanal ağ alt ağları arasında trafiğin düzgün yönlendirilmesini sağlamak amacıyla sanal ağınızda kullanıcı tanımlı yolları yapılandırmanız gerekir.

### <a name="gatewayports"></a>Sanal ağ geçidimde neden belirli bağlantı noktaları açıldı?

Azure altyapı iletişimi için gereklidirler. Bunlar Azure sertifikaları tarafından korunur (kilitlenir). Uygun sertifikaları olmadan, bu ağ geçitlerinin müşterileri de dahil dış varlıklar, bu uç noktalarında etkiye neden olamazlar.

Bir sanal ağ geçidi, bir NIC 'nin müşterinin özel ağına dokunduktan ve bir NIC ortak ağa bakan bir NIC 'e sahip olan çok bağlantılı bir cihaza sahiptir. Azure altyapı varlıkları uyum nedenleriyle müşterinin özel ağlarına dokunamazlar; bu nedenle altyapı iletişimi için ortak uç noktaları kullanmaları gerekir. Ortak uç noktalar düzenli aralıklarla Azure güvenlik denetimi tarafından taranır.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Ağ geçidi türleri, gereksinimleri ve verimliliği hakkında daha fazla bilgi

Daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="s2s"></a>Siteden Siteye bağlantılar ve VPN cihazları

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

## <a name="P2S"></a>Yerel Azure sertifika doğrulamasını kullanarak Noktadan Siteye

Bu bölüm Resource Manager dağıtım modeli için geçerlidir.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>RADIUS kimlik doğrulamasını kullanarak Noktadan Siteye

Bu bölüm Resource Manager dağıtım modeli için geçerlidir.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>Sanal Ağdan Sanal Ağa ve Çok Siteli bağlantılar

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

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

## <a name="ipsecike"></a>IPsec/IKE ilkesi

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>Şirket içi ve dışı bağlantısı ve VM'ler

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Sanal makinem sanal bir ağdaysa, şirket içi ve dışı bağlantım varsa VM’ye nasıl bağlanmalıyım?

Birkaç seçeneğiniz vardır. Sanal makineniz için RDP’yi etkinleştirdiyseniz, özel IP adresini kullanarak sanal makinenize bağlanabilirsiniz. Bu durumda, özel IP adresini ve bağlanmak istediğiniz bağlantı noktasını (genellikle 3389) belirtmeniz gerekir. Sanal makinenizde trafik için bağlantı noktası yapılandırmanız gerekir.

Ayrıca, aynı sanal ağda bulunan başka bir sanal makineden sanal makinenize özel IP adresi ile bağlanabilirsiniz. Sanal ağınızın dışında bir konumdan bağlanıyorsanız özel IP adresi kullanarak sanal makinenizde RDP gerçekleştiremezsiniz. Örneğin, yapılandırılmış bir Noktadan Siteye sanal ağınız varsa ve bilgisayarınızdan bağlantı kurmuyorsanız, sanal makineyi özel IP adresi ile bağlayamazsınız.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Sanal makinem şirket içi ve dışı bağlantılı bir sanal ağdaysa, VM’me ait trafiğin tümü bu bağlantıdan geçer mi?

Hayır. Bir tek, belirttiğiniz sanal ağ Yerel Ağ Ip adresi aralıklarında bulunan hedef IP’si olan trafik sanal ağ geçidinden geçer. Sanal ağ içinde yer alan bir hedef IP'ye sahip olan trafik, sanal ağ içinde kalır. Diğer trafik ortak ağlara yük dengeleyiciyle gönderilir veya zorlamalı tünel kullanılırsa, Azure VPN ağ geçidi üzerinden gönderilir.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>VM ile RDP bağlantısı sorunlarını nasıl giderebilirim?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>Sanal Ağ SSS

Ek sanal ağ ek bilgilerini [Virtual Network SSS](../virtual-network/virtual-networks-faq.md) bölümünde görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

* VPN Gateway hakkında daha fazla bilgi için bkz. [VPN Gateway Hakkında](vpn-gateway-about-vpngateways.md).
* VPN Gateway yapılandırma ayarları hakkında daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md).

**"OpenVPN", OpenVPN Inc 'nin ticari markasıdır.**
