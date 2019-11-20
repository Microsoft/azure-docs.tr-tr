---
title: Sanal ağ eşlemesi ile sanal ağları bağlama-PowerShell | Microsoft Docs
description: Bu makalede, Azure PowerShell kullanarak sanal ağ eşlemesi ile sanal ağları bağlamayı öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 63584eedb0e51ddbca0d644bc17db3facd9225b5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164030"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>PowerShell kullanarak sanal ağ eşlemesi ile sanal ağları bağlama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sanal ağ eşlemesi ile sanal ağları birbirine bağlayabilirsiniz. Sanal ağlar eşlendikten sonra, kaynaklar aynı sanal ağ üzerindeymiş gibi, aynı gecikme süresi ve bant genişliği ile her iki sanal ağdaki kaynaklar birbiriyle iletişim kurabilir. Bu makalede şunları öğreneceksiniz:

* İki sanal ağ oluşturma
* Sanal ağ eşlemesi iki sanal ağı bağlama
* Her sanal ağa sanal makine (VM) dağıtma
* Sanal makineler arasında iletişim

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-virtual-networks"></a>Sanal ağlar oluşturma

Bir sanal ağ oluşturmadan önce, sanal ağ ve bu makalede oluşturulan tüm diğer kaynaklar için bir kaynak grubu oluşturmanız gerekir. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/16*adres ön ekine sahip *myVirtualNetwork1* adlı bir sanal ağ oluşturur.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)ile bir alt ağ yapılandırması oluşturun. Aşağıdaki örnek, 10.0.0.0/24 adres ön eki ile bir alt ağ yapılandırması oluşturur:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Alt ağ yapılandırmasını, alt ağı oluşturan [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)ile sanal ağa yazın:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

10.1.0.0/16 adres ön ekine ve bir alt ağa sahip bir sanal ağ oluşturun:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Sanal ağları eşleme

[Add-Azvirtualnetworkeşlemesiyle](/powershell/module/az.network/add-azvirtualnetworkpeering)bir eşleme oluşturun. Aşağıdaki örnek, *myVirtualNetwork1* ile *myVirtualNetwork2*eşler.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Önceki komut yürütüldükten sonra döndürülen çıktıda, **Peeringstate** öğesinin *başlatıldığını*görürsünüz. Eşleme, *myVirtualNetwork2* ile *myVirtualNetwork1*arasında eşleme oluşturulana kadar *başlatılmış* durumda kalır. *MyVirtualNetwork2* ile *myVirtualNetwork1*arasında bir eşleme oluşturun.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Önceki komut yürütüldükten sonra döndürülen çıktıda, **Peeringstate** ' in *bağlı*olduğunu görürsünüz. Azure ayrıca *myVirtualNetwork1-myVirtualNetwork2* eşlemesinin eşleme durumunu *bağlı*olarak değiştirdi. *MyVirtualNetwork1-myVirtualNetwork2* eşlemesinin eşleme durumunun [Get-Azvirtualnetworkeşlemesiyle](/powershell/module/az.network/get-azvirtualnetworkpeering) *bağlantılı* olarak değiştirildiğini doğrulayın.

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Bir sanal ağdaki kaynaklar, her iki sanal ağ içindeki eşler için **Peeringstate** *bağlı*olana kadar diğer sanal ağdaki kaynaklarla iletişim kuramaz.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sonraki bir adımda aralarında iletişim kurabilmeniz için her sanal ağ üzerinde bir sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

[New-azvm](/powershell/module/az.compute/new-azvm)Ile bir VM oluşturun. Aşağıdaki örnek, *myVirtualNetwork1* sanal ağında *MYVM1* adlı bir VM oluşturur. `-AsJob` seçeneği, sanal makineyi arka planda oluşturur, böylece sonraki adıma devam edebilirsiniz. İstendiğinde, VM 'de oturum açmak istediğiniz kullanıcı adını ve parolayı girin.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Sanal makinenin oluşturulması birkaç dakika sürer. Azure VM 'yi oluşturup çıktıyı PowerShell 'e döndürünceye kadar sonraki adımlara devam edin.

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

Bir VM 'nin genel IP adresine İnternet 'ten bağlanabilirsiniz. Bir sanal makinenin genel IP adresini döndürmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanın. Aşağıdaki örnek,*myVm1* sanal makinesinin genel IP adresini döndürür:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Yerel bilgisayarınızdan *myVm1* VM ile bir Uzak Masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. `<publicIpAddress>` değerini önceki komutta döndürülen IP adresi ile değiştirin.

```
mstsc /v:<publicIpAddress>
```

Bir Uzak Masaüstü Protokolü (. rdp) dosyası oluşturulur, bilgisayarınıza indirilir ve açılır. Kullanıcı adını ve parolayı girin ( **daha fazla seçenek**belirlemeniz, sonra **farklı bir hesap kullanmanız**, VM oluştururken girdiğiniz kimlik bilgilerini belirtmek Için) ve ardından **Tamam**' a tıklayın. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet** veya **Devam**’a tıklayın.

*MyVm1* VM 'de, Windows Güvenlik Duvarı üzerinden Internet Denetim Iletisi Protokolü 'NÜ (ICMP) etkinleştirin. böylece, PowerShell kullanarak bu VM 'yi daha sonraki bir adımda *myVm2* 'den ping hale getirebilirsiniz:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Bu makaledeki VM 'Ler arasında iletişim kurmak için PING kullanılmasına karşın, üretim dağıtımları için Windows Güvenlik Duvarı üzerinden ıCMP 'ye izin önerilmez.

*myVm2* sanal makinesine bağlanmak için, *myVm1* sanal makinesinde bir komut isteminden aşağıdaki komutu girin:

```
mstsc /v:10.1.0.4
```

*MyVm1*üzerinde ping 'yı etkinleştirdiğiniz Için artık *myVm2* VM 'DEKI bir komut isteminden IP adresine göre ping işlemi yapabilirsiniz:

```
ping 10.0.0.4
```

Dört yanıt alırsınız. Hem *myVm1* hem de *myVm2* sanal makinesine yönelik RDP oturumlarınızın bağlantısını kesin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sanal ağ eşlemesi ile aynı Azure bölgesindeki iki ağı bağlamayı öğrendiniz. Farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region) ve [farklı Azure aboneliklerinde](create-peering-different-subscriptions.md#powershell) sanal ağları eşleyebilir ve eşleme ile [hub ve bağlı bileşen ağ tasarımları](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) oluşturabilirsiniz. Sanal ağ eşlemesi hakkında daha fazla bilgi için bkz. [Sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md) ve [Sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md).

Bir VPN aracılığıyla [kendi bilgisayarınızı bir sanal ağa bağlayabilirsiniz](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve bir sanal ağdaki kaynaklarla veya eşlenmiş sanal ağlarda etkileşim kurabilirsiniz. Sanal ağ makalelerinde kapsanan birçok görevi tamamlaması için yeniden kullanılabilir betikler için bkz. [betik örnekleri](powershell-samples.md).