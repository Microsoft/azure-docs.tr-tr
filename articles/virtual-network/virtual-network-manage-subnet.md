---
title: Azure sanal ağ alt ağını ekleme, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Sanal ağlarla ilgili bilgilerin nerede bulunacağını ve Azure 'da bir sanal ağ alt ağının nasıl ekleneceğini, değiştirileceğini veya silineceğini öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 1e655b20d2f6295f0d6cfe8008fee7b360525611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774296"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Sanal ağ alt ağını ekleme, değiştirme veya silme

Sanal ağ alt ağını ekleme, değiştirme veya silme hakkında bilgi edinin. Sanal ağa dağıtılan tüm Azure kaynakları, sanal ağ içindeki bir alt ağa dağıtılır. Sanal ağlarınız için yeni başladıysanız, [sanal ağa genel bakış](virtual-networks-overview.md) veya bir [hızlı başlangıç](quick-create-portal.md)işlemini tamamlayarak bunlarla ilgili daha fazla bilgi edinebilirsiniz. Bir sanal ağı yönetme hakkında daha fazla bilgi edinmek için bkz. [sanal ağ oluşturma, değiştirme veya silme](manage-virtual-network.md).

## <a name="before-you-begin"></a>Başlamadan önce

Hesabınız yoksa, etkin abonelikle bir Azure hesabı ayarlayın. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ardından Bu makalenin herhangi bir bölümündeki adımları başlatmadan önce bu görevlerden birini doldurun: 

- **Portal kullanıcıları**: [Azure Portal](https://portal.azure.com) Azure hesabınızla oturum açın.

- **PowerShell kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın ya da bilgisayarınızdan PowerShell 'i çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Azure Cloud Shell tarayıcısı sekmesinde **ortam Seç** açılan listesini bulun ve daha önce seçilmemişse **PowerShell** ' i seçin.

    PowerShell 'i yerel olarak çalıştırıyorsanız, Azure PowerShell Module sürüm 1.0.0 veya üstünü kullanın. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az.Network` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). `Connect-AzAccount`Azure ile bağlantı oluşturmak için de ' i çalıştırın.

- **Azure komut satırı arabirimi (CLI) kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da CLI 'yı bilgisayarınızdan çalıştırın. Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure CLı sürüm 2.0.31 veya üstünü kullanın. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). `az login`Azure ile bağlantı oluşturmak için de ' i çalıştırın.

Üzerinde oturum açmak veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı rolü](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="add-a-subnet"></a>Alt ağ ekleme

1. Sanal ağlarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

2. Bir alt ağ eklemek istediğiniz sanal ağın adını seçin.

3. **Ayarlar**' dan **alt**  >  **ağ alt ağı**' nı seçin.

4. **Alt ağ ekle** iletişim kutusunda, aşağıdaki ayarlar için değerler girin:

    | Ayar | Açıklama |
    | --- | --- |
    | **Ad** | Ad, sanal ağ içinde benzersiz olmalıdır. Diğer Azure hizmetleriyle maksimum uyumluluk için, adın ilk karakteri olarak bir harf kullanmanızı öneririz. Örneğin, Azure Application Gateway, bir sayı ile başlayan bir alt ağa dağıtılır. |
    | **Adres aralığı** | <p>Aralık, sanal ağın adres alanı içinde benzersiz olmalıdır. Aralık, sanal ağ içindeki diğer alt ağ adres aralıklarıyla çakışamaz. Adres alanının sınıfsız Inter-Domain yönlendirme (CıDR) gösterimi kullanılarak belirtilmesi gerekir.</p><p>Örneğin, *10.0.0.0/16* adres alanına sahip bir sanal ağda, *10.0.0.0/22* olan bir alt ağ adres alanı tanımlayabilirsiniz. Belirtebileceğiniz en küçük Aralık */29*, alt ağ IÇIN sekiz IP adresi sağlar. Azure, protokol uyumluluğu için her bir alt ağdaki ilk ve son adresi ayırır. Azure hizmeti kullanımı için üç ek adres ayrılır. Sonuç olarak, */29* adres aralığı ile bir alt ağ tanımlamak alt ağda KULLANILABILIR üç IP adresi ile sonuçlanır.</p><p>Bir sanal ağı bir VPN ağ geçidine bağlamayı planlıyorsanız, bir ağ geçidi alt ağı oluşturmanız gerekir. [Ağ geçidi alt ağları için belirli adres aralığı konuları](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)hakkında daha fazla bilgi edinin. Belirli koşullar altında alt ağ eklendikten sonra adres aralığını değiştirebilirsiniz. Bir alt ağ adres aralığını değiştirme hakkında bilgi edinmek için bkz. [alt ağ ayarlarını değiştirme](#change-subnet-settings).</p> |
    | **Ağ güvenlik grubu** | Alt ağın gelen ve giden ağ trafiğini filtrelemek için, var olan bir ağ güvenlik grubunu bir alt ağla ilişkilendirebilirsiniz. Ağ güvenlik grubu, sanal ağla aynı abonelikte ve konumda bulunmalıdır. [Ağ güvenlik grupları](./network-security-groups-overview.md) ve [ağ güvenlik grubu oluşturma](tutorial-filter-network-traffic.md)hakkında daha fazla bilgi edinin. |
    | **Yol tablosu** | Diğer ağlara ağ trafiği yönlendirmeyi denetlemek için, isteğe bağlı olarak mevcut bir yol tablosunu bir alt ağ ile ilişkilendirebilirsiniz. Yol tablosu, sanal ağla aynı abonelikte ve konumda bulunmalıdır. [Azure yönlendirme](virtual-networks-udr-overview.md) ve [yol tablosu oluşturma](tutorial-create-route-table-portal.md)hakkında daha fazla bilgi edinin. |
    | **Hizmet uç noktaları** | <p>Bir alt ağ, isteğe bağlı olarak bir veya daha fazla hizmet uç noktası etkin olabilir. Hizmet için bir hizmet uç noktasını etkinleştirmek istiyorsanız, hizmet uç noktalarını **Hizmetler** listesinden etkinleştirmek istediğiniz hizmeti veya hizmetleri seçin. Azure, konumu bir uç nokta için otomatik olarak yapılandırır. Varsayılan olarak Azure, sanal ağın bölgesi için hizmet uç noktalarını yapılandırır. Azure, bölgesel yük devretme senaryolarını desteklemek için uç noktalarını Azure depolama için [Azure eşlenmiş bölgeleriyle](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) otomatik olarak yapılandırır.</p><p>Hizmet uç noktasını kaldırmak için hizmet uç noktasını kaldırmak istediğiniz hizmetin seçimini kaldırın. Hizmet uç noktaları ve için etkinleştiribilecekleri hizmetler hakkında daha fazla bilgi edinmek için bkz. [sanal ağ hizmeti uç noktaları](virtual-network-service-endpoints-overview.md). Hizmet için bir hizmet uç noktasını etkinleştirdikten sonra, hizmet ile oluşturulan bir kaynağın alt ağı için ağ erişimini de etkinleştirmeniz gerekir. Örneğin, **Microsoft. Storage** için hizmet uç noktasını etkinleştirirseniz, ağ erişimi vermek Istediğiniz tüm Azure depolama hesaplarına ağ erişimini de etkinleştirmeniz gerekir. Hizmet uç noktasının etkinleştirildiği alt ağlara ağ erişimini etkinleştirmek için, hizmet uç noktasını etkinleştirdiğiniz tek hizmet için belgelere bakın.</p><p>Bir alt ağ için hizmet uç noktasının etkinleştirildiğini doğrulamak için, alt ağdaki herhangi bir ağ arabirimi için [geçerli yolları](diagnose-network-routing-problem.md) görüntüleyin. Bir uç nokta yapılandırdığınızda, hizmetin adres ön ekine sahip bir *varsayılan* yol ve **virtualnetworkserviceendpoint**'in bir sonraki atlama türü görürsünüz. Yönlendirme hakkında daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).</p> |
    | **Alt ağ temsilcisi** | Bir alt ağda isteğe bağlı olarak bir veya daha fazla temsilci etkin olabilir. Alt ağ temsili, hizmet dağıtımı sırasında benzersiz bir tanımlayıcı kullanarak alt ağda hizmete özel kaynaklar oluşturmak için hizmete açık izinler verir. Bir hizmet için temsilci seçmek için, **Hizmetler** listesinden yetkisini atamak istediğiniz hizmeti seçin. |

5. Alt ağı seçtiğiniz sanal ağa eklemek için **Tamam**' ı seçin.

### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alt ağ ayarlarını değiştir

1. Sanal ağlarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

2. Değiştirmek istediğiniz alt ağı içeren sanal ağın adını seçin.

3. **Ayarlar**' dan **alt ağlar**' ı seçin.

4. Alt ağlar listesinde, ayarlarını değiştirmek istediğiniz alt ağı seçin.

5. Alt ağ sayfasında, aşağıdaki ayarlardan birini değiştirin:

    | Ayar | Açıklama |
    | --- | --- |
    | **Adres aralığı** | Alt ağ içinde hiçbir kaynak dağıtılmamışsa, adres aralığını değiştirebilirsiniz. Alt ağda herhangi bir kaynak varsa, kaynakları başka bir alt ağa taşımanız ya da önce alt ağdan silmeniz gerekir. Bir kaynağı taşımak veya silmek için aldığınız adımlar kaynağa bağlı olarak değişir. Alt ağlardaki kaynakları taşımayı veya silmeyi öğrenmek için, bu kaynak türlerinin her biri için belgeleri okuyun. [Bir alt ağ ekleme](#add-a-subnet)adım 4 ' te **adres aralığı** kısıtlamalarına bakın. |
    | **Kullanıcılar** | Yerleşik rolleri veya kendi özel rollerinizi kullanarak alt ağa erişimi denetleyebilirsiniz. Alt ağa erişim için rol ve kullanıcı atama hakkında daha fazla bilgi edinmek için bkz. [Azure rolleri atama](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). |
    | **Ağ güvenlik grubu** ve **Yol tablosu** | Bkz. 4. adım [alt ağ ekleme](#add-a-subnet). |
    | **Hizmet uç noktaları** | <p>[Alt ağ ekleme](#add-a-subnet)adım 4 ' te hizmet uç noktaları bölümüne bakın. Var olan bir alt ağ için bir hizmet uç noktası etkinleştirirken, alt ağdaki herhangi bir kaynakta hiçbir kritik görevin çalışmadığını doğrulayın. Hizmet uç noktaları, alt ağdaki her ağ arabirimindeki yolları geçer. Hizmet uç *noktaları, varsayılan* yolu *0.0.0.0/0* adres ön eki ve sonraki atlama türü ile birlikte kullanarak hizmetin adres öneklerine ve *virtualnetworkserviceendpoint*'in bir sonraki atlama türüne sahip yeni bir yol kullanmaktır.</p><p>Anahtar sırasında, herhangi bir açık TCP bağlantısı sonlandırılabilir. Hizmet uç noktası, tüm ağ arabirimleri için hizmete trafik akışı yeni rota ile güncelleştirilene kadar etkin değildir. Yönlendirme hakkında daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).</p> |
    | **Alt ağ temsilcisi** | [Alt ağ ekleme](#add-a-subnet)adım 4 ' te hizmet uç noktaları bölümüne bakın. Alt ağ temsilcisi, sıfır veya birden çok temsilci için etkinleştirilmiş olarak değiştirilebilir. Bir hizmetin bir kaynağı alt ağda zaten dağıtılmışsa, hizmetin tüm kaynakları kaldırılana kadar alt ağ temsili eklenemez veya kaldırılamaz. Farklı bir hizmet için temsilci seçmek istiyorsanız, **Hizmetler** listesinden yetkisini atamak istediğiniz hizmeti seçin. |

6. **Kaydet**’i seçin.

### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Bir alt ağı silme

Alt ağı yalnızca alt ağda kaynak yoksa silebilirsiniz. Kaynaklar alt ağdaysa, alt ağı silebilmek için önce bu kaynakları silmeniz gerekir. Kaynağı silmek için aldığınız adımlar kaynağa bağlı olarak değişir. Alt ağlardaki kaynakları silmeyi öğrenmek için, bu kaynak türlerinin her biri için belgeleri okuyun.

1. Sanal ağlarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

2. Silmek istediğiniz alt ağı içeren sanal ağın adını seçin.

3. **Ayarlar**' dan **alt ağlar**' ı seçin.

4. Alt ağlar listesinde, silmek istediğiniz alt ağı seçin.

5. **Sil**' i seçin ve ardından onay Iletişim kutusunda **Evet** ' i seçin.

### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network VNET subnet Delete](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>İzinler

Alt ağlardaki görevleri yapmak için, hesabınız [ağ katılımcısı rolüne](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) veya aşağıdaki tabloda uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır:

|Eylem                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft. Network/virtualNetworks/alt ağlar/okuma                           |   Sanal ağ alt ağını oku              |
|Microsoft. Network/virtualNetworks/alt ağlar/yazma                          |   Sanal ağ alt ağını oluşturma veya güncelleştirme  |
|Microsoft. Network/virtualNetworks/alt ağlar/Sil                         |   Sanal ağ alt ağını silme            |
|Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action                    |   Sanal ağa ekleme                     |
|Microsoft. Network/virtualNetworks/alt ağlar/Jodavetli Aserviceendpoint/Action  |   Bir alt ağ için hizmet uç noktasını etkinleştirme     |
|Microsoft. Network/virtualNetworks/alt ağlar/virtualMachines/Read           |   Sanal makineleri bir alt ağda al       |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak sanal ağ ve alt ağlar oluşturun
- Sanal ağlar için [Azure ilke tanımları](./policy-reference.md) oluşturma ve atama