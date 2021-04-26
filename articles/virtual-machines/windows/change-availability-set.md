---
title: Azure PowerShell kullanarak VM kullanılabilirlik kümesini değiştirme
description: Azure PowerShell kullanarak sanal makineniz için kullanılabilirlik kümesini değiştirmeyi öğrenin.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: 99985d0bb2294c538efa712e477cc6f8a2eb4938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102498481"
---
# <a name="change-the-availability-set-for-a-vm-using-azure-powershell"></a>Azure PowerShell kullanarak bir VM 'nin kullanılabilirlik kümesini değiştirme    
Aşağıdaki adımlarda, Azure PowerShell kullanarak bir VM 'nin kullanılabilirlik kümesinin nasıl değiştirileceği açıklanır. VM, bir kullanılabilirlik kümesine yalnızca oluşturulduğunda eklenebilir. Kullanılabilirlik kümesini değiştirmek için sanal makineyi silip yeniden oluşturmanız gerekir. 

Bu makale hem Linux hem de Windows VM 'Leri için geçerlidir.

Bu makale, [Azure Cloud Shell](https://shell.azure.com/powershell) ve [az PowerShell Module](/powershell/azure/install-az-ps) sürümü 1.2.0 kullanılarak 2/12/2019 ' de son test edilmiştir.

Bu örnek, VM 'nin bir yük dengeleyiciye bağlı olup olmadığını kontrol etmez. VM 'niz bir yük dengeleyiciye eklenmişse, bu durumu işlemek için betiği güncelleştirmeniz gerekir. 


## <a name="change-the-availability-set"></a>Kullanılabilirlik kümesini değiştirme 

Aşağıdaki betik, gerekli bilgileri toplama, özgün VM 'yi silme ve sonra yeni bir kullanılabilirlik kümesinde yeniden oluşturma örneği sağlar.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Sonraki adımlar

Ek bir [veri diski](attach-managed-disk-portal.md)ekleyerek sanal makinenize ek depolama alanı ekleyin.
