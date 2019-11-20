---
title: Ağ trafiğini yönlendirme Azure PowerShell | Microsoft Docs
description: Bu makalede, PowerShell kullanarak bir rota tablosu ile ağ trafiğini yönlendirmeyi öğrenin.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 986371e479f7718fff2e1699401987cb0ca8f623
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163997"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>PowerShell kullanarak bir rota tablosu ile ağ trafiğini yönlendirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure varsayılan olarak bir sanal ağ içindeki tüm alt ağlar arasında gerçekleşen trafiği otomatik olarak yönlendirir. Azure’ın varsayılan yönlendirmesini geçersiz kılmak için kendi yönlendirmelerinizi oluşturabilirsiniz. Örneğin, bir ağ sanal gereci üzerinden alt ağlar arasındaki trafiği yönlendirmek isteyebilirsiniz. Bu makalede şunları öğreneceksiniz:

* Yönlendirme tablosu oluşturma
* Yönlendirme oluşturma
* Birden fazla alt ağa sahip bir sanal ağ oluşturma
* Yönlendirme tablosunu bir alt ağ ile ilişkilendirme
* Trafiği yönlendiren bir NVA oluşturma
* Sanal makineleri (VM) farklı alt ağlara dağıtma
* NVA aracılığıyla trafiği bir alt ağdan başka birine yönlendirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makale Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

Bir rota tablosu oluşturabilmeniz için, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek, bu makalede oluşturulan tüm kaynaklar için *Myresourcegroup* adlı bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzRouteTable](/powershell/module/az.network/new-azroutetable)ile bir rota tablosu oluşturun. Aşağıdaki örnek *Myroutetablepublic*adlı bir yol tablosu oluşturur.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Yönlendirme oluşturma

[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)ile rota tablosu nesnesini alarak rota oluşturun, [Add-azrouteconfig](/powershell/module/az.network/add-azrouteconfig)ile bir rota oluşturun ve ardından Route yapılandırmasını [set-azroutetable](/powershell/module/az.network/set-azroutetable)ile rota tablosuna yazın.

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

Bir rota tablosunu bir alt ağ ile ilişkilendirebilmeniz için önce bir sanal ağ ve alt ağ oluşturmanız gerekir. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/16*adres ön ekine sahip *myVirtualNetwork* adlı bir sanal ağ oluşturur.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)ile üç alt ağ yapılandırması oluşturarak üç alt ağ oluşturun. Aşağıdaki örnek, *genel*, *özel*ve *DMZ* alt ağları için üç alt ağ yapılandırması oluşturur:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Sanal ağda alt ağları oluşturan [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)ile sanal ağa alt ağ yapılandırması yazın:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

*Myroutetablepublic* yol tablosunu [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) ile *ortak* alt ağla Ilişkilendirin ve sonra alt ağ yapılandırmasını [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)ile sanal ağa yazın.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>NVA oluşturma

NVA; yönlendirme, güvenlik duvarı oluşturma veya WAN iyileştirmesi gibi ağ işlevlerini gerçekleştiren bir VM'dir.

VM oluşturmadan önce bir ağ arabirimi oluşturun.

### <a name="create-a-network-interface"></a>Ağ arabirimi oluşturma

Bir ağ arabirimi oluşturmadan önce, Get [-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)ile sanal ağ kimliğini, sonra [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)ile alt ağ kimliğini almanız gerekir. *DMZ* alt ağında IP iletimi etkin olan [New-aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) ile bir ağ arabirimi oluşturun:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>VM oluşturma

Bir VM oluşturmak ve buna var olan bir ağ arabirimi eklemek için, önce [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)Ile bir VM yapılandırması oluşturmanız gerekir. Yapılandırma, önceki adımda oluşturulan ağ arabirimini içerir. Bir Kullanıcı adı ve parola istendiğinde, VM 'de oturum açmak istediğiniz kullanıcı adını ve parolayı seçin.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

VM yapılandırmasını [New-AzVM](/powershell/module/az.compute/new-azvm)ile kullanarak VM 'yi oluşturun. Aşağıdaki örnek, *Myvmnva*ADLı bir VM oluşturur.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` seçeneği, sanal makineyi arka planda oluşturur, böylece sonraki adıma devam edebilirsiniz.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

*Genel* alt ağdan gelen trafiğin, sonraki bir adımda ağ sanal gereci aracılığıyla *özel* alt ağa yönlendirildiğini doğrulayabilmeniz Için sanal ağda iki VM oluşturun.

[New-AzVM](/powershell/module/az.compute/new-azvm)ile *ortak* alt ağda bir VM oluşturun. Aşağıdaki örnek, *myVirtualNetwork* sanal ağının *genel* alt AĞıNDA *myVmPublic* adlı bir VM oluşturur.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

*Özel* alt ağda bir VM oluşturun.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturuluncaya ve Azure çıktıyı PowerShell 'e döndürünceye kadar sonraki adıma devam etmeyin.

## <a name="route-traffic-through-an-nva"></a>Trafiği NVA üzerinden yönlendirme

*MyVmPrivate* VM 'nın genel IP adresini döndürmek için [Get-azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanın. Aşağıdaki örnek *myVmPrivate* VM 'nın genel IP adresini döndürür:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Yerel bilgisayarınızdan *myVmPrivate* VM ile bir Uzak Masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. `<publicIpAddress>` değerini önceki komutta döndürülen IP adresi ile değiştirin.

```
mstsc /v:<publicIpAddress>
```

İndirilen RDP dosyasını açın. İstendiğinde **Bağlan**’ı seçin.

Sanal makineyi oluştururken belirttiğiniz kullanıcı adını ve parolayı girin (sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan**’ı seçmeniz gerekebilir), ardından **Tamam**’ı seçin. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet**’i seçin.

Sonraki bir adımda, yönlendirmeyi test etmek için `tracert.exe` komutu kullanılır. Tracert, Windows Güvenlik Duvarı üzerinden reddedilen Internet Denetim Iletisi Protokolü 'Nü (ıCMP) kullanır. PowerShell’den *myVmPrivate* sanal makinesinde aşağıdaki komutu girerek Windows güvenlik duvarı üzerinden ICMP’yi etkinleştirin:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Bu makalede yönlendirmeyi test etmek için izleme yolu kullanılıyor olsa da, üretim dağıtımları için Windows Güvenlik Duvarı üzerinden ıCMP 'ye izin önerilmez.

IP iletmeyi etkinleştirme bölümünde VM 'nin ağ arabirimi için Azure 'da IP iletmeyi etkinleştirdiniz. VM içindeki işletim sistemi veya VM içinde çalışan bir uygulama da ağ trafiğini iletebilmelidir. *Myvmnva*'nın işletim SISTEMI içinde IP iletmeyi etkinleştirin.

*MyVmPrivate* VM 'deki bir komut Isteminden, *Myvmnva*'ya Uzak Masaüstü:

``` 
mstsc /v:myvmnva
```

İşletim sistemi içinde IP iletmeyi etkinleştirmek için *myVmNva* sanal makinesinden PowerShell’de aşağıdaki komutu girin:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

*myVmNva* sanal makinesini yeniden başlatarak uzak masaüstü oturumunun bağlantısını da kesin.

*myVmPrivate* VM bağlantısı devam ederken, *myVmNva* VM yeniden başlatıldıktan sonra *myVmPublic* VM ile bir uzak masaüstü oturumu oluşturun:

``` 
mstsc /v:myVmPublic
```

PowerShell’den *myVmPublic* sanal makinesinde aşağıdaki komutu girerek Windows güvenlik duvarı üzerinden ICMP’yi etkinleştirin:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Ağ trafiğinin *myVmPrivate* sanal makinesinden *myVmPublic* sanal makinesine yönlendirilmesini test etmek için *myVmPublic* sanal makinesinde PowerShell’den aşağıdaki komutu girin:

```
tracert myVmPrivate
```

Yanıt aşağıdaki örneğe benzer:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

İlk atlamanın, NVA özel IP adresi olan 10.0.2.4 olduğunu görebilirsiniz. İkinci atlama ise *myVmPrivate* sanal makinesinin özel IP adresi olan 10.0.1.4’tür. *myRouteTablePublic* yönlendirme tablosuna eklenip *Genel* alt ağ ile ilişkilendirilen yönlendirme, Azure’un trafiği doğrudan *Özel* alt ağına yönlendirmek yerine NVA aracılığıyla yönlendirmesine neden olmuştur.

*myVmPublic* VM ile uzak masaüstü oturumunu kapatın. *myVmPrivate* VM bağlantınız hala açıktır.

Ağ trafiğinin *myVmPublic* sanal makinesinden *myVmPrivate* sanal makinesine yönlendirilmesini test etmek için *myVmPrivate* sanal makinesinde bir komut isteminden aşağıdaki komutu girin:

```
tracert myVmPublic
```

Yanıt aşağıdaki örneğe benzer:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Trafiğin *myVmPrivate* sanal makinesinden *myVmPublic* sanal makinesine doğrudan yönlendirildiğini görebilirsiniz. Varsayılan olarak Azure, trafiği doğrudan alt ağlar arasında yönlendirir.

*myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir rota tablosu oluşturdunuz ve bir alt ağ ile ilişkilendirdiyseniz. Trafiği ortak bir alt ağdan özel bir alt ağa yönlendirmekte olan basit bir ağ sanal gereci oluşturdunuz. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)'nden güvenlik DUVARı ve WAN iyileştirmesi gibi ağ işlevleri gerçekleştiren, önceden yapılandırılmış çeşitli ağ sanal gereçlerini dağıtın. Yönlendirme hakkında daha fazla bilgi için bkz. [Yönlendirmeye genel bakış](virtual-networks-udr-overview.md) ve [Yönlendirme tablosunu yönetme](manage-route-table.md).

Bir sanal ağ içinde çok sayıda Azure kaynağına dağıtabilmenize karşın, bazı Azure PaaS hizmetlerinin kaynakları bir sanal ağa dağıtılamaz. Yine de, bazı Azure PaaS hizmetlerinin kaynaklarına erişimi yalnızca bir sanal ağ alt ağından gelecek trafikle kısıtlayabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [PaaS kaynaklarına ağ erişimini kısıtlama](tutorial-restrict-network-access-to-resources-powershell.md).