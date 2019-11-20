---
title: PowerShell ile bir Azure VM için işletim sistemi diskini değiştirme
description: PowerShell kullanarak bir Azure sanal makinesi tarafından kullanılan işletim sistemi diskini değiştirme.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ec66892804f3c2d1f831168a2955f2498462cbf3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033021"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>PowerShell kullanarak bir Azure VM tarafından kullanılan işletim sistemi diskini değiştirme

Var olan bir VM varsa, ancak diski bir yedekleme diski veya başka bir işletim sistemi diski için değiştirmek istiyorsanız, işletim sistemi disklerini değiştirmek için Azure PowerShell kullanabilirsiniz. VM 'yi silip yeniden oluşturmanız gerekmez. Zaten kullanımda olmadığı sürece, başka bir kaynak grubunda yönetilen bir disk de kullanabilirsiniz.

 

VM 'nin stopped\serbest bırakılmış olması gerekir, ardından yönetilen diskin kaynak KIMLIĞI, farklı bir yönetilen diskin kaynak KIMLIĞI ile değiştirilebilir.

VM boyutunun ve depolama türünün iliştirmek istediğiniz diskle uyumlu olduğundan emin olun. Örneğin, kullanmak istediğiniz disk Premium depolamada ise, VM 'nin Premium depolama alanı (DS serisi boyutu gibi) olması gerekir. Her iki diskin de aynı boyutta olması gerekir.

[Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) kullanarak bir kaynak grubundaki disklerin listesini alın

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Kullanmak istediğiniz diskin adına sahip olduğunuzda, sanal makine için işletim sistemi diski olarak ayarlayın. Bu örnek, *Myvm* adlı VM 'yi kaldırır ve yeni işletim sistemi diski olarak *newdisk* adlı diski atar. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Sonraki adımlar**

Bir diskin kopyasını oluşturmak için bkz. [disk anlık görüntüsü](snapshot-copy-managed-disk.md).
