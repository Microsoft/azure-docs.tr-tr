---
title: Azure PowerShell kullanarak yönetilen diskler depolamayı farklı disk türleri arasında dönüştürme
description: Azure PowerShell kullanarak Azure yönetilen diskleri farklı diskler türleri arasında dönüştürme.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: 658dfbb654920c0dd90c4b4caaac7a5ca5962c81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607305"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Yönetilen bir diskin depolama türünü güncelleştirme

Azure tarafından yönetilen disklerin dört disk türü vardır: Azure Ultra diskler, Premium SSD, standart SSD ve standart HDD. Premium SSD, standart SSD ve standart HDD arasında performans gereksinimlerinize göre geçiş yapabilirsiniz. Ya da bir ultra diske geçiş yapamazsınız, yeni bir tane dağıtmanız gerekir.

Bu işlev, yönetilmeyen diskler için desteklenmez. Ancak, disk türleri arasında geçiş yapabilmesi için, [yönetilmeyen bir diski yönetilen bir diske kolayca dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md) .



## <a name="before-you-begin"></a>Başlamadan önce

* Dönüştürme, sanal makinenin (VM) yeniden başlatılmasını gerektirdiğinden, önceden var olan bir bakım penceresi sırasında disk depolama alanınızı geçirmeyi zamanlamanız gerekir.
* Diskiniz yönetilmiyorsa, depolama seçenekleri arasında geçiş yapabilmeniz [için önce onu bir yönetilen diske dönüştürün](convert-unmanaged-to-managed-disks.md) .

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Bir VM 'nin tüm yönetilen disklerini bir hesaptan diğerine değiştirme

Bu örnek, bir sanal makinenin disklerinin tümünün Premium depolamaya nasıl dönüştürüleceğini gösterir. Ancak, bu örnekteki $storageType değişkenini değiştirerek, sanal makinenin disk türünü standart SSD veya standart HDD 'e dönüştürebilirsiniz. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](../sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standart ve Premium arasında yönetilen diskleri tek tek değiştirme

Geliştirme ve test iş yükünüz için, maliyetlerinizi azaltmak üzere standart ve Premium disklerin bir karışımını istemeniz gerekebilir. Yalnızca daha iyi performansa ihtiyacı olan diskleri yükseltmeyi tercih edebilirsiniz. Bu örnekte, tek bir VM diskinin standart 'dan Premium depolamaya nasıl dönüştürüleceği gösterilmektedir. Ancak, bu örnekteki $storageType değişkenini değiştirerek, sanal makinenin disk türünü standart SSD veya standart HDD 'e dönüştürebilirsiniz. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](../sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçme şeklini gösterir:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Yönetilen diskleri bir disk türünden diğerine değiştirme

Şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Sanal makineler** listesinden VM 'yi seçin.
3. VM durdurulmamışsa, VM **'ye genel bakış** bölmesinin üst kısmında **Durdur** ' u seçin ve VM 'nin durdurulmasını bekleyin.
4. VM 'nin bölmesinde, menüden **diskler** ' i seçin.
5. Dönüştürmek istediğiniz diski seçin.
6. Menüden **yapılandırma** ' yı seçin.
7. **Hesap türünü** özgün disk türünden istenen disk türüne değiştirin.
8. **Kaydet**' i seçin ve disk bölmesini kapatın.

Disk türü dönüştürmesi anında gerçekleşir. Dönüştürmeden sonra sanal makinenizin başlamasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Anlık görüntü](snapshot-copy-managed-disk.md)kullanarak VM 'nin salt okunurdur bir kopyasını oluşturun.
