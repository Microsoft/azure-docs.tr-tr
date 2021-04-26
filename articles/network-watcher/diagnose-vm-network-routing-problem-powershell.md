---
title: Bir VM ağ yönlendirme sorununu tanılama-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure ağ Izleyicisi 'nin sonraki atlama özelliğini kullanarak bir sanal makine ağ yönlendirme sorununu tanılamayı öğreneceksiniz.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 3486da98206ee74341d45247b59cf79769105093
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065999"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Bir sanal makine ağ yönlendirme sorununu tanılama-Azure PowerShell

Bu makalede bir sanal makineyi (VM) dağıtırsınız ve ardından bir IP adresi ile URL 'ye iletişimleri kontrol edersiniz. Bir iletişim hatasının nedenini ve bu hatayı nasıl çözeceğinizi belirlersiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale Azure PowerShell `Az` modülünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.



## <a name="create-a-vm"></a>VM oluşturma

Bir sanal makine oluşturabilmeniz için sanal makineyi içerecek bir kaynak grubu oluşturmanız gerekir. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

[New-azvm](/powershell/module/az.compute/new-azvm)ile VM 'yi oluşturun. Bu adımı çalıştırırken kimlik bilgileri istenir. Girdiğiniz değerler, sanal makinenin kullanıcı adı ve parolası olarak yapılandırılır.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Sanal makinenin oluşturulması birkaç dakika sürer. Sanal makine oluşturulup PowerShell çıktı döndürünceye kadar kalan adımlara devam etmeyin.

## <a name="test-network-communication"></a>Ağ iletişimini test etme

Ağ iletişimini ağ Izleyicisi ile test etmek için, önce test etmek istediğiniz VM 'nin bulunduğu bölgede bir ağ izleyicisi etkinleştirmeniz ve ardından iletişim sınaması için ağ Izleyicisi 'nin sonraki atlama özelliğini kullanmanız gerekir.

## <a name="enable-network-watcher"></a>Ağ izleyicisini etkinleştirme

Doğu ABD bölgesinde zaten etkinleştirilmiş bir ağ izleyicisi varsa, Ağ İzleyicisini almak için [Get-Aznetworkizleyici](/powershell/module/az.network/get-aznetworkwatcher) ' yi kullanın. Aşağıdaki örnekte, *NetworkWatcherRG* kaynak grubunda bulunan *NetworkWatcher_eastus* adlı mevcut ağ izleyicisi alınır:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Doğu ABD bölgesinde etkinleştirilmiş bir ağ izleyicisi yoksa, Doğu ABD bölgesinde bir Ağ İzleyicisi oluşturmak için [New-Aznetworkizleyici](/powershell/module/az.network/new-aznetworkwatcher) ' yi kullanın:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Sonraki atlamayı kullanma

Azure, varsayılan hedeflerin yollarını otomatik olarak oluşturur. Varsayılan yolları geçersiz kılmak için özel yollar oluşturabilirsiniz. Bazı durumlarda, özel yollar iletişimin başarısız olmasına neden olabilir. Bir VM 'den yönlendirmeyi test etmek için [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) komutunu kullanarak trafik belirli bir adres için hedeflenen bir sonraki yönlendirme atini tespit edin.

Sanal makineden, www.bing.com adresinin IP adreslerinden birine giden iletişimi test etme:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Birkaç saniye sonra çıktı, **Nexthoptype** 'un **Internet** olduğunu ve **routetableıd** 'in **sistem yolu** olduğunu bildirir. Bu sonuç, hedefe geçerli bir yol olduğunu bilmenizi sağlar.

Sanal makineden 172.31.0.100 adresine giden iletişimi test etme:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Döndürülen çıktı, **hiçbir** bir **nexthoptype** olmadığı ve **Routetableıd** 'in de **sistem rotası** olduğunu size bildirir. Bu sonuç, hedefin geçerli bir sistem yolu olmasına rağmen trafiği hedefe yönlendiren bir sonraki atlama olmadığını size bildirir.

## <a name="view-details-of-a-route"></a>Bir yolun ayrıntılarını görüntüleme

Yönlendirmeyi daha fazla analiz etmek için [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) komutuyla ağ arabirimine yönelik geçerli yolları gözden geçirin:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Aşağıdaki metni içeren çıkış döndürülür:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Önceki çıktıda görebileceğiniz gibi **0.0.0.0/0** ' ın **addresspredüzeltmesini** içeren yol, diğer yönlendirmelerin adres ön eklerinin Içindeki adreslere hiçbir şekilde **Internet**'in bir sonraki atına yol açabilir. Ayrıca çıktıda da görebileceğiniz gibi, 172.31.0.100 adresini içeren 172.16.0.0/12 ön ekine varsayılan bir yol vardır ancak **Nexthoptype** **none** olur. Azure, 172.16.0.0/12 için varsayılan bir yol oluşturur ancak bir neden olmadıkça sonraki atlama türünü belirtmez. Örneğin, 172.16.0.0/12 adres aralığını sanal ağın adres alanına eklediyseniz Azure, **Nexthoptype** 'ı yol için **sanal ağ** olarak değiştirir. Ardından bir denetim, **sanal ağı** **nexthoptype** olarak gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir VM oluşturdunuz ve VM 'den ağ yönlendirmesi tanılandı. Azure’un birkaç varsayılan yol oluşturduğunu öğrendiniz ve iki farklı hedefin yolunu test ettiniz. [Azure'da yönlendirme](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ve [özel yollar oluşturma](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route) hakkında daha fazla bilgi edinin.

Giden VM bağlantıları için, ağ izleyicisinin [bağlantısını sorun giderme](network-watcher-connectivity-powershell.md) ÖZELLIĞINI kullanarak VM ile bir uç nokta arasında gecikme süresi ve reddedilen ağ trafiği de belirleyebilirsiniz. Ağ Izleyicisi Bağlantı İzleyicisi özelliğini kullanarak bir sanal makine ile IP adresi veya URL gibi bir uç nokta arasındaki iletişimi izleyebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [ağ bağlantısını izleme](connection-monitor.md).
