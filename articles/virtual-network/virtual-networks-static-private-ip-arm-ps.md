---
title: Statik özel IP adresi ile VM oluşturma-Azure PowerShell
description: PowerShell kullanarak özel IP adresine sahip bir sanal makine oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98783793"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>PowerShell kullanarak statik bir özel IP adresi ile sanal makine oluşturma

Statik bir özel IP adresi ile bir sanal makine (VM) oluşturabilirsiniz. Bir VM 'ye bir alt ağdan hangi adresin atandığını seçmek istiyorsanız, dinamik bir adres yerine statik bir özel IP adresi atayın. [Statik özel IP adresleri](./public-ip-addresses.md#allocation-method)hakkında daha fazla bilgi edinin. Var olan bir VM 'ye atanan özel IP adresini dinamik veya genel IP adresleriyle çalışacak şekilde değiştirmek için bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Aşağıdaki adımları yerel bilgisayarınızdan veya Azure Cloud Shell kullanarak tamamlayabilirsiniz. Yerel bilgisayarınızı kullanmak için [Azure PowerShell yüklü](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)olduğundan emin olun. Azure Cloud Shell kullanmak için, takip eden herhangi bir komut kutusunun sağ üst köşesinde bulunan **deneyin** ' i seçin. Cloud Shell sizi Azure 'da oturum açar.

1. Cloud Shell kullanılıyorsa adım 2 ' ye atlayın. Bir komut oturumu açın ve ile Azure 'da oturum açın `Connect-AzAccount` .
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, Doğu ABD Azure bölgesinde bir kaynak grubu oluşturur:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) ve [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) komutlarıyla bir alt ağ yapılandırması ve sanal ağ oluşturun:

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Sanal ağda bir ağ arabirimi oluşturun ve [New-Aznetworkınterfaceipconfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) ve [New-aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) komutlarıyla alt ağdan ağ arabirimine özel bir IP adresi atayın:

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)Ile bir VM yapılandırması oluşturun ve ardından VM 'yi [New-azvm](/powershell/module/az.Compute/New-azVM)ile oluşturun. İstendiğinde, sanal makine için oturum açma kimlik bilgileri olarak kullanılacak bir Kullanıcı adı ve parola girin:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> İşletim sistemine özel IP adresi ayarları ekleyebilse de, [bir işletim sistemine özel IP adresi ekleme](virtual-network-network-interface-addresses.md#private)' yi okuduktan sonra yapmamasını öneririz.
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> VM 'ye internet 'ten erişmek için, VM 'ye bir genel IP adresi atamanız gerekir. Dinamik bir özel IP adresi atamasını statik atamaya de değiştirebilirsiniz. Ayrıntılar için bkz. [IP adreslerini ekleme veya değiştirme](virtual-network-network-interface-addresses.md). Ayrıca, ağ güvenlik grubunu ağ arabirimine, içinde ağ arabirimini oluşturduğunuz alt ağa veya her ikisine ilişkilendirerek ağ trafiğini sanal makinemle sınırlandırmanıza önerilir. Ayrıntılar için bkz. [ağ güvenlik gruplarını yönetme](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

- [Özel IP adresleri](./private-ip-addresses.md) hakkında daha fazla bilgi edinin ve bir Azure sanal makinesine [STATIK bir özel IP adresi](virtual-network-network-interface-addresses.md#add-ip-addresses) atama.
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makineleri oluşturma hakkında daha fazla bilgi edinin.
