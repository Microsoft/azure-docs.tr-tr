---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e3f996ab2a42057198368759c75f10e911d5f54
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68936919"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP tüm Azure VPN Gateway SKU'larında destekleniyor mu?
Hayır, BGP Azure **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** ve **HighPerformance** VPN ağ geçitlerinde desteklenir. **Temel** SKU DESTEKLENMEZ.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Azure İlke Temelli VPN ağ geçitleriyle BGP kullanabilir miyim?
Hayır, BGP yalnızca Rota Temelli VPN Gateway’lerinde desteklenir.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Özel ASN’ler (Otonom Sistem Numaralarını) kullanabilir miyim?
Evet, kendi ortak ASN’lerinizi veya özel ASN’lerinizi hem şirket içi ağlarda, hem de Azure sanal ağlarda kullanabilirsiniz.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>32 bit ASN’ler (Otonom Sistem Numaraları) kullanabilir miyim?
Hayır, Azure VPN Gateway’ler şu anda 16 bit ASN’leri destekler.

### <a name="are-there-asns-reserved-by-azure"></a>Azure tarafından ayrılan bir ASN var mı?
Evet, aşağıdaki ASN’ler iç ve dış eşlemeler için Azure tarafından ayrılmıştır:

* Genel ASNs 'ler: 8074, 8075, 12076
* Özel ASNs: 65515, 65517, 65518, 65519, 65520

Bu ASN’leri Azure VPN ağ geçitlerine bağlanırken şirket içi VPN cihazlarınız için belirtemezsiniz.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Kullanamayacağım başka ASN var mı?
Evet, aşağıdaki ASN'ler [IANA tarafından ayrılmıştır](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ve Azure VPN ağ geçidinizde yapılandırılamaz:

23456, 64496-64511, 65535-65551 and 429496729

### <a name="what-private-asns-can-i-use"></a>Hangi özel ASNs 'yi kullanabilirim?
Kullanılabilecek kullanılabilir özel ASNs aralığı şunlardır:

* 64512-65514, 65521-65534

Bu ASNs 'ler ıANA veya Azure tarafından kullanılmak üzere ayrılmamıştır ve bu nedenle Azure VPN Gateway atamak için kullanılabilir.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Aynı ASN’yi hem şirket içi VPN ağlarında, hem de Azure VNet'lerde kullanabilir miyim?
Hayır, BGP’yle bunlara birlikte bağlanıyorsanız, şirket içi ağlar ve Azure VNet'ler arasında farklı ASN’ler atamanız gerekir. Şirket içi ve dışı karışık bağlantınız için BGP'nin etkin olup olmamasından bağımsız olarak Azure VPN Ağ Geçitlerine varsayılan 65515 ASN'si atanmıştır. VPN Gateway oluştururken farklı ASN atayarak bu varsayılan değeri geçersiz kılabilir veya ağ geçidini oluşturduktan sonra ASN’yi değiştirebilirsiniz. Şirket içi ASN’lerinizi ilgili Azure Yerel Ağ Geçitlerine atamanız gerekir.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN Gateway’ler bana hangi adres öneklerini tanıtacak?
Azure VPN Gateway şirket içi BGP cihazlarınıza şu rotaları tanıtacaktır:

* VNet adres önekleriniz
* Azure VPN Gateway’e bağlı her Yerel Ağ Geçidi için adres önekleri
* Azure VPN Gateway’e bağlı diğer BGP eşdeğer oturumlarından öğrenilen rotalar; **varsayılan rota ve herhangi bir VNet önekiyle çakışan rotalar dışında**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Azure VPN Gateway 'e kaç önek tanıtırım?
En fazla 4000 ön eki destekliyoruz. Ön ek sayısı bu sınırı aşarsa BGP oturumu düşürülür.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Varsayılan yolu (0.0.0.0/0) Azure VPN ağ geçitlerine tanıtabilir miyim?
Evet.

Bunun yapılması, tüm sanal ağ çıkış trafiğini şirket içi sitenize yönelmeye zorlar ve sanal ağ VM’lerinin, İnternet’ten VM’lere RDP veya SSH gibi doğrudan İnternet’ten gelen ortak iletişimi kabul etmesini engeller.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Tam ön eklerimi Sanal Ağ ön eklerim olarak tanıtabilir miyim?

Hayır, aynı ön eklerin Sanal Ağ adresi ön eklerinden biri olarak tanıtılması Azure platformu tarafından engellenir veya filtrelenir. Ancak, Sanal Ağınızda bulunanların üst kümesi olan bir ön ek tanıtabilirsiniz. 

Örneğin, sanal ağınızda 10.0.0.0/16 adres alanı kullanılıyorsa 10.0.0.0/8 olarak tanıtabilirsiniz. Ancak 10.0.0.0/16 veya 10.0.0.0/24 tanıtamazsınız.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>VNet - VNet bağlantılarımla BGP kullanabilir miyim?
Evet, BGP’yi hem şirket içi bağlantılar için, hem de VNet - VNet bağlantıları için kullanabilirsiniz.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Azure VPN Gateway’lerim için BGP’yi BGP olmayan bağlantılarla karıştırabilir miyim?
Evet, aynı Azure VPN Gateway için BGP ve BGP olmayan bağlantıları karıştırabilirsiniz.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN Gateway BGP transit rotasını destekliyor mu?
Evet, BGP transit rotası desteklense de, özel durum olarak Azure VPN Gateway’lerinin varsayılan rotaları diğer BGP eşdeğerlerine **TANITMAMALARI** geçerlidir. Transit rotayı birden fazla Azure VPN Gateway’de etkinleştirmek için tüm ara VNet - VNet bağlantılarında BGP’yi etkinleştirmelisiniz. Daha fazla bilgi için bkz. [BGP hakkında](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN Gateway ve şirket içi ağım arasında birden fazla tünelim olabilir mi?
Evet, bir Azure VPN Gateway ve şirket içi ağınız arasında birden fazla S2S tüneli kurabilirsiniz. Bu tünellerin tümünün Azure VPN Gateway’lerinize yönelik tünellerin toplam sayısına karşılık hesaplanacağını ve iki tünelde de BGP özelliğini etkinleştirmeniz gerektiğini lütfen unutmayın.

Örneğin, Azure VPN Gateway'iniz ve şirket içi ağınız arasında iki yedekli tüneliniz varsa, Azure VPN Gateway'inizin toplam kotasından (Standart için 10, Yüksek Performanslı için 30) 2 tünel kullanacaktır.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>BGP bulunan iki Azure VNet arasında birden çok tünelim olabilir mi?
Evet, ancak sanal ağ geçitlerinden en az birinin etkin-etkin yapılandırmada olması gerekir.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>BGP’yi ExpressRoute/S2S VPN birlikte varolma yapılandırmasında S2S VPN için kullanabilir miyim?
Evet. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN Gateway BGP Eşdeğer IP’si için hangi adresi kullanıyor?
Azure VPN ağ geçidi, etkin-etkin VPN ağ geçitleri için GatewaySubnet aralığından tek bir IP adresi ayırır veya etkin-etkin VPN ağ geçitleri için iki IP adresi ayırır. PowerShell (Get-AzVirtualNetworkGateway) kullanarak ayrılan gerçek BGP IP adreslerini alabilir, "bgpPeeringAddress" özelliğini arayabilir) veya Azure portal (ağ geçidi yapılandırma sayfasında "BGP ASN 'yi Yapılandır" özelliğinin altında).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN cihazımdaki BGP Eşdeğer IP adreslerinin gereksinimleri nelerdir?
Şirket içi BGP eş adresiniz, VPN CIHAZıNıZıN genel IP adresi veya VPN Gateway VNET adres alanı ile aynı olmamalıdır. BGP Eşdeğer IP’si için VPN cihazında farklı bir IP adresi kullanın. Bu, cihaz üzerindeki geri döngü arabirimine atanmış bir adres olabilir, ancak bir APIPA (169.254.x.x) adresi olamayacağını da hatırlatmak isteriz. Bu adresi, konumu temsil eden ilgili Yerel Ağ Geçidi’nde belirtin.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP kullandığımda Yerel Ağ Geçidi için adres önekim olarak ne belirtmeliyim?
Azure Yerel Ağ Geçidi, şirket içi ağ için başlangıç adresi öneklerini belirtir. BGP ile, BGP Eşdeğer IP adresinizin konak önekini (/32 önek) bu şirket içi ağın adres alanı olarak ayırmalısınız. BGP Eşdeğer IP’niz 10.52.255.254 olursa, bu şirket içi ağda temsil edilen Yerel Ağ Geçidine ait "10.52.255.254/32" olarak belirtmelisiniz. Azure VPN Gateway’in S2S VPN tüneli aracılığıyla BGP oturumunu başlatmasını sağlamak içindir.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP eşdeğer oturumu için şirket içi VPN cihazıma ne eklemeliyim?
VPN cihazınızdaki Azure BGP Eşdeğer IP adresinin, IPsec S2S VPN tüneline yönlenmiş konak rotasını eklemelisiniz. Örneğin, Azure VPN Eşdeğer IP’si "10.12.255.30" olursa, "10.12.255.30" için VPN cihazınızdaki eşleşen IPsec tüneli arabiriminin sonraki durak arabirimine sahip bir konak rotası eklemelisiniz.
