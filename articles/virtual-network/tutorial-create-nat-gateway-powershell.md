---
title: 'Öğretici: NAT Gateway oluşturma-PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Kullanmaya başlamak Azure PowerShell kullanarak bir NAT ağ geçidi oluşturdu.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620253"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak NAT ağ geçidi oluşturma

Bu öğreticide, Azure sanal ağ NAT hizmetini nasıl kullanacağınız gösterilmektedir. Azure 'da bir sanal makine için giden bağlantı sağlamak üzere bir NAT ağ geçidi oluşturacaksınız. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturun.
> * Sanal makine oluşturur.
> * Bir NAT ağ geçidi oluşturun ve sanal ağla ilişkilendirin.
> * Sanal makineye bağlanın ve NAT IP adresini doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek **eastus2** konumunda **Myresourcegroupnat** adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>NAT ağ geçidini oluşturma

Bu bölümde, NAT ağ geçidini ve destekleyici kaynakları oluşturacağız.

* Internet 'e erişmek için NAT ağ geçidi için bir veya daha fazla genel IP adresi gerekir. **Myresourcegroupnat** Içinde **Mypublicıp** adlı bir genel IP adresi kaynağı oluşturmak Için [New-azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) ' i kullanın. 

* [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway)ile genel BIR Azure NAT ağ geçidi oluşturun. Bu komutun sonucu **Mtypiklılicıp** genel IP adresini kullanan **mynatgateway** adlı bir ağ geçidi kaynağı oluşturur. Boşta kalma zaman aşımı 10 dakikaya ayarlanır.  

* Myresourcegroup **adlı bir** alt ağ Ile **myvnet** adlı bir alt ağ ile **myresourcegroup** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) [kullanan bir](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) sanal ağ oluşturun. Sanal ağın IP adresi alanı **10.1.0.0/16**' dır. Sanal ağ içindeki alt ağ **10.1.0.0/24**' dir.

* Sanal makineye erişmek için **Mybastionhost** adlı bir Azure savunma ana bilgisayarı oluşturun. Savunma konağını oluşturmak için [Yeni azgı](/powershell/module/az.network/new-azbastion) kullanın. [Yeni-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)ile savunma konağı için genel bir IP adresi oluşturun.

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Sanal makine

Bu bölümde, NAT ağ geçidini test etmek ve giden bağlantının genel IP adresini doğrulamak için bir sanal makine oluşturacaksınız.

* [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)ile bir ağ arabirimi oluşturun.

* VM için [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)ile bir Yönetici Kullanıcı adı ve parola ayarlayın.

* Sanal makineyi şu şekilde oluşturun:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Bir sonraki bölüme geçmeden önce sanal makine oluşturma işleminin tamamlanmasını bekleyin.

## <a name="test-nat-gateway"></a>Test NAT ağ geçidi

Bu bölümde, NAT ağ geçidini test edeceğiz. Önce NAT ağ geçidinin genel IP 'sini keşfedeceğiz. Ardından, test sanal makinesine bağlanır ve NAT ağ geçidi üzerinden giden bağlantıyı doğrulayacağız.
    
1. [Azure portalda](https://portal.azure.com) oturum açma

1. **Genel bakış** ekranında NAT ağ GEÇIDININ genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **mypublicıp**' yi seçin.

2. Genel IP adresini bir yere getirin:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ağ geçidinin genel IP adresini bulma" border="true":::

3. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegroupnat** kaynak grubunda bulunan **myvm** ' yi seçin.

4. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

5. Mavi **kullanımı** savunma düğmesini seçin.

6. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

7. **Mytestvm**'de **Internet Explorer** 'ı açın.

8. **https://whatsmyip.com** Adres çubuğuna yazın.

9. Görünen IP adresinin, önceki adımda not ettiğiniz NAT ağ geçidi adresiyle eşleştiğini doğrulayın:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Dış giden IP 'yi gösteren Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiz, sanal ağ, sanal makine ve NAT ağ geçidini aşağıdaki adımlarla silin:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal ağ NAT hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Sanal ağ NAT genel bakış](nat-overview.md)
