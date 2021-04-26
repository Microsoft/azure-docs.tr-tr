---
title: Özel bağlantı-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı 'nda özel bağlantının nasıl çalıştığını öğrenin.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e239b6b00c5a5e993834a10fca30de02b9f715ff
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065523"
---
# <a name="private-link-for-azure-database-for-mysql"></a>MySQL için Azure Veritabanı’na Yönelik Özel Bağlantı

Özel Bağlantı Azure’daki çeşitli PaaS hizmetlerine özel bir uç nokta üzerinden bağlanmanızı sağlar. Azure Özel Bağlantı, Azure hizmetlerini özel sanal ağınıza (VNet) getirir. Sanal ağdaki diğer kaynaklar gibi PaaS kaynaklarına da özel IP adresini kullanarak erişebilirsiniz.

Özel bağlantı işlevselliğini destekleyen PaaS hizmetlerinin listesi için özel bağlantı [belgelerini](../private-link/index.yml)gözden geçirin. Özel uç nokta, belirli bir [sanal](../virtual-network/virtual-networks-overview.md) ağ ve alt ağ içindeki özel bir IP adresidir.

> [!NOTE]
> Özel bağlantı özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında MySQL için Azure veritabanı sunucuları için kullanılabilir. Veritabanı sunucusunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="data-exfiltration-prevention"></a>Veri sızdırmayı önleme

Veri EX-MySQL için Azure veritabanı 'nda, bir veritabanı yöneticisi gibi yetkili bir Kullanıcı, bir sistemden veri ayıklayabilip onu kuruluşun dışında başka bir konuma veya sisteme taşıyabildikdedir. Örneğin, Kullanıcı, verileri üçüncü tarafa ait bir depolama hesabına taşıtır.

Batı ABD ' de sağlanan bir MySQL için Azure veritabanı sunucusuna bağlanan bir Azure sanal makinesi (VM) içinde MySQL çalışma sunucusunu çalıştıran bir senaryoya göz önünde bulundurun. Aşağıdaki örnekte, ağ erişim denetimleri kullanılarak MySQL için Azure veritabanı 'ndaki genel uç noktalarla erişimin nasıl sınırlandıralınacağını gösterilmektedir.

* *Azure HIZMETLERININ kapalı çalışmasına Izin ver* ayarını yaparak, genel uç nokta aracılığıyla Azure veritabanı Için Azure veritabanı trafiğini devre dışı bırakın. Bir IP adresinin veya aralıklarının sunucuya [güvenlik duvarı kuralları](./concepts-firewall-rules.md) veya [sanal ağ hizmeti uç noktaları](./concepts-data-access-and-security-vnet.md)aracılığıyla erişmesine izin verilmediğinden emin olun.

* Yalnızca VM 'nin özel IP adresini kullanarak MySQL için Azure veritabanı 'na giden trafiğe izin verin. Daha fazla bilgi için [hizmet uç noktası](concepts-data-access-and-security-vnet.md) ve [VNET güvenlik duvarı kuralları](howto-manage-vnet-using-portal.md)makalesine bakın.

* Azure VM 'de, ağ güvenlik grupları (NSG 'ler) ve hizmet etiketleri kullanarak giden bağlantı kapsamını aşağıda gösterildiği gibi daraltın

    * Hizmet etiketi = SQL için trafiğe izin veren bir NSG kuralı belirtin *. WestUs* -yalnızca Batı ABD Içindeki MySQL Için Azure veritabanı 'na bağlantıya izin veriliyor
    * *Hizmet etiketi = SQL* -tüm bölgelerde MySQL Için Azure veritabanı 'na güncelleştirme bağlantılarını reddetmek için bir NSG kuralı (daha yüksek önceliğe sahip) belirtin</br></br>

Bu kurulumun sonunda, Azure VM yalnızca Batı ABD bölgesindeki MySQL için Azure veritabanı 'na bağlanabilir. Ancak, bağlantı, MySQL için tek bir Azure veritabanıyla sınırlı değildir. VM, aboneliğin parçası olmayan veritabanları da dahil olmak üzere Batı ABD bölgesindeki MySQL için Azure veritabanı 'na yine de bağlanabilir. Yukarıdaki senaryodaki veri taşalım kapsamını belirli bir bölgeye azalttık, ancak bunu tamamen ortadan kaldırdık.</br>

Özel bağlantı ile artık özel uç noktaya erişimi kısıtlamak için NSG 'ler gibi ağ erişim denetimlerini ayarlayabilirsiniz. Tek tek Azure PaaS kaynakları, belirli özel uç noktalara eşlenir. Kötü amaçlı bir Insider, eşlenen PaaS kaynağına (örneğin, MySQL için Azure veritabanı) ve başka bir kaynağa erişebilir.

## <a name="on-premises-connectivity-over-private-peering"></a>Özel eşleme üzerinde şirket içi bağlantısı

Şirket içi makinelerden ortak uç noktaya bağlandığınızda, IP adresinizin sunucu düzeyinde bir güvenlik duvarı kuralı kullanılarak IP tabanlı güvenlik duvarına eklenmesi gerekir. Bu model geliştirme veya test iş yükleri için bireysel makinelere erişim sağlamak için iyi bir şekilde çalıştığından, bir üretim ortamında yönetilmesi zordur.

Özel bağlantı ile, [Express Route](https://azure.microsoft.com/services/expressroute/) (er), özel eşleme veya [VPN tüneli](../vpn-gateway/index.yml)kullanarak özel uç noktaya şirket içi erişimi etkinleştirebilirsiniz. Daha sonra, genel uç nokta aracılığıyla tüm erişimi devre dışı bırakabilir ve IP tabanlı güvenlik duvarını kullanmaz.

> [!NOTE]
> Bazı durumlarda, MySQL için Azure veritabanı ve VNet alt ağı farklı aboneliklerde bulunur. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelikte da **Microsoft. Dbformyısql** kaynak sağlayıcısının kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="configure-private-link-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için özel bağlantı yapılandırma

### <a name="creation-process"></a>Oluşturma Işlemi

Özel bağlantıları etkinleştirmek için özel uç noktalar gereklidir. Bu işlem, aşağıdaki nasıl yapılır kılavuzlarından yararlanarak yapılabilir.

* [Azure portalı](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Onay Işlemi
Ağ Yöneticisi özel uç nokta (PE) oluşturduğunda, MySQL yöneticisi özel uç nokta bağlantısını (PEC) MySQL için Azure veritabanı 'na yönetebilir. Ağ Yöneticisi ile DBA arasındaki görev ayrımı, MySQL için Azure veritabanı bağlantısı yönetimi için yararlıdır. 

* Azure portal MySQL için Azure veritabanı sunucu kaynağına gidin. 
    * Sol bölmedeki özel uç nokta bağlantılarını seçin
    * Tüm özel uç nokta bağlantılarının (lar) bir listesini gösterir
    * Karşılık gelen özel uç nokta (PE) oluşturuldu

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Özel uç nokta portalını seçin":::

* Listeden tek bir PEC seçin.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="onay bekleyen özel uç noktayı seçin":::

* MySQL Server Yöneticisi bir PEC 'i onaylamayı veya reddetmeyi seçebilir ve isteğe bağlı olarak kısa bir metin yanıtı ekleyebilir.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Özel uç nokta iletisini seçin":::

* Onay veya reddetme sonrasında, liste, yanıt metniyle birlikte uygun durumu yansıtır

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Özel uç nokta son durumunu seçin":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nın özel bağlantı durumlarını kullanın

İstemciler aynı VNet 'ten özel uç noktaya, aynı bölgedeki veya bölgelerde bulunan eşlenmiş [VNET](../virtual-network/virtual-network-peering-overview.md) 'e veya bölgeler arasında [VNET-VNet bağlantısı](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aracılığıyla bağlanabilir. Ayrıca, istemciler ExpressRoute, özel eşleme veya VPN tüneli kullanarak şirket içinden bağlanabilir. Aşağıda, yaygın kullanım durumlarını gösteren basitleştirilmiş bir diyagram bulunur.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Özel uç noktaya genel bakış ' ı seçin":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Eşlenen sanal ağdaki (VNet) bir Azure VM 'den bağlanma
Eşlenen VNet 'teki bir Azure VM 'den MySQL için Azure veritabanı 'na bağlantı kurmak üzere [VNET eşlemesini](../virtual-network/tutorial-connect-virtual-networks-powershell.md) yapılandırın.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet-VNet ortamında bir Azure VM 'sinden bağlantı kurma
Farklı bir bölgedeki veya abonelikteki bir Azure VM 'den MySQL için Azure veritabanı bağlantısı kurmak üzere [VNET-VNET VPN Gateway bağlantısını](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırın.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN üzerinden şirket içi bir ortamdan bağlanma
Şirket içi bir ortamdan MySQL için Azure veritabanı 'na bağlantı kurmak için seçeneklerden birini seçin ve uygulayın:

* [Noktadan siteye bağlantı](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Konumlar arası VPN bağlantısı](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute devresi](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Özel bağlantı, güvenlik duvarı kurallarıyla birleştirildi

Özel bağlantıyı güvenlik duvarı kurallarıyla birlikte kullandığınızda aşağıdaki durumlar ve sonuçlar mümkündür:

* Herhangi bir güvenlik duvarı kuralı yapılandırmadıysanız, varsayılan olarak, MySQL için Azure veritabanı 'na hiçbir trafik erişemeyecektir.

* Ortak trafiği veya bir hizmet uç noktasını yapılandırırsanız ve özel uç noktalar oluşturursanız, ilgili güvenlik duvarı kuralı tarafından farklı gelen trafik türlerine izin verilir.

* Herhangi bir ortak trafik veya hizmet uç noktası yapılandırmazsanız ve özel uç noktalar oluşturursanız, MySQL için Azure veritabanı 'nda yalnızca özel uç noktalar aracılığıyla erişilebilir. Genel trafiği veya bir hizmet uç noktası yapılandırmazsanız, tüm onaylanan özel uç noktalar reddedildikten veya silindikten sonra, MySQL için Azure veritabanı 'na hiçbir trafik erişemez.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı genel erişimini reddetme

MySQL için Azure veritabanı 'na erişmek üzere yalnızca özel uç noktalara bağlanmak istiyorsanız, veritabanı sunucusunda **ortak ağ erişimini engelle** özelliğini ayarlayarak tüm genel uç noktaları (yani [güvenlik duvarı kuralları](concepts-firewall-rules.md) ve [VNET hizmet uç noktaları](concepts-data-access-and-security-vnet.md)) ayarını devre dışı bırakabilirsiniz. 

Bu ayar *Evet* olarak ayarlandığında, MySQL Için Azure veritabanı 'na yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir. Bu ayar *Hayır* olarak ayarlandığında, istemciler, güvenlik duvarınız veya VNET hizmeti uç noktası ayarlarınıza göre MySQL Için Azure veritabanınıza bağlanabilir. Ayrıca, özel ağ erişiminin değeri ayarlandıktan sonra müşteriler var olan ' güvenlik duvarı kurallarını ' ve ' VNet hizmeti uç noktası kuralları ' ekleyemez ve/veya güncelleştiremez.

> [!Note]
> Bu özellik, MySQL için Azure veritabanı-tek sunucu Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarını desteklediği tüm Azure bölgelerinde kullanılabilir.
>
> Bu ayarın, MySQL için Azure veritabanı için SSL ve TLS yapılandırması üzerinde hiçbir etkisi yoktur.

Azure portal 'ten MySQL için Azure veritabanı 'na **genel ağ erişimini reddetme** hakkında bilgi edinmek için, bkz. [reddetme genel ağ erişimini yapılandırma](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Sonraki adımlar

MySQL için Azure veritabanı güvenlik özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* MySQL için Azure veritabanı 'nda bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı desteği](./concepts-firewall-rules.md).

* MySQL için Azure veritabanınız için bir sanal ağ hizmeti uç noktası yapılandırma hakkında bilgi edinmek için bkz. [sanal ağlardan erişimi yapılandırma](./concepts-data-access-and-security-vnet.md).

* MySQL bağlantısı için Azure veritabanı 'na genel bakış için bkz. [MySQL Için Azure veritabanı bağlantı mimarisi](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
