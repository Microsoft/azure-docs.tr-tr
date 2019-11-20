---
title: Sanal ağ oluşturma-hızlı başlangıç-Azure PowerShell
titlesuffix: Azure Virtual Network
description: Bu hızlı başlangıçta, Azure portalını kullanarak sanal ağ oluşturmayı öğreneceksiniz. Sanal ağ, sanal makineler gibi Azure kaynaklarının birbiriyle ve internet ile özel olarak iletişim kurmasına olanak tanır.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 1d30b35264707c59c899cc3a224e4affa2a4696e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161531"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak sanal ağ oluşturma

Sanal ağ, sanal makineler (VM) gibi Azure kaynaklarının birbirleriyle ve internet ile özel olarak iletişim kurmasına olanak tanır. Bu hızlı başlangıçta, sanal ağ oluşturmayı öğreneceksiniz. Bir sanal ağ oluşturduktan sonra, sanal ağa iki sanal makine dağıtacaksınız. Daha sonra internet 'ten VM 'lere bağlanır ve sanal ağ üzerinden özel olarak iletişim kurabilirsiniz.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bunun yerine PowerShell 'i yerel olarak yükleyip kullanmaya karar verirseniz, bu hızlı başlangıç, Azure PowerShell Module sürümü 1.0.0 veya üzerini kullanmanızı gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Bkz. Install and Upgrade Info için [Azure PowerShell Module](/powershell/azure/install-az-ps) .

Son olarak, PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount`çalıştırmanız da gerekir. Bu komut Azure ile bir bağlantı oluşturur.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Kaynak grubu ve sanal ağ oluşturma

Kaynak grubunuzu ve Sanal ağınızı yapılandırmak için yapmanız gereken birkaç adım vardır.

### <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Bir sanal ağ oluşturabilmeniz için önce sanal ağı barındırmak üzere bir kaynak grubu oluşturmanız gerekir. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir kaynak grubu oluşturun. Bu örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun. Bu örnek, *EastUS* konumunda *myVirtualNetwork* adlı varsayılan bir sanal ağ oluşturur:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alt ağ ekleme

Azure, kaynakları bir sanal ağ içindeki bir alt ağa dağıtır, bu nedenle bir alt ağ oluşturmanız gerekir. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)ile *Default* adlı bir alt ağ yapılandırması oluşturun:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Alt ağı sanal ağla ilişkilendir

Alt ağ yapılandırmasını [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)ile sanal ağa yazabilirsiniz. Bu komut alt ağ oluşturur:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sanal ağ üzerinde iki sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

[New-AzVM](/powershell/module/az.compute/new-azvm)Ile ilk VM 'yi oluşturun. Sonraki komutu çalıştırdığınızda kimlik bilgileri istenir. VM için bir Kullanıcı adı ve parola girin:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

`-AsJob` seçeneği, sanal makineyi arka planda oluşturur. Sonraki adıma devam edebilirsiniz.

Azure, arka planda VM oluşturmaya başladığında, bu geri doğru bir şey alacaksınız:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

Şu komutla ikinci VM 'yi oluşturun:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Başka bir Kullanıcı ve parola oluşturmanız gerekecektir. Azure, sanal makinenin oluşturulması birkaç dakika sürer.

> [!IMPORTANT]
> Azure bitene kadar bir sonraki adımla devam etmeyin.  Bu işlem, PowerShell 'e çıkış döndürdüğünde yapıldığını anlarsınız.

## <a name="connect-to-a-vm-from-the-internet"></a>İnternet'ten bir sanal makineye bağlanma

Bir sanal makinenin genel IP adresini döndürmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanın. Bu örnek *myVm1* VM 'nın genel IP adresini döndürür:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Yerel bilgisayarınızda bir komut istemi açın. `mstsc` komutunu çalıştırın. `<publicIpAddress>`, son adımdan döndürülen genel IP adresiyle değiştirin:

> [!NOTE]
> Bu komutları yerel bilgisayarınızdaki bir PowerShell isteminden çalıştırıyorsanız ve az PowerShell modülü 1,0 veya sonraki bir sürümü kullanıyorsanız, bu arabirime devam edebilirsiniz.

```cmd
mstsc /v:<publicIpAddress>
```
1. İstendiğinde **Bağlan**’ı seçin.

1. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

    > [!NOTE]
    > VM oluştururken girdiğiniz kimlik bilgilerini belirtmek için **farklı bir hesap kullanmak** > **daha fazla seçenek** belirlemeniz gerekebilir.

1. **Tamam**’ı seçin.

1. Bir sertifika uyarısı alabilirsiniz. Bunu yaparsanız **Evet** ' i veya **devam et**' i seçin.

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

1. *MyVm1*uzak masaüstünde PowerShell ' i açın.

1. `ping myVm2` yazın.

    Aşağıdakine benzer bir şey alacaksınız:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Internet Denetim Iletisi Protokolü 'Nü (ıCMP) kullandığından, ping işlemi başarısız olur. Varsayılan olarak, Windows Güvenlik duvarınız üzerinden ıCMP 'ye izin verilmez.

1. *MyVm2* 'in daha sonraki bir *adımda ping yapmasına* izin vermek için şu komutu girin:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Bu komut, Windows Güvenlik Duvarı üzerinden gelen ıCMP 'ye izin verir.

1. *myVm1* ile uzak masaüstü bağlantısını kapatın.

1. [İnternet 'ten BIR sanal makineye bağlanma](#connect-to-a-vm-from-the-internet)bölümündeki adımları yineleyin. Bu kez, *myVm2*'e bağlanın.

1. *myVm2* sanal makinesindeki bir komut isteminden `ping myvm1` komutunu girin.

    Aşağıdakine benzer bir şey alacaksınız:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Önceki bir adımda *myVm1* sanal makinesinde Windows güvenlik duvarı üzerinden ICMP’ye izin verdiğinizden, *myVm1*’den yanıt alırsınız.

1. *myVm2* ile uzak masaüstü bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sanal ağ ve VM 'Ler ile işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, varsayılan bir sanal ağ ve iki sanal makine oluşturdunuz. İnternet’ten bir sanal makineye bağlandınız ve sanal makine ile başka bir sanal makine arasında özel olarak iletişim kurdunuz. Sanal ağ ayarları hakkında daha fazla bilgi için [Sanal ağı yönetme](manage-virtual-network.md) başlıklı konuya bakın.

Azure, sanal makineler arasında sınırsız özel iletişime izin verir. Varsayılan olarak, Azure yalnızca İnternet 'ten Windows VM 'lerine gelen Uzak Masaüstü bağlantılarına izin verir. Farklı türlerde VM ağı iletişimleri yapılandırma hakkında daha fazla bilgi edinmek için [ağ trafiğini filtreleme](tutorial-filter-network-traffic.md) öğreticisine gidin.
