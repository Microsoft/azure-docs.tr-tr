---
title: Özel bağlantı-PostgreSQL için Azure veritabanı-tek sunucu
description: Özel bağlantının PostgreSQL için Azure veritabanı-tek sunucu ile nasıl çalıştığını öğrenin.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e4b6a6090bf0e5e332a960cba8ec565df9dd55c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872277"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>PostgreSQL-Single sunucusu için Azure veritabanı için özel bağlantı

Özel bağlantı, PostgreSQL için Azure veritabanı için özel uç noktalar oluşturmanızı sağlar-tek sunucu sanal ağınızın içine (VNet) getirebilir. Özel uç nokta, bir alt ağ içinde, VNet 'teki diğer kaynaklar gibi veritabanı sunucunuza bağlanmak için kullanabileceğiniz özel bir IP 'yi kullanıma sunar.

Özel bağlantı işlevselliğini destekleyen PaaS hizmetlerinin listesi için özel bağlantı [belgelerini](../private-link/index.yml)gözden geçirin. Özel uç nokta, belirli bir [sanal](../virtual-network/virtual-networks-overview.md) ağ ve alt ağ içindeki özel bir IP adresidir.

> [!NOTE]
> Özel bağlantı özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında PostgreSQL için Azure veritabanı sunucuları için kullanılabilir. Veritabanı sunucusunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="data-exfiltration-prevention"></a>Veri sızdırmayı önleme

Data EX-PostgreSQL için Azure veritabanı tek sunucu, bir veritabanı yöneticisi gibi yetkili bir kullanıcının bir sistemden veri ayıklayabilmesini ve uygulamayı kuruluşun dışındaki başka bir konuma veya sisteme taşımasına olanak sağlar. Örneğin, Kullanıcı, verileri üçüncü tarafa ait bir depolama hesabına taşıtır.

Batı ABD ' de sağlanan PostgreSQL için Azure veritabanı 'na bağlanan bir Azure sanal makinesi (VM) içinde PGAdmin çalıştıran bir senaryoya göz önünde bulundurun. Aşağıdaki örnekte, ağ erişim denetimleri kullanılarak PostgreSQL için Azure veritabanı 'nın genel uç noktalarıyla erişimin nasıl sınırlandırması gösterilmektedir.

* *Azure HIZMETLERININ kapalı çalışmasına Izin ver* ' i ayarlayarak, tüm Azure hizmet trafiğini ortak uç nokta aracılığıyla PostgreSQL Için Azure veritabanı 'na devre dışı bırakın. Bir IP adresinin veya aralıklarının sunucuya [güvenlik duvarı kuralları](./concepts-firewall-rules.md) veya [sanal ağ hizmeti uç noktaları](./concepts-data-access-and-security-vnet.md)aracılığıyla erişmesine izin verilmediğinden emin olun.

* VM 'nin özel IP adresini kullanarak yalnızca PostgreSQL için Azure veritabanı tek sunuculu trafiğe izin verin. Daha fazla bilgi için [hizmet uç noktası](concepts-data-access-and-security-vnet.md) ve [VNET güvenlik duvarı kuralları](howto-manage-vnet-using-portal.md) makalesine bakın.

* Azure VM 'de, ağ güvenlik grupları (NSG 'ler) ve hizmet etiketleri kullanarak giden bağlantı kapsamını aşağıda gösterildiği gibi daraltın

    * Hizmet etiketi = SQL için trafiğe izin veren bir NSG kuralı belirtin *. WestUS* -yalnızca PostgreSQL Için Azure veritabanı 'na bağlantıya izin veriliyor Batı ABD tek sunucu
    * *Hizmet etiketi = SQL* -tüm bölgelerde PostgreSQL veritabanına bağlantıları reddetmek için bir NSG kuralı (daha yüksek önceliğe sahip) belirtin</br></br>

Bu kurulumun sonunda, Azure VM yalnızca Batı ABD bölgesindeki PostgreSQL için Azure veritabanı 'na bağlanabilir. Ancak, bağlantı tek bir PostgreSQL için Azure veritabanı tek sunucu ile sınırlı değildir. VM, aboneliğin parçası olmayan veritabanları da dahil olmak üzere Batı ABD bölgesindeki bir PostgreSQL için Azure veritabanı 'na yine de bağlanabilir. Yukarıdaki senaryodaki veri taşalım kapsamını belirli bir bölgeye azalttık, ancak bunu tamamen ortadan kaldırdık.</br>

Özel bağlantı ile artık özel uç noktaya erişimi kısıtlamak için NSG 'ler gibi ağ erişim denetimlerini ayarlayabilirsiniz. Tek tek Azure PaaS kaynakları, belirli özel uç noktalara eşlenir. Kötü amaçlı bir Insider yalnızca eşlenmiş PaaS kaynağına (örneğin, PostgreSQL için Azure veritabanı) ve başka bir kaynağa erişebilir.

## <a name="on-premises-connectivity-over-private-peering"></a>Özel eşleme üzerinde şirket içi bağlantısı

Şirket içi makinelerden ortak uç noktaya bağlandığınızda, IP adresinizin sunucu düzeyinde bir güvenlik duvarı kuralı kullanılarak IP tabanlı güvenlik duvarına eklenmesi gerekir. Bu model geliştirme veya test iş yükleri için bireysel makinelere erişim sağlamak için iyi bir şekilde çalıştığından, bir üretim ortamında yönetilmesi zordur.

Özel bağlantı ile, [Express Route](https://azure.microsoft.com/services/expressroute/) (er), özel eşleme veya [VPN tüneli](../vpn-gateway/index.yml)kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilirsiniz. Daha sonra, genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP tabanlı güvenlik duvarını kullanmaz.

> [!NOTE]
> Bazı durumlarda PostgreSQL için Azure veritabanı ve sanal ağ alt ağı farklı aboneliklerde bulunur. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelikte da **Microsoft. DBforPostgreSQL** kaynak sağlayıcısının kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>PostgreSQL için Azure veritabanı tek sunucu için özel bağlantıyı yapılandırma

### <a name="creation-process"></a>Oluşturma Işlemi

Özel bağlantıları etkinleştirmek için özel uç noktalar gereklidir. Bu işlem, aşağıdaki nasıl yapılır kılavuzlarından yararlanarak yapılabilir.

* [Azure portalı](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Onay Işlemi
Ağ Yöneticisi özel uç noktayı (PE) oluşturduktan sonra, PostgreSQL Yöneticisi özel uç nokta bağlantısını (PEC) PostgreSQL için Azure veritabanı 'na yönetebilir. Ağ Yöneticisi ve DBA arasındaki bu görev ayrımı, PostgreSQL için Azure veritabanı bağlantısı yönetimi için yararlıdır. 

* Azure portal PostgreSQL için Azure veritabanı sunucu kaynağına gidin. 
    * Sol bölmedeki özel uç nokta bağlantılarını seçin
    * Tüm özel uç nokta bağlantılarının (lar) bir listesini gösterir
    * Karşılık gelen özel uç nokta (PE) oluşturuldu

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Özel uç nokta portalını seçin":::

* Listeden tek bir PEC seçin.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="onay bekleyen özel uç noktayı seçin":::

* PostgreSQL Sunucu Yöneticisi bir PEC 'i onaylamayı veya reddetmeyi seçebilir ve isteğe bağlı olarak kısa bir metin yanıtı ekleyebilir.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Özel uç nokta iletisini seçin":::

* Onay veya reddetme sonrasında, liste, yanıt metniyle birlikte uygun durumu yansıtır

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Özel uç nokta son durumunu seçin":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı için özel bağlantı kullanım durumları


İstemciler aynı VNet 'ten özel uç noktaya, aynı bölgedeki veya bölgelerde bulunan eşlenmiş [VNET](../virtual-network/virtual-network-peering-overview.md) 'e veya bölgeler arasında [VNET-VNet bağlantısı](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aracılığıyla bağlanabilir. Ayrıca, istemciler ExpressRoute, özel eşleme veya VPN tüneli kullanarak şirket içinden bağlanabilir. Aşağıda, yaygın kullanım durumlarını gösteren basitleştirilmiş bir diyagram bulunur.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Özel uç noktaya genel bakış ' ı seçin":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Eşlenen sanal ağdaki (VNet) bir Azure VM 'den bağlanma
[VNET eşlemesini](../virtual-network/tutorial-connect-virtual-networks-powershell.md) , PostgreSQL Için Azure veritabanı 'na bağlantı kuracak şekilde yapılandırın-eşlenmiş VNET 'Teki BIR Azure VM 'den tek bir sunucu.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet-VNet ortamında bir Azure VM 'sinden bağlantı kurma
Bir PostgreSQL için Azure veritabanı 'na bağlantı kurmak için [VNET-VNET VPN Gateway bağlantısını](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırın-farklı bir bölgedeki veya abonelikteki BIR Azure VM 'den tek bir sunucu.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN üzerinden şirket içi bir ortamdan bağlanma
Şirket içi bir ortamdan PostgreSQL için Azure veritabanı-tek sunucu arasında bağlantı kurmak için seçeneklerden birini seçin ve uygulayın:

* [Noktadan siteye bağlantı](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Konumlar arası VPN bağlantısı](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute devresi](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Özel bağlantı, güvenlik duvarı kurallarıyla birleştirildi

Özel bağlantıyı güvenlik duvarı kurallarıyla birlikte kullandığınızda aşağıdaki durumlar ve sonuçlar mümkündür:

* Herhangi bir güvenlik duvarı kuralı yapılandırmazsanız, varsayılan olarak, PostgreSQL için Azure veritabanı 'na tek sunuculu hiçbir trafik erişemeyecektir.

* Ortak trafiği veya bir hizmet uç noktasını yapılandırırsanız ve özel uç noktalar oluşturursanız, ilgili güvenlik duvarı kuralı tarafından farklı gelen trafik türlerine izin verilir.

* Herhangi bir ortak trafik veya hizmet uç noktası yapılandırmazsanız ve özel uç noktalar oluşturursanız, PostgreSQL için Azure veritabanı tek sunuculu yalnızca özel uç noktalar aracılığıyla erişilebilir. Genel trafiği veya bir hizmet uç noktasını yapılandırmazsanız, tüm onaylanan özel uç noktalar reddedilir veya silindikten sonra, hiçbir trafik PostgreSQL için Azure veritabanı 'na tek sunucuya erişemez.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>PostgreSQL için Azure veritabanı tek sunucu için genel erişimi reddetme

Yalnızca PostgreSQL için Azure veritabanı tek sunucuya erişim için özel uç noktalara erişmek istiyorsanız, veritabanı sunucusunda **ortak ağ erişimini engelle** özelliğini ayarlayarak tüm genel uç noktaları ([güvenlik duvarı kuralları](concepts-firewall-rules.md) ve [VNET hizmet uç noktaları](concepts-data-access-and-security-vnet.md)) ayarlamayı devre dışı bırakabilirsiniz. 

Bu ayar *Evet* olarak ayarlandığında, PostgreSQL Için Azure veritabanı 'na yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir. Bu ayar *hiçbir Istemci yok* olarak ayarlandığında, güvenlik duvarınız veya VNET hizmeti uç noktası ayarına göre PostgreSQL Için Azure veritabanınıza bağlanabilir. Ayrıca, özel ağ erişiminin değeri ayarlandıktan sonra müşteriler var olan ' güvenlik duvarı kurallarını ' ve ' VNet hizmeti uç noktası kuralları ' ekleyemez ve/veya güncelleştiremez.

> [!Note]
> Bu özellik, PostgreSQL için Azure veritabanı-tek sunucu Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarını desteklediği tüm Azure bölgelerinde kullanılabilir.
>
> Bu ayarın, PostgreSQL için Azure veritabanı tek sunuculu SSL ve TLS yapılandırmalarına etkisi yoktur.

Azure portal 'den PostgreSQL için Azure veritabanı 'na yönelik **genel ağ erişimini reddetme** hakkında bilgi edinmek için, bkz. [reddetme genel ağ erişimini yapılandırma](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Sonraki adımlar

PostgreSQL için Azure veritabanı tek sunuculu güvenlik özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* PostgreSQL için Azure veritabanı tek sunuculu bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı desteği](./concepts-firewall-rules.md).

* PostgreSQL için Azure veritabanı tek sunuculu bir sanal ağ hizmeti uç noktası yapılandırma hakkında bilgi edinmek için bkz. [sanal ağlardan erişimi yapılandırma](./concepts-data-access-and-security-vnet.md).

* PostgreSQL için Azure veritabanı 'na tek sunucu bağlantısı hakkında genel bakış için bkz. [PostgreSQL Için Azure veritabanı bağlantı mimarisi](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
