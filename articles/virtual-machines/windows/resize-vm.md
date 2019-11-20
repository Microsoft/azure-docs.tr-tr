---
title: Azure 'da bir Windows sanal makinesini yeniden boyutlandırmak için PowerShell 'i kullanma
description: Azure PowerShell kullanarak Kaynak Yöneticisi dağıtım modelinde oluşturulan bir Windows sanal makinesini yeniden boyutlandırın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 4b30f2fd8e095b00898e083e33c23c7c9a915b99
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073361"
---
# <a name="resize-a-windows-vm"></a>Windows VM 'yi yeniden boyutlandırma

Bu makalede, Azure PowerShell kullanarak bir VM 'yi farklı bir [VM boyutuna](sizes.md) nasıl taşıyacağınız gösterilmektedir.

Bir sanal makine (VM) oluşturduktan sonra VM boyutunu değiştirerek VM 'yi yukarı veya aşağı ölçeklendirebilirsiniz. Bazı durumlarda, önce VM 'yi serbest bırakın. Yeni boyut, şu anda VM 'yi barındıran donanım kümesinde yoksa bu durum oluşabilir.

VM 'niz Premium Depolama kullanıyorsa, Premium Depolama desteğini almak için boyutun bir **s** sürümünü seçtiğinizden emin olun. Örneğin, Standard_E4_v3 yerine Standard_E4**s**_v3 seçin.

 

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Bir kullanılabilirlik kümesinde değil Windows VM 'yi yeniden boyutlandırma

Bazı değişkenleri ayarlayın. Değerleri kendi bilgileriniz ile değiştirin.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM 'nin barındırıldığı donanım kümesinde kullanılabilir olan VM boyutlarını listeleyin. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

İstediğiniz boyut listeleniyorsa, sanal makineyi yeniden boyutlandırmak için aşağıdaki komutları çalıştırın. İstenen boyut listelenmemişse adım 3 ' e gidin.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

İstediğiniz boyut listelenmemişse, VM 'yi serbest bırakmak, yeniden boyutlandırmak ve VM 'yi yeniden başlatmak için aşağıdaki komutları çalıştırın. **\<newVMsize >** istediğiniz boyutla değiştirin.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> VM 'nin ayırmayı kaldırma, sanal makineye atanan tüm dinamik IP adreslerini yayınlar. İşletim sistemi ve veri diskleri etkilenmez. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki Windows VM 'yi yeniden boyutlandırma

Bir kullanılabilirlik kümesindeki bir VM için yeni boyut, şu anda VM 'yi barındıran donanım kümesinde yoksa, VM 'nin yeniden boyutlandırılması için kullanılabilirlik kümesindeki tüm VM 'Lerin serbest bırakılmasıyla sonuçlanır. Ayrıca, bir VM yeniden boyutlandırılırken kullanılabilirlik kümesindeki diğer VM 'lerin boyutunu güncelleştirmeniz gerekebilir. Bir kullanılabilirlik kümesindeki bir VM 'yi yeniden boyutlandırmak için aşağıdaki adımları gerçekleştirin.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM 'nin barındırıldığı donanım kümesinde kullanılabilir olan VM boyutlarını listeleyin. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

İstenen boyut listeleniyorsa, sanal makineyi yeniden boyutlandırmak için aşağıdaki komutları çalıştırın. Listede yoksa bir sonraki bölüme gidin.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
İstediğiniz boyut listelenmemişse, kullanılabilirlik kümesindeki tüm VM 'Leri serbest bırakmak, VM 'Leri yeniden boyutlandırmak ve yeniden başlatmak için aşağıdaki adımlarla devam edin.

Kullanılabilirlik kümesindeki tüm VM 'Leri durdurun.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Kullanılabilirlik kümesindeki VM 'Leri yeniden boyutlandırın ve yeniden başlatın.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Sonraki adımlar

Ek ölçeklenebilirlik için birden fazla sanal makine örneği çalıştırın ve ölçeği ölçeklendirin. Daha fazla bilgi için bkz. [sanal makine ölçek kümesindeki Windows makinelerini otomatik olarak ölçeklendirme](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

