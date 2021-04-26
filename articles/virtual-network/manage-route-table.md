---
title: Azure yol tablosu oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Sanal ağ trafiği yönlendirme hakkında bilgi bulmayı ve yol tablosu oluşturma, değiştirme veya silme hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783386"
---
# <a name="create-change-or-delete-a-route-table"></a>Rota tablosunu oluşturma, değiştirme veya silme

Azure, Azure alt ağları, sanal ağlar ve şirket içi ağlar arasındaki trafiği otomatik olarak yönlendirir. Azure 'un varsayılan yönlendirmesinde herhangi birini değiştirmek istiyorsanız, bir yol tablosu oluşturarak bunu yapabilirsiniz. Sanal ağlarda yönlendirmeye yeni başladıysanız, [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md) bölümünde veya bir [öğreticiyi](tutorial-create-route-table-portal.md)tamamlayarak bu konuda daha fazla bilgi edinebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Hesabınız yoksa, etkin abonelikle bir Azure hesabı ayarlayın. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ardından Bu makalenin herhangi bir bölümündeki adımları başlatmadan önce bu görevlerden birini doldurun:

- **Portal kullanıcıları**: [Azure Portal](https://portal.azure.com) Azure hesabınızla oturum açın.

- **PowerShell kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın ya da bilgisayarınızdan PowerShell 'i çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Azure Cloud Shell tarayıcısı sekmesinde **ortam Seç** açılan listesini bulun ve daha önce seçilmemişse **PowerShell** ' i seçin.

    PowerShell 'i yerel olarak çalıştırıyorsanız, Azure PowerShell Module sürüm 1.0.0 veya üstünü kullanın. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az.Network` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). `Connect-AzAccount`Azure ile bağlantı oluşturmak için de ' i çalıştırın.

- **Azure komut satırı arabirimi (CLI) kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da CLI 'yı bilgisayarınızdan çalıştırın. Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure CLı sürüm 2.0.31 veya üstünü kullanın. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). `az login`Azure ile bağlantı oluşturmak için de ' i çalıştırın.

Oturum açmak veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı rolüne](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

Azure konumu ve aboneliği başına oluşturabileceğiniz yol tablolarının sayısı için bir sınır vardır. Ayrıntılar için bkz. [ağ limitleri-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. [Azure portalı](https://portal.azure.com) menüsünde veya **Giriş** sayfasında **Kaynak oluştur**’u seçin.

1. Arama kutusuna *yol tablosu* girin. Arama sonuçlarında **yol tablosu** göründüğünde, bunu seçin.

1. **Yol tablosu** sayfasında **Oluştur**' u seçin.

1. **Yol tablosu oluştur** iletişim kutusunda:

    1. Yol tablosu için bir **ad** girin.
    1. **Aboneliğinizi** seçin.
    1. Mevcut bir **kaynak grubu** seçin veya yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin.
    1. Bir **konum** seçin.
    1. Yönlendirme tablosunu bir VPN ağ geçidi aracılığıyla şirket içi ağınıza bağlı bir sanal ağda bir alt ağla ilişkilendirmeyi planlıyorsanız ve şirket içi rotalarınızı alt ağdaki ağ arabirimlerine yaymak istemiyorsanız, **sanal ağ geçidi yol yaymayı** **devre dışı** olarak ayarlayın.

1. Yeni yol tablonuzu oluşturmak için **Oluştur** ' u seçin.

### <a name="create-route-table---commands"></a>Rota tablosu oluşturma-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Rota tablolarını görüntüleme

Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin. Aboneliğinizde mevcut olan yol tabloları listelenir.

### <a name="view-route-table---commands"></a>Rota tablosunu görüntüleme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table List](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Rota tablosunun ayrıntılarını görüntüleme

1. Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, ayrıntılarını görüntülemek istediğiniz yol tablosunu seçin.

1. Yol tablosu sayfasında, **Ayarlar**' ın altında yol tablosundaki **yolları** veya yol tablosunun ilişkilendirildiği **alt ağları** görüntüleyin.

Ortak Azure ayarları hakkında daha fazla bilgi edinmek için aşağıdaki bilgilere bakın:

- [Etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md)
- [Erişim denetimi (IAM)](../role-based-access-control/overview.md)
- [Etiketler](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Kilitler](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Otomasyon betiği](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Rota tablosunun ayrıntılarını görüntüleme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Yol tablosunu değiştirme

1. Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, değiştirmek istediğiniz yol tablosunu seçin.

En yaygın değişiklikler, rotalar [eklemek](#create-a-route) , yönlendirmeleri [kaldırmak](#delete-a-route) , rota tablolarını alt ağlarla [ilişkilendirmek](#associate-a-route-table-to-a-subnet) veya rota tablolarının alt ağlardan [ilişkilendirmesini ortadan](#dissociate-a-route-table-from-a-subnet) kaldırmasıdır.

### <a name="change-a-route-table---commands"></a>Yol tablosunu değiştirme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

İsteğe bağlı olarak bir rota tablosunu bir alt ağ ile ilişkilendirebilirsiniz. Bir yol tablosu, sıfır veya daha fazla alt ağ ile ilişkilendirilebilir. Yol tabloları sanal ağlarla ilişkili olmadığından, yol tablosunun ilişkilendirilmesini istediğiniz her alt ağ için bir rota tablosu ilişkilendirmeniz gerekir. Azure, sanal ağ bir Azure sanal ağ geçidine (ExpressRoute veya VPN) bağlıysa, rota tabloları, [varsayılan yollar](virtual-networks-udr-overview.md#default)ve şirket içi bir ağdan yayılan yollar temelinde alt ağdan çıkan tüm trafiği yönlendirir. Yönlendirme tablosu ile aynı Azure konumunda ve abonelikte bulunan sanal ağlardaki alt ağlarla yalnızca bir rota tablosu ilişkilendirebilirsiniz.

1. Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

1. Sanal ağ listesinde, bir yol tablosunu ilişkilendirmek istediğiniz alt ağı içeren sanal ağı seçin.

1. Sanal ağ menü çubuğunda **alt ağlar**' ı seçin.

1. Yol tablosunu ilişkilendirmek istediğiniz alt ağı seçin.

1. **Yol tablosu**' nda alt ağla ilişkilendirmek istediğiniz yol tablosunu seçin.

1. **Kaydet**’i seçin.

Sanal ağınız bir Azure VPN Gateway 'e bağlıysa, bir yol tablosunu *0.0.0.0/0* hedefine sahip bir yol içeren [ağ geçidi alt ağıyla](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) ilişkilendirmeyin. Bunun yapılması, ağ geçidinin düzgün çalışmasını engelleyebilir. Bir rotada *0.0.0.0/0* kullanma hakkında daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Rota tablosunu ilişkilendirme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Bir alt ağdan yol tablosunun ilişkilendirmesini kaldırma

Bir alt ağdan yol tablosunun ilişkilendirmesini kaldırdığınızda, Azure trafiği [varsayılan yollarına](virtual-networks-udr-overview.md#default)göre yönlendirir.

1. Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

1. Sanal ağ listesinde, bir yol tablosunun ilişkilendirmesini kaldırmak istediğiniz alt ağı içeren sanal ağı seçin.

1. Sanal ağ menü çubuğunda **alt ağlar**' ı seçin.

1. Yol tablosunun ilişkilendirmesini kaldırmak istediğiniz alt ağı seçin.

1. **Yol tablosunda** **hiçbiri**' ni seçin.

1. **Kaydet**’i seçin.

### <a name="dissociate-a-route-table---commands"></a>Rota tablosunun ilişkilendirmesini kaldırma-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Yol tablosunu silme

Herhangi bir alt ağ ile ilişkili bir yol tablosunu silemezsiniz. Bir yol tablosunun silmeyi denemeden önce tüm alt ağlardan [ilişkilendirmesini](#dissociate-a-route-table-from-a-subnet) kaldırın.

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, silmek istediğiniz yol tablosunu seçin.

1. **Sil**' i seçin ve ardından onay Iletişim kutusunda **Evet** ' i seçin.

### <a name="delete-a-route-table---commands"></a>Yol tablosunu silme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Yönlendirme oluşturma

Yol tablosu başına her bir Azure konumu ve aboneliği için kaç yol oluşturabileceğiniz bir sınır vardır. Ayrıntılar için bkz. [ağ limitleri-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, yol eklemek istediğiniz yol tablosunu seçin.

1. Yol tablosu menü çubuğundan **rotalar**  >  **Ekle**' yi seçin.

1. Yol tablosu içindeki yol için benzersiz bir **yol adı** girin.

1. Trafiği yönlendirmek istediğiniz, sınıfsız Inter-Domain yönlendirme (CıDR) gösteriminde **Adres önekini** girin. Ön ek, yol tablosu içinde birden fazla rotada çoğaltılamaz, ancak ön ek başka bir ön ek içinde olabilir. Örneğin, bir rotada önek olarak *10.0.0.0/16* tanımladıysanız, *10.0.0.0/22* adres ön ekine sahip başka bir yol tanımlayabilirsiniz. Azure, en uzun ön ek eşleşmesi temelinde trafik için bir yol seçer. Daha fazla bilgi için bkz. [Azure 'un bir yolu nasıl seçtiği](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. **Sonraki atlama türünü** seçin. Sonraki atlama türleri hakkında daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).

1. **Sanal Gereç** bir **sonraki atlama türünü** seçerseniz, **sonrakı atlama adresi** için bir IP adresi girin.

1. **Tamam**’ı seçin.

### <a name="create-a-route---commands"></a>Rota oluşturma-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Rotaları görüntüle

Bir yol tablosu sıfır veya daha fazla yol içeriyor. Yolları görüntülerken listelenen bilgiler hakkında daha fazla bilgi edinmek için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md).

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, rotaları görüntülemek istediğiniz yol tablosunu seçin.

1. Yolların listesini görmek için yol tablosu menü çubuğunda **rotalar** ' ı seçin.

### <a name="view-routes---commands"></a>Yolları görüntüle-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route List](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Bir yolun ayrıntılarını görüntüleme

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, ayrıntılarını görüntülemek istediğiniz yolu içeren yol tablosunu seçin.

1. Yolların listesini görmek için yol tablosu menü çubuğunda **rotalar** ' ı seçin.

1. Ayrıntılarını görüntülemek istediğiniz yolu seçin.

### <a name="view-details-of-a-route---commands"></a>Yol komutlarının ayrıntılarını görüntüleme

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route Show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Rota değiştirme

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, değiştirmek istediğiniz yolu içeren yol tablosunu seçin.

1. Yolların listesini görmek için yol tablosu menü çubuğunda **rotalar** ' ı seçin.

1. Değiştirmek istediğiniz yolu seçin.

1. Var olan ayarları yeni ayarlarına değiştirip **Kaydet**' i seçin.

### <a name="change-a-route---commands"></a>Rota değiştirme komutları

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route Update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Bir yolu silme

1. Yönlendirme tablolarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

1. Yol tablosu listesinde, silmek istediğiniz yolu içeren yol tablosunu seçin.

1. Yolların listesini görmek için yol tablosu menü çubuğunda **rotalar** ' ı seçin.

1. Silmek istediğiniz yolu seçin.

1. **Sil**' i seçin, ardından onay Iletişim kutusunda **Evet** ' i seçin.

### <a name="delete-a-route---commands"></a>Rota silme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route Delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Geçerli yolları görüntüle

VM 'ye bağlı her ağ arabirimine yönelik etkili rotalar, oluşturduğunuz yol tablolarının bir birleşimidir, Azure 'un varsayılan yolları ve şirket içi ağlardan bir Azure sanal ağ geçidi üzerinden Sınır Ağ Geçidi Protokolü (BGP) aracılığıyla yayılan yollar. Bir ağ arabirimine yönelik geçerli yolların anlaşılmasında, yönlendirme sorunlarını giderirken yararlı olur. Çalışan bir VM 'ye bağlı herhangi bir ağ arabirimi için geçerli yolları görüntüleyebilirsiniz.

1. VM 'lerinizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

1. Sanal makine listesinde, için geçerli yolları görüntülemek istediğiniz VM 'yi seçin.

1. VM menü çubuğunda **ağ iletişimi**' ni seçin.

1. Bir ağ arabiriminin adını seçin.

1. Ağ arabirimi menü çubuğunda, **etkin yollar**' ı seçin.

1. Trafiği yönlendirmek istediğiniz konum için doğru yolun mevcut olup olmadığını görmek için etkin yolların listesini gözden geçirin. Bu listede [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md)bölümünde gördüğünüz sonraki atlama türleri hakkında daha fazla bilgi edinin.

### <a name="view-effective-routes---commands"></a>Geçerli rotaları görüntüleme-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network Nic Show-geçerli-Route-Table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>İki uç nokta arasındaki yönlendirmeyi doğrula

Bir sanal makine ile başka bir Azure kaynağının IP adresi, bir şirket içi kaynak veya Internet 'teki bir kaynak arasındaki sonraki atlama türünü belirleyebilirsiniz. Yönlendirme sorunlarını giderirken Azure 'un yönlendirmeyi belirleme yararlı olur. Bu görevi gerçekleştirmek için, var olan bir ağ izleyicisine sahip olmanız gerekir. Mevcut bir ağ izleyicisine sahip değilseniz, [bir Ağ İzleyicisi örneği oluşturma](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)' daki adımları tamamlayarak bir tane oluşturun.

1. Ağınızı izleyicileri yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Ağ Izleyicisi 'ni** arayıp seçin.

1. Ağ İzleyicisi menü çubuğunda, **İleri atlama**' yi seçin.

1. **Ağ İzleyicisi 'nde | Sonraki atlama** sayfası:

    1. **Aboneliğinizi** ve yönlendirmeyi doğrulamak istediğiniz kaynak sanal makinenin **kaynak grubunu** seçin.

    1. **Sanal makineyi** ve VM 'ye bağlı **ağ arabirimini** seçin.
    
    1. Yönlendirmeyi doğrulamak istediğiniz ağ arabirimine atanan bir **kaynak IP adresi** girin.

    1. Yönlendirmeyi doğrulamak istediğiniz **hedef IP adresini** girin.

1. **Sonraki durak**' ı seçin.

Kısa bir bekleme sonrasında Azure, sonraki atlama türünü ve trafiği yönlendiren yolun KIMLIĞINI bildirir. [Sanal ağ trafiği yönlendirmesinde](virtual-networks-udr-overview.md)döndürülen sonraki atlama türleri hakkında daha fazla bilgi edinin.

### <a name="validate-routing-between-two-endpoints---commands"></a>İki uç nokta arasındaki yönlendirmeyi doğrula-komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network izleyici Show-Next-Hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>İzinler

Rota tablolarında ve rotalarındaki görevleri yapmak için, hesabınız [ağ katılımcısı rolüne](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) veya aşağıdaki tabloda listelenen uygun eylemlere atanmış özel bir [role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır:

| Eylem                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/Read                              |   Rota tablosu okuma                                    |
| Microsoft. Network/routeTables/Write                             |   Rota tablosu oluşturma veya güncelleştirme                        |
| Microsoft. Network/routeTables/Delete                            |   Yol tablosunu silme                                  |
| Microsoft. Network/routeTables/JOIN/Action                       |   Yönlendirme tablosunu bir alt ağ ile ilişkilendirme                   |
| Microsoft. Network/routeTables/rotalar/Read                       |   Bir yolu oku                                          |
| Microsoft. Network/routeTables/rotalar/Write                      |   Rota oluşturma veya güncelleştirme                              |
| Microsoft. Network/routeTables/rotalar/Delete                     |   Bir yolu silme                                        |
| Microsoft. Network/NetworkInterfaces/effectiveRouteTable/Action  |   Ağ arabirimi için geçerli yol tablosunu al |
| Microsoft. Network/networkWatchers/Sonrakii/eylem                |   Bir VM 'den sonraki atlamayı alır                           |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak bir rota tablosu oluşturma
- Sanal ağlar için [Azure ilke tanımları](./policy-reference.md) oluşturma ve atama