---
title: Azure sanal ağ trafiği yönlendirme
titlesuffix: Azure Virtual Network
description: Azure’ın sanal ağ trafiğini nasıl yönlendirdiğini ve bu yönlendirmeyi nasıl özelleştirebileceğinizi öğrenin.
services: virtual-network
documentationcenter: na
author: malopMSFT
manager: ''
editor: v-miegge
tags: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: b26f876fbe07b1667a579fc040562f1d6ee8a85e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871795"
---
# <a name="virtual-network-traffic-routing"></a>Sanal ağ trafiğini yönlendirme

Azure, şirket içi ve İnternet kaynakları arasındaki trafiğin Azure tarafından nasıl yönlendirdiği hakkında bilgi edinin. Azure, bir Azure sanal ağı içindeki her alt ağ için bir yol tablosu oluşturur ve sistemin varsayılan yollarını tabloya ekler. Sanal ağlar ve alt ağlar hakkında daha fazla bilgi için [Sanal ağa genel bakış](virtual-networks-overview.md) makalesini inceleyin. Azure’ın sistem yollarından bazılarını [özel yollar](#custom-routes) ile geçersiz kılabilir ve yol tablolarına özel yollar ekleyebilirsiniz. Azure bir alt ağdan giden trafiği, alt ağın yol tablosundaki yolları temel alarak yönlendirir.

## <a name="system-routes"></a>Sistem yolları

Azure, sistem yollarını otomatik olarak oluşturur ve bir sanal ağ içindeki her alt ağa bu yolları atar. Sistem yolları oluşturma ya da sistem yollarını kaldırma seçeneğiniz yoktur, ancak bazı sistem yollarını [özel yollar](#custom-routes) ile geçersiz kılabilirsiniz. Azure her alt ağ için varsayılan sistem yollarını oluşturur ve belirli Azure özelliklerini kullandığınızda, belirli alt ağlara ya da tüm alt ağlara başka [isteğe bağlı varsayılan yollar](#optional-default-routes) ekler.

### <a name="default"></a>Varsayılan

Her yol, bir adres ön eki ve sonraki atlama türünü içerir. Alt ağdan ayrılan trafik, bir yolun adres ön eki içinde IP adresine gönderildiğinde ön eki içeren yol, Azure’ın kullandığı yoldur. Birden fazla yolun aynı ön eki içermesi veya ön eklerin çakışması durumunda [Azure’ın nasıl yol seçtiği](#how-azure-selects-a-route) hakkında daha fazla bilgi edinin. Bir sanal ağ oluşturulduğunda Azure, sanal ağ içindeki her alt ağ için aşağıdaki varsayılan sistem yollarını otomatik olarak oluşturur:

|Source |Adres ön ekleri                                        |Sonraki atlama türü  |
|-------|---------                                               |---------      |
|Varsayılan|Sanal ağa özel                           |Sanal ağ|
|Varsayılan|0.0.0.0/0                                               |Internet       |
|Varsayılan|10.0.0.0/8                                              |Yok.           |
|Varsayılan|192.168.0.0/16                                          |Yok.           |
|Varsayılan|100.64.0.0/10                                           |Yok.           |

Önceki tabloda listelenen sonraki atlama türleri, Azure’ın listelenen adres ön ekine yönelik giden trafiği nasıl yönlendirdiğini göstermektedir. Sonraki atlama türlerinin açıklamaları:

* **Sanal ağ**: Bir sanal ağın [Adres alanı](manage-virtual-network.md#add-or-remove-an-address-range) içindeki adres aralıkları arasında trafiği yönlendirir. Azure, bir sanal ağın adres alanı içinde tanımlanmış her bir adres aralığına karşılık gelen bir adres ön eki ile yol oluşturur. Sanal ağ adres alanında tanımlanmış birden fazla adres aralığı varsa, Azure her bir adres aralığı için ayrı bir yol oluşturur. Azure her bir adres aralığı için oluşturulan yolları kullanarak alt ağlar arasındaki trafiği otomatik olarak yönlendirir. Azure’ın alt ağlar arasında trafiği yönlendirmesi için ağ geçitleri tanımlamanız gerekmez. Bir sanal ağ, alt ağlar içerebilir ve her bir alt ağ için tanımlı adres aralıkları olabilir. Ancak alt ağ adres aralıklarının tümü, bir sanal ağın adres alanı içindeki adres aralığında bulunduğundan Azure, alt ağ adres aralıkları için varsayılan yollar *oluşturmaz*.<br>
* **Internet**: Adres ön eki tarafından belirtilen trafiği Internet 'e yönlendirir. Sistemin varsayılan yolu 0.0.0.0/0 adres ön ekini belirtir. Azure’ın varsayılan yollarını geçersiz kılmazsanız Azure, bir sanal ağ içinde bulunan ve bir adres aralığı ile belirtilmemiş olan tüm adresler için trafiği İnternet’e yönlendirir (bir özel durum dışında). Hedef adres Azure hizmetlerinden biriyse Azure, trafiği İnternet’e yönlendirmek yerine, Azure'ın temel ağı üzerinden doğrudan hizmete yönlendirir. Sanal ağın içinde bulunduğu Azure bölgesi veya Azure hizmeti örneğinin dağıtıldığı Azure bölgesi ne olursa olsun, Azure hizmetleri arasındaki trafik İnternet'i dolaşmaz. 0\.0.0.0/0 adres ön eki için Azure'ın varsayılan sistem yolunu bir [özel yol](#custom-routes) ile geçersiz kılabilirsiniz.<br>
* **Hiçbiri**: **Hiçbir** sonraki atlama türüne yönlendirilen trafik alt ağ dışında yönlendirilmek yerine bırakılır. Azure aşağıdaki adres ön ekleri için otomatik olarak varsayılan yollar oluşturur:<br>

    * **10.0.0.0/8 ve 192.168.0.0/16**: RFC 1918 ' de özel kullanım için ayrılmıştır.<br>
    * **100.64.0.0/10**: RFC 6598 ' de ayrılmıştır.

    Bir sanal ağın adres alanı içinde önceki adres aralıklarından herhangi birini atamanız durumunda Azure, yolun **Hiçbiri** olan sonraki atlama türünü **Sanal ağ** şeklinde otomatik olarak değiştirir. Ayrılmış dört adres ön ekini içeren, ancak bunlarla aynı olmayan bir sanal ağın adres alanına bir adres aralığı atamanız durumunda Azure, ön ekin yolunu kaldırır ve sizin eklediğiniz adres ön eki için, sonraki atlama türü **Sanal ağ** olacak şekilde bir yol ekler.

### <a name="optional-default-routes"></a>İsteğe bağlı varsayılan yollar

Azure, farklı Azure özellikleri için ek olarak varsayılan sistem yolları ekler. Ancak bunun için ilgili özellikleri etkinleştirmiş olmanız gerekir. Özelliğe bağlı olarak Azure, sanal ağ içindeki belirli alt ağlara veya sanal ağ içindeki tüm alt ağlara isteğe bağlı varsayılan yollar ekler. Farklı özellikleri etkinleştirdiğinizde Azure’ın ekleyebileceği ek sistem yolları ve sonraki atlama türleri şunlardır:

|Source                 |Adres ön ekleri                       |Sonraki atlama türü|Yolun eklendiği sanal ağ içindeki alt ağ|
|-----                  |----                                   |---------                    |--------|
|Varsayılan                |Sanal ağa benzersiz, örneğin: 10.1.0.0/16|VNet eşlemesi                 |Tümü|
|Sanal ağ geçidi|Şirket içinden BGP aracılığıyla tanıtılan veya yerel ağ geçidinde yapılandırılan ön ekler     |Sanal ağ geçidi      |Tümü|
|Varsayılan                |Birden çok                               |VirtualNetworkServiceEndpoint|Yalnızca bir hizmet uç noktasının etkinleştirildiği alt ağ.|

* **Sanal ağ (VNet) eşlemesi**: İki sanal ağ arasında bir sanal ağ eşlemesi oluşturduğunuzda her bir adres aralığı için bir eşleme oluşturulan her bir sanal ağın adres alanı içinde bir yol eklenir. [Sanal ağ eşlemesi](virtual-network-peering-overview.md) hakkında daha fazla bilgi edinin.<br>
* **Sanal ağ geçidi**: Bir sanal ağa bir sanal ağ geçidi eklendiğinde, sonraki atlama türü olarak listelenen *sanal ağ geçidi* ile bir veya daha fazla yol eklenir. Ağ geçidi yolları alt ağa eklediğinden, kaynak da *sanal ağ geçidi* olur. Şirket içi ağ geçidiniz, sınır ağ geçidi protokolü ([BGP](#border-gateway-protocol)) yollarını bir Azure sanal ağ geçidi ile değiştirirse şirket içi ağ geçidinden yayılan her yol için bir yol eklenir. Bir Azure sanal ağ geçidine en az sayıda yolun yayılması için, şirket içi yolları mümkün olan en büyük adres aralıklarında özetlemeniz önerilir. Bir Azure sanal ağ geçidine yayabileceğiniz yolların sayısı sınırlıdır. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) makalesini inceleyin.<br>
* **Virtualnetworkserviceendpoint**: Hizmete hizmet uç noktası etkinleştirdiğinizde, belirli hizmetlerin genel IP adresleri Azure tarafından yol tablosuna eklenir. Hizmet uç noktaları bir sanal ağdaki belirli alt ağlar için etkinleştirildiğinden, yol yalnızca bir hizmet uç noktasının etkinleştirildiği alt ağın yol tablosuna eklenir. Azure hizmetlerinin genel IP adresleri düzenli olarak değişir. Adresler değiştiğinde Azure, yol tablosundaki adresleri otomatik olarak yönetir. [Sanal ağ hizmet uç noktaları](virtual-network-service-endpoints-overview.md) ve hizmet uç noktaları oluşturabileceğiniz hizmetler hakkında daha fazla bilgi edinin.<br>

    > [!NOTE]
    > **Sanal ağ eşlemesi** ve **VirtualNetworkServiceEndpoint** türündeki sonraki atlamalar yalnızca Azure Resource Manager dağıtım modeli ile oluşturulmuş sanal ağlardaki alt ağların yol tablolarına eklenir. Sonraki atlama türleri, klasik dağıtım modeli ile oluşturulmuş sanal ağ alt ağlarıyla ilişkili yol tablolarına eklenmez. Azure [dağıtım modelleri](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hakkında daha fazla bilgi edinin.

## <a name="custom-routes"></a>Özel yollar

[Kullanıcı tanımlı](#user-defined) yollar oluşturarak veya [sınır ağ geçidi protokolü](#border-gateway-protocol) (BGP) yollarını şirket içi ağ geçidiniz ile bir Azure sanal ağ geçidi arasında değiştirerek özel yollar oluşturabilirsiniz. 

### <a name="user-defined"></a>Kullanıcı tanımlı

Azure’ın varsayılan sistem yollarını geçersiz kılmak veya bir alt ağın yol tablosuna başka yollar eklemek için Azure’da özel veya kullanıcı tanımlı yollar oluşturabilirsiniz. Azure’da bir yol tablosu oluşturun ve sonra yol tablosunu sıfır veya daha fazla sanal ağ alt ağı ile ilişkilendirin. Her alt ağ ile ilişkili sıfır veya bir yol tablosu olabilir. Bir yol tablosuna ekleyebileceğiniz en fazla yol sayısı ve bir Azure aboneliği için oluşturabileceğiniz en fazla kullanıcı tanımlı yol tablosu sayısı hakkında daha fazla bilgi için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) makalesini inceleyin. Yol tablosu oluşturup bunu bir alt ağ ile ilişkilendirirseniz, tablo içindeki yollar, Azure’ın bir alt ağa varsayılan olarak eklediği yollarla birleştirilir veya bu varsayılan yolları geçersiz kılar.

Kullanıcı tanımlı bir yol oluştururken belirtebileceğiniz sonraki atlama türleri:

* **Sanal Gereç**: Sanal Gereç, genellikle güvenlik duvarı gibi bir ağ uygulaması çalıştıran bir sanal makinedir. Bir sanal ağa dağıtabileceğiniz önceden yapılandırılmış çeşitli ağ sanal gereçleri hakkında bilgi edinmek için [Azure Market](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)’e bakın. **Sanal gereç** atlama türü ile bir rota oluşturduğunuzda, sonraki atlama IP adresi de belirtirsiniz. IP adresi şu özelliklerde olabilir:

    * Bir sanal makineye bağlı ağ arabiriminin [özel IP adresi](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses). Ağ trafiğini kendi adresi dışında bir adrese ileten bir sanal makineye bağlı olan tüm ağ arabirimlerinde *IP iletimini etkinleştir* seçeneği açık olmalıdır. Ayar, bir ağ arabirimi için Azure’ın kaynak ve hedef denetimini devre dışı bırakır. [Bir ağ arabirimi için IP iletimini etkinleştirme](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) hakkında daha fazla bilgi edinin. *IP iletimini etkinleştir* seçeneği bir Azure ayarı olmasına rağmen, cihazın Azure ağ arabirimlerine atanmış özel IP adresleri arasındaki trafiği iletmesi için sanal makinenin işletim sistemi içinde IP iletimini etkinleştirmeniz de gerekebilir. Cihazın trafiği bir genel IP adresine yönlendirmesi gerekirse, trafiği ara sunucuyla yönlendirmelidir veya ağ adresi kaynağın özel IP adresini kendi özel IP adresine çevirmelidir (ardından, trafiği İnternet'e göndermeden önce Azure'da ağ adresi genel IP adresine çevrilir). Sanal makine içindeki gerekli ayarları belirlemek için işletim sisteminize veya ağ uygulamanıza ait belgelere bakın. Azure'da giden bağlantıları anlamak için, bkz. [Giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>

        > [!NOTE]
        > Sanal gereci, sanal gereçten yönlendirilen kaynakların dağıtıldığı alt ağdan farklı bir alt ağa yönlendirin. Sanal gerecin aynı alt ağa dağıtılması ve sonra sanal gereçten trafiği yönlendiren alt ağa bir yol tablosunun uygulanması, trafiğin alt ağdan asla ayrılmadığı yönlendirme döngüleri ile sonuçlanabilir.

    * Bir Azure [iç yük dengeleyicisinin](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) özel IP adresi. İç yük dengeleyici genellikle [ağ sanal gereçleri için yüksek kullanılabilirlik stratejisinin](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json) bir parçasıdır.

    Adres ön eki 0.0.0.0/0 ve sonraki atlama türü sanal gereç olan bir yol tanımlayabilir ve gerecin trafiği inceleyip trafiği ileteceğini veya bırakacağını belirlemesini sağlayabilirsiniz. 0\.0.0.0/0 adres ön ekini içeren kullanıcı tanımlı bir ol oluşturmak isterseniz öncelikle [0.0.0.0/0 adres ön eki](#default-route) bölümünü okuyun.

* **Sanal ağ geçidi**: Belirli adres öneklerini hedefleyen trafiğin bir sanal ağ geçidine yönlendirilmesini istediğiniz zaman belirtin. Sanal ağ geçidi **VPN** türü ile oluşturulmalıdır. ExpressRoute ile Kullanıcı tanımlı bir rotada **ExpressRoute** türü olarak oluşturulan bir sanal ağ geçidi belirtemezsiniz, özel yollar için BGP kullanmanız gerekir. 0\.0.0.0/0 adres ön ekini hedefleyen trafiği [yol tabanlı](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype) sanal ağ geçidine yönlendiren bir yol tanımlayabilirsiniz. Şirket içinde trafiği inceleyen ve trafiğin iletileceğini veya bırakılacağını belirleyen bir cihazınız olabilir. 0\.0.0.0/0 adres ön eki için kullanıcı tanımlı bir ol oluşturmak isterseniz öncelikle [0.0.0.0/0 adres ön eki](#default-route) bölümünü okuyun. [VPN sanal ağ geçidi için BGP’yi etkinleştirdiyseniz](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), 0.0.0.0/0 adres ön eki için kullanıcı tanımlı bir yol yapılandırmak yerine 0.0.0.0/0 ön eki ile BGP üzerinden bir yol tanıtabilirsiniz.<br>
* **Hiçbiri**: Trafiği bir hedefe iletmek yerine bir adres ön ekine ne zaman bırakmak istediğinizi belirtin. Bir özelliği tam olarak yapılandırmadıysanız, Azure bazı isteğe bağlı sistem yolları için *Hiçbiri* seçeneğini listeleyebilir. Örneğin, *Sanal ağ geçidi* ya da *Sanal gereç* **Sonraki atlama türü** ile **Sonraki atlama IP adresi** olarak *Hiçbiri* seçeneğinin listelendiğini görürseniz, bunun nedeni cihazın çalışmaması veya tam olarak yapılandırılmamış olması olabilir. Azure, sonraki atlama türü **Hiçbiri** olan ayrılmış adres ön ekleri için sistem [varsayılan yolları](#default) oluşturur.<br>
* **Sanal ağ**: Bir sanal ağ içinde varsayılan yönlendirmeyi ne zaman geçersiz kılmak istediğinizi belirtin. **Sanal ağ** atlama türü ile neden yol oluşturabileceğinize ilişkin bir örnek için [Yönlendirme örneği](#routing-example) makalesini inceleyin.<br>
* **Internet**: Bir adres ön ekine giden trafiği açık olarak Internet 'e yönlendirmek istediğinizde veya Azure hizmetleri 'nin Azure omurga ağı içinde tutulduğu genel IP adresleriyle ilgili trafik olmasını istiyorsanız belirtin.

Kullanıcı tanımlı yollarda sonraki atlama türü olarak **VNet eşlemesi** veya **VirtualNetworkServiceEndpoint** seçeneğini belirtemezsiniz. Sonraki atlama türü **VNet eşlemesi** veya **VirtualNetworkServiceEndpoint** olan yollar yalnızca bir sanal ağ eşlemesi ya da hizmet uç noktası yapılandırdığınızda Azure tarafından oluşturulur.

## <a name="next-hop-types-across-azure-tools"></a>Azure Araçları genelinde sonraki atlama türleri

Sonraki atlama türleri için gösterilen ve başvurulan ad, Azure portalı ile komut satırı araçları ve Azure Resource Manager ile klasik dağıtım modelleri arasında farklıdır. Aşağıdaki tabloda farklı araçlar ve [dağıtım modelleri](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ile her bir sonraki atlama türüne başvurmak için kullanılan adlar listelenir:

|Sonraki atlama türü                   |Azure CLI ve PowerShell (Resource Manager) |Azure klasik CLI ve PowerShell (klasik)|
|-------------                   |---------                                       |-----|
|Sanal ağ geçidi         |VirtualNetworkGateway                           |VPNGateway|
|Sanal ağ                 |VNetLocal                                       |VNETLocal (asm modunda klasik CLI’de kullanılamaz)|
|Internet                        |Internet                                        |İnternet (asm modunda klasik CLI’de kullanılamaz)|
|Sanal gereç               |VirtualAppliance                                |VirtualAppliance|
|Yok.                            |None                                            |Null (asm modunda klasik CLI’de kullanılamaz)|
|Sanal ağ eşleme         |VNet eşlemesi                                    |Geçerli değil|
|Sanal ağ hizmet uç noktaları|VirtualNetworkServiceEndpoint                   |Geçerli değil|

### <a name="border-gateway-protocol"></a>Sınır ağ geçidi protokolü

Şirket içi ağ geçidi, sınır ağ geçidi protokolünü (BGP) kullanarak bir Azure sanal ağ geçidi ile yolları değiştirebilir. BGP’nin bir Azure sanal ağ geçidi ile kullanılması, ağ geçidini oluştururken seçtiğiniz türe bağlıdır. Seçtiğiniz tür aşağıdakilerden biri ise:

* **ExpressRoute**: Şirket içi yolları Microsoft Edge yönlendiricisine tanıtmak için BGP kullanmanız gerekir. Tür olarak dağıtılan bir sanal ağ geçidi dağıtırsanız, ExpressRoute sanal ağ geçidine trafiği zorlamak için Kullanıcı tanımlı yollar oluşturamazsınız: ExpressRoute. Örneğin, Express Route’tan bir Sanal Ağ Gerecine trafiği zorlamak için kullanıcı tanımlı yolları kullanabilirsiniz.<br>
* **VPN**: İsteğe bağlı olarak BGP kullanabilirsiniz. Ayrıntılar için [Siteden siteye VPN bağlantıları ile BGP](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konusunu inceleyin.

BGP kullanarak Azure ile yolları değiştirdiğinizde, tanıtılan her ön ek için bir sanal ağdaki tüm alt ağların yol tablosuna ayrı bir yol eklenir. Yol, kaynak ve sonraki atlama türü olarak *Sanal ağ geçidi* listelenerek eklenir. 

ER ve VPN Gateway yol yayma, bir alt ağda yol tablosundaki bir özellik kullanılarak devre dışı bırakılabilir. BGP kullanarak Azure ile yolları Exchange 'e yönlendirdiğinizde, rotalar sanal ağ geçidi yol yayma devre dışı olan tüm alt ağların yol tablosuna eklenmez. VPN bağlantıları, sonraki atlama türü *Sanal ağ geçidi* olan [özel yollar](#custom-routes) kullanılarak gerçekleştirilir. Ayrıntılar için bkz. [sanal ağ geçidi yol yaymayı devre dışı bırakma](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Azure yolu nasıl seçer?

Giden trafik bir alt ağ üzerinden gönderildiğinde, Azure en uzun ön ek eşleştirme algoritmasını kullanarak hedef IP adresine göre bir yol seçer. Örneğin, bir yol tablosunun iki yolu vardır: Bir yol, 10.0.0.0/24 adres önekini belirtir, diğer yol ise 10.0.0.0/16 adres ön ekini belirtir. 10.0.0.5 her iki adres ön ekinde bulunsa da 10.0.0.0/24 adres ön eki 10.0.0.0/16’dan daha uzun olduğu için Azure 10.0.0.5 adres ön ekini hedefleyen trafiği, 10.0.0.0/24 adres ön eki ile yolda belirtilen sonraki atlama türüne yönlendirir. 10.0.1.5 değeri 10.0.0.0/24 adres ön ekinde bulunmadığı ve bu nedenle 10.0.0.0/16 adres ön eki eşleşen en uzun ön ek olduğu için, Azure 10.0.1.5’i hedefleyen trafiği 10.0.0.0/16 adres ön ekinde belirtilen yoldaki sonraki atlama türüne yönlendirir.

Birden fazla yol aynı adres ön ekini içeriyorsa, Azure aşağıdaki öncelik sırasına göre yol türünü seçer:

1. Kullanıcı tanımlı yol
1. BGP yolu
1. Sistem yolu

> [!NOTE]
> BGP rotaları daha belirli olsa bile, sanal ağ, sanal ağ eşlemesi veya sanal ağ hizmet uç noktaları ile ilgili trafik için sistem rotaları, tercih edilen rotalardır.

Örneğin, bir yol tablosu aşağıdaki yolları içerir:


|Source   |Adres ön ekleri  |Sonraki atlama türü           |
|---------|---------         |-------                 |
|Varsayılan  | 0.0.0.0/0        |Internet                |
|Kullanıcı     | 0.0.0.0/0        |Sanal ağ geçidi |

Trafik yol tablosundaki başka bir yolun adres ön ekleri dışında bir IP adresini hedeflediğinde, kullanıcı tanımlı yollar sistem varsayılan yollarından daha yüksek öncelikli olduğu için Azure **Kullanıcı** kaynağına sahip yolu seçer.

Tablodaki yolların açıklamalarını içeren kapsamlı bir yönlendirme tablosu için [Yönlendirme örneği](#routing-example) makalesini inceleyin.

## <a name="default-route"></a>0.0.0.0/0 adres ön eki

Adres ön eki 0.0.0.0/0 olan bir yol, Azure’a bir alt ağın yol tablosundaki başka bir yolun adres ön eki dahilinde olmayan bir IP adresini hedefleyen trafiği nasıl yönlendireceğini söyler. Bir alt ağ oluşturulduğunda, Azure 0.0.0.0/0 adres ön eki için sonraki atlama türü **İnternet** olan bir [varsayılan](#default) yol oluşturur. Bu yolu geçersiz kılmazsanız, Azure başka bir yolun adres ön ekine dahil olmayan IP adreslerini hedefleyen tüm trafiği İnternet’e yönlendirir. Bunun istisnası, Azure hizmetlerinin genel IP adreslerine giden trafiğin Azure omurga ağında kalması ve İnternet’e yönlendirilmemesidir. Bu yolu bir [özel](#custom-routes) yol ile geçersiz kılarsanız, yol tablosundaki başka bir yolun adres ön eklerinde bulunmayan adresleri hedefleyen trafik, özel bir yolda belirttiğiniz seçeneğe göre bir ağ sanal gerecine veya sanal ağ geçidine gönderilir.

0\.0.0.0/0 adres ön ekini geçersiz kıldığınızda, alt ağdan çıkarak sanal ağ geçidi veya sanal gerece giden trafiğe ek olarak Azure'ın varsayılan yönlendirmesinde aşağıdaki değişiklikler gerçekleşir: 

* Azure, Azure hizmetlerinin genel IP adreslerini hedefleyen trafiği dahil etmek üzere tüm trafiği yolda belirtilen sonraki atlama türüne gönderir. Adres ön eki 0.0.0.0/0 olan yol için sonraki atlama türü **İnternet** ise, sanal ağın veya Azure hizmet kaynağının bulunduğu bölge ne olursa olsun, alt ağdan giden ve Azure hizmetlerinin genel IP adreslerini hedefleyen trafik Azure’ın temel ağından hiçbir zaman ayrılmaz. Ancak, sonraki atlama türü **Sanal ağ geçidi** veya **Sanal gereç** olan kullanıcı tanımlı bir yol veya BGP yolu oluşturduğunuzda, [hizmet uç noktalarını](virtual-network-service-endpoints-overview.md) etkinleştirmediğiniz Azure hizmetlerinin genel IP adreslerine gönderilen trafiği dahil etmek için tüm trafik yolda belirtilen sonraki atlama türüne gönderilir. Bir hizmet için hizmet uç noktasını etkinleştirdiyseniz, adres ön eki 0.0.0.0/0 olan bir yolda hizmete giden trafik sonraki atlama türüne yönlendirilmez. Bunun nedeni, hizmete yönelik adres ön eklerinin, hizmet uç noktasını etkinleştirdiğinizde Azure’ın oluşturduğu yolda belirtilmesi ve hizmetin adres ön eklerinin 0.0.0.0/0 değerinden uzun olmasıdır.
* Bundan böyle alt ağdaki kaynaklara İnternet’ten doğrudan erişemezsiniz. Gelen trafik sanal ağdaki kaynağa ulaşmadan önce 0.0.0.0/0 adres ön ekine sahip bir yol için sonraki atlama türü tarafından belirtilen cihaz üzerinden geçiyorsa, alt ağdaki kaynaklara İnternet’ten dolaylı olarak erişebilirsiniz. Yol sonraki atlama türü için aşağıdaki değerleri içeriyorsa:<br>

    * **Sanal Gereç**: Gereç şunları sağlamalıdır:<br>

        * İnternet üzerinden erişilebilir olması<br>
        * Kendisine atanmış bir genel IP adresine sahip olması,<br>
        * Cihazla iletişimi engelleyen ilişkili bir ağ güvenlik grubu kuralına sahip olmaması<br>
        * İletişimi reddetmemesi<br>
        * Ağ adresini çevirip iletebilmesi veya trafik ile alt ağdaki hedef kaynak arasında ara sunucu oluşturabilmesi ve trafiği İnternet’e geri döndürebilmesi.

    * **Sanal ağ geçidi**: Ağ geçidi bir ExpressRoute sanal ağ geçidi ise, şirket içi Internet 'e bağlı bir cihaz, ağ adresi çevirisi ve iletme veya trafiği ExpressRoute 'un [özel eşlemesi](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering)aracılığıyla alt ağdaki hedef kaynağa ara sunucu ile elde edebilir. 

Sanal ağınız bir Azure VPN ağ geçidine bağlıysa, rota tablosunu 0.0.0.0/0 hedefine sahip bir rota içeren [ağ geçidi alt ağına](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) ilişkilendirmeyin. Bunun yapılması, ağ geçidinin düzgün çalışmasını engelleyebilir. Ayrıntılı bilgi için [VPN Gateway FAQ](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports) sayfasındaki (VPN Gateway ile ilgili SSS) *Why are certain ports opened on my VPN gateway?* (VPN ağ geçidimde belirli bağlantı noktalarının açık olma nedeni nedir?) sorusuna bakın.

İnternet ile Azure arasında sanal ağ geçitleri ve sanal gereçler kullanılırken uygulama ayrıntıları için [Azure ile şirket içi veri merkeziniz arasında DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Azure ile İnternet arasında DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) konularını inceleyin.

## <a name="routing-example"></a>Yönlendirme örneği

Bu makaledeki kavramları göstermek için aşağıdaki bölümlerde şu konular açıklanmaktadır:

* Gereksinimleri içeren bir senaryo<br>
* Gereksinimleri karşılamak için gereken özel yollar<br>
* Gereksinimleri karşılamak için gereken varsayılan ve özel yolları içeren bir alt ağ için mevcut olan yol tablosu

> [!NOTE]
> Bu örnek, önerilen veya en iyi yöntem uygulaması olacak şekilde tasarlanmamıştır. Bunun yerine, yalnızca bu makaledeki kavramları göstermek için verilmektedir.

### <a name="requirements"></a>Gereksinimler

1. Aynı Azure bölgesinde iki sanal ağı uygulayın ve kaynakların sanal ağlar arasında iletişim kurmasını etkinleştirin.
1. Bir şirket içi ağı, İnternet üzerinden bir VPN tüneli aracılığıyla her iki sanal ağ ile güvenli bir şekilde iletişim kurmak için etkinleştirin. *Alternatif olarak bir ExpressRoute bağlantısı kullanılabilir, ancak bu örnekte VPN bağlantısı kullanılmaktadır.*
1. Bir sanal ağdaki alt ağ için:

    * Azure Depolama’ya giden trafik ve alt ağ içindeki trafik dışında alt ağdan giden tüm trafiği, inceleme ve günlüğe kaydetme amacıyla bir ağ sanal gerecinden geçmeye zorlayın.<br>
    * Alt ağ içindeki özel IP adresleri arasında gerçekleşen trafiği incelemeyin; trafiğin tüm kaynaklar arasında akmasına izin verin.<br>
    * Diğer sanal ağı hedefleyen tüm giden trafiği bırakın.<br>
    * Azure depolamaya giden trafiği bir ağ sanal gerecinden geçmeye zorlamadan doğrudan depolamaya gitmesini sağlayın.

1. Diğer tüm alt ağlar ile sanal ağlar arasındaki tüm trafiğe izin verin.

### <a name="implementation"></a>Uygulama

Aşağıdaki resimde, Azure Resource Manager dağıtım modeli ile gerçekleştirilen ve önceki gereksinimleri karşılayan bir uygulama gösterilmektedir:

![Ağ diyagramı](./media/virtual-networks-udr-overview/routing-example.png)

Oklar trafik akışını gösterir. 

### <a name="route-tables"></a>Yol tabloları

#### <a name="subnet1"></a>Subnet1

Resimdeki *Subnet1* için yol tablosu aşağıdaki yolları içerir:

|id  |Source |Durum  |Adres ön ekleri    |Sonraki atlama türü          |Sonraki atlama IP adresi|Kullanıcı tanımlı yol adı| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1\.   |Varsayılan|Geçersiz|10.0.0.0/16         |Sanal ağ        |                   |              |
|2   |Kullanıcı   |Etkin |10.0.0.0/16         |Sanal gereç      |10.0.100.4         |VNet1 içinde  |
|3   |Kullanıcı   |Etkin |10.0.0.0/24         |Sanal ağ        |                   |Subnet1 içinde|
|4   |Varsayılan|Geçersiz|10.1.0.0/16         |VNet eşlemesi           |                   |              |
|5   |Varsayılan|Geçersiz|10.2.0.0/16         |VNet eşlemesi           |                   |              |
|6   |Kullanıcı   |Etkin |10.1.0.0/16         |Yok.                   |                   |ToVNet2-1-Bırak|
|7   |Kullanıcı   |Etkin |10.2.0.0/16         |Yok.                   |                   |ToVNet2-2-Bırak|
|8   |Varsayılan|Geçersiz|10.10.0.0/16        |Sanal ağ geçidi|[X.X.X.X]          |              |
|9   |Kullanıcı   |Etkin |10.10.0.0/16        |Sanal gereç      |10.0.100.4         |Şirket-İçine    |
|10  |Varsayılan|Etkin |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Varsayılan|Geçersiz|0.0.0.0/0           |Internet               |                   |              |
|12  |Kullanıcı   |Etkin |0.0.0.0/0           |Sanal gereç      |10.0.100.4         |Varsayılan-NVA   |

Her bir yol kimliğinin açıklaması aşağıdaki gibidir:

1. 10.0.0.0/16, sanal ağ için adres alanında tanımlanmış tek adres alanı olduğundan Azure bu yolu *Virtual-network-1* içindeki tüm alt ağlar için otomatik olarak eklemiştir. Ön ek 0.0.0.0/0’dan uzun olduğu ve diğer yolların herhangi birine ait adres ön ekleri dahilinde olmadığı için, yol ID2’deki kullanıcı tanımlı yol oluşturulmasaydı, 10.0.0.1 ile 10.0.255.254 arasındaki adrese gönderilen trafik sanal ağ içinde yönlendirilirdi. Kullanıcı tanımlı bir yol olan ID2 eklendiğinde, varsayılan yol ile aynı ön eke sahip olduğundan ve kullanıcı tanımlı yollar varsayılan yolları geçersiz kıldığından Azure, *Etkin* olan durumu *Geçersiz* olarak değiştirmiştir. Kullanıcı tanımlı ID2 yolunun içinde bulunduğu yol tablosu *Subnet2* ile ilişkili olmadığından, bu yolun durumu *Subnet2* için hala *Etkin* şeklindedir.
2. 10.0.0.0/16 adres ön eki için kullanıcı tanımlı yol *Virtual-network-1* sanal ağındaki *Subnet1* alt ağı ile ilişkili olduğunda Azure bu yolu eklemiştir. Adres sanal gereç sanal makinesine atanmış özel IP adresi olduğu için kullanıcı tanımlı yol, sanal gerecin IP adresi olarak 10.0.100.4’ü belirtir. Bu yolun içinde bulunduğu yol tablosu *Subnet2* ile ilişkili değildir, bu nedenle *Subnet2* yol tablosunda görünmez. Bu yol, sanal ağ sonraki atlama türü üzerinden sanal ağ içindeki 10.0.0.1 ve 10.0.255.254 adreslerine trafiği otomatik olarak yönlendiren 10.0.0.0/16 ön eki (ID1) için varsayılan yolu geçersiz kılar. Bu yol, giden tüm trafiği bir sanal gereç üzerinden yönlendirmeye zorlayan [gereksinim](#requirements) 3’ü karşılar.
3. 10.0.0.0/24 adres ön eki için kullanıcı tanımlı yol *Subnet1* alt ağı ile ilişkili olduğunda Azure bu yolu eklemiştir. 10.0.0.1 ile 10.0.0.254 arasındaki adresleri hedefleyen trafik, ID2 yolundan daha uzun bir ön eke sahip olduğu için önceki kuralda (ID2) belirtilen sanal gerece yönlendirilmek yerine alt ağda kalır. Bu yol *Subnet2* ile ilişkili değildir, bu nedenle *Subnet2* yol tablosunda görünmez. Bu yol, *Subnet1* içindeki trafik için ID2 yolunu etkili bir şekilde geçersiz kılar. Bu yol [gereksinim](#requirements) 3’ü karşılar.
4. Sanal ağ *Virtual-network-2* ile eşlendiğinde Azure, *Virtual-network-1* içindeki tüm alt ağlar için ID 4 ve 5’teki yolları otomatik olarak eklemiştir. *Virtual-Network-2* ' nin adres alanında iki adres aralığı vardır: 10.1.0.0/16 ve 10.2.0.0/16, bu nedenle Azure her Aralık için bir yol ekledi. Ön ek 0.0.0.0/0’dan uzun olduğu ve diğer yolların herhangi birine ait adres ön ekleri dahilinde olmadığı için, ID 6 ve 7’deki kullanıcı tanımlı yollar oluşturulmasaydı, 10.1.0.1-10.1.255.254 ile 10.2.0.1-10.2.255.254 arasındaki adrese gönderilen trafik eşlenmiş sanal ağa yönlendirilirdi. ID 6 ve 7’deki yollar ID 4 ve 5’teki yollarla aynı ön eklere sahip olduğundan ve kullanıcı tanımlı yollar varsayılan yolları geçersiz kıldığından bu yollar eklendiğinde Azure, *Etkin* olan durumu *Geçersiz* olarak değiştirmiştir. 4 ve 5. Kimliklerde bulunan Kullanıcı tanımlı yolların bulunduğu yol tablosu, *Subnet2*ile ilişkili olmadığından, kimlik 4 ve 5 ' teki yolların durumu hala *Subnet2*için *etkindir* . [Gereksinim](#requirements) 1’i karşılamak için bir sanal ağ eşlemesi oluşturulmuştur.
5. ID4 ile aynı açıklama.
6. 10.1.0.0/16 ve 10.2.0.0/16 adres ön ekleri için kullanıcı tanımlı yollar *Subnet1* alt ağı ile ilişkili olduğunda Azure bu yolu ve ID7’deki yolu eklemiştir. Kullanıcı tanımlı yollar varsayılan yolları geçersiz kıldığı için, 10.1.0.1-10.1.255.254 ile 10.2.0.1-10.2.255.254 arasındaki adresleri hedefleyen trafik eşlenmiş sanal ağa yönlendirilmek yerine Azure tarafından bırakılır. Yollar *Subnet2* ile ilişkili değildir, bu nedenle *Subnet2* yol tablosunda görünmez. Yollar *Subnet1*’den ayrılan trafik için ID4 ve ID5 yollarını geçersiz kılar. ID6 ve ID7 yolları, diğer sanal ağı hedefleyen trafiği bırakmaya yönelik [gereksinim](#requirements) 3’ü karşılar.
7. ID6 ile aynı açıklama.
8. Sanal ağ içinde VPN türünde sanal ağ geçidi oluşturulduğunda Azure, *Virtual-network-1* içindeki tüm alt ağlar için bu yolu otomatik olarak eklemiştir. Azure, sanal ağ geçidinin genel IP adresini yol tablosuna eklemiştir. 10.10.0.1 ile 10.10.255.254 arasındaki herhangi bir adrese gönderilen trafik, sanal ağ geçidine yönlendirilir. Ön ek 0.0.0.0/0’dan daha uzundur ve başka bir yolun adres ön ekleri dahilinde değildir. [Gereksinim](#requirements) 2’yi karşılamak için bir sanal ağ geçidi oluşturulmuştur.
9. 10.10.0.0/16 adres ön eki için *Subnet1* ile ilişkili yol tablosuna bir kullanıcı tanımlı yol eklendiğinde Azure bu yolu eklemiştir. Bu yol ID8’i geçersiz kılar. Yol, trafiği doğrudan şirket içine yönlendirmek yerine şirket içi ağı hedefleyen tüm trafiği inceleme için bir NVA’ya gönderir. Bu yol [gereksinim](#requirements) 3’ü karşılamak amacıyla oluşturulmuştur.
10. Alt ağ için bir Azure hizmetinde hizmet uç noktası etkinleştirildiğinde Azure bu yolu alt ağa otomatik olarak eklemiştir. Azure, trafiği Azure altyapı ağı üzerinden alt ağdan hizmetin genel IP adresine yönlendirir. Ön ek 0.0.0.0/0’dan daha uzundur ve başka bir yolun adres ön ekleri dahilinde değildir. Azure Depolama’yı hedefleyen trafiğin doğrudan Azure Depolama’ya gitmesini sağlamaya yönelik [gereksinim](#requirements) 3’ü karşılamak üzere bir hizmet uç noktası oluşturulmuştur.
11. Azure bu yolu *Virtual-network-1* ve *Virtual-network-2* içindeki tüm alt ağların yol tablosuna otomatik olarak eklemiştir. 0\.0.0.0/0 adres ön eki en kısa ön ektir. Daha uzun bir adres ön ekindeki adrese gönderilen her türlü trafik, diğer yollara göre yönlendirilir. Varsayılan olarak, Azure diğer yollardan birinde belirtilen adresler dışındaki adresleri hedefleyen tüm trafiği İnternet’e yönlendirir. 0\.0.0.0/0 adres ön eki (ID12) için kullanıcı tanımlı bir yol alt ağ ile ilişkili olduğunda Azure, *Subnet1* alt ağının *Etkin* olan durumunu otomatik olarak *Geçersiz* yapmıştır. Yol başka bir sanal ağdaki başka bir alt ağ ile ilişkili olmadığından, bu yolun durumu her iki sanal ağdaki diğer tüm alt ağlar için hala *Etkin* şeklindedir.
12. 0\.0.0.0/0 adres ön eki için kullanıcı tanımlı yol *Subnet1* alt ağı ile ilişkili olduğunda Azure bu yolu eklemiştir. Kullanıcı tanımlı yol, sanal gerecin IP adresi olarak 10.0.100.4’ü belirtir. Bu yol *Subnet2* ile ilişkili değildir, bu nedenle *Subnet2* yol tablosunda görünmez. Başka bir yolun adres ön eklerine dahil olmayan adreslere yönelik tüm trafik sanal gerece gönderilir. Kullanıcı tanımlı bir yol varsayılan yolu geçersiz kıldığından, bu yolun eklenmesi 0.0.0.0/0 adres ön ekinin (ID11) *Subnet1* için *Etkin* olan varsayılan yol durumunu *Geçersiz* olarak değiştirmiştir. Bu yol, üçüncü [gereksinimi](#requirements)karşılayacak şekilde bulunur.

#### <a name="subnet2"></a>Subnet2

Resimdeki *Subnet2* için yol tablosu aşağıdaki yolları içerir:

|Source  |Durum  |Adres ön ekleri    |Sonraki atlama türü             |Sonraki atlama IP adresi|
|------- |-------|------              |-------                   |--------           
|Varsayılan |Etkin |10.0.0.0/16         |Sanal ağ           |                   |
|Varsayılan |Etkin |10.1.0.0/16         |VNet eşlemesi              |                   |
|Varsayılan |Etkin |10.2.0.0/16         |VNet eşlemesi              |                   |
|Varsayılan |Etkin |10.10.0.0/16        |Sanal ağ geçidi   |[X.X.X.X]          |
|Varsayılan |Etkin |0.0.0.0/0           |Internet                  |                   |
|Varsayılan |Etkin |10.0.0.0/8          |None                      |                   |
|Varsayılan |Etkin |100.64.0.0/10       |Yok.                      |                   |
|Varsayılan |Etkin |192.168.0.0/16      |Yok.                      |                   |

*Subnet2* yol tablosu Azure tarafından oluşturulan tüm varsayılan yolları ve isteğe bağlı VNet eşlemesi ile Sanal ağ geçidi isteğe bağlı yollarını içerir. Sanal ağa ağ geçidi ve eşleme eklendiğinde Azure, sanal ağ içindeki tüm alt ağlara isteğe bağlı yollar eklemiştir. 0\.0.0.0/0 adres ön eki için Kullanıcı tanımlı yol *Subnet1*'ye eklendiğinde Azure, 10.0.0.0/8, 192.168.0.0/16 ve 100.64.0.0/10 adres ön eklerinin yollarını *Subnet1* Route tablosundan kaldırdı.  

## <a name="next-steps"></a>Sonraki adımlar

* [Yollar ve ağ sanal gereci içeren bir kullanıcı tanımlı yol tablosu oluşturma](tutorial-create-route-table-portal.md)<br>
* [Azure VPN Gateway için BGP yapılandırma](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [ExpressRoute ile BGP kullanma](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [Bir alt ağ için tüm yolları görüntüleme](diagnose-network-routing-problem.md). Kullanıcı tanımlı yol tablosu, bir alt ağın varsayılan yollarını değil, yalnızca kullanıcı tanımlı ve BGP yollarını gösterir. Tüm yollar görüntülendiğinde ağ arabiriminin içinde bulunduğu alt ağa ait varsayılan, BGP ve kullanıcı tanımlı yollar gösterilir.<br>
* Sanal makine ile hedef IP adresi arasında [sonraki atlama türünü belirleyin](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Azure Ağ İzleyicisi sonraki atlama özelliği, trafiğin bir alt ağdan ayrıldığını ve olması gereken yere yönlendirildiğini belirlemenizi sağlar.
