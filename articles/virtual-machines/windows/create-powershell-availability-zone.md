---
title: Bölge temelli Windows VM oluşturma-Azure PowerShell
description: Azure PowerShell ile bir kullanılabilirlik alanında Windows sanal makinesi oluşturma
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: aacb8c951de1fb29dc08bdb6ef0b68b23966f326
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033867"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>PowerShell ile bir kullanılabilirlik alanında Windows sanal makinesi oluşturma

Bu makalede, Azure PowerShell kullanarak bir Azure kullanılabilirlik alanında Windows Server 2016 çalıştıran bir Azure sanal makinesi oluşturma hakkında detaylı bilgiler sağlanmaktadır. [Kullanılabilirlik alanı](../../availability-zones/az-overview.md), bir Azure bölgesinde fiziksel olarak ayrılmış bir alandır. Uygulamalarınızı beklenmeyen hatalardan veya tüm veri merkezinin kaybedilmesinden korumak için kullanılabilirlik alanlarından yararlanın.

Kullanılabilirlik alanı kullanmak için, [desteklenen bir Azure bölgesinde](../../availability-zones/az-overview.md#services-support-by-region) sanal makinenizi oluşturun.

 

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

`Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>VM SKU kullanılabilirliğini denetleme
VM boyutları veya SKU'ların kullanılabilirliği, bölge ve alanlara göre farklılık gösterebilir. Kullanılabilirlik Alanları kullanımını planlamanıza yardımcı olmak üzere, kullanılabilir VM SKU'larını Azure bölgesine ve alana göre listeleyebilirsiniz. Bu özellik, uygun bir VM boyutu seçmenizi ve alanlar arasında istenen dayanıklılığı elde etmenizi sağlar. Farklı VM türleri ve boyutları hakkında daha fazla bilgi için bkz. [VM Boyutlarına genel bakış](sizes.md).

Kullanılabilir VM SKU 'Larını [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) komutuyla görüntüleyebilirsiniz. Aşağıdaki örnekte kullanılabilir VM SKU'ları *eastus2* bölgesinde içinde listelenmiştir:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

Çıktı, her VM boyutunun kullanılabilir olduğu Kullanılabilir Alanlarını gösteren aşağıdaki sıkıştırılmış örneğe benzer:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bu örnekte, *eastus2* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturulur. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Ağ kaynakları oluşturma

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Bir sanal ağ, alt ağ ve genel IP adresi oluşturma 
Bu kaynaklar, sanal makineye ağ bağlantısı sağlamak ve sanal makineyi İnternet'e bağlamak için kullanılır. IP adresini bir kullanılabilirlik alanında oluşturun (bu örnekte *2*). Sonraki bir adımda, IP adresini oluşturmak için kullanılan aynı bölgede sanal makineyi oluşturursunuz.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Ağ güvenliği grubu ve ağ güvenliği grup kuralı oluşturma 
Ağ güvenliği grubu, gelen ve giden kuralları kullanarak sanal makinenin güvenliğini sağlar. Bu durumda, bağlantı noktası 3389 için gelen masaüstü bağlantılarına izin veren bir gelen kuralı oluşturulur. Ayrıca, gelen web trafiği sağlayan bağlantı noktası 80 için bir gelen kuralı oluşturmak istiyoruz.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Sanal makine için bir ağ kartı oluşturma 
Sanal makine için [New-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) ile bir ağ kartı oluşturun. Ağ kartı, sanal makineyi bir alt ağa, ağ güvenliği grubuna ve genel IP adresine bağlar.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

Sanal makine yapılandırması oluşturun. Bu yapılandırma, sanal makineyi dağıtırken kullanılan sanal makine görüntüsü, boyutu ve kimlik doğrulama yapılandırması gibi ayarları içerir. Bu örnekteki *Standard_DS1_v2* boyutu, kullanılabilirlik alanlarında desteklenir. Bu yapılandırma, IP adresini oluştururken ayarladığınız kullanılabilirlik alanını da belirtir. Bu adımı çalıştırırken kimlik bilgileri istenir. Girdiğiniz değerler, sanal makinenin kullanıcı adı ve parolası olarak yapılandırılır.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)ile sanal makineyi oluşturun.

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Yönetilen disk için bölgeyi onaylama

VM’nin IP adresi kaynağını, VM ile aynı kullanılabilirlik alanında oluşturdunuz. Sanal makine için yönetilen disk kaynağı, aynı kullanılabilirlik alanında oluşturulur. Bunu [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)ile doğrulayabilirsiniz:

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

Çıktı, yönetilen diskin VM ile aynı kullanılabilirlik alanında olduğunu gösterir:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kullanılabilirlik alanında nasıl sanal makine oluşturulacağını öğrendiniz. Azure VM 'Leri için [kullanılabilirlik](availability.md) hakkında daha fazla bilgi edinin.
