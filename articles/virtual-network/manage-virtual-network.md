---
title: Azure sanal ağını oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Azure 'da bir sanal ağ oluşturmayı, değiştirmeyi veya silmeyi öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 2c2994c310369a0a6fe26ccc2c1e2e5de6680349
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084707"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Sanal ağ oluşturma, değiştirme veya silme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Var olan bir sanal ağ için sanal ağ oluşturma ve silme ve DNS sunucuları ve IP adresi alanları gibi ayarları değiştirme hakkında bilgi edinin. Sanal ağlarınız için yeni başladıysanız, [sanal ağa genel bakış](virtual-networks-overview.md) veya bir [öğreticiyi](quick-create-portal.md)tamamlama hakkında daha fazla bilgi edinebilirsiniz. Bir sanal ağ, alt ağlar içerir. Alt ağları oluşturma, değiştirme ve silme hakkında bilgi edinmek için bkz. [alt ağları yönetme](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, https://portal.azure.comaçın ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure ile bağlantı oluşturmak için `az login` çalıştırmanız da gerekir.
- Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. **Sanal ağ** > **ağ** > **kaynak oluştur** ' u seçin.
2. Aşağıdaki ayarlara ait değerleri girin veya seçin ve ardından **Oluştur**' u seçin:
   - **Ad**: ad, içinde sanal ağı oluşturmak için seçtiğiniz [kaynak grubunda](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) benzersiz olmalıdır. Sanal ağ oluşturulduktan sonra adı değiştiremezsiniz. Zaman içinde birden çok sanal ağ oluşturabilirsiniz. Adlandırma önerileri için bkz. [adlandırma kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Bir adlandırma kuralını takip etmek, birden çok sanal ağı yönetmeyi kolaylaştırır.
   - **Adres alanı**: bir sanal ağın adres alanı, CIDR gösteriminde belirtilen bir veya daha fazla çakışmayan adres aralığından oluşur. Tanımladığınız adres aralığı ortak veya özel olabilir (RFC 1918). Adres aralığını genel veya özel olarak tanımladığınıza göre, adres aralığına yalnızca sanal ağ içinden, birbirine bağlı sanal ağlardan ve sanal ağa bağladığınız şirket içi ağlardan erişilebilir. Aşağıdaki adres aralıklarını ekleyemezsiniz:
     - 224.0.0.0/4 (çok noktaya yayın)
     - 255.255.255.255/32 (yayın)
     - 127.0.0.0/8 (geri döngü)
     - 169.254.0.0/16 (bağlantı-yerel)
     - 168.63.129.16/32 (iç DNS, DHCP ve Azure Load Balancer [durum araştırması](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Portalda sanal ağı oluştururken yalnızca bir adres aralığı tanımlayabilmenize karşın, sanal ağ oluşturulduktan sonra adres alanına daha fazla adres aralığı ekleyebilirsiniz. Mevcut bir sanal ağa adres aralığı ekleme hakkında bilgi edinmek için bkz. [adres aralığı ekleme veya kaldırma](#add-or-remove-an-address-range).

     >[!WARNING]
     >Bir sanal ağda başka bir sanal ağla veya şirket içi ağla çakışan adres aralıkları varsa, iki ağ bağlanamaz. Bir adres aralığı tanımlayabilmeniz için önce sanal ağı diğer sanal ağlara veya gelecekte şirket içi ağlara bağlamak isteyip istemediğinizi göz önünde bulundurun.
     >
     >

     - **Alt ağ adı**: alt ağ adı sanal ağ içinde benzersiz olmalıdır. Alt ağ oluşturulduktan sonra alt ağ adını değiştiremezsiniz. Bir sanal ağ oluşturmak için sanal ağ gerekli olmasa bile, Portal bir sanal ağ oluşturduğunuzda bir alt ağ tanımlamanızı gerektirir. Portalda, bir sanal ağ oluştururken yalnızca bir alt ağ tanımlayabilirsiniz. Sanal ağ oluşturulduktan sonra, sanal ağa daha sonra daha fazla alt ağ ekleyebilirsiniz. Bir sanal ağa alt ağ eklemek için bkz. [alt ağları yönetme](virtual-network-manage-subnet.md). Azure CLı veya PowerShell kullanarak birden çok alt ağa sahip bir sanal ağ oluşturabilirsiniz.

       >[!TIP]
       >Bazen, yöneticiler alt ağlar arasındaki trafik yönlendirmeyi filtrelemek veya denetlemek için farklı alt ağlar oluşturur. Alt ağları tanımladıktan önce, alt ağlarınız arasında trafiği nasıl filtrelemek ve dolaştırmak istediğinizi göz önünde bulundurun. Alt ağlar arasındaki trafiği Filtreleme hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](security-overview.md). Azure, trafiği otomatik olarak alt ağlar arasında yönlendirir, ancak Azure varsayılan yollarını geçersiz kılabilirsiniz. Varsayılan alt ağ trafiği yönlendirmesi hakkında daha fazla bilgi edinmek için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md).
       >

     - **Alt ağ adres aralığı**: Aralık, sanal ağ için girdiğiniz adres alanı içinde olmalıdır. Belirtebileceğiniz en küçük Aralık/29, alt ağ için sekiz IP adresi sağlar. Azure, protokol uyumluluğu için her bir alt ağdaki ilk ve son adresi ayırır. Azure hizmeti kullanımı için üç ek adres ayrılır. Sonuç olarak,/29 alt ağ adres aralığına sahip bir sanal ağ, yalnızca üç adet kullanılabilir IP adresine sahiptir. Bir sanal ağı bir VPN ağ geçidine bağlamayı planlıyorsanız, bir ağ geçidi alt ağı oluşturmanız gerekir. [Ağ geçidi alt ağları için belirli adres aralığı konuları](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)hakkında daha fazla bilgi edinin. Belirli koşullar altında alt ağ oluşturulduktan sonra adres aralığını değiştirebilirsiniz. Bir alt ağ adres aralığını değiştirme hakkında bilgi edinmek için bkz. [alt ağları yönetme](virtual-network-manage-subnet.md).
     - **Abonelik**: bir [abonelik](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)seçin. Aynı sanal ağı birden fazla Azure aboneliğinde kullanamazsınız. Ancak, sanal ağ [eşlemesi](virtual-network-peering-overview.md)ile diğer aboneliklerdeki sanal ağlara bir abonelikte sanal ağ bağlayabilirsiniz. Sanal ağa bağlandığınız tüm Azure kaynakları, sanal ağla aynı abonelikte olmalıdır.
     - **Kaynak grubu**: var olan bir [kaynak grubunu](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) seçin veya yeni bir tane oluşturun. Sanal ağa bağlandığınız bir Azure kaynağı, sanal ağla aynı kaynak grubunda veya farklı bir kaynak grubunda olabilir.
     - **Konum**: bölge olarak da bilinen bir Azure [konumu](https://azure.microsoft.com/regions/)seçin. Bir sanal ağ yalnızca bir Azure konumunda olabilir. Ancak, bir sanal ağı, bir VPN ağ geçidi kullanarak, başka bir konumdaki sanal ağa bağlayabilirsiniz. Sanal ağa bağlandığınız tüm Azure kaynakları, sanal ağla aynı konumda olmalıdır.

**Komut**

- Azure CLı: [az Network VNET Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Sanal ağları ve ayarları görüntüle

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden, ayarlarını görüntülemek istediğiniz sanal ağı seçin.
3. Seçtiğiniz sanal ağ için aşağıdaki ayarlar listelenir:
   - **Genel bakış**: adres alanı ve DNS sunucuları dahil olmak üzere sanal ağ hakkında bilgi sağlar. Aşağıdaki ekran görüntüsünde, **Myvnet**adlı bir sanal ağın genel bakış ayarları gösterilmektedir:

     ![Ağ arabirimine genel bakış](./media/manage-virtual-network/vnet-overview.png)

     **Kaynak grubu** veya **abonelik adı**' nın yanındaki **Değiştir** ' i seçerek bir sanal ağı farklı bir aboneliğe veya kaynak grubuna taşıyabilirsiniz. Bir sanal ağı nasıl taşıyacağınızı öğrenmek için bkz. [kaynakları farklı bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Makalede önkoşulları ve Azure portal, PowerShell ve Azure CLı kullanarak kaynakların nasıl taşınacağı listelenmektedir. Sanal ağa bağlı tüm kaynaklar sanal ağla birlikte taşınmalıdır.
   - **Adres alanı**: sanal ağa atanan adres alanları listelenir. Adres alanına adres aralığı ekleme ve kaldırma hakkında bilgi edinmek için [adres aralığı ekleme veya kaldırma](#add-or-remove-an-address-range)bölümündeki adımları uygulayın.
   - **Bağlı cihazlar**: sanal ağa bağlı tüm kaynaklar listelenir. Önceki ekran görüntüsünde, sanal ağa üç ağ arabirimi ve bir yük dengeleyici bağlı. Oluşturduğunuz ve sanal ağa bağlandığınız tüm yeni kaynaklar listelenir. Sanal ağa bağlı bir kaynağı silerseniz, listede artık görünmez.
   - **Alt ağlar**: sanal ağ içinde bulunan alt ağların bir listesi gösterilir. Bir alt ağ ekleme ve kaldırma hakkında bilgi edinmek için bkz. [alt ağları yönetme](virtual-network-manage-subnet.md).
   - **DNS sunucuları**: Azure iç DNS sunucusunun mı yoksa özel bir DNS sunucusunun, sanal ağa bağlı cihazlar için ad çözümlemesi mi sağladığını belirtebilirsiniz. Azure portal kullanarak bir sanal ağ oluşturduğunuzda, Azure 'un DNS sunucuları bir sanal ağ içindeki ad çözümlemesi için varsayılan olarak kullanılır. DNS sunucularını değiştirmek için, bu makaledeki [DNS sunucularını değiştirme](#change-dns-servers) bölümündeki adımları uygulayın.
   - Eşlemeler **: abonelikte**mevcut eşlemeler varsa, bunlar burada listelenir. Mevcut eşlemeler için ayarları görüntüleyebilir veya eşlemeler oluşturabilir, değiştirebilir veya silebilirsiniz. Eşlemeler hakkında daha fazla bilgi edinmek için bkz. [sanal ağ eşlemesi](virtual-network-peering-overview.md).
   - **Özellikler**: sanal AĞıN kaynak kimliği ve bulunduğu Azure aboneliği dahil olmak üzere sanal ağla ilgili ayarları görüntüler.
   - **Diyagram**: diyagram, sanal ağa bağlı tüm cihazların görsel bir gösterimini sağlar. Diyagramda cihazlarla ilgili bazı önemli bilgiler bulunur. Bu görünümdeki bir cihazı yönetmek için diyagramda aygıtı seçin.
   - **Ortak Azure ayarları**: Genel Azure ayarları hakkında daha fazla bilgi edinmek için aşağıdaki bilgilere bakın:
     - [Etkinlik Günlüğü](../azure-monitor/platform/activity-logs-overview.md)
     - [Erişim denetimi (ıAM)](../role-based-access-control/overview.md)
     - [Etiketler](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Kaynaktaki](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Otomasyon betiği](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Komut**

- Azure CLı: [az Network VNET Show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adres aralığı ekleme veya kaldırma

Bir sanal ağ için adres aralıklarını ekleyebilir ve kaldırabilirsiniz. CıDR gösteriminde bir adres aralığı belirtilmelidir ve aynı sanal ağ içindeki diğer adres aralıklarıyla çakışamaz. Tanımladığınız adres aralıkları genel veya özel (RFC 1918) olabilir. Adres aralığını genel veya özel olarak tanımladığınıza göre, adres aralığına yalnızca sanal ağ içinden, birbirine bağlı sanal ağlardan ve sanal ağa bağladığınız şirket içi ağlardan erişilebilir. 

Kendisiyle ilişkili alt ağınız yoksa, bir sanal ağın adres aralığını azaltabilirsiniz. Aksi takdirde, yalnızca adres aralığını genişletebilirsiniz, örneğin bir/16 ile/8 arasında değişiklik yapabilirsiniz. Küçük bir adres aralığıyla başlayabilir ve daha sonra genişletebilir veya ek aralıklar ekleyebilirsiniz.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Aşağıdaki adres aralıklarını ekleyemezsiniz:

- 224.0.0.0/4 (çok noktaya yayın)
- 255.255.255.255/32 (yayın)
- 127.0.0.0/8 (geri döngü)
- 169.254.0.0/16 (bağlantı-yerel)
- 168.63.129.16/32 (iç DNS, DHCP ve Azure Load Balancer [durum araştırması](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Adres aralığı eklemek veya kaldırmak için:

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden adres aralığını eklemek veya kaldırmak istediğiniz sanal ağı seçin.
3. **Ayarlar**altında **Adres alanı**' nı seçin.
4. Aşağıdaki seçeneklerden birini doldurun:
    - **Adres aralığı Ekle**: yeni adres aralığını girin. Adres aralığı, sanal ağ için tanımlanan mevcut bir adres aralığıyla çakışamaz.
    - **Adres aralığını kaldır**: kaldırmak istediğiniz adres aralığının sağında **.** .. öğesini seçin ve **Kaldır**' ı seçin. Adres aralığında bir alt ağ varsa, adres aralığını kaldıramazsınız. Bir adres aralığını kaldırmak için, önce adres aralığında bulunan tüm alt ağları (ve alt ağlardaki kaynakları) silmeniz gerekir.
5. **Kaydet**’i seçin.

**Komut**

- Azure CLı: [az Network VNET Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS sunucularını değiştirme

Sanal ağa bağlı tüm VM 'Ler, sanal ağ için belirttiğiniz DNS sunucularıyla kayıt kaydeder. Ayrıca, ad çözümlemesi için belirtilen DNS sunucusunu kullanır. Bir VM 'deki her ağ arabirimi (NIC) kendi DNS sunucusu ayarlarına sahip olabilir. Bir NIC 'nin kendi DNS sunucusu ayarları varsa, sanal ağın DNS sunucusu ayarlarını geçersiz kılar. NIC DNS ayarları hakkında daha fazla bilgi için bkz. [ağ arabirimi görevleri ve ayarları](virtual-network-network-interface.md#change-dns-servers). Azure Cloud Services VM 'Ler ve rol örnekleri için ad çözümlemesi hakkında daha fazla bilgi edinmek için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md). Bir DNS sunucusunu eklemek, değiştirmek veya kaldırmak için:

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden, DNS sunucularını değiştirmek istediğiniz sanal ağı seçin.
3. **Ayarlar**altında **DNS sunucuları**' nı seçin.
4. Aşağıdaki seçeneklerden birini seçin:
   - **Varsayılan (Azure tarafından sağlanmış)** : tüm kaynak adları ve özel ıp adresleri Azure DNS sunucularına otomatik olarak kaydedilir. Aynı sanal ağa bağlı herhangi bir kaynak arasındaki adları çözebilirsiniz. Bu seçeneği, sanal ağlardaki adları çözümlemek için kullanamazsınız. Sanal ağlarda adları çözümlemek için özel bir DNS sunucusu kullanmanız gerekir.
   - **Özel**: bir sanal ağ için Azure sınırına kadar bir veya daha fazla sunucu ekleyebilirsiniz. DNS sunucusu limitleri hakkında daha fazla bilgi edinmek için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Aşağıdaki seçenekleriniz vardır:
   - **Adres ekleyin**: sunucuyu sanal ağ DNS sunucuları listenize ekler. Bu seçenek ayrıca DNS sunucusunu Azure 'a kaydeder. Azure ile bir DNS sunucusu zaten kaydolduysanız, listeden bu DNS sunucusunu seçebilirsiniz.
   - **Adresi Kaldır**: kaldırmak istediğiniz sunucunun yanında **..** . ' ı seçin ve ardından **kaldırın**. Sunucu silindiğinde bu sanal ağ listesinden sunucu kaldırılır. DNS sunucusu, diğer sanal ağlarınızın kullanması için Azure 'da kayıtlı kalır.
   - **DNS sunucusu adreslerini yeniden sırala**: DNS sunucularınızı, ortamınız için doğru sırada listediğinizi doğrulamanız önemlidir. DNS sunucusu listeleri belirtildikleri sırada kullanılır. Hepsini bir kez deneme ayarı olarak çalışmamaları. Listedeki ilk DNS sunucusuna ulaşılırsa istemci, DNS sunucusunun düzgün çalışıp çalışmadığını fark etmeksizin bu DNS sunucusunu kullanır. Listelenen tüm DNS sunucularını kaldırın ve ardından bunları istediğiniz sırayla yeniden ekleyin.
   - **Bir adresi değiştirin**: listedeki DNS sunucusunu vurgulayın ve yeni adresi girin.
5. **Kaydet**’i seçin.
6. Sanal ağa bağlı olan VM 'Leri yeniden başlatarak yeni DNS sunucusu ayarları atanır. VM 'Ler yeniden başlatılana kadar geçerli DNS ayarlarını kullanmaya devam eder.

**Komut**

- Azure CLı: [az Network VNET Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Sanal ağı silme

Bir sanal ağı, yalnızca bağlı kaynak yoksa silebilirsiniz. Sanal ağ içindeki herhangi bir alt ağa bağlı kaynaklar varsa, önce sanal ağ içindeki tüm alt ağlara bağlı olan kaynakları silmeniz gerekir. Kaynağı silmek için aldığınız adımlar kaynağa bağlı olarak değişir. Alt ağlara bağlı kaynakların nasıl silineceğini öğrenmek için, silmek istediğiniz her kaynak türünün belgelerini okuyun. Bir sanal ağı silmek için:

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden silmek istediğiniz sanal ağı seçin.
3. **Ayarlar**altında **bağlı cihazlar**' ı seçerek sanal ağa bağlı bir cihaz olmadığını doğrulayın. Bağlı cihazlar varsa, sanal ağı silebilmeniz için önce bunları silmeniz gerekir. Bağlı cihaz yoksa **genel bakış**' ı seçin.
4. **Sil**’i seçin.
5. Sanal ağı silme işlemini onaylamak için **Evet**' i seçin.

**Komut**

- Azure CLı: [Azure ağı VNET silme](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>İzinler

Sanal ağlarda görevleri gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                  |   Ad                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft. Network/virtualNetworks/Read   |   Sanal ağ okuma              |
|Microsoft. Network/virtualNetworks/Write  |   Sanal ağ oluşturma veya güncelleştirme  |
|Microsoft. Network/virtualNetworks/Delete |   Sanal ağı silme            |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak sanal ağ oluşturma
- Sanal ağlar için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama
