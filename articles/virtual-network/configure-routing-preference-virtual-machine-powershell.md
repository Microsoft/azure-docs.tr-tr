---
title: VM için yönlendirme tercihini Yapılandırma-Azure PowerShell
description: Azure PowerShell kullanarak yönlendirme tercihi seçimine sahip bir genel IP adresi ile VM oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672960"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Azure PowerShell kullanarak bir VM için yönlendirme tercihini yapılandırma

Bu makalede, bir sanal makine için yönlendirme tercihini yapılandırma gösterilmektedir. Internet 'e ait trafiği yönlendirme tercih seçeneği olarak **Internet** ' i SEÇTIĞINIZDE, ISS ağı aracılığıyla yönlendirilecektir. Varsayılan yönlendirme, Microsoft Global ağı aracılığıyla yapılır.

Bu makalede, Azure PowerShell kullanarak ISS ağı üzerinden trafiği yönlendirmek üzere ayarlanmış bir genel IP ile sanal makine oluşturma işlemi gösterilmektedir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
1. Cloud Shell kullanılıyorsa adım 2 ' ye atlayın. Bir komut oturumu açın ve ile Azure 'da oturum açın `Connect-AzAccount` .
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, Doğu ABD Azure bölgesinde bir kaynak grubu oluşturur:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Sanal makinelerinize Internet 'ten erişmek için genel IP adreslerine ihtiyacınız vardır. [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)Ile genel IP adresleri oluşturun. Aşağıdaki örnek, *Doğu ABD* bölgesindeki *myresourcegroup* kaynak grubunda *mypublicıp* yönlendirme tercihi türü *Internet* adlı bir IPv4 Genel IP adresi oluşturur:

```azurepowershell-interactive
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

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

Bir VM 'yi dağıtmadan önce, destekleyici ağ kaynakları (ağ güvenlik grubu, sanal ağ ve sanal NIC) oluşturmanız gerekir.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek, *mynsg* adlı bir NSG oluşturur

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek *Mysubnet* Ile *myvnet* adlı bir sanal ağ oluşturur:

### <a name="create-a-subnet"></a>Alt ağ oluşturma

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>NIC oluşturma

[New-Aznetworkınterface] (/PowerShell/Module/az.htm Network/New-aznetworkınterfaceile sanal NIC 'ler oluşturun. Aşağıdaki örnek bir sanal NIC oluşturur.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

VM’ler için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) ile bir yönetici kullanıcı adı ve parola ayarlayın:

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Artık [New-azvm](/powershell/module/az.compute/new-azvm)ile VM oluşturabilirsiniz. Aşağıdaki örnekte, zaten mevcut değilse iki VM ve gerekli sanal ağ bileşenleri oluşturulur.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>VM 'ye ağ trafiğine izin ver

İnternet 'ten genel IP adresine bağlanabilmeniz için, ağ arabirimiyle ilişkilendirdiğiniz ağ güvenlik grubu, ağ arabiriminin içinde bulunduğu alt ağ veya her ikisinin de gerekli bağlantı noktalarının açık olduğundan emin olun. [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)veya [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)kullanarak bir ağ arabirimi ve alt ağının etkin güvenlik kurallarını görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

* [Genel IP adreslerinde yönlendirme tercihi](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
* Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
* [Genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.