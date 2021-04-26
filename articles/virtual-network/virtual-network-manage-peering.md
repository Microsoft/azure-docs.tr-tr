---
title: Azure sanal ağ eşlemesi oluşturma, değiştirme veya silme | Microsoft Docs
description: Sanal ağ eşlemesi oluşturun, değiştirin veya silin. Sanal ağ eşlemesi ile, sanal ağları aynı bölgedeki ve bölgeler arasında bağlanırsınız.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222679"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Sanal ağ eşlemesi oluşturma, değiştirme veya silme

Sanal ağ eşlemesi oluşturmayı, değiştirmeyi veya silmeyi öğrenin. Sanal ağ eşlemesi, Azure omurga ağı aracılığıyla aynı bölgedeki ve bölgeler arası (genel VNet eşlemesi olarak da bilinir) sanal ağlara bağlanmanızı sağlar. Eşlendikten sonra, sanal ağlar ayrı kaynaklar olarak yönetilmeye devam eder. Sanal ağ eşlemeden yeni başladıysanız, [sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md) bölümünde veya bir [öğreticiyi](tutorial-connect-virtual-networks-portal.md)tamamlayarak bunun hakkında daha fazla bilgi edinebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız açın https://portal.azure.com ve eşleme ile çalışmak için [gerekli izinlere](#permissions) sahip bir hesapla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız, `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için, eşleme ile çalışmak için [gerekli izinlere](#permissions) sahip bir hesapla çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure ile bir bağlantı oluşturmak için, eşleme ile çalışmak için [gerekli izinlere](#permissions) sahip bir hesapla çalıştırmanız gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="create-a-peering"></a>Eşleme oluşturma

Eşleme oluşturmadan önce, gereksinimler ve kısıtlamalar ve [gerekli izinler](#permissions)hakkında bilgi edinin.

1. Azure portal üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin. Listede görünürse **sanal ağları (klasik)** seçmeyin, ancak klasik dağıtım modeliyle dağıtılan bir sanal ağdan eşleme oluşturamazsınız.
2. Eşleme oluşturmak istediğiniz sanal ağı listeden seçin.
3. **Ayarlar** altında, eşlemeler ' **i seçin.**
4. **+ Ekle**'yi seçin. 
5. <a name="add-peering"></a>Aşağıdaki ayarlar için değerleri girin veya seçin:
    - **Ad:** Eşleme adı, sanal ağ içinde benzersiz olmalıdır.
    - **Sanal ağ dağıtım modeli:** Sahip olduğunuz sanal ağın hangi dağıtım modeline dağıtıldığını seçin.
    - **Kaynak kimliğimi biliyorum:** Eşler arasında istediğiniz sanal ağa okuma erişiminiz varsa, bu onay kutusunu işaretlenmemiş olarak bırakın. Ulaşmak istediğiniz sanal ağa veya aboneliğe okuma erişiminiz yoksa, bu kutuyu işaretleyin. Kutusunu işaretlendiğinde görünen **kaynak kimliği** kutusunda, eşler arasında istediğiniz sanal ağın tam kaynak kimliğini girin. Girdiğiniz kaynak KIMLIĞI, aynı veya bu sanal ağ ile [desteklenen farklı](#requirements-and-constraints) bir Azure [bölgesinde](https://azure.microsoft.com/regions) bulunan bir sanal ağ için olmalıdır. Tam kaynak KIMLIĞI şuna benzer `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . Bir sanal ağın özelliklerini görüntüleyerek bir sanal ağın kaynak KIMLIĞINI alabilirsiniz. Bir sanal ağın özelliklerini görüntülemeyi öğrenmek için bkz. [sanal ağları yönetme](manage-virtual-network.md#view-virtual-networks-and-settings). Abonelik, eşleme oluşturmakta olduğunuz sanal ağ aboneliğinden farklı bir Azure Active Directory kiracısıyla ilişkilendirilirse, önce her kiracıdan bir kullanıcıyı ters kiracıya [Konuk Kullanıcı](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) olarak ekleyin.
    - **Abonelik:** Eş almak istediğiniz sanal ağın [aboneliğini](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) seçin. Bir veya daha fazla abonelik, hesabınızın okuma erişimine sahip olduğu abonelik sayısına bağlı olarak listelenir. **Kaynak kimliği** onay kutusunu işaretlerseniz, bu ayar kullanılamaz.
    - **Sanal ağ:** Eş almak istediğiniz sanal ağı seçin. Azure dağıtım modeli aracılığıyla oluşturulan bir sanal ağ seçebilirsiniz. Farklı bir bölgede bir sanal ağ seçmek istiyorsanız, [desteklenen bir bölgedeki](#cross-region)sanal ağı seçmeniz gerekir. Listede görünür olması için sanal ağa okuma erişiminizin olması gerekir. Bir sanal ağ listeleniyorsa, ancak gri renkte olduğunda, sanal ağ adres alanının bu sanal ağın adres alanıyla örtüşmesine karşın bu durum olabilir. Sanal ağ adres alanları çakışırsa, bunlar eşlenmez. **Kaynak kimliği** onay kutusunu işaretlerseniz, bu ayar kullanılamaz.
    - **Sanal ağ erişimine Izin ver:** Varsayılan akış aracılığıyla iki sanal ağ arasında iletişimi etkinleştirmek istiyorsanız **etkin** (varsayılan) seçeneğini belirleyin `VirtualNetwork` . Sanal ağlar arasındaki iletişimin etkinleştirilmesi, sanal ağa bağlı kaynakların aynı bant genişliği ve aynı sanal ağa bağlıymış gibi gecikme süresiyle birbirleriyle iletişim kurmasına olanak tanır. İki sanal ağdaki kaynaklar arasındaki tüm iletişimler Azure özel ağı üzerinden yapılır. Ağ güvenlik grupları için **VirtualNetwork** hizmet etiketi, bu ayar **etkinleştirildiğinde** sanal ağı ve eşlenmiş sanal ağı kapsar. (Ağ güvenlik grubu hizmet etiketleri hakkında daha fazla bilgi için bkz. [ağ güvenlik gruplarına genel bakış](./network-security-groups-overview.md#service-tags).) Trafiğin eşlenmiş sanal ağa varsayılan olarak akmasını istemiyorsanız **devre dışı** ' yı seçin. Başka bir sanal ağla bir sanal ağ ile eşlendiyseniz ancak bazen iki sanal ağ arasında varsayılan trafik akışını devre dışı bırakmak istiyorsanız **devre dışı** seçeneğini belirleyebilirsiniz. Etkinleştirme/devre dışı bırakma, eşlemeleri silip yeniden oluşturmaya kıyasla daha kullanışlı olabilir. Bu ayar devre dışı bırakıldığında, trafik, eşlenen sanal ağlar arasında varsayılan olarak akar; Ancak, uygun IP adreslerini veya uygulama güvenlik gruplarını içeren bir [ağ güvenlik grubu](./network-security-groups-overview.md) kuralıyla açıkça izin verildiğinde trafik yine de akışa eklenebilir.
      > [!WARNING]
      > **Sanal ağ erişimine Izin ver** ayarı devre dışı bırakıldığında yalnızca **VirtualNetwork** hizmeti etiketinin tanımı değişir. Bu ayar açıklamasında açıklandığı gibi, eş bağlantı genelinde trafik *akışını tam olarak* engellemez.    
    - **İletilen trafiğe Izin ver:** Bir sanal ağdaki (sanal ağdan kaynaklanan) bir ağ sanal gereci tarafından *iletilen* trafiğin eşleme yoluyla bu sanal ağa akmasını sağlamak için bu kutuyu işaretleyin. Örneğin, Spoke1, Spoke2 ve hub adlı üç sanal ağı göz önünde bulundurun. Her bir bağlı ağ sanal ağı ile hub sanal ağı arasında bir eşleme bulunur, ancak sanal ağ sanal ağları arasında eşlemeler yok. Hub sanal ağında bir ağ sanal gereci dağıtılır ve Kullanıcı tanımlı yollar, ağ sanal gereci üzerinden alt ağlar arasında trafiği yönlendiren her bir bağlı bileşen sanal ağına uygulanır. Bu onay kutusu, her bir bağlı ağ sanal ağı ve hub sanal ağı arasındaki eşleme için denetlenmezse, hub sanal ağlar arasındaki trafiği iletmediğinden, bağlı olan sanal ağlar arasında trafik akmaz. Bu özelliğin etkinleştirilmesi, eşleme yoluyla iletilen trafiğe izin veriyorsa, Kullanıcı tanımlı yollar veya ağ sanal aygıtları oluşturmaz. Kullanıcı tanımlı yollar ve ağ sanal cihazları ayrı olarak oluşturulur. [Kullanıcı tanımlı rotalar](virtual-networks-udr-overview.md#user-defined)hakkında bilgi edinin. Azure VPN Gateway üzerinden sanal ağlar arasında trafik iletilirse bu ayarı denetlemeniz gerekmez.
    - **Ağ geçidi aktarımına Izin ver:** Bu sanal ağa bağlı bir sanal ağ geçidi varsa ve eşlenmiş sanal ağdan gelen trafiğin ağ geçidiyle akmasını sağlamak istiyorsanız bu kutuyu işaretleyin. Örneğin, bu sanal ağ bir sanal ağ geçidi üzerinden şirket içi ağa iliştirilebilir. Ağ geçidi bir ExpressRoute veya VPN Gateway olabilir. Bu kutunun işaretlenmesi, eşlenen sanal ağ trafiğinin bu sanal ağa bağlı ağ geçidi üzerinden şirket içi ağa akmasını sağlar. Bu kutuyu işaret ederseniz, eşlenmiş sanal ağ bir ağ geçidi yapılandırılmış olamaz. Eşlenen sanal ağ, diğer sanal ağdan bu sanal ağa eşleme ayarlanırken **uzak ağ geçitleri kullan** onay kutusunun işaretli olması gerekir. Bu kutuyu işaretsiz bırakırsanız (varsayılan), eşlenen sanal ağdan gelen trafik hala bu sanal ağa akar, ancak bu sanal ağa bağlı bir sanal ağ geçidi üzerinden akamaz. Eşleme bir sanal ağ (Kaynak Yöneticisi) ve bir sanal ağ (klasik) arasındaysa, ağ geçidinin sanal ağda (Kaynak Yöneticisi) olması gerekir.

       Bir VPN ağ geçidi, trafiği şirket içi bir ağa iletmenin yanı sıra ağ geçidini, sanal ağların birbirleriyle eşlenmesine gerek kalmadan ağ geçidinin bulunduğu sanal ağlar arasında iletebilir. Bir hub 'da VPN Gateway kullanmak istediğinizde trafiği iletmek için bir VPN ağ geçidi kullanmak yararlı olur ( **iletilen trafiğe Izin ver** için tanımlanan hub ve bağlı bileşen örneğine bakın) sanal ağ, birbirleriyle eşlenmeyen bağlı bileşen sanal ağları arasında trafiği yönlendirebilir. Aktarım için bir ağ geçidinin kullanılmasına izin verme hakkında daha fazla bilgi edinmek için bkz. [sanal ağ eşağında aktarım için BIR VPN ağ geçidi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bu senaryo, sonraki atlama türü olarak sanal ağ geçidini belirten Kullanıcı tanımlı yolların uygulanması gerektirir. [Kullanıcı tanımlı rotalar](virtual-networks-udr-overview.md#user-defined)hakkında bilgi edinin. Bir VPN ağ geçidini, Kullanıcı tanımlı bir rotada bir sonraki atlama türü olarak belirtebilirsiniz, bir ExpressRoute ağ geçidini Kullanıcı tanımlı bir rotada sonraki atlama türü olarak belirtemezsiniz.

    - **Uzak ağ geçitlerini kullan:** Bu sanal ağdan gelen trafiğin, eşlemiş olduğunuz sanal ağa bağlı bir sanal ağ geçidi üzerinden akmasını sağlamak için bu kutuyu işaretleyin. Örneğin, eşlemiş olduğunuz sanal ağın, şirket içi bir ağla iletişime olanak tanıyan bir VPN ağ geçidi eklenmiş olması gerekir.  Bu kutunun işaretlenmesi, bu sanal ağdan gelen trafiğin eşlenmiş sanal ağa bağlı VPN ağ geçidi üzerinden akmasını sağlar. Bu kutuyu işaretlerseniz, eşlenmiş sanal ağa bağlı bir sanal ağ geçidi olmalıdır ve **ağ geçidi aktarımına Izin ver** onay kutusunun işaretli olması gerekir. Bu kutuyu işaretsiz bırakırsanız (varsayılan), eşlenen sanal ağdan gelen trafik yine de bu sanal ağa akabilir, ancak bu sanal ağa bağlı bir sanal ağ geçidi üzerinden akamaz.
    
      Bu sanal ağ için yalnızca bir eşleme bu ayarın etkinleştirilmesini sağlayabilir.

      Sanal ağınızda zaten yapılandırılmış bir ağ geçidiniz varsa uzak ağ geçitlerini kullanamazsınız. Aktarım için bir ağ geçidi kullanma hakkında daha fazla bilgi edinmek için bkz. [sanal ağ eşağında aktarım için BIR VPN ağ geçidi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Şirket içi trafiği bir eşlenmiş VNet 'e geçişli olarak göndermek için bir sanal ağ geçidi kullanırsanız, şirket içi VPN cihazının eşlenen VNet IP aralığı ' ilginç ' trafiğe ayarlanmalıdır. Aksi takdirde, şirket içi kaynaklarınız eşlenen VNet 'teki kaynaklarla iletişim kuramaz.

6. Eşlemeyi seçtiğiniz sanal ağa eklemek için **Tamam ' ı** seçin.

Farklı aboneliklerde ve dağıtım modellerindeki sanal ağlar arasında eşleme uygulamaya yönelik adım adım yönergeler için bkz. [sonraki adımlar](#next-steps).

### <a name="commands"></a>Komutlar

- **Azure CLI**: [az Network VNET eşleme Create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-Azvirtualnetworkeşleme](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Eşleme ayarlarını görüntüleme veya değiştirme

Bir eşlemeyi değiştirmeden önce, gereksinimler ve kısıtlamalar ve [gerekli izinler](#permissions)hakkında bilgi edinin.

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin. Listede görünürse **sanal ağları (klasik)** seçmeyin, ancak klasik dağıtım modeliyle dağıtılan bir sanal ağdan eşleme oluşturamazsınız.
2. Eşleme ayarlarını değiştirmek istediğiniz sanal ağı listeden seçin.
3. **Ayarlar** altında, eşlemeler ' **i seçin.**
4. Görüntülemek veya ayarlarını değiştirmek istediğiniz eşlemeyi seçin.
5. Uygun ayarı değiştirin. Eşleme oluşturma [adımının 5. adımında](#add-peering) her ayar için seçenekler hakkında bilgi edinin.
6. **Kaydet**’i seçin.

**Komutlar**

- **Azure CLI**: [az](/cli/azure/network/vnet/peering) Network VNET eşleme listesi bir sanal ağın eşlemelerini listelemek için az [Network VNET eşlemesi](/cli/azure/network/vnet/peering) , belirli bir eşlemenin ayarlarını göstermek için, az [Network VNET eşleme Update](/cli/azure/network/vnet/peering) de eşleme ayarlarını değiştirmek için. |
- **PowerShell**: [Get-azvirtualnetworkeşlemei](/powershell/module/az.network/get-azvirtualnetworkpeering) görünümü eşleme ayarlarını ve [set-azvirtualnetworkeşleme](/powershell/module/az.network/set-azvirtualnetworkpeering) ayarını değiştirmek için kullanın.

## <a name="delete-a-peering"></a>Eşlemeyi silme

Bir eşlemeyi silmeden önce hesabınızın [gerekli izinlere](#permissions)sahip olduğundan emin olun.

Eşleme silindiğinde, bir sanal ağdan gelen trafik artık eşlenmiş sanal ağa akar. Kaynak Yöneticisi aracılığıyla dağıtılan sanal ağlar eşlenirken, her sanal ağın diğer sanal ağla bir eşlemesi vardır. Bir sanal ağdan eşlemeyi silmek, sanal ağlar arasındaki iletişimi devre dışı bıraksa da, diğer sanal ağdan eşlemeyi silmez. Diğer sanal ağda bulunan eşlemenin eşleme durumu **bağlantısı kesildi**. İlk sanal ağda eşlemeyi yeniden oluşturmanız ve her iki sanal ağın da eşleme durumu *bağlı* olarak değiştiği sürece eşlemeyi yeniden oluşturamazsınız.

Sanal ağların bazen iletişim kurmasını istiyorsanız, her zaman bir eşlemeyi silmek yerine, **sanal ağ erişimine Izin ver** ayarını **devre dışı** olarak ayarlayabilirsiniz. Nasıl yapılacağını öğrenmek için, bu makalenin eşleme oluşturma bölümünün 6. adımını okuyun. Ağ erişimini silmenin ve yeniden oluşturma özelliğinden daha kolay bir şekilde devre dışı bırakmayı ve etkinleştirmeyi fark edebilirsiniz.

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin. Listede görünürse **sanal ağları (klasik)** seçmeyin, ancak klasik dağıtım modeliyle dağıtılan bir sanal ağdan eşleme oluşturamazsınız.
2. Eşleme silmek istediğiniz sanal ağı listeden seçin.
3. **Ayarlar** altında, eşlemeler ' **i seçin.**
4. Silmek istediğiniz eşlemenin sağ tarafında, **..**. öğesini seçin, **Sil**' i seçin ve ardından ilk sanal ağdan eşlemeyi silmek için **Evet** ' i seçin.
5. Eşleme içindeki diğer sanal ağdan eşlemeyi silmek için önceki adımları izleyin.

**Komutlar**

- **Azure CLI**: [az Network VNET eşlemesi Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-Azvirtualnetworkeşleme](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Gereksinimler ve kısıtlamalar

- <a name="cross-region"></a>Sanal ağları aynı bölgede veya farklı bölgelerde eşleyebilir. Farklı bölgelerdeki sanal ağları eşleme *genel VNET eşlemesi* olarak da adlandırılır. 
- Genel eşleme oluştururken, eşlenen sanal ağlar herhangi bir Azure genel bulut bölgesinde veya Çin bulut bölgelerinde veya kamu bulut bölgelerinde bulunabilir. Bulutlar arasında eşdüzey olamaz. Örneğin, Azure genel bulutundaki bir sanal ağ, Azure Çin bulutu 'ndaki bir VNet ile birleştirilemez.
- Bir sanal ağ içindeki kaynaklar, genel olarak eşlenen sanal ağın Temel iç yük dengeleyicisinin ön uç IP adresiyle iletişim kuramaz. Temel Load Balancer için yalnızca aynı bölge içinde destek vardır. Hem Sanal Ağ Eşleme hem de Genel Sanal Ağ Eşleme için Standart Load Balancer desteği vardır. Küresel VNet eşlemesi üzerinden çalışmayan temel bir yük dengeleyici kullanan hizmetler [burada belgelenmiştir.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Uzak ağ geçitlerini kullanabilir veya genel olarak eşlenmiş sanal ağlarda ve yerel olarak eşlenmiş sanal ağlarda ağ geçidi aktarımına izin verebilirsiniz.
- Sanal ağlar aynı veya farklı aboneliklerde olabilir. Farklı aboneliklerde sanal ağları eşler, her iki abonelik de aynı veya farklı Azure Active Directory kiracısıyla ilişkilendirilebilir. Zaten bir AD kiracınız yoksa, [bir tane oluşturabilirsiniz](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- Eşin sanal ağlarda çakışmayan IP adresi alanları olmalıdır.
- Bir sanal ağ başka bir sanal ağla eşlendikten sonra sanal ağın adres alanından adres aralıklarını ekleyemez veya silemezsiniz. Adres aralıklarını ekleme veya kaldırma, eşlemeyi silme, adres aralıklarını ekleme veya kaldırma, sonra eşlemeyi yeniden oluşturma. Sanal ağlardan adres aralıklarını eklemek veya adres aralıklarını kaldırmak için bkz. [sanal ağları yönetme](manage-virtual-network.md).
- Kaynak Yöneticisi üzerinden dağıtılmış iki sanal ağı veya klasik dağıtım modeli aracılığıyla dağıtılan bir sanal ağ ile Kaynak Yöneticisi aracılığıyla dağıtılan bir sanal ağı eşleyebilirsiniz. Klasik dağıtım modeliyle oluşturulan iki sanal ağı eşleyemezsiniz. Azure dağıtım modelleriyle ilgili bilgi sahibi değilseniz [Azure dağıtım modellerini anlama](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesini okuyun. Klasik dağıtım modeliyle oluşturulan iki sanal ağı bağlamak için [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) kullanabilirsiniz.
- Resource Manager ile oluşturulmuş olan iki sanal ağı eşlerken eşlemedeki her sanal ağ için bir eşleme yapılandırılması gerekir. Eşleme durumu için aşağıdaki türlerden birini görürsünüz: 
  - *Başlatıldı:* Birinci sanal ağdan ikinci sanal ağa eşleme oluşturduğunuzda, eşleme durumu *başlatılır*. 
  - *Bağlı:* İkinci sanal ağdan ilk sanal ağa eşleme oluşturduğunuzda, eşleme durumu *bağlanır*. İlk sanal ağ için eşleme durumunu görüntülediğinizde, durumunun *başlatıldığı* ' dan *bağlı*' ya değiştiğini görürsünüz. Her iki sanal ağ eşlemesi için de eşleme durumu *bağlanana* kadar eşleme başarılı bir şekilde kurulmadı.
- Klasik dağıtım modeli aracılığıyla oluşturulmuş bir sanal ağla Kaynak Yöneticisi aracılığıyla oluşturulan bir sanal ağı eşlemeden, yalnızca Kaynak Yöneticisi aracılığıyla dağıtılan sanal ağ için bir eşleme yapılandırırsınız. Bir sanal ağ (klasik) için eşlemeyi veya klasik dağıtım modeli aracılığıyla dağıtılan iki sanal ağ arasında yapılandırılamaz. Sanal ağdan (Kaynak Yöneticisi) sanal ağa (klasik) eşleme oluşturduğunuzda, eşleme durumu *güncelleştiriliyor*, daha sonra *bağlantılı* olarak değişir.
- İki sanal ağ arasında bir eşleme oluşturulur. Tek başına eşlemeler geçişli değildir. Aralarında eşleme oluşturursanız:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  VirtualNetwork1 ile VirtualNetwork3 arasında bir eşleme yoktur. VirtualNetwork1 ve VirtualNetwork3 arasında bir sanal ağ eşlemesi oluşturmak istiyorsanız VirtualNetwork1 ve VirtualNetwork3 arasında bir eşleme oluşturmanız gerekir. VirtualNetwork1 ile VirtualNetwork3 arasında bir eşleme yoktur. VirtualNetwork1 ve VirtualNetwork3 'in doğrudan iletişim kurmasını istiyorsanız, VirtualNetwork1 ve VirtualNetwork3 arasında açık bir eşleme oluşturmanız veya hub ağındaki bir NVA üzerinden gitmeniz gerekir.  
- Eşlenen sanal ağlardaki adları varsayılan Azure ad çözümlemesi kullanarak çözümleyemez. Diğer sanal ağlardaki adları çözümlemek için, [özel etki alanları](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya özel bir DNS sunucusu için Azure DNS kullanmanız gerekir. Kendi DNS sunucunuzu ayarlamayı öğrenmek için, bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Aynı bölgedeki eşlenmiş sanal ağlardaki kaynaklar aynı bant genişliği ve gecikme süresi ile aynı sanal ağ içinde olup olmadıkları ile birbirleriyle iletişim kurabilir. Ancak, her bir sanal makine boyutunun en fazla ağ bant genişliği vardır. Farklı sanal makine boyutları için en fazla ağ bant genişliği hakkında daha fazla bilgi edinmek için bkz. [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makine boyutları.
- Bir sanal ağ başka bir sanal ağla eşlenebilir ve ayrıca bir Azure sanal ağ geçidi ile başka bir sanal ağa da bağlanabilir. Sanal ağlar hem eşleme hem de ağ geçidi aracılığıyla bağlandığında, sanal ağlar arasındaki trafik, ağ geçidi yerine eşleme yapılandırması üzerinden akar.
- Yeni yolların istemciye indirildiğinden emin olmak için sanal ağ eşlemesi başarıyla yapılandırıldıktan sonra Noktadan siteye VPN istemcileri yeniden indirilmelidir.
- Sanal ağ eşlemesi kullanan girdi ve çıktı trafiği için nominal bir ücret uygulanır. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>İzinler

Sanal ağ eşlemesi ile çalışmak için kullandığınız hesapların aşağıdaki rollere atanması gerekir:

- [Ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Kaynak Yöneticisi aracılığıyla dağıtılan bir sanal ağ için.
- [Klasik ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): klasik dağıtım modeliyle dağıtılan bir sanal ağ için.

Hesabınız önceki rollerden birine atanmamışsa, aşağıdaki tablodan gerekli eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanması gerekir:

| Eylem                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | A ile sanal ağ B 'ye bir eşleme oluşturmak için gereklidir. sanal ağ A 'nın sanal ağ olması gerekir (Kaynak Yöneticisi)          |
| Microsoft. Network/virtualNetworks/eş/eylem                   | B (Kaynak Yöneticisi) sanal ağından A sanal ağına eşleme oluşturmak için gereklidir                                                       |
| Microsoft. ClassicNetwork/virtualNetworks/peer/Action                   | B (klasik) sanal ağı A ile sanal ağa eşleme oluşturmak için gereklidir                                                                |
| Microsoft. Network/virtualNetworks/Virtualnetworkpeerler/okuma   | Sanal Ağ eşlemesini okuma   |
| Microsoft. Network/virtualNetworks/Virtualnetworkpeerler/Sil | Sanal Ağ eşlemesini silme |

## <a name="next-steps"></a>Sonraki adımlar

- Aynı veya farklı aboneliklerde, aynı veya farklı dağıtım modelleriyle oluşturulmuş sanal ağlar arasında, bir sanal ağ eşlemesi oluşturulur. Aşağıdaki senaryolardan biri için öğreticiyi tamamlayın:

  |Azure dağıtım modeli             | Abonelik  |
  |---------                          |---------|
  |Her ikisi de Resource Manager              |[Aynı](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Farklı](create-peering-different-subscriptions.md)|
  |Biri Resource Manager, diğeri klasik  |[Aynı](create-peering-different-deployment-models.md)|
  |                                   |[Farklı](create-peering-different-deployment-models-subscriptions.md)|

- [Hub ve bağlı bileşen ağ topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) oluşturmayı öğrenin
- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak sanal ağ eşlemesi oluşturma
- Sanal ağlar için [Azure ilke tanımları](./policy-reference.md) oluşturma ve atama