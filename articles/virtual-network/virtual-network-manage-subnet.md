---
title: Azure sanal ağ alt ağını ekleme, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Azure 'da bir sanal ağ alt ağı ekleme, değiştirme veya silme hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 4af8c6dbbcbb877351018eb881f4d3b0b447bbce
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098974"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Sanal ağ alt ağını ekleme, değiştirme veya silme

Sanal ağ alt ağını ekleme, değiştirme veya silme hakkında bilgi edinin. Bir sanal ağa dağıtılan tüm Azure kaynakları, bir sanal ağ içindeki bir alt ağa dağıtılır. Sanal ağlarınız için yeni başladıysanız, [sanal ağa genel bakış](virtual-networks-overview.md) veya bir [öğreticiyi](quick-create-portal.md)tamamlama hakkında daha fazla bilgi edinebilirsiniz. Bir sanal ağ oluşturmak, değiştirmek veya silmek için bkz. [sanal ağı yönetme](manage-virtual-network.md).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, açın https://portal.azure.com ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLI 'yi yerel olarak çalıştırıyorsanız, Azure ile bir bağlantı oluşturmak için çalıştırmanız `az login` da gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="add-a-subnet"></a>Alt ağ ekleme

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden alt ağ eklemek istediğiniz sanal ağı seçin.
3. **AYARLAR** altında **Alt ağlar**’ı seçin.
4. **+ Alt ağ**' ı seçin.
5. Aşağıdaki parametreler için değerler girin:
   - **Ad**: Ad, sanal ağ içinde benzersiz olmalıdır. Diğer Azure hizmetleriyle maksimum uyumluluk için, adın ilk karakteri olarak bir harf kullanmanızı öneririz. Örneğin, Azure Application Gateway, bir sayı ile başlayan bir alt ağa dağıtılır.
   - **Adres aralığı**: Aralık, sanal ağın adres alanı içinde benzersiz olmalıdır. Aralık, sanal ağ içindeki diğer alt ağ adresi aralıklarıyla çakışamaz. Adres alanının, sınıfsız etki alanları arası yönlendirme (CıDR) gösterimi kullanılarak belirtilmesi gerekir. Örneğin, 10.0.0.0/16 adres alanına sahip bir sanal ağda, 10.0.0.0/24 alt ağ adres alanı tanımlayabilirsiniz. Belirtebileceğiniz en küçük Aralık/29, alt ağ için sekiz IP adresi sağlar. Azure, protokol uyumluluğu için her bir alt ağdaki ilk ve son adresi ayırır. Azure hizmeti kullanımı için üç ek adres ayrılır. Sonuç olarak,/29 adres aralığı ile bir alt ağ tanımlamak alt ağda kullanılabilir üç IP adresi ile sonuçlanır. Bir sanal ağı bir VPN ağ geçidine bağlamayı planlıyorsanız, bir ağ geçidi alt ağı oluşturmanız gerekir. [Ağ geçidi alt ağları için belirli adres aralığı konuları](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)hakkında daha fazla bilgi edinin. Belirli koşullar altında alt ağ eklendikten sonra adres aralığını değiştirebilirsiniz. Bir alt ağ adres aralığını değiştirme hakkında bilgi edinmek için bkz. [alt ağ ayarlarını değiştirme](#change-subnet-settings).
   - **Ağ güvenlik grubu**: Alt ağ için gelen ve giden ağ trafiğini filtrelemek için sıfır veya var olan bir ağ güvenlik grubunu bir alt ağla ilişkilendirebilirsiniz. Ağ güvenlik grubu, sanal ağla aynı abonelikte ve konumda bulunmalıdır. [Ağ güvenlik grupları](security-overview.md) ve [ağ güvenlik grubu oluşturma](tutorial-filter-network-traffic.md)hakkında daha fazla bilgi edinin.
   - **Yol tablosu**: Diğer ağlara ağ trafiği yönlendirmeyi denetlemek için sıfır veya var olan bir yol tablosunu bir alt ağ ile ilişkilendirebilirsiniz. Yol tablosu, sanal ağla aynı abonelikte ve konumda bulunmalıdır. [Azure yönlendirme](virtual-networks-udr-overview.md) ve [yol tablosu oluşturma](tutorial-create-route-table-portal.md) hakkında daha fazla bilgi edinin
   - **Hizmet uç noktaları:** Bir alt ağda sıfır veya birden fazla hizmet uç noktası etkin olabilir. Hizmet için bir hizmet uç noktasını etkinleştirmek istiyorsanız, hizmet uç noktalarını **Hizmetler** listesinden etkinleştirmek istediğiniz hizmeti veya hizmetleri seçin. Konum, bir uç nokta için otomatik olarak yapılandırılır. Varsayılan olarak, hizmet uç noktaları sanal ağın bölgesi için yapılandırılır. Azure depolama için bölgesel yük devretme senaryolarını desteklemek üzere, uç noktalar otomatik olarak [Azure eşlenmiş bölgelere](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)yapılandırılır.
   - **Alt ağ temsili:** Bir alt ağ için birden fazla temsilci etkin olabilir. Alt ağ temsili, hizmeti dağıtma sırasında benzersiz bir tanımlayıcı kullanarak alt ağda hizmete özel kaynaklar oluşturmak için hizmete açık izinler verir. Bir hizmet için temsilci seçmek için, **Hizmetler** listesinden yetkisini atamak istediğiniz hizmeti seçin.

       Hizmet uç noktasını kaldırmak için hizmet uç noktasını kaldırmak istediğiniz hizmetin seçimini kaldırın. Hizmet uç noktaları ve için etkinleştiribilecekleri hizmetler hakkında daha fazla bilgi edinmek için bkz. [sanal ağ hizmeti uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md). Hizmet için bir hizmet uç noktasını etkinleştirdikten sonra, hizmet ile oluşturulan bir kaynağın alt ağı için ağ erişimini de etkinleştirmeniz gerekir. Örneğin, *Microsoft. Storage*için hizmet uç noktasını etkinleştirirseniz, ağ erişimi vermek Istediğiniz tüm Azure depolama hesaplarına ağ erişimini de etkinleştirmeniz gerekir. Hizmet uç noktasının etkinleştirildiği alt ağlara ağ erişimini etkinleştirme hakkında ayrıntılar için, hizmet uç noktasını etkinleştirdiğiniz bireysel hizmet belgelerine bakın.

     Bir alt ağ için hizmet uç noktasının etkinleştirildiğini doğrulamak için, alt ağdaki herhangi bir ağ arabirimi için [geçerli yolları](diagnose-network-routing-problem.md) görüntüleyin. Bir uç nokta yapılandırıldığında, hizmetin adres ön ekine sahip bir *varsayılan* yol ve bir Nexthoptype **virtualnetworkserviceendpoint**görürsünüz. Yönlendirme hakkında daha fazla bilgi için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md).
6. Alt ağı seçtiğiniz sanal ağa eklemek için **Tamam**' ı seçin.

**Komut**

- Azure CLı: [az Network VNET subnet Create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alt ağ ayarlarını değiştir

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden, ayarlarını değiştirmek istediğiniz alt ağı içeren sanal ağı seçin.
3. **AYARLAR** altında **Alt ağlar**’ı seçin.
4. Alt ağlar listesinde, ayarlarını değiştirmek istediğiniz alt ağı seçin. Aşağıdaki ayarları değiştirebilirsiniz:

    - **Adres Aralığı:** Alt ağ içinde hiçbir kaynak dağıtılmamışsa, adres aralığını değiştirebilirsiniz. Alt ağda herhangi bir kaynak varsa, kaynakları başka bir alt ağa taşımanız ya da önce alt ağdan silmeniz gerekir. Bir kaynağı taşımak veya silmek için aldığınız adımlar kaynağa bağlı olarak değişir. Alt ağlardaki kaynakları taşımayı veya silmeyi öğrenmek için, taşımak veya silmek istediğiniz her kaynak türünün belgelerini okuyun. [Ağ ekleme](#add-a-subnet)'nin 5. adımında **adres aralığı** kısıtlamalarına bakın.
    - **Kullanıcılar**: Yerleşik rolleri veya kendi özel rollerinizi kullanarak alt ağa erişimi denetleyebilirsiniz. Alt ağa erişim için rol ve kullanıcı atama hakkında daha fazla bilgi edinmek için bkz. [Azure kaynaklarınıza erişimi yönetmek için rol atamasını kullanma](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Ağ güvenlik grubu** ve **yol tablosu**: [Alt ağ ekleme](#add-a-subnet)adım 5 ' i inceleyin.
    - **Hizmet uç noktaları**: [Alt ağ ekleme](#add-a-subnet)' nin 5. adımında hizmet uç noktaları bölümüne bakın. Var olan bir alt ağ için bir hizmet uç noktası etkinleştirirken, alt ağdaki herhangi bir kaynakta hiçbir kritik görevin çalışmadığını doğrulayın. Hizmet uç noktaları alt ağdaki her ağ arabirimindeki *yolları, varsayılan*yolu *0.0.0.0/0* adres ön eki ve sonraki atlama türü ile birlikte kullanarak, hizmetin adres öneklerine ve bir sonraki atlama türüne sahip yeni bir yol kullanmasını sağlar *Virtualnetworkserviceendpoint*. Anahtar sırasında, herhangi bir açık TCP bağlantısı sonlandırılabilir. Hizmet uç noktası, tüm ağ arabirimleri için hizmete trafik akışı yeni rota ile güncelleştirilene kadar etkinleştirilmez. Yönlendirme hakkında daha fazla bilgi için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md).
    - **Alt ağ temsili:** [Alt ağ ekleme](#add-a-subnet)' nin 5. adımında hizmet uç noktaları bölümüne bakın. Alt ağ temsilcisi, sıfır veya birden çok temsilci için etkinleştirilmiş olarak değiştirilebilir. Bir hizmetin bir kaynağı alt ağda zaten dağıtılmışsa, hizmetin tüm kaynakları kaldırılana kadar alt ağ temsili eklenemez veya kaldırılamaz. Farklı bir hizmet için temsilci seçmek istiyorsanız, **Hizmetler** listesinden yetkisini atamak istediğiniz hizmeti seçin.
5. **Kaydet**’i seçin.

**Komut**

- Azure CLı: [az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Bir alt ağı silme

Alt ağı yalnızca alt ağda kaynak yoksa silebilirsiniz. Alt ağda kaynaklar varsa, alt ağı silebilmek için alt ağdaki kaynakları silmeniz gerekir. Kaynağı silmek için aldığınız adımlar kaynağa bağlı olarak değişir. Alt ağlardaki kaynakları silmeyi öğrenmek için, silmek istediğiniz her kaynak türünün belgelerini okuyun.

1. Portalın üst kısmındaki arama kutusuna, arama kutusuna *sanal ağlar* girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
2. Sanal ağlar listesinden, silmek istediğiniz alt ağı içeren sanal ağı seçin.
3. **AYARLAR** altında **Alt ağlar**’ı seçin.
4. Alt ağlar listesinde, silmek istediğiniz alt ağ için sağdaki **...** öğesini seçin
5. **Sil**' i seçin ve ardından **Evet**' i seçin.

**Komut**

- Azure CLı: [az Network VNET Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>İzinler

Alt ağlarda görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

|Action                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft. Network/virtualNetworks/alt ağlar/okuma                           |   Sanal ağ alt ağını oku              |
|Microsoft. Network/virtualNetworks/alt ağlar/yazma                          |   Sanal ağ alt ağını oluşturma veya güncelleştirme  |
|Microsoft. Network/virtualNetworks/alt ağlar/Sil                         |   Sanal ağ alt ağını silme            |
|Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action                    |   Sanal ağa ekleme                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Bir alt ağ için hizmet uç noktasını etkinleştirme     |
|Microsoft. Network/virtualNetworks/alt ağlar/virtualMachines/Read           |   Sanal makineleri bir alt ağda al       |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak sanal ağ ve alt ağlar oluşturun
- Sanal ağlar için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama
