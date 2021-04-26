---
title: Genel IP adresi için yönlendirme tercihini Yapılandırma-Azure PowerShell
titlesuffix: Azure Virtual Network
description: Azure PowerShell kullanarak genel IP adresi için yönlendirme tercihini nasıl yapılandıracağınızı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667885"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Azure PowerShell kullanarak genel IP adresi için yönlendirme tercihini yapılandırma

Bu makalede, Azure PowerShell kullanarak genel bir IP adresi için ISS ağı (**Internet** seçeneği) aracılığıyla yönlendirme tercihini yapılandırma gösterilmektedir. Genel IP adresini oluşturduktan sonra, internet 'e gelen ve giden trafik için aşağıdaki Azure kaynaklarıyla ilişkilendirebilirsiniz:

* Sanal makine
* Sanal makine ölçek kümesi
* Azure Kubernetes Service (AKS)
* Internet 'e yönelik yük dengeleyici
* Application Gateway
* Azure Güvenlik Duvarı

Varsayılan olarak, genel IP adresi için yönlendirme tercihi tüm Azure hizmetleri için Microsoft Global Network 'e ayarlanır ve herhangi bir Azure hizmetiyle ilişkilendirilebilir.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 6.9.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir kaynak grubu oluşturun. Bu örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Internet yönlendirme tercihi ile genel IP oluşturma

Aşağıdaki komut, *Doğu ABD* Azure bölgesinde *Internet* olarak yönlendirme tercih türü olan yeni bir genel IP oluşturur:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Yukarıdaki oluşturulmuş genel IP adresini bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesiyle ilişkilendirebilirsiniz. Öğretici sayfasındaki CLı bölümünü kullanın: genel IP adresini VM 'niz ile ilişkilendirmek için bir [sanal makineyle genel IP adresi ilişkilendirin](associate-public-ip-address-vm.md#azure-cli) . Ayrıca, yük dengeleyici **ön uç** yapılandırmasına atayarak yukarıda oluşturulan genel IP adresini bir [Azure Load Balancer](../load-balancer/load-balancer-overview.md)ile ilişkilendirebilirsiniz. Genel IP adresi yükü dengelenmiş bir sanal IP adresi (VIP) olarak işlev görür.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP adreslerinde yönlendirme tercihi](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
- [Azure PowerShell kullanarak BIR VM için yönlendirme tercihini yapılandırın](configure-routing-preference-virtual-machine-powershell.md).
