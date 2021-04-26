---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953465"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP tüm Azure VPN Gateway SKU'larında destekleniyor mu?

BGP, temel SKU hariç tüm Azure VPN Gateway SKU 'Larında desteklenir.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>BGP 'yi Azure Policy VPN Gateway 'ler ile kullanabilir miyim?

Hayır, BGP yalnızca rota tabanlı VPN ağ geçitlerinde desteklenir.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Hangi ASNs (özerk sistem numaraları) kullanabilirim?

Hem şirket içi ağlarınız hem de Azure sanal ağlarınız için kendi genel Ilişkinizi veya özel ans 'leri kullanabilirsiniz. Azure veya ıANA tarafından ayrılan aralıkları kullanamazsınız.

Aşağıdaki ASNs 'ler Azure veya ıANA tarafından ayrılmıştır:
* Azure tarafından ayrılan ASNs:

  * Ortak ASN’ler: 8074, 8075, 12076
  * Özel ASN’ler: 65515, 65517, 65518, 65519, 65520
* [IANA tarafından ayrılmış](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)ASNs:

  * 23456, 64496-64511, 65535-65551 and 429496729

Azure VPN ağ geçitlerine bağlanırken şirket içi VPN cihazlarınız için bu ASNs belirtemezsiniz.

### <a name="can-i-use-32-bit-4-byte-asns"></a>32 bit (4 baytlık) ASNs kullanabilir miyim?

Evet, VPN Gateway artık 32-bit (4 baytlık) ASNs 'yi destekliyor. ASN 'yi ondalık biçimde kullanarak yapılandırmak için PowerShell, Azure CLı veya Azure SDK 'yı kullanın.

### <a name="what-private-asns-can-i-use"></a>Hangi özel ASNs 'yi kullanabilirim?

Kullanılabilen özel ASNs aralıkları şunlardır:

* 64512-65514 ve 65521-65534

Bu ASNs 'ler ıANA veya Azure tarafından kullanılmak üzere ayrılmamıştır ve bu nedenle Azure VPN Gateway 'e atamak için kullanılabilir.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>BGP eşi IP 'si için hangi adresi VPN Gateway kullanır?

Varsayılan olarak, VPN Gateway Active-standby VPN Gateway 'ler için *Gatewaysubnet* aralığından tek bir IP adresi ayırır veya etkin-etkin VPN ağ geçitleri IÇIN iki IP adresi ayırır. VPN ağ geçidini oluşturduğunuzda bu adresler otomatik olarak ayrılır. PowerShell kullanarak veya Azure portal bularak, ayrılmış gerçek BGP IP adresini alabilirsiniz. PowerShell 'de **Get-AzVirtualNetworkGateway**' i kullanın ve **Bgppeeringaddress** özelliğini arayın. Azure portal, **ağ geçidi yapılandırması** SAYFASıNDA **BGP ASN özelliğini yapılandırma** ' ya bakın.

Şirket içi VPN yönlendiricileriniz, BGP IP adresleri olarak **APIPA** IP adreslerini (169.254. x. x) kullanıyorsa, Azure VPN ağ geçidiniz üzerinde ek BIR **Azure APIPA BGP IP adresi** belirtmeniz gerekir. Azure VPN Gateway, yerel ağ geçidinde belirtilen şirket içi APIPA BGP eşi veya APIPA olmayan, şirket içi BGP eşi için özel IP adresi ile kullanılacak APIPA adresini seçer. Daha fazla bilgi için bkz. [BGP 'Yi yapılandırma](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN cihazımın BGP eşi IP adresleri için gereksinimler nelerdir?

Şirket içi BGP eş adresiniz, VPN cihazınızın genel IP adresiyle veya VPN ağ geçidinin sanal ağ adresi alanından aynı olmamalıdır. BGP eşi IP 'niz için VPN cihazında farklı bir IP adresi kullanın. Bu, cihazdaki geri döngü arabirimine atanmış bir adres olabilir (normal bir IP adresi veya bir APIPA adresi). Cihazınız BGP için bir APIPA adresi kullanıyorsa, [BGP 'Yi yapılandırma](../articles/vpn-gateway/bgp-howto.md)bölümünde açıklandığı gibi, Azure VPN ağ GEÇIDINIZDE BIR APIPA BGP IP adresi belirtmeniz gerekir. Bu adresi, konumu temsil eden karşılık gelen yerel ağ geçidinde belirtin.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP 'yi kullandığım sırada yerel ağ geçidi için adres ön ekleri olarak ne belirtmem gerekir?

> [!IMPORTANT]
>
>Bu, daha önce belgelenen gereksinimin bir değişikidir. Bir bağlantı için BGP kullanıyorsanız, karşılık gelen yerel ağ geçidi kaynağı için **Adres alanı** alanını boş bırakın. Azure VPN Gateway, IPSec tüneli üzerinden şirket içi BGP eşi IP 'ye dahili olarak bir ana bilgisayar yolu ekler. **Adres alanı** alanına/32 yolunu eklemeyin. Bu, yedekli ve şirket içi VPN cihazı BGP IP 'si olarak bir APIPA adresi kullanırsanız, bu alana eklenemez. **Adres alanı** alanına başka ön ekler eklersenız, BGP aracılığıyla öğrenilen yolların yanı sıra Azure VPN Gateway 'e statik yollar olarak eklenirler.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Aynı ASN 'yi hem şirket içi VPN ağları hem de Azure sanal ağları için kullanabilir miyim?

Hayır, şirket içi ağlarınız ve Azure sanal ağlarınız arasında, bunları BGP ile birlikte bağlıyorsanız, farklı ASNs atamanız gerekir. Azure VPN ağ geçitlerinde, şirket içi bağlantınız için BGP 'nin etkin olup olmadığı için varsayılan ASN 65515 ' dir. VPN ağ geçidini oluştururken farklı bir ASN atayarak bu varsayılanı geçersiz kılabilirsiniz veya ağ geçidi oluşturulduktan sonra ASN 'yi değiştirebilirsiniz. Şirket içi güvenlerinizi ilgili Azure yerel ağ geçitlerine atamanız gerekir.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN Gateway’ler bana hangi adres öneklerini tanıtacak?

Ağ geçitleri, şirket içi BGP cihazlarınıza aşağıdaki yolları duyurur:

* Sanal ağ adresi ön ekleri.
* Azure VPN Gateway 'e bağlı her yerel ağ geçidi için adres ön ekleri.
* Azure VPN Gateway 'e bağlı diğer BGP eşleme oturumlarından öğrenilen yollar, herhangi bir sanal ağ öneki ile çakışan varsayılan yol veya rotalar dışında.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Azure VPN Gateway 'de kaç önek tanıtırım?

Azure VPN Gateway en fazla 4000 ön eki destekler. Ön ek sayısı bu sınırı aşarsa BGP oturumu düşürülür.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Varsayılan yolu (0.0.0.0/0) Azure VPN ağ geçitlerine tanıtabilir miyim?

Evet. Bunun, tüm sanal ağ çıkış trafiğini şirket içi sitenize doğru zoryacağını unutmayın. Ayrıca, sanal ağ VM 'lerinin internet 'ten VM 'lere, RDP veya SSH gibi genel iletişimi kabul etmesini engeller.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Sanal ağ öneklerim olarak tam önekleri tanıtımda yapabilir miyim?

Hayır, sanal ağ adresi öneklerinizden herhangi biri ile aynı önekleri duyurmak, Azure tarafından engellenecek veya filtrelenecektir. Bununla birlikte, sanal ağınızın içinde sahip olduğunuz bir üst küme olan bir ön ek tanıtabilirsiniz. 

Örneğin, sanal ağınız 10.0.0.0/16 adres alanını kullandıysanız 10.0.0.0/8 ' i tanıtabilirsiniz. Ancak 10.0.0.0/16 veya 10.0.0.0/24 ' i duyuramıyoruz.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Sanal ağlar arasındaki bağlantılarıma sahip BGP kullanabilir miyim?

Evet, BGP 'yi şirket içi bağlantılar ve sanal ağlar arasındaki bağlantılar için kullanabilirsiniz.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Azure VPN Gateway’lerim için BGP’yi BGP olmayan bağlantılarla karıştırabilir miyim?

Evet, aynı Azure VPN Gateway için BGP ve BGP olmayan bağlantıları karıştırabilirsiniz.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN Gateway BGP geçiş yönlendirmesini destekliyor mu?

Evet, Azure VPN ağ geçitlerinin diğer BGP eşlerine varsayılan yollar bildirmeyen özel durum ile BGP aktarma yönlendirmesi desteklenir. Birden çok Azure VPN ağ geçidi arasında geçiş yönlendirmeyi etkinleştirmek için sanal ağlar arasındaki tüm ara bağlantılarda BGP 'yi etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. [BGP hakkında](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN ağ geçidi ve şirket içi ağlarım arasında birden fazla tünele sahip olabilir miyim?

Evet, bir Azure VPN ağ geçidi ve şirket içi ağınız arasında birden çok siteden siteye (S2S) VPN tüneli oluşturabilirsiniz. Bu tünellerin tümü, Azure VPN ağ geçitleriniz için Toplam Tünel sayısına göre sayılır ve her iki tünelde BGP 'yi etkinleştirmeniz gerekir.

Örneğin, Azure VPN ağ geçidiniz ile şirket içi ağlarınızdan biri arasında iki yedekli Tüneliniz varsa, Azure VPN ağ geçidinizin toplam kotasının 2 tünellerini tüketir.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>BGP ile iki Azure sanal ağı arasında birden fazla tünele sahip olabilir miyim?

Evet, ancak sanal ağ geçitlerinden en az birinin etkin-etkin yapılandırmada olması gerekir.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Azure ExpressRoute ve S2S VPN birlikte bulunma yapılandırmasında S2S VPN için BGP kullanabilir miyim?

Evet.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP eşdeğer oturumu için şirket içi VPN cihazıma ne eklemeliyim?

VPN cihazınıza Azure BGP eş IP adresinin bir konak yolunu ekleyin. Bu yol, IPSec S2S VPN tünelini işaret eder. Örneğin, Azure VPN eş IP 'si 10.12.255.30 ise, VPN cihazınızda eşleşen IPSec tüneli arabiriminin bir sonraki atlama arabirimine sahip 10.12.255.30 için bir ana bilgisayar yolu eklersiniz.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Sanal ağ geçidi, BGP ile S2S bağlantıları için BFD 'yi destekliyor mu?

Hayır. Çift yönlü Iletme algılaması (BFD) BGP ile birlikte kullanabileceğiniz ve standart BGP "keepkeep" kullanarak daha hızlı komşu kesinti süresini tespit eden bir protokoldür. BFD, genel İnternet veya geniş alan ağı bağlantılarında değil, LAN ortamlarında çalışmak için tasarlanan alt saniye zamanlayıcıları kullanır.

Genel internet üzerinden yapılan bağlantılar için, belirli paketlerin gecikilmesi veya hatta bırakılması olağandışı olmadığından, bu ısrarlı zamanlayıcılar için bir tutarsızlık eklenebilir. Bu kararsızlık, yolların BGP tarafından sönmesine neden olabilir. Alternatif olarak, şirket içi cihazınızı varsayılan, 60-ikinci "KeepAlive" aralığından ve 180 saniyelik ayrı tutma zamanlayıcıdan daha düşük zamanlayıcılar ile yapılandırabilirsiniz. Bu, daha hızlı yakınsama süresine neden olur.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Azure VPN ağ geçitleri BGP eşleme oturumları veya bağlantıları başlatır mi?

Ağ Geçidi, VPN ağ geçitlerinde özel IP adresleri kullanılarak yerel ağ geçidi kaynaklarında belirtilen şirket içi BGP eşi IP adreslerine BGP eşleme oturumları başlatacak. Bu, şirket içi BGP IP adreslerinin APIPA aralığında veya normal özel IP adreslerinde olup olmadığına bakılmaksızın olur. Şirket içi VPN cihazlarınız APIPA adreslerini BGP IP 'si olarak kullanıyorsa, bağlantıları başlatmak için BGP konuşmacıyı yapılandırmanız gerekir.