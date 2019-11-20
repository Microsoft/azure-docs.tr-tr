---
title: Azure ağ arabirimi için IP adreslerini yapılandırma | Microsoft Docs
description: Bir ağ arabirimi için özel ve genel IP adreslerini ekleme, değiştirme ve kaldırma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: 7df58c3f866ffd28348ecfa2e43bdccbd1d96001
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965707"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure ağ arabirimi için IP adreslerini ekleme, değiştirme veya kaldırma

Bir ağ arabirimi için genel ve özel IP adreslerini ekleme, değiştirme ve kaldırma hakkında bilgi edinin. Bir ağ arabirimine atanan özel IP adresleri, bir sanal makinenin bir Azure sanal ağı ve bağlı ağlarda diğer kaynaklarla iletişim kurmasını sağlar. Özel bir IP adresi, öngörülemeyen bir IP adresi kullanılarak Internet 'e giden iletişimin da yapılmasını sağlar. Bir ağ arabirimine atanan [genel IP adresi](virtual-network-public-ip-address.md) , Internet 'ten bir sanal makineye gelen iletişimi sağlar. Bu adres, öngörülebilir bir IP adresi kullanarak sanal makineden Internet 'e giden iletişimi de sağlar. Ayrıntılar için bkz. [Azure 'da giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Bir ağ arabirimi oluşturmanız, değiştirmeniz veya silmeniz gerekiyorsa, [ağ arabirimini yönetme](virtual-network-network-interface.md) makalesini okuyun. Ağ arabirimlerini bir sanal makineden ağ arabirimleri eklemek veya kaldırmak gerekirse, [ağ arabirimlerini Ekle veya Kaldır](virtual-network-network-interface-vm.md) makalesini okuyun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, https://portal.azure.com ' ı açın ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure ile bağlantı oluşturmak için `az login` ' ı çalıştırmanız gerekir.

Oturum açarken veya Azure 'a bağlanırken kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [ağ arabirimi izinlerinde](virtual-network-network-interface.md#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="add-ip-addresses"></a>IP adresi ekle

[Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesinde listelenen limitlerin içinde, bir ağ arabirimine gereken sayıda [özel](#private) ve [genel](#public) [IPv4](#ipv4) adresi ekleyebilirsiniz. Var olan bir ağ arabirimi için bir [IKINCIL IP yapılandırmasına](#secondary) (var olan ikincil IP yapılandırmaları olmadığı sürece) özel bir IPv6 adresi ekleyebilirsiniz. Her ağ arabirimi en fazla bir IPv6 özel adresine sahip olabilir. İsteğe bağlı olarak bir IPv6 ağ arabirimi yapılandırmasına genel bir IPv6 adresi ekleyebilirsiniz. IPv6 adreslerini kullanma hakkında ayrıntılı bilgi için bkz. [IPv6](#ipv6) .

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Listeden bir IPv4 adresi eklemek istediğiniz ağ arabirimini seçin.
3. **Ayarlar**altında **IP yapılandırması**' nı seçin.
4. **IP yapılandırması**altında **+ Ekle**' yi seçin.
5. Aşağıdakileri belirtip **Tamam**' ı seçin:

   |Ayar|Gerekli mi?|Ayrıntılar|
   |---|---|---|
   |Adı|Yes|Ağ arabirimi için benzersiz olmalıdır|
   |Tür|Yes|Var olan bir ağ arabirimine bir IP yapılandırması ekliyorsanız ve her bir ağ arabiriminin bir [birincil](#primary) IP yapılandırması olması gerektiğinden, tek seçeneğiniz **ikincildir**.|
   |Özel IP adresi atama yöntemi|Yes|[**Dinamik**](#dynamic): Azure, ağ arabiriminin dağıtıldığı alt ağ adres aralığı için bir sonraki kullanılabilir adresi atar. [**Statik**](#static): ağ arabiriminin dağıtıldığı alt ağ adres aralığı için kullanılmayan bir adres atarsınız.|
   |Genel IP adresi|Hayır|**Devre dışı:** Şu anda IP yapılandırmasıyla ilişkili genel IP adresi kaynağı yok. **Etkin:** Mevcut bir IPv4 Genel IP adresi seçin veya yeni bir tane oluşturun. Genel IP adresi oluşturmayı öğrenmek için [genel IP adresleri](virtual-network-public-ip-address.md#create-a-public-ip-address) makalesini okuyun.|
6. Sanal makine işletim [sistemlerine birden çok IP adresi atama](virtual-network-multiple-ip-addresses-portal.md#os-config) makalesindeki yönergeleri tamamlayarak, IKINCIL özel IP adreslerini sanal makine işletim sistemine el ile ekleyin. IP adreslerini bir sanal makine işletim sistemine el ile eklemeden önce [, özel IP adreslerine göz atın.](#private) Sanal makine işletim sistemine genel IP adresleri eklemeyin.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-Aznetworkınterfaceipconfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP adresi ayarlarını değiştir

Bir IPv4 adresinin atama yöntemini değiştirmeniz, statik IPv4 adresini değiştirmeniz veya bir ağ arabirimine atanan genel IP adresini değiştirmeniz gerekebilir. Bir sanal makinede ikincil ağ arabirimiyle ilişkili ikincil bir IP yapılandırmasının özel IPv4 adresini değiştiriyorsanız ( [birincil ve ikincil ağ arabirimleri](virtual-network-network-interface-vm.md)hakkında daha fazla bilgi edinin), sanal makineyi durdurulmuş olarak yerleştirin ( serbest bırakıldı) aşağıdaki adımları tamamlamadan önce durumu:

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Listeden için IP adresi ayarlarını görüntülemek veya değiştirmek istediğiniz ağ arabirimini seçin.
3. **Ayarlar**altında **IP yapılandırması**' nı seçin.
4. Listeden değiştirmek istediğiniz IP yapılandırmasını seçin.
5. [IP yapılandırması ekleme](#add-ip-addresses)' nin 5. adımında bulunan ayarlarla ilgili bilgileri kullanarak ayarları istediğiniz şekilde değiştirin.
6. **Kaydet**’i seçin.

>[!NOTE]
>Birincil ağ arabiriminin birden çok IP yapılandırması varsa ve birincil IP yapılandırmasının özel IP adresini değiştirirseniz, birincil ve ikincil IP adreslerini Windows içindeki ağ arabirimine el ile yeniden atamanız gerekir (Linux için gerekli değildir) . IP adreslerini bir işletim sistemi içindeki bir ağ arabirimine el ile atamak için bkz. [sanal makinelere birden çok IP adresi atama](virtual-network-multiple-ip-addresses-portal.md#os-config). IP adreslerini bir sanal makine işletim sistemine el ile eklemeden önce özel göz önünde bulundurulması gereken [özel IP adresleri bölümüne bakın](#private) . Sanal makine işletim sistemine genel IP adresleri eklemeyin.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic ip-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-Aznetworkınterfaceipconfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP adreslerini kaldır

[Özel](#private) ve [genel](#public) IP adreslerini bir ağ arabiriminden kaldırabilirsiniz, ancak bir ağ arabirimine, her zaman en az bir özel IPv4 adresi atanmış olmalıdır.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Listeden IP adreslerini kaldırmak istediğiniz ağ arabirimini seçin.
3. **Ayarlar**altında **IP yapılandırması**' nı seçin.
4. [İkincil](#secondary) bir IP yapılandırması seçin ( [birincil](#primary) yapılandırmayı silemezsiniz), silmek **istediğiniz Sil ' i seçin ve**sonra silme işlemini onaylamak için **Evet**' i seçin. Yapılandırmaya ilişkili bir genel IP adresi kaynağı varsa, kaynağın IP yapılandırmasından ilişkisi kaldırılır, ancak kaynak silinmez.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic ip-config Delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-Aznetworkınterfaceipconfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP yapılandırması

[Özel](#private) ve (isteğe bağlı) [genel](#public) IP adresleri, bir ağ arabirimine atanan BIR veya daha fazla IP yapılandırmasına atanır. İki tür IP yapılandırması vardır:

### <a name="primary"></a>Birincil

Her ağ arabirimine bir birincil IP yapılandırması atanır. Birincil IP yapılandırması:

- Kendisine atanmış [özel](#private) bir [IPv4](#ipv4) adresi vardır. Birincil IP yapılandırmasına özel bir [IPv6](#ipv6) adresi atayamazsınız.
- Ayrıca kendisine atanmış bir [genel](#public) IPv4 adresi de olabilir. Birincil (IPv4) IP yapılandırmasına ortak bir IPv6 adresi atayamazsınız. 

### <a name="secondary"></a>İkincil

Bir birincil IP yapılandırmasına ek olarak, bir ağ arabirimine atanmış sıfır veya daha fazla ikincil IP yapılandırması olabilir. İkincil IP yapılandırması:

- Kendisine atanmış özel bir IPv4 veya IPv6 adresi olmalıdır. Adres IPv6 ise, ağ arabiriminin yalnızca bir ikincil IP yapılandırması olabilir. Adres IPv4 ise, ağ arabirimine atanmış birden fazla ikincil IP yapılandırması olabilir. Bir ağ arabirimine kaç özel ve ortak IPv4 adresinin atanabileceği hakkında daha fazla bilgi edinmek için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesi.
- Ayrıca, kendisine atanmış bir genel IPv4 veya IPv6 adresi olabilir. Bir ağ arabirimine birden çok IPv4 adresinin atanması, şu senaryolarda yararlı olur:
  - Tek bir sunucuda farklı IP adreslerine ve SSL sertifikalarına sahip birden fazla web sitesi veya hizmetin barındırılması.
  - Bir güvenlik duvarı veya yük dengeleyici gibi bir ağ sanal gereci görevi gören bir sanal makine.
  - Herhangi bir ağ arabirimi için özel IPv4 adreslerinden herhangi birini bir Azure Load Balancer arka uç havuzuna ekleme özelliği. Geçmişte, bir arka uç havuzuna yalnızca birincil ağ arabiriminin birincil IPv4 adresi eklenebilir. Çoklu IPv4 yapılandırmalarının yük dengelenmesi hakkında daha fazla bilgi için bkz. [birden çok IP yapılandırmasına Yük Dengeleme](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesi. 
  - Bir ağ arabirimine atanan bir IPv6 adresinin yükünü dengeleyebilme. Özel bir IPv6 adresine Yük Dengelemesi hakkında daha fazla bilgi edinmek için bkz. [Yük Dengeleme IPv6 adresleri](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesi.

## <a name="address-types"></a>Adres türleri

[IP yapılandırmasına](#ip-configurations)aşağıdaki IP adresi türlerini atayabilirsiniz:

### <a name="private"></a>Özel

Özel [IPv4](#ipv4) veya IPv6 adresleri, bir sanal makinenin bir sanal ağdaki diğer kaynaklarla veya diğer bağlı ağlarda iletişim kurmasını sağlar. 

Varsayılan olarak, Azure DHCP sunucuları, Azure ağ arabiriminin [BIRINCIL IP yapılandırması](#primary) Için özel IPv4 adresini, sanal makine işletim sistemi içindeki ağ arabirimine atar. Gerekli olmadığı takdirde, sanal makinenin işletim sistemi içindeki bir ağ arabiriminin IP adresini asla el ile ayarlamanız gerekir.

> [!WARNING]
> Bir sanal makinenin işletim sistemi içindeki bir ağ arabiriminin birincil IP adresi olarak ayarlanan IPv4 adresi, bir sanal makineye bağlı birincil ağ arabiriminin birincil IP yapılandırmasına atanan özel IPv4 adresinden farklı olursa Azure 'da, sanal makine bağlantısını kaybedersiniz.

Sanal makinenin işletim sistemi içindeki bir ağ arabiriminin IP adresini el ile ayarlamak için gereken senaryolar vardır. Örneğin, bir Azure sanal makinesine birden çok IP adresi eklerken bir Windows işletim sisteminin birincil ve ikincil IP adreslerini el ile ayarlamanız gerekir. Bir Linux sanal makinesi için yalnızca ikincil IP adreslerini el ile ayarlamanız gerekebilir. Ayrıntılar için bkz. [BIR VM işletim SISTEMINE IP adresleri ekleme](virtual-network-multiple-ip-addresses-portal.md#os-config) . Bir IP yapılandırmasına atanan adresi değiştirmeniz gerekiyorsa şunları yapmanız önerilir:

1. Sanal makinenin Azure DHCP sunucularından bir adres aldığından emin olun. Aldıktan sonra, IP adresi atamasını işletim sistemi içinde DHCP olarak değiştirin ve sanal makineyi yeniden başlatın.
2. Sanal makineyi durdurun (serbest bırakın).
3. Azure 'da IP yapılandırması için IP adresini değiştirin.
4. Sanal makineyi başlatın.
5. Azure 'da ayarlandıklarınızı eşleştirmek için işletim sistemi içindeki ikincil IP adreslerini (ve ayrıca Windows 'daki birincil IP adresini) [el ile yapılandırın](virtual-network-multiple-ip-addresses-portal.md#os-config) .

Önceki adımları izleyerek, Azure 'daki ağ arabirimine ve bir sanal makinenin işletim sistemine atanan özel IP adresi aynı kalır. Aboneliğinizdeki bir işletim sistemi içinde IP adreslerini el ile belirlediğiniz sanal makineleri izlemek için, sanal makinelere bir Azure [etiketi](../azure-resource-manager/resource-group-using-tags.md) eklemeyi göz önünde bulundurun. Örneğin, "IP adresi ataması: statik" kullanabilirsiniz. Bu şekilde, işletim sistemi içinde için IP adresini el ile ayarladığınız sanal makineleri aboneliğinizde kolayca bulabilirsiniz.

Bir sanal makinenin aynı veya bağlı sanal ağlardaki diğer kaynaklarla iletişim kurmasına imkan tanıma ek olarak, bir özel IP adresi de bir sanal makinenin Internet 'e giden iletişim kurmasına olanak tanır. Giden bağlantılar, Azure tarafından öngörülemeyen bir genel IP adresine çevrilmiş kaynak ağ adresidir. Azure giden Internet bağlantısı hakkında daha fazla bilgi edinmek için, [Azure giden Internet bağlantısı](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesini okuyun. Internet 'ten gelen bir sanal makinenin özel IP adresi ile iletişim kuramayamezsiniz. Giden bağlantılarınız tahmin edilebilir bir genel IP adresi gerektiriyorsa, genel IP adresi kaynağını bir ağ arabirimiyle ilişkilendirin.

### <a name="public"></a>Genel

Bir genel IP adresi kaynağı aracılığıyla atanan genel IP adresleri, bir sanal makineye Internet üzerinden gelen bağlantıyı etkinleştirir. Internet 'e giden bağlantılar, öngörülebilir bir IP adresi kullanır. Ayrıntılar için bkz. [Azure 'da giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Bir IP yapılandırmasına genel bir IP adresi atayabilir, ancak bunu yapmanız gerekmez. Bir genel IP adresi kaynağını ilişkilendirerek bir sanal makineye genel IP adresi atamadıysanız, sanal makine yine de Internet ile giden iletişim kurabilir. Bu durumda, özel IP adresi Azure tarafından öngörülemeyen bir genel IP adresine çevrilmiş kaynak ağ adresidir. Genel IP adresi kaynakları hakkında daha fazla bilgi için bkz. [genel IP adresi kaynağı](virtual-network-public-ip-address.md).

Bir ağ arabirimine atayabileceğiniz özel ve genel IP adresi sayısı için sınırlar vardır. Ayrıntılar için [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini okuyun.

> [!NOTE]
> Azure, bir sanal makinenin özel IP adresini genel bir IP adresine çevirir. Sonuç olarak, bir sanal makinenin işletim sistemi kendisine atanan genel IP adreslerinin farkında değildir; bu nedenle, işletim sistemi içinde genel bir IP adresini elle atamaya gerek yoktur.

## <a name="assignment-methods"></a>Atama yöntemleri

Ortak ve özel IP adresleri aşağıdaki atama yöntemlerinden biri kullanılarak atanır:

### <a name="dynamic"></a>Dinamik

Dinamik özel IPv4 ve IPv6 (isteğe bağlı) adresleri varsayılan olarak atanır.

- **Yalnızca genel**: Azure, adresi her bir Azure bölgesine özgü bir aralıktan atar. Her bölgeye hangi aralıkların atandığını öğrenmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653). Bir sanal makine durdurulduğunda (serbest bırakıldığında) adres değişebilir, sonra yeniden başlatılır. Atama yöntemini kullanarak bir IP yapılandırmasına genel IPv6 adresi atayamazsınız.
- **Yalnızca özel**: Azure her alt ağ adres aralığında ilk dört adresi ayırır ve adresleri atamaz. Azure, alt ağ adres aralığından sonraki kullanılabilir adresi bir kaynağa atar. Örneğin, alt ağ adres aralığının 10.0.0.0/16 olduğunu varsayarsak ve 10.0.0.0.4-10.0.0.14 adresleri zaten atanmışsa (.0-.3 ayrılmıştır), Azure kaynağa 10.0.0.15 adresini atar. Dinamik, varsayılan ayırma yöntemidir. Dinamik IP adresleri bir kez atandıktan sonra, ancak ağ arabirimi silinirse, aynı sanal ağ içinde farklı bir alt ağa atanırsa veya ayırma yöntemi statik olarak değiştirilip farklı bir IP adresi belirtilirse serbest bırakılır. Varsayılan olarak, dinamik olan ayırma yöntemini statik olarak değiştirdiğinizde Azure dinamik olarak atanmış önceki adresi statik adres olarak atar. 

### <a name="static"></a>Statik

Bir IP yapılandırmasına ortak veya özel statik IPv4 veya IPv6 adresi atayabilirsiniz (isteğe bağlı olarak). Azure 'un statik ortak IPv4 adresleri atama hakkında daha fazla bilgi edinmek için bkz. [genel IP adresleri](virtual-network-public-ip-address.md).

- **Yalnızca genel**: Azure, adresi her bir Azure bölgesine özgü bir aralıktan atar. Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz. Atanan genel IP adresi kaynağı silinene veya atama yöntemi dinamik olarak değiştirilene kadar adres değişmez. Genel IP adresi kaynağı bir IP yapılandırmasıyla ilişkiliyse, atama yöntemi değiştirilmeden önce IP yapılandırmasından ilişkilendirilmesi gerekir.
- **Yalnızca özel**: alt ağın adres aralığından bir adres seçip atarsınız. Alt ağ adres aralığından atadığınız adres, alt ağa adres aralığının ilk dört adresi dışında kalan ve şu anda alt ağda başka bir kaynağa atanmamış olan herhangi bir adres olabilir. Statik adresler ancak ağ arabirimi silindiğinde serbest bırakılır. Ayırma yöntemini statik olarak değiştirirseniz, adres, alt ağın adres aralığında bir sonraki kullanılabilir adres olmasa bile, Azure daha önce atanan dinamik IP adresini statik adres olarak dinamik olarak atar. Ağ arabirimi aynı sanal ağ içinde farklı bir alt ağa atandığında da adres değişir; ama ağ arabirimini farklı bir alt ağa atamak için, önce statik ayırma yöntemini dinamik olarak değiştirmeniz gerekir. Ağ arabirimini farklı bir alt ağa atadıktan sonra, ayırma yöntemini yine statik yapabilir ve yeni alt ağ adres aralığından bir IP adresi atayabilirsiniz.

## <a name="ip-address-versions"></a>IP adresi sürümleri

Adres atarken aşağıdaki sürümleri belirtebilirsiniz:

### <a name="ipv4"></a>IPv4

Her ağ arabirimi, atanmış bir [özel](#private) [IPv4](#ipv4) adresine sahip bir [birincil](#primary) IP yapılandırmasına sahip olmalıdır. Her birinin bir IPv4 özel ve (isteğe bağlı olarak) bir IPv4 [genel](#public) IP adresi olan bir veya daha fazla [İkincil](#secondary) IP yapılandırması ekleyebilirsiniz.

### <a name="ipv6"></a>IPv6

Bir ağ arabiriminin bir ikincil IP yapılandırmasına sıfır veya bir özel [IPv6](#ipv6) adresi atayabilirsiniz. Ağ arabirimi var olan ikincil IP yapılandırmasına sahip olamaz. Her ağ arabirimi en fazla bir IPv6 özel adresine sahip olabilir. İsteğe bağlı olarak bir IPv6 ağ arabirimi yapılandırmasına genel bir IPv6 adresi ekleyebilirsiniz.

> [!NOTE]
> Portalı kullanarak bir IPv6 adresiyle bir ağ arabirimi oluşturabilirsiniz, ancak portalı kullanarak var olan bir ağ arabirimini yeni veya var olan bir sanal makineye ekleyemezsiniz. PowerShell veya Azure CLı kullanarak özel bir IPv6 adresine sahip bir ağ arabirimi oluşturun ve ardından bir sanal makine oluştururken ağ arabirimini bağlayın. Var olan bir sanal makineye atanmış özel bir IPv6 adresi olan bir ağ arabirimi iliştiremezsiniz. Herhangi bir aracı (portal, CLı veya PowerShell) kullanarak bir sanal makineye bağlı herhangi bir ağ arabirimi için bir IP yapılandırmasına özel bir IPv6 adresi ekleyemezsiniz.

Birincil veya ikincil IP yapılandırmasına ortak bir IPv6 adresi atayamazsınız.

## <a name="skus"></a>SKU

Temel veya standart SKU ile genel bir IP adresi oluşturulur. SKU farklılıkları hakkında daha fazla bilgi için bkz. [genel IP adreslerini yönetme](virtual-network-public-ip-address.md).

> [!NOTE]
> Standart bir SKU genel IP adresini bir sanal makinenin ağ arabirimine atadığınızda amaçlanan trafiğe bir [ağ güvenlik grubuyla](security-overview.md#network-security-groups) açıkça izin vermeniz gerekir. Bir ağ güvenlik grubu oluşturup ilişkilendirene ve istenen trafiğe açıkça izin verene kadar kaynakla erişim kurma girişimleri başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar
Farklı IP yapılandırmalarına sahip bir sanal makine oluşturmak için aşağıdaki makaleleri okuyun:

|Görev|Araç|
|---|---|
|Birden çok ağ arabirimi ile VM oluşturma|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Birden çok IPv4 adresi olan tek bir NIC VM oluşturma|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Özel bir IPv6 adresine sahip tek bir NIC sanal makinesi oluşturma (Azure Load Balancer arka plan)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager şablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
