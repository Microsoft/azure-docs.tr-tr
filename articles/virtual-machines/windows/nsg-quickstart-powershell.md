---
title: Azure PowerShell kullanarak bir VM 'ye bağlantı noktalarını açma
description: Azure Resource Manager Dağıtım modunu kullanarak Windows sanal makinenize bir bağlantı noktası açma/bitiş noktası oluşturma hakkında bilgi edinin ve Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 3577c97b55baebe08ca4020b0c570160d551dfd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033069"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>PowerShell kullanarak Azure 'da bir VM 'ye bağlantı noktalarını ve uç noktaları açma
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Hızlı komutlar
Bir ağ güvenlik grubu ve ACL kuralları oluşturmak için [Azure PowerShell yüklü en son sürümüne](/powershell/azureps-cmdlets-docs)ihtiyacınız vardır. Ayrıca [, Azure Portal kullanarak da bu adımları](nsg-quickstart-portal.md)uygulayabilirsiniz.

Azure hesabınızda oturum açın:

```powershell
Connect-AzAccount
```

Aşağıdaki örneklerde parametre adlarını kendi değerlerinizle değiştirin. *Myresourcegroup*, *mynetworksecuritygroup*ve *myvnet*dahil olmak üzere örnek parametre adları.

[New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)ile bir kural oluşturun. Aşağıdaki örnek, *80*numaralı bağlantı noktasında *TCP* trafiğine Izin vermek Için *mynetworksecuritygrouprule* adlı bir kural oluşturur:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Ardından, [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) Ile ağ güvenlik grubunuzu oluşturun ve yenı oluşturduğunuz http kuralını aşağıdaki gibi atayın. Aşağıdaki örnek, *Mynetworksecuritygroup*adlı bir ağ güvenlik grubu oluşturur:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Şimdi ağ güvenlik grubunuzu bir alt ağa atayalim. Aşağıdaki örnek, [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)ile birlikte $VNET *myvnet* adlı var olan bir sanal ağı değişkene atar:

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

[Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig)Ile ağ güvenlik grubunuzu alt ağınızla ilişkilendirin. Aşağıdaki örnek, *mysubnet* adlı alt ağı ağ güvenlik grubunuz ile ilişkilendirir:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Son olarak, değişikliklerinizin etkili olması için Sanal ağınızı [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) ile güncelleştirin:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Ağ güvenlik grupları hakkında daha fazla bilgi
Buradaki hızlı komutlar, sanal makinenize akan trafikle çalışmaya başlamanızı sağlar. Ağ güvenlik grupları, kaynaklarınıza erişimi denetlemek için çok sayıda harika özellik ve ayrıntı düzeyi sağlar. [Burada ağ güvenlik grubu ve ACL kuralları oluşturma](tutorial-virtual-network.md#secure-network-traffic)hakkında daha fazla bilgi edinebilirsiniz.

Yüksek oranda kullanılabilir Web uygulamaları için sanal makinelerinizi bir Azure Load Balancer arkasına yerleştirmeniz gerekir. Yük dengeleyici, trafiği, trafik filtrelemesi sağlayan bir ağ güvenlik grubuyla sanal makinelere dağıtır. Daha fazla bilgi için bkz. [Azure 'Da Linux sanal makinelerinin yükünü dengelemek, yüksek oranda kullanılabilir bir uygulama oluşturmak için](tutorial-load-balancer.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekte, HTTP trafiğine izin vermek için basit bir kural oluşturdunuz. Aşağıdaki makalelerde daha ayrıntılı ortamlar oluşturma hakkında bilgi edinebilirsiniz:

* [Azure Resource Manager’a genel bakış](../../azure-resource-manager/resource-group-overview.md)
* [Ağ güvenlik grubu nedir?](../../virtual-network/security-overview.md)
* [Yük dengeleyiciler için Azure Resource Manager genel bakış](../../load-balancer/load-balancer-arm.md)

