---
title: Azure ağ arabirimi oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Bir ağ arabiriminin ne olduğunu ve nasıl oluşturulacağını, ayarlarının nasıl değiştirileceğini ve bir tane silineceğini öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: 809e40f6616e8ab022a31d8dd29d4a5386c5e844
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838420"
---
# <a name="create-change-or-delete-a-network-interface"></a>Ağ arabirimi oluşturma, değiştirme veya silme

Bir ağ arabirimini oluşturmayı, ayarlarını değiştirmeyi ve silmeyi öğrenin. Bir ağ arabirimi, bir Azure sanal makinesinin internet, Azure ve şirket içi kaynaklarla iletişim kurmasını sağlar. Azure portal kullanarak bir sanal makine oluştururken, Portal sizin için varsayılan ayarlarla bir ağ arabirimi oluşturur. Bunun yerine, özel ayarlarla ağ arabirimleri oluşturmayı tercih edebilir ve bir sanal makineye bir veya daha fazla ağ arabirimi oluşturabilirsiniz. Mevcut bir ağ arabirimi için varsayılan ağ arabirimi ayarlarını da değiştirmek isteyebilirsiniz. Bu makalede, özel ayarlarla bir ağ arabirimi oluşturma, ağ filtresi (ağ güvenlik grubu) ataması, alt ağ atama, DNS sunucusu ayarları ve IP iletimi gibi mevcut ayarları değiştirme ve bir ağ arabirimini silme işlemleri açıklanmaktadır.

Bir ağ arabirimi için IP adresi eklemeniz, değiştirmeniz veya kaldırmanız gerekirse bkz. [IP adreslerini yönetme](virtual-network-network-interface-addresses.md). Sanal makinelerden ağ arabirimleri eklemeniz veya ağ arabirimlerini kaldırmanız gerekiyorsa bkz. [ağ arabirimlerini ekleme veya kaldırma](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, https://portal.azure.comaçın ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.28 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure ile bağlantı oluşturmak için `az login` çalıştırmanız da gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="create-a-network-interface"></a>Ağ arabirimi oluşturma

Azure portal kullanarak bir sanal makine oluştururken, Portal sizin için varsayılan ayarlarla bir ağ arabirimi oluşturur. Tüm ağ arabirimi ayarlarınızı belirtirseniz, özel ayarlarla bir ağ arabirimi oluşturabilir ve sanal makineyi oluştururken ağ arabirimini bir sanal makineye iliştirebilirsiniz (PowerShell veya Azure CLı kullanarak). Ayrıca, bir ağ arabirimi oluşturup var olan bir sanal makineye (PowerShell veya Azure CLı kullanarak) ekleyebilirsiniz. Var olan bir ağ arabirimiyle bir sanal makine oluşturmayı veya var olan sanal makinelerden ağ arabirimlerini ekleme veya kaldırma hakkında bilgi edinmek için bkz. [ağ arabirimlerini ekleme veya kaldırma](virtual-network-network-interface-vm.md). Bir ağ arabirimi oluşturmadan önce, içinde bir ağ arabirimi oluşturduğunuz aynı konum ve abonelikte var olan bir [sanal ağınızın](manage-virtual-network.md) olması gerekir.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. **Ağ arabirimleri**altında **+ Ekle** ' yi seçin.
3. Aşağıdaki ayarlara ait değerleri girin veya seçin ve ardından **Oluştur**' u seçin:

    |Ayar|Gerekli mi?|Ayrıntılar|
    |---|---|---|
    |Ad|Evet|Ad, seçtiğiniz kaynak grubu içinde benzersiz olmalıdır. Zaman içinde, büyük olasılıkla Azure aboneliğinizde birkaç ağ arabirimine sahip olacaksınız. Birkaç ağ arabirimini yönetmeyi kolaylaştırmak için bir adlandırma kuralı oluşturma konusunda öneriler için bkz. [adlandırma kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Ağ arabirimi oluşturulduktan sonra ad değiştirilemez.|
    |Sanal ağ|Evet|Ağ arabirimi için sanal ağı seçin. Ağ arabirimiyle aynı abonelikte ve konumda bulunan bir sanal ağa yalnızca bir ağ arabirimi atayabilirsiniz. Bir ağ arabirimi oluşturulduktan sonra, atandığı sanal ağı değiştiremezsiniz. Ağ arabirimini ekleyeceğiniz sanal makine, ağ arabirimiyle aynı konum ve abonelikte de bulunmalıdır.|
    |Alt ağ|Evet|Seçtiğiniz sanal ağ içinden bir alt ağ seçin. Ağ arabiriminin oluşturulduktan sonra atandığı alt ağı değiştirebilirsiniz.|
    |Özel IP adresi ataması|Evet| Bu ayarda, IPv4 adresi için atama yöntemini seçiyoruz. Şu atama yöntemlerinden birini seçin: **dinamik:** bu seçeneği belirlediğinizde, Azure seçtiğiniz alt ağın adres alanından bir sonraki kullanılabilir adresi otomatik olarak atar. **Statik:** Bu seçeneği seçerken, seçtiğiniz alt ağın adres alanından kullanılabilir bir IP adresini el ile atamanız gerekir. Statik ve dinamik adresler, siz değiştirene veya ağ arabirimi silinene kadar değişmez. Ağ arabirimi oluşturulduktan sonra atama yöntemini değiştirebilirsiniz. Azure DHCP sunucusu bu adresi, sanal makinenin işletim sistemi içindeki ağ arabirimine atar.|
    |Ağ güvenlik grubu|Hayır| **None**olarak ayarlı bırakın, var olan bir [ağ güvenlik grubunu](security-overview.md)seçin veya [bir ağ güvenlik grubu oluşturun](tutorial-filter-network-traffic.md). Ağ güvenlik grupları bir ağ arabiriminden gelen ve giden ağ trafiğini filtrelemenizi sağlar. Bir ağ arabirimine sıfır veya bir ağ güvenlik grubu uygulayabilirsiniz. Ağ arabiriminin atandığı alt ağa sıfır veya bir ağ güvenlik grubu da uygulanabilir. Ağ arabirimine bir ağ güvenlik grubu uygulandığında ve ağ arabiriminin atandığı alt ağ için bazı durumlarda beklenmedik sonuçlar oluşur. Ağ arabirimlerine ve alt ağlara uygulanan ağ güvenlik gruplarının sorunlarını gidermek için bkz. [ağ güvenlik gruplarında sorun giderme](diagnose-network-traffic-filter-problem.md).|
    |Abonelik|Evet|Azure [aboneliklerinizden](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)birini seçin. Bir ağ arabirimini ve bağlandığınız sanal ağı aynı abonelikte mevcut olması gerekir.|
    |Özel IP adresi (IPv6)|Hayır| Bu onay kutusunu seçerseniz, ağ arabirimine atanan IPv4 adresine ek olarak ağ arabirimine bir IPv6 adresi atanır. Ağ arabirimleriyle IPv6 kullanımı hakkında önemli bilgiler için bu makalenin IPv6 bölümüne bakın. IPv6 adresi için bir atama yöntemi seçemezsiniz. Bir IPv6 adresi atamayı seçerseniz, bu, dinamik yöntemle atanır.
    |IPv6 adı (yalnızca **özel IP adresi (IPv6)** onay kutusu işaretlendiğinde görüntülenir) |Evet, **özel IP adresi (IPv6)** onay kutusu işaretliyse.| Bu ad, ağ arabirimi için bir ikincil IP yapılandırmasına atanır. IP konfigürasyonları hakkında daha fazla bilgi için bkz. [ağ arabirimi ayarlarını görüntüleme](#view-network-interface-settings).|
    |Kaynak grubu|Evet|Var olan bir [kaynak grubunu](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) seçin veya bir kaynak grubu oluşturun. Bir ağ arabirimi, sizin eklediğiniz sanal makineden veya bağlandığınız sanal makineye göre aynı veya farklı bir kaynak grubunda bulunabilir.|
    |Konum|Evet|Bir ağ arabirimini ve bağlandığınız sanal ağı, bir bölge olarak da adlandırılan aynı [konumda](https://azure.microsoft.com/regions)bulunmalıdır.|

Portal, bir genel IP adresini oluştururken ağ arabirimine atama seçeneğini sağlamaz; ancak Portal, bir genel IP adresi oluşturur ve portalı kullanarak bir sanal makine oluşturduğunuzda ağ arabirimine atar. Oluşturduktan sonra ağ arabirimine genel IP adresi ekleme hakkında bilgi edinmek için bkz. [IP adreslerini yönetme](virtual-network-network-interface-addresses.md). Genel IP adresi olan bir ağ arabirimi oluşturmak istiyorsanız, ağ arabirimini oluşturmak için CLı veya PowerShell kullanmanız gerekir.

Portal, ağ arabirimi oluştururken uygulama güvenlik gruplarına ağ arabirimini atama seçeneği sağlamaz, ancak Azure CLı ve PowerShell bunu yapmaz. Ancak, ağ arabirimi bir sanal makineye bağlı olduğu sürece, portalı kullanarak bir uygulama güvenlik grubuna mevcut bir ağ arabirimi atayabilirsiniz. Bir uygulama güvenlik grubuna ağ arabirimi atamayı öğrenmek için bkz. [uygulama güvenlik gruplarına ekleme veya kaldırma](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure, ağ arabirimi bir sanal makineye eklendikten ve sanal makine ilk kez başlatıldıktan sonra ağ arabirimine bir MAC adresi atar. Azure 'un ağ arabirimine atadığı MAC adresini belirtemezsiniz. Ağ arabirimi silininceye veya birincil ağ arabiriminin birincil IP yapılandırmasına atanan özel IP adresi değiştirilinceye kadar MAC adresi ağ arabirimine atanmış olarak kalır. IP adresleri ve IP konfigürasyonları hakkında daha fazla bilgi edinmek için bkz. [IP adreslerini yönetme](virtual-network-network-interface-addresses.md)

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Ağ arabirimi ayarlarını görüntüle

Bir ağ arabirimi oluşturulduktan sonra, çoğu ayarı görüntüleyebilir ve değiştirebilirsiniz. Portal, ağ arabirimi için DNS sonekini veya uygulama güvenlik grubu üyeliğini görüntülemez. DNS sonekini ve uygulama güvenlik grubu üyeliğini görüntülemek için PowerShell veya Azure CLı [komutlarını](#view-settings-commands) kullanabilirsiniz.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Listeden ayarlarını görüntülemek veya değiştirmek istediğiniz ağ arabirimini seçin.
3. Seçtiğiniz ağ arabirimi için aşağıdaki öğeler listelenir:
   - **Genel bakış:** Ağ arabirimi hakkında, kendisine atanan IP adresleri, ağ arabiriminin atandığı sanal ağ/alt ağ ve ağ arabiriminin eklendiği sanal makine (bire eklenmişse) hakkında bilgi sağlar. Aşağıdaki resimde **mywebserver256**: ![ağ arabirimine genel bakış adlı bir ağ arabirimine ilişkin genel bakış ayarları gösterilmektedir](./media/virtual-network-network-interface/nic-overview.png)

     **Kaynak grubunun** veya **abonelik adının**yanındaki (**Değiştir**) seçeneğini belirleyerek bir ağ arabirimini farklı bir kaynak grubuna veya aboneliğe taşıyabilirsiniz. Ağ arabirimini taşırsanız, ağ arabirimiyle ilişkili tüm kaynakları onunla birlikte taşımanız gerekir. Ağ arabirimi bir sanal makineye bağlıysa, örneğin, sanal makineyi ve diğer sanal makineyle ilgili kaynakları da taşımanız gerekir. Bir ağ arabirimini taşımak için bkz. [kaynağı yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). Makalede önkoşulları ve Azure portal, PowerShell ve Azure CLı kullanarak kaynakların nasıl taşınacağı listelenmektedir.
   - **IP yapılandırması:** IP yapılandırmalarına atanan ortak ve özel IPv4 ve IPv6 adresleri burada listelenmiştir. Bir IP yapılandırmasına bir IPv6 adresi atanmışsa, adres görüntülenmez. IP konfigürasyonları ve IP adreslerini ekleme ve kaldırma hakkında daha fazla bilgi edinmek için bkz. [Azure ağ arabirimi IÇIN IP adreslerini yapılandırma](virtual-network-network-interface-addresses.md). IP iletme ve alt ağ atama da bu bölümde yapılandırılır. Bu ayarlar hakkında daha fazla bilgi için bkz. [IP Iletmeyi etkinleştirme veya devre dışı bırakma](#enable-or-disable-ip-forwarding) ve [alt ağ atamasını değiştirme](#change-subnet-assignment).
   - **DNS sunucuları:** Azure DHCP sunucuları tarafından bir ağ arabiriminin hangi DNS sunucusuna atandığını belirtebilirsiniz. Ağ arabirimi, ağ arabiriminin atandığı sanal ağdan ayarı alabilir veya atandığı sanal ağ için ayarı geçersiz kılan özel bir ayara sahiptir. Görüntülendiklerinizi değiştirmek için bkz. [DNS sunucularını değiştirme](#change-dns-servers).
   - **Ağ güvenlik grubu (NSG):** Hangi NSG 'nin ağ arabirimiyle ilişkili olduğunu (varsa) görüntüler. NSG, ağ arabirimine yönelik ağ trafiğini filtrelemek için gelen ve giden kuralları içerir. Bir NSG ağ arabirimiyle ilişkiliyse, ilişkili NSG 'nin adı görüntülenir. Görüntülendiklerinizi değiştirmek için, bkz. [bir ağ güvenlik grubunu ilişkilendirme veya](#associate-or-dissociate-a-network-security-group)ilişkisini kaldırma.
   - **Özellikler:** Ağ arabirimi hakkında, MAC adresi (ağ arabirimi bir sanal makineye bağlı değilse boş) ve içinde mevcut olan abonelik gibi anahtar ayarları görüntüler.
   - **Etkin güvenlik kuralları:**  Ağ arabirimi çalışan bir sanal makineye bağlıysa ve bir NSG ağ arabirimiyle, kendisine atanmış alt ağ veya her ikisine de ilişkiliyse güvenlik kuralları listelenir. Nelerin görüntülendikleriniz hakkında daha fazla bilgi edinmek için bkz. [etkin güvenlik kurallarını görüntüleme](#view-effective-security-rules). NSG 'ler hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](security-overview.md).
   - **Geçerli rotalar:** Ağ arabirimi çalışan bir sanal makineye eklenmişse yollar listelenir. Yollar, Azure varsayılan yollarının, Kullanıcı tanımlı yolların ve ağ arabiriminin atandığı alt ağ için var olabilecek tüm BGP yollarının bir birleşimidir. Nelerin görüntülendikleriniz hakkında daha fazla bilgi edinmek için bkz. [geçerli yolları görüntüleme](#view-effective-routes). Azure varsayılan yolları ve Kullanıcı tanımlı rotalar hakkında daha fazla bilgi edinmek için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md).
   - **Ortak Azure Resource Manager ayarları:**  Ortak Azure Resource Manager ayarları hakkında daha fazla bilgi için bkz. [etkinlik günlüğü](../azure-monitor/platform/activity-logs-overview.md), [ERIŞIM denetimi (IAM)](../role-based-access-control/overview.md), [Etiketler](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [kilitler](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ve [Otomasyon betiği](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Komut**

Bir ağ arabirimine bir IPv6 adresi atanırsa, PowerShell çıktısı adresin atandığı olguyu döndürür, ancak atanan adresi döndürmez. Benzer şekilde, CLı adresin atandığı olguyu döndürür, ancak adresin çıktısında *null* değerini döndürür.

|Araç|Komut|
|---|---|
|CLI|ağ arabirimlerini abonelikte görüntülemek için [az Network Nic List](/cli/azure/network/nic) ; [az Network Nic](/cli/azure/network/nic) bir ağ arabiriminin ayarlarını görüntülemek için gösterir|
|PowerShell|Abonelik içindeki ağ arabirimlerini görüntülemek için [Get-Aznetworkınterface](/powershell/module/az.network/get-aznetworkinterface) veya bir ağ arabirimi için ayarları görüntüle|

## <a name="change-dns-servers"></a>DNS sunucularını değiştirme

DNS sunucusu, Azure DHCP sunucusu tarafından sanal makine işletim sistemi içindeki ağ arabirimine atanır. Atanan DNS sunucusu, DNS sunucusu ayarının bir ağ arabirimi için olduğu her şey olur. Bir ağ arabirimi için ad çözümleme ayarları hakkında daha fazla bilgi için bkz. [sanal makineler Için ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md). Ağ arabirimi ayarları sanal ağdan alabilir veya sanal ağ ayarını geçersiz kılan kendi benzersiz ayarlarını kullanabilir.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Bir DNS sunucusunu listeden değiştirmek istediğiniz ağ arabirimini seçin.
3. **Ayarlar**altında **DNS sunucuları** ' nı seçin.
4. Aşağıdakilerden birini seçin:
   - **Sanal ağdan al**: ağ arabiriminin atandığı sanal ağ IÇIN tanımlanan DNS sunucusu ayarını devralacak şekilde bu seçeneği belirleyin. Sanal ağ düzeyinde, özel bir DNS sunucusu ya da Azure tarafından belirtilen DNS sunucusu tanımlanmıştır. Azure tarafından sunulan DNS sunucusu, aynı sanal ağa atanan kaynakların ana bilgisayar adlarını çözümleyebilir. Farklı sanal ağlara atanmış kaynakları çözümlemek için FQDN kullanılması gerekir.
   - **Özel**: birden çok sanal ağ üzerindeki adları çözümlemek IÇIN kendi DNS sunucunuzu yapılandırabilirsiniz. DNS sunucusu olarak kullanmak istediğiniz sunucunun IP adresini girin. Belirttiğiniz DNS sunucusu adresi yalnızca bu ağ arabirimine atanır ve ağ arabiriminin atandığı sanal ağ için herhangi bir DNS ayarını geçersiz kılar.
     >[!Note]
     >VM, bir kullanılabilirlik kümesinin parçası olan bir NIC kullanıyorsa, kullanılabilirlik kümesinin parçası olan tüm NIC 'lerden sanal makinelerin her biri için belirtilen tüm DNS sunucuları devralınacaktır.
5. **Kaydet**’i seçin.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-Aznetworkınterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>IP iletmeyi etkinleştirme veya devre dışı bırakma

IP iletimi, bir ağ arabiriminin bağlı olduğu sanal makineye izin sağlar:
- Ağ arabirimine atanmış IP yapılandırmalarının hiçbirine atanan IP adreslerinden birine yönelik olmayan ağ trafiği alın.
- Ağ trafiğinin IP yapılandırmalarının birine atanenden farklı bir kaynak IP adresine sahip ağ trafiği gönderin.

Bu ayar, sanal makinenin iletmesi gereken trafiği alan sanal makineye bağlı her ağ arabirimi için etkinleştirilmelidir. Bir sanal makine, birden çok ağ arabirimine veya buna bağlı tek bir ağ arabirimine sahip olup olmadığını trafiği iletebilir. IP iletimi bir Azure ayarı olsa da, sanal makinenin güvenlik duvarı, WAN iyileştirmesi ve yük dengeleme uygulamaları gibi trafiği iletebilmesi için bir uygulama çalıştırması gerekir. Bir sanal makine ağ uygulamaları çalıştırıyorsa, sanal makine genellikle ağ sanal gereci olarak adlandırılır. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)'nde ağ sanal gereçlerini dağıtmaya hazırlanma listesini görebilirsiniz. IP iletimi, genellikle Kullanıcı tanımlı yollarla kullanılır. Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar](virtual-networks-udr-overview.md).

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. İçin IP iletmeyi etkinleştirmek veya devre dışı bırakmak istediğiniz ağ arabirimini seçin.
3. **Ayarlar** bölümünde **IP yapılandırması** ' nı seçin.
4. Ayarı değiştirmek için **etkin** veya **devre dışı** (varsayılan ayar) seçeneğini belirleyin.
5. **Kaydet**’i seçin.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-Aznetworkınterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alt ağ atamasını Değiştir

Ağ arabiriminin atandığı alt ağı (sanal ağı) değiştirebilirsiniz.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Alt ağ atamasını değiştirmek istediğiniz ağ arabirimini seçin.
3. **Ayarlar**altında **IP yapılandırması** ' nı seçin. Listelenen herhangi bir IP yapılandırması için herhangi bir özel IP adresi **(statik)** varsa, aşağıdaki ADıMLARı tamamlayarak IP adresi atama yöntemini dinamik olarak değiştirmeniz gerekir. Ağ arabirimi için alt ağ atamasını değiştirmek üzere, tüm özel IP adreslerinin dinamik atama yöntemiyle atanması gerekir. Adresler dinamik yöntemle atanırsa, beş adımdan devam edin. Statik atama yöntemiyle herhangi bir IPv4 adresi atanmışsa, atama yöntemini dinamik olarak değiştirmek için aşağıdaki adımları izleyin:
   - IP yapılandırmaları listesinden için IPv4 adresi atama yöntemini değiştirmek istediğiniz IP yapılandırmasını seçin.
   - Özel IP adresi **atama** yöntemi için **dinamik** ' i seçin. Statik atama yöntemiyle bir IPv6 adresi atayamazsınız.
   - **Kaydet**’i seçin.
4. **Alt** ağ açılan listesinden ağ arabirimini taşımak istediğiniz alt ağı seçin.
5. **Kaydet**’i seçin. Yeni dinamik adresler yeni alt ağ için alt ağ adres aralığından atanır. Ağ arabirimini yeni bir alt ağa atadıktan sonra seçerseniz, yeni alt ağ adres aralığından statik bir IPv4 adresi atayabilirsiniz. Bir ağ arabirimi için IP adreslerini ekleme, değiştirme ve kaldırma hakkında daha fazla bilgi için bkz. [IP adreslerini yönetme](virtual-network-network-interface-addresses.md).

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic ip-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-Aznetworkınterfaceipconfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Uygulama güvenlik gruplarına Ekle veya Kaldır

Ağ arabirimi bir sanal makineye bağlıysa, portalı kullanarak bir ağ arabirimini yalnızca bir uygulama güvenlik grubundan kaldırabilirsiniz. Ağ arabiriminin bir sanal makineye bağlı olup olmadığına bakılmaksızın, bir ağ arabirimini bir uygulama güvenlik grubundan bir ağ arabirimi eklemek veya kaldırmak için PowerShell veya Azure CLı kullanabilirsiniz. [Uygulama güvenlik grupları](security-overview.md#application-security-groups) ve [uygulama güvenlik grubu oluşturma](manage-network-security-group.md)hakkında daha fazla bilgi edinin.

1. Portalın üst kısmındaki *kaynak, hizmet ve belgeleri ara* kutusuna, bir uygulama güvenlik grubuna eklemek veya kaldırmak istediğiniz bir ağ arabirimine sahip bir sanal makinenin adını yazmaya başlayın. SANAL makinenizin adı arama sonuçlarında göründüğünde, bunu seçin.
2. **AYARLAR**'ın altında **Ağ İletişimi**’ni seçin.  **Uygulama güvenlik gruplarını Yapılandır**' ı seçin, ağ arabirimini eklemek istediğiniz uygulama güvenlik gruplarını seçin veya ağ arabirimini kaldırmak istediğiniz uygulama güvenlik gruplarının seçimini kaldırın ve ardından **Kaydet**' i seçin. Aynı uygulama güvenlik grubuna yalnızca aynı sanal ağda bulunan ağ arabirimleri eklenebilir. Uygulama güvenlik grubu, ağ arabirimiyle aynı konumda bulunmalıdır.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-Aznetworkınterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Bir ağ güvenlik grubunu ilişkilendir veya ilişkisini kaldır

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *ağ arabirimleri* girin. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Bir ağ güvenlik grubunu ilişkilendirmek istediğiniz veya bir ağ güvenlik grubunun ilişkisini kaldırmak istediğiniz ağ arabirimini listeden seçin.
3. **Ayarlar**altında **ağ güvenlik grubu** ' nu seçin.
4. **Düzenle**’yi seçin.
5. Ağ Güvenlik **grubu ' nu seçin ve ağ** arabirimiyle ilişkilendirmek istediğiniz ağ güvenlik grubunu seçin veya bir ağ güvenlik grubunun ilişkilendirmesini kaldırmak için **hiçbiri**' ni seçin.
6. **Kaydet**’i seçin.

**Komut**

- Azure CLı: [az Network Nic Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [set-Aznetworkınterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Ağ arabirimini silme

Bir ağ arabirimini, bir sanal makineye bağlı olmadığı sürece silebilirsiniz. Bir ağ arabirimi bir sanal makineye bağlıysa, önce sanal makineyi durdurulmuş (serbest bırakıldı) duruma yerleştirmeniz, ardından ağ arabirimini sanal makineden ayırmanız gerekir. Bir ağ arabirimini bir sanal makineden ayırmak için, bir [sanal makineden ağ arabirimini ayırma](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm)bölümündeki adımları uygulayın. Ancak sanal makineye bağlı tek ağ arabiriminiz varsa, bir ağ arabirimini sanal makineden ayıramazsınız. Bir sanal makinede her zaman en az bir ağ arabirimi eklenmiş olmalıdır. Bir sanal makineyi silmek ona bağlı tüm ağ arabirimlerini ayırır, ancak ağ arabirimlerini silmez.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *ağ arabirimleri*yazın. Arama sonuçlarında **ağ arabirimleri** görüntülendiğinde, bunu seçin.
2. Ağ arabirimlerinin listesinden silmek istediğiniz ağ arabiriminin sağ tarafındaki **..** . seçeneğini belirleyin.
3. **Sil**’i seçin.
4. Ağ arabirimini silmeyi onaylamak için **Evet** ' i seçin.

Bir ağ arabirimini sildiğinizde, kendisine atanan MAC veya IP adresleri serbest bırakılır.

**Komut**

|Araç|Komut|
|---|---|
|CLI|[az Network Nic Delete](/cli/azure/network/nic)|
|PowerShell|[Remove-Aznetworkınterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Bağlantı sorunlarını çözme

Bir sanal makineden veya bir sanal makineyle iletişim kuramayadıysanız ağ güvenlik grubu güvenlik kuralları veya bir ağ arabirimi için geçerli rotalar soruna neden olabilir. Sorunu çözmeye yardımcı olmak için aşağıdaki seçenekleriniz vardır:

### <a name="view-effective-security-rules"></a>Geçerli güvenlik kurallarını görüntüleme

Bir sanal makineye bağlı her ağ arabirimine yönelik etkili güvenlik kuralları, bir ağ güvenlik grubunda ve [varsayılan güvenlik kurallarında](security-overview.md#default-security-rules)oluşturduğunuz kuralların bir birleşimidir. Bir ağ arabirimi için geçerli güvenlik kurallarını anlamak, bir sanal makineden veya bir sanal makineden neden iletişim kuramayadığınızı belirlemenize yardımcı olabilir. Çalışan bir sanal makineye bağlı herhangi bir ağ arabirimi için etkili kuralları görüntüleyebilirsiniz.

1. Portalın üst kısmındaki arama kutusuna, için geçerli güvenlik kurallarını görüntülemek istediğiniz bir sanal makinenin adını girin. Bir sanal makinenin adını bilmiyorsanız, arama kutusuna *sanal makineler* girin. Arama sonuçlarında **sanal makineler** görüntülendiğinde, bunu seçin ve ardından listeden bir sanal makine seçin.
2. **Ayarlar**altında **ağ** ' ı seçin.
3. Bir ağ arabiriminin adını seçin.
4. **Destek + sorun giderme**altında **geçerli güvenlik kuralları** ' nı seçin.
5. Gerekli gelen ve giden iletişiminizin doğru kurallarının olup olmadığını öğrenmek için etkili güvenlik kuralları listesini gözden geçirin. [Ağ güvenlik grubuna genel bakış](security-overview.md)listesinde gördükleriniz hakkında daha fazla bilgi edinin.

Azure ağ Izleyicisi 'nin IP akışı doğrulama özelliği, güvenlik kurallarının bir sanal makine ve uç nokta arasındaki iletişimi engelleyip engellemediğini belirlemenize de yardımcı olabilir. Daha fazla bilgi için bkz. [IP akışı doğrulama](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Komut**

- Azure CLı: [az Network Nic List-etkin-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Geçerli yolları görüntüle

Bir sanal makineye bağlı ağ arabirimlerine yönelik etkili rotalar, bir Azure sanal ağ geçidi aracılığıyla BGP aracılığıyla şirket içi ağlardan yayılan bir varsayılan yollar, oluşturduğunuz yollar ve tüm yollar birleşimidir. Bir ağ arabirimine yönelik geçerli yolların anlaşılmasının nedeni, bir sanal makineden veya bir sanal makineden neden iletişim kuramayadığınızı belirlemenize yardımcı olabilir. Çalışan bir sanal makineye bağlı herhangi bir ağ arabirimi için geçerli yolları görüntüleyebilirsiniz.

1. Portalın üst kısmındaki arama kutusuna, için geçerli güvenlik kurallarını görüntülemek istediğiniz bir sanal makinenin adını girin. Bir sanal makinenin adını bilmiyorsanız, arama kutusuna *sanal makineler* girin. Arama sonuçlarında **sanal makineler** görüntülendiğinde, bunu seçin ve ardından listeden bir sanal makine seçin.
2. **Ayarlar**altında **ağ** ' ı seçin.
3. Bir ağ arabiriminin adını seçin.
4. **Destek + sorun giderme**altında **geçerli rotalar** ' ı seçin.
5. Gerekli gelen ve giden iletişim için doğru yolların mevcut olup olmadığını anlamak üzere etkin yolların listesini gözden geçirin. [Yönlendirme genel bakış](virtual-networks-udr-overview.md)bölümünde listede gördükleriniz hakkında daha fazla bilgi edinin.

Azure ağ Izleyicisi 'nin sonraki atlama özelliği, yolların bir sanal makine ve uç nokta arasındaki iletişimi engelleyip engellemediğini belirlemenize de yardımcı olabilir. Daha fazla bilgi için bkz. [sonraki atlama](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Komut**

- Azure CLı: [az Network Nic Show-etkin-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>İzinler

Ağ arabirimlerinde görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun izinlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                                                     | Ad                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft. Network/NetworkInterfaces/Read                                   | Ağ arabirimi al                                     |
| Microsoft. Network/NetworkInterfaces/Write                                  | Ağ arabirimi oluştur veya güncelleştir                        |
| Microsoft. Network/NetworkInterfaces/JOIN/Action                            | Sanal makineye bir ağ arabirimi iliştirme           |
| Microsoft. Network/NetworkInterfaces/Delete                                 | Ağ arabirimini Sil                                  |
| Microsoft. Network/NetworkInterfaces/Jodavetli Aprivateıp/eylem                | Servi aracılığıyla bir kaynağı ağ arabirimine ekleme...     |
| Microsoft. Network/NetworkInterfaces/effectiveRouteTable/Action             | Ağ arabirimi etkin yol tablosu al               |
| Microsoft. Network/NetworkInterfaces/effectiveNetworkSecurityGroups/Action  | Ağ arabirimi etkin güvenlik gruplarını al           |
| Microsoft. Network/NetworkInterfaces/loadBalancers/Read                     | Ağ arabirimi yük dengeleyicileri al                      |
| Microsoft. Network/NetworkInterfaces/serviceAssociations/Read               | Hizmet ilişkilendirmesini al                                   |
| Microsoft. Network/NetworkInterfaces/serviceAssociations/Write              | Hizmet ilişkilendirmesi oluşturma veya güncelleştirme                    |
| Microsoft. Network/NetworkInterfaces/serviceAssociations/Delete             | Hizmet ilişkilendirmesini Sil                                |
| Microsoft. Network/NetworkInterfaces/serviceAssociations/Validate/Action    | Hizmet ilişkilendirmesini doğrula                              |
| Microsoft. Network/NetworkInterfaces/ipconfigurations/Read                  | Ağ arabirimi IP yapılandırmasını al                    |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanarak bırden çok NIC ile VM oluşturma
- [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) veya [PowerShell](virtual-network-multiple-ip-addresses-powershell.md) kullanarak birden çok IPv4 adresı IÇEREN tek bir NIC VM oluşturma
- [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)veya [Azure Resource Manager şablonunu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kullanarak özel bir IPv6 adresine (Azure Load Balancer arka planda) sahip tek bir NIC sanal makinesi oluşturma
- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonu](template-samples.md) kullanarak bir ağ arabirimi oluşturun
- Sanal ağlar için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama
