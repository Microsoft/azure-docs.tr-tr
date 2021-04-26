---
title: Ağ trafiğini filtrele-Azure PowerShell | Microsoft Docs
description: Bu makalede, PowerShell kullanarak bir ağ güvenlik grubu ile ağ trafiğini bir alt ağa nasıl filtreleyeceğinizi öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9dca3d95f9fe810c90c27a32250d1b8ffcf89ac8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065353"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>PowerShell kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme

Bir sanal ağ alt ağına gelen ve sanal ağ alt ağından giden ağ trafiğini, bir ağ güvenlik grubu ile filtreleyebilirsiniz. Ağ güvenlik grupları, ağ trafiğini IP adresi, bağlantı noktası ve protokole göre filtreleyen güvenlik kuralları içerir. Güvenlik kuralları bir alt ağda dağıtılmış kaynaklara uygulanır. Bu makalede şunları öğreneceksiniz:

* Ağ güvenlik grubu ve güvenlik kuralları oluşturma
* Bir sanal ağ oluşturma ve ağ güvenlik grubunu alt ağ ile ilişkilendirme
* Sanal makineleri (VM) bir alt ağa dağıtma
* Trafik filtrelerini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Bir ağ güvenlik grubu, güvenlik kuralları içerir. Güvenlik kuralları, bir kaynak ve hedefi belirtir. Kaynaklar ve hedefler, uygulama güvenlik grupları olabilir.

### <a name="create-application-security-groups"></a>Uygulama güvenlik grupları oluşturma

Önce Bu makalede oluşturulan tüm kaynaklar için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnekte *eastus* konumunda bir kaynak grubu oluşturulmaktadır:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)ile bir uygulama güvenlik grubu oluşturun. Uygulama güvenlik grubu, benzer bağlantı noktası filtreleme gereksinimlerine sahip sunucuları gruplandırmanızı sağlar. Aşağıdaki örnek iki uygulama güvenlik grubu oluşturur.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Güvenlik kuralları oluşturma

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile bir güvenlik kuralı oluşturun. Aşağıdaki örnek, internetten gelen trafiğin 80 ve 443 numaralı bağlantı noktaları üzerinden *myWebServers* uygulama güvenlik grubuna gitmesine izin veren bir kural oluşturur:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Bu makalede, *Myasgmgmtservers* sanal MAKINESI için RDP (bağlantı noktası 3389) internet 'e açıktır. Üretim ortamları için 3389 numaralı bağlantı noktasını Internet 'e sunmak yerine, bir [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [özel](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ağ bağlantısı kullanarak yönetmek istediğiniz Azure kaynaklarına bağlanmanız önerilir.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek *myNsg* adlı bir ağ güvenlik grubu oluşturur:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek *myVirtualNetwork* adlı bir sanal ağ oluşturur:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)ile bir alt ağ yapılandırması oluşturun ve sonra alt ağ yapılandırmasını [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)ile sanal ağa yazın. Aşağıdaki örnek, sanal ağa *mySubnet* adlı bir alt ağ ekler ve *myNsg* ağ güvenlik grubunu onunla ilişkilendirir:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

VM 'Leri oluşturmadan önce, [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)ile alt ağa sahip sanal ağ nesnesini alın:

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

[New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)Ile her VM için BIR genel IP adresi oluşturun:

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

[New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)ile iki ağ arabirimi oluşturun ve ağ arabirimine BIR genel IP adresi atayın. Aşağıdaki örnek bir ağ arabirimi oluşturur, *myVmWeb* genel IP adresini onunla ilişkilendirir ve *myAsgWebServers* uygulama güvenlik grubunun bir üyesi yapar:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Aşağıdaki örnek bir ağ arabirimi oluşturur, *myVmMgmt* genel IP adresini onunla ilişkilendirir ve *myAsgMgmtServers* uygulama güvenlik grubunun bir üyesi yapar:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Daha sonraki bir adımda trafik filtrelemesini doğrulayabilmek için sanal ağda iki VM oluşturun.

[New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)Ile bir VM yapılandırması oluşturun ve ardından VM 'yi [New-azvm](/powershell/module/az.compute/new-azvm)ile oluşturun. Aşağıdaki örnek, web sunucusu olarak görev yapacak bir VM oluşturur. `-AsJob` seçeneği, sonraki adıma devam edebilmeniz için arka planda sanal makineyi oluşturur:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Yönetim sunucusu olarak görev yapacak bir VM oluşturun:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Sanal makinenin oluşturulması birkaç dakika sürer. Azure VM oluşturma işlemini tamamlayana kadar sonraki adıma geçmeyin.

## <a name="test-traffic-filters"></a>Trafik filtrelerini test etme

Bir sanal makinenin genel IP adresini döndürmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanın. Aşağıdaki örnek,*myVmMgmt* sanal makinesinin genel IP adresini döndürür:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Yerel bilgisayarınızdan *myVmMgmt* ile bir uzak masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. `<publicIpAddress>` değerini önceki komutta döndürülen IP adresi ile değiştirin.

```
mstsc /v:<publicIpAddress>
```

İndirilen RDP dosyasını açın. İstendiğinde **Bağlan**’ı seçin.

Sanal makineyi oluştururken belirttiğiniz kullanıcı adını ve parolayı girin (sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan**’ı seçmeniz gerekebilir), ardından **Tamam**’ı seçin. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet**’i seçin.

*myVmMgmt* sanal makinesine bağlı ağ arabiriminin içinde bulunduğu *myAsgMgmtServers* uygulama güvenlik grubuna 3389 numaralı bağlantı noktasının internetten gelmesine izin verildiği için bağlantı başarılı olur.

PowerShell üzerinden *myVmWeb* sanal makinesi ile *myVmMgmt* sanal makinesi arasında bir uzak masaüstü bağlantısı oluşturmak için aşağıdaki komutu kullanın:

``` 
mstsc /v:myvmWeb
```

Her bir ağ güvenlik grubu içindeki varsayılan güvenlik kuralı bir sanal ağ içindeki tüm IP adresleri arasında tüm bağlantı noktaları üzerinden trafiğe izin verdiği için bağlantı başarılı olur. *myAsgWebServers* güvenlik kuralı internetten 3389 numaralı gelen bağlantı noktasına izin vermediği için *myVmWeb* sanal makinesi ile bir uzak masaüstü bağlantısı oluşturamazsınız.

PowerShell’den *myVmWeb* sanal makinesine Microsoft IIS yüklemek için aşağıdaki komutu kullanın:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

IIS yüklemesi tamamlandıktan sonra *myVmWeb* sanal makinesinin bağlantısını kesmeniz durumunda *myVmMgmt* VM uzak masaüstü bağlantısında kalırsınız. IIS karşılama ekranını görüntülemek için bir internet tarayıcısı açın ve http: \/ /Myvmwebadresine gidin.

*myVmMgmt* sanal makinesiyle bağlantıyı kesin.

Bilgisayarınızda *myVmWeb* sunucusundan genel IP adresini almak için Powershell'den aşağıdaki komutu girin:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

*myVmWeb* web sunucusuna Azure dışından erişebildiğinizi onaylamak için bilgisayarınızda bir internet tarayıcısı açın ve `http://<public-ip-address-from-previous-step>` sayfasına göz atın. *myVmWeb* sanal makinesine bağlı ağ arabiriminin içinde bulunduğu *myAsgWebServers* uygulama güvenlik grubuna 80 numaralı bağlantı noktasının internetten gelmesine izin verildiği için bağlantı başarılı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir ağ güvenlik grubu oluşturdunuz ve bunu bir sanal ağ alt ağıyla ilişkilendirdiyseniz. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek bkz. [Ağ güvenlik grubuna genel bakış](./network-security-groups-overview.md) ve [Ağ güvenlik grubunu yönetme](manage-network-security-group.md).

Azure, varsayılan olarak trafiği alt ağlar arasında yönlendirir. Bunun yerine, alt ağlar arasındaki trafiği, örneğin, güvenlik duvarı olarak görev yapan bir VM aracılığıyla yönlendirmeyi seçebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [yol tablosu oluşturma](tutorial-create-route-table-powershell.md).