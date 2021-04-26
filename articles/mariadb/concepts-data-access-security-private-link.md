---
title: Özel bağlantı-MariaDB için Azure veritabanı
description: Özel bağlantının, MariaDB için Azure veritabanı için nasıl çalıştığını öğrenin.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: a1b97586eeeab1d5d1917f2d1cec9e0f71e9e329
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867466"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı için özel bağlantı

Özel bağlantı, MariaDB için Azure veritabanı için özel uç noktalar oluşturmanızı sağlar ve bu nedenle Azure hizmetlerini özel sanal ağınız (VNet) içinde kullanıma sunar. Özel uç nokta, MariaDB veritabanı sunucusu için Azure veritabanı 'na, VNet 'teki diğer kaynaklar gibi bağlanmak için kullanabileceğiniz özel bir IP 'yi kullanıma sunar.

Özel bağlantı işlevselliğini destekleyen PaaS hizmetlerinin listesi için özel bağlantı [belgelerini](../private-link/index.yml)gözden geçirin. Özel uç nokta, belirli bir [sanal](../virtual-network/virtual-networks-overview.md) ağ ve alt ağ içindeki özel bir IP adresidir.

> [!NOTE]
> Özel bağlantı özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Veritabanı sunucusunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="data-exfiltration-prevention"></a>Veri sızdırmayı önleme

MariaDB için Azure veritabanı 'nda veri değişim-bir veritabanı yöneticisi gibi yetkili bir Kullanıcı, bir sistemden veri ayıklayıp onu kuruluşun dışında başka bir konuma veya sisteme taşıyabildik. Örneğin, Kullanıcı, verileri üçüncü tarafa ait bir depolama hesabına taşıtır.

MariaDB için Azure veritabanı örneğine bağlanan bir Azure VM içinde MariaDB çalışma ekranı çalıştıran bir senaryoya göz önünde bulundurun. Bu MariaDB örneği Batı ABD veri merkezinde bulunur. Aşağıdaki örnekte, ağ erişim denetimleri kullanılarak MariaDB için Azure veritabanı 'nda genel uç noktalarla erişimin nasıl sınırlandırılcağı gösterilmektedir.

* Azure hizmetlerinin kapalı çalışmasına Izin ver ' i ayarlayarak, genel uç nokta aracılığıyla Azure veritabanı için Azure veritabanı trafiğini devre dışı bırakın. Bir IP adresinin veya aralıklarının sunucuya [güvenlik duvarı kuralları](concepts-firewall-rules.md) veya [sanal ağ hizmeti uç noktaları](concepts-data-access-security-vnet.md)aracılığıyla erişmesine izin verilmediğinden emin olun.

* Yalnızca, VM 'nin özel IP adresini kullanarak MariaDB için Azure veritabanı 'na giden trafiğe izin verin. Daha fazla bilgi için [hizmet uç noktası](concepts-data-access-security-vnet.md) ve [VNET güvenlik duvarı kuralları](howto-manage-vnet-portal.md)makalesine bakın.

* Azure VM 'de, ağ güvenlik grupları (NSG 'ler) ve hizmet etiketleri kullanarak giden bağlantı kapsamını aşağıdaki şekilde daraltın:

    * Hizmet etiketi = SQL için trafiğe izin veren bir NSG kuralı belirtin. WestUs-Batı ABD yalnızca MariaDB için Azure veritabanı 'na bağlantıya izin veriliyor
    * Hizmet etiketi = SQL-tüm bölgelerde MariaDB veritabanına bağlantıları reddetmek için bir NSG kuralı (daha yüksek önceliğe sahip) belirtin</br></br>

Bu kurulumun sonunda, Azure VM yalnızca Batı ABD bölgesindeki MariaDB için Azure veritabanı 'na bağlanabilir. Ancak, bağlantı, MariaDB için tek bir Azure veritabanı ile sınırlı değildir. VM, aboneliğin parçası olmayan veritabanları da dahil olmak üzere Batı ABD bölgesindeki MariaDB için Azure veritabanı 'na yine de bağlanabilir. Yukarıdaki senaryodaki veri taşalım kapsamını belirli bir bölgeye azalttık, ancak bunu tamamen ortadan kaldırdık.</br>

Özel bağlantı ile artık özel uç noktaya erişimi kısıtlamak için NSG 'ler gibi ağ erişim denetimlerini ayarlayabilirsiniz. Tek tek Azure PaaS kaynakları, belirli özel uç noktalara eşlenir. Kötü amaçlı bir Insider yalnızca eşlenmiş PaaS kaynağına erişebilir (örneğin, MariaDB için Azure veritabanı) ve başka bir kaynak olamaz.

## <a name="on-premises-connectivity-over-private-peering"></a>Özel eşleme üzerinde şirket içi bağlantısı

Şirket içi makinelerden ortak uç noktaya bağlandığınızda, IP adresinizin sunucu düzeyinde bir güvenlik duvarı kuralı kullanılarak IP tabanlı güvenlik duvarına eklenmesi gerekir. Bu model geliştirme veya test iş yükleri için bireysel makinelere erişim sağlamak için iyi bir şekilde çalıştığından, bir üretim ortamında yönetilmesi zordur.

Özel bağlantı ile, [Express Route](https://azure.microsoft.com/services/expressroute/) (er), özel eşleme veya [VPN tüneli](../vpn-gateway/index.yml)kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilirsiniz. Daha sonra, genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP tabanlı güvenlik duvarını kullanmaz.

> [!NOTE]
> Bazı durumlarda, MariaDB için Azure veritabanı ve sanal ağ alt ağı farklı aboneliklerdedir. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelikte da **Microsoft. Dbformarıdb** kaynak sağlayıcısının kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı için özel bağlantı yapılandırma

### <a name="creation-process"></a>Oluşturma Işlemi

Özel bağlantıları etkinleştirmek için özel uç noktalar gereklidir. Bu işlem, aşağıdaki nasıl yapılır kılavuzlarından yararlanarak yapılabilir.

* [Azure portalı](howto-configure-privatelink-portal.md)
* [CLI](howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Onay Işlemi

Ağ Yöneticisi özel uç nokta (PE) oluşturduğunda, yönetici özel uç nokta bağlantısını (PEC) MariaDB için Azure veritabanı 'na yönetebilir. Ağ Yöneticisi ve DBA arasındaki bu görev ayrımı, MariaDB bağlantısı için Azure veritabanı yönetimine yardımcı olur. 

* Azure portal, MariaDB sunucu kaynağı için Azure veritabanı 'na gidin. 
    * Sol bölmedeki özel uç nokta bağlantılarını seçin
    * Tüm özel uç nokta bağlantılarının (lar) bir listesini gösterir
    * Karşılık gelen özel uç nokta (PE) oluşturuldu

![Özel uç nokta portalını seçin](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Listeden tek bir PEC seçin.

![onay bekleyen özel uç noktayı seçin](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MariaDB Sunucu Yöneticisi bir PEC 'i onaylamayı veya reddetmeyi seçebilir ve isteğe bağlı olarak kısa bir metin yanıtı ekleyebilir.

![Özel uç nokta iletisini seçin](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Onay veya reddetme sonrasında, liste, yanıt metniyle birlikte uygun durumu yansıtır

![Özel uç nokta son durumunu seçin](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı için özel bağlantı durumları kullanın


İstemciler aynı VNet 'ten özel uç noktaya, aynı bölgedeki veya bölgelerde bulunan eşlenmiş [VNET](../virtual-network/virtual-network-peering-overview.md) 'e veya bölgeler arasında [VNET-VNet bağlantısı](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aracılığıyla bağlanabilir. Ayrıca, istemciler ExpressRoute, özel eşleme veya VPN tüneli kullanarak şirket içinden bağlanabilir. Aşağıda, yaygın kullanım durumlarını gösteren basitleştirilmiş bir diyagram bulunur.

![Özel uç noktaya genel bakış ' ı seçin](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Eşlenen sanal ağdaki (VNet) bir Azure VM 'den bağlanma
Eşlenmiş VNet 'teki bir Azure VM 'den MariaDB için Azure veritabanı 'na bağlantı kurmak üzere [VNET eşlemesini](../virtual-network/tutorial-connect-virtual-networks-powershell.md) yapılandırın.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet-VNet ortamında bir Azure VM 'sinden bağlantı kurma
Farklı bir bölgede veya abonelikte bulunan bir Azure VM 'den MariaDB için Azure veritabanı bağlantısı kurmak üzere [VNET-VNET VPN Gateway bağlantısını](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırın.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN üzerinden şirket içi bir ortamdan bağlanma
Şirket içi bir ortamdan MariaDB için Azure veritabanı arasında bağlantı kurmak için seçeneklerden birini seçin ve uygulayın:

* [Noktadan siteye bağlantı](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Konumlar arası VPN bağlantısı](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute devresi](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Özel bağlantı, güvenlik duvarı kurallarıyla birleştirildi

Özel bağlantıyı güvenlik duvarı kurallarıyla birlikte kullandığınızda aşağıdaki durumlar ve sonuçlar mümkündür:

* Herhangi bir güvenlik duvarı kuralı yapılandırmazsanız, varsayılan olarak, MariaDB için Azure veritabanı 'na hiçbir trafik erişemeyecektir.

* Ortak trafiği veya bir hizmet uç noktasını yapılandırırsanız ve özel uç noktalar oluşturursanız, ilgili güvenlik duvarı kuralı tarafından farklı gelen trafik türlerine izin verilir.

* Herhangi bir ortak trafik veya hizmet uç noktası yapılandırmazsanız ve özel uç noktalar oluşturursanız, MariaDB için Azure veritabanı 'nda yalnızca özel uç noktalar aracılığıyla erişilebilir. Genel trafiği veya bir hizmet uç noktası yapılandırmazsanız, tüm onaylanan özel uç noktalar reddedildikten veya silindikten sonra, hiçbir trafik, MariaDB için Azure veritabanı 'na erişemeyecektir.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı genel erişimini reddetme

MariaDB için Azure veritabanı 'na erişmek üzere yalnızca özel uç noktalara tamamen ulaşmak istiyorsanız, veritabanı sunucusunda **ortak ağ erişimi reddetme** yapılandırmasını ayarlayarak tüm genel uç noktaları ([güvenlik duvarı kuralları](concepts-firewall-rules.md) ve [VNET hizmet uç noktaları](concepts-data-access-security-vnet.md)) ayarını devre dışı bırakabilirsiniz. 

Bu ayar *Evet* olarak ayarlandığında, MariaDB Için Azure veritabanı ile yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir. Bu ayar *Hayır* olarak ayarlandığında, istemcileri, güvenlik duvarınız veya VNET hizmeti uç noktası ayarlarınıza göre MariaDB Için Azure veritabanınıza bağlanabilir. Ayrıca, özel ağ erişiminin değeri ayarlandıktan sonra müşteriler var olan ' güvenlik duvarı kurallarını ' ve ' VNet hizmeti uç noktası kuralları ' ekleyemez ve/veya güncelleştiremez.

> [!Note]
> Bu özellik, PostgreSQL için Azure veritabanı-tek sunucu Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarını desteklediği tüm Azure bölgelerinde kullanılabilir.
>
> Bu ayarın, MariaDB için Azure veritabanınız için SSL ve TLS yapılandırmalarına etkisi yoktur.

Azure portal 'ten MariaDB için Azure veritabanınız için **ortak ağ erişimini reddetme** hakkında bilgi edinmek için, bkz. [reddetme genel ağ erişimini yapılandırma](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Sonraki adımlar

MariaDB güvenlik özellikleri için Azure veritabanı hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* MariaDB için Azure veritabanı için bir güvenlik duvarı yapılandırmak üzere bkz. [güvenlik duvarı desteği](concepts-firewall-rules.md).

* MariaDB için Azure veritabanınız için bir sanal ağ hizmeti uç noktası yapılandırma hakkında bilgi edinmek için bkz. [sanal ağlardan erişimi yapılandırma](concepts-data-access-security-vnet.md).

* MariaDB bağlantısı için Azure veritabanı 'na genel bakış için bkz. [MariaDB Için Azure veritabanı bağlantı mimarisi](concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
