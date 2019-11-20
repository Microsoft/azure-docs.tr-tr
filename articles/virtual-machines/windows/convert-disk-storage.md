---
title: Azure yönetilen diskler depolamasını standart 'ten Premium veya Premium 'a standart olarak dönüştürme
description: Azure PowerShell kullanarak, Azure yönetilen disklerini standartdan Premium veya Premium 'a dönüştürme.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b63d4d0247fd640e961d03d336d6b211473c48fb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033918"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Yönetilen bir diskin depolama türünü güncelleştirme

Azure yönetilen disklerinin dört disk türü vardır: Azure Ultra SSDs (Önizleme), Premium SSD, standart SSD ve standart HDD. Performans gereksinimlerinize göre üç GA disk türü (Premium SSD, standart SSD ve standart HDD) arasında geçiş yapabilirsiniz. Ya da bir ultra SSD 'ye geçiş yapamazsınız, yeni bir tane dağıtmanız gerekir.

Bu işlev, yönetilmeyen diskler için desteklenmez. Ancak, disk türleri arasında geçiş yapabilmesi için, [yönetilmeyen bir diski yönetilen bir diske kolayca dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md) .

 

## <a name="prerequisites"></a>Önkoşullar

* Dönüştürme, sanal makinenin (VM) yeniden başlatılmasını gerektirdiğinden, önceden var olan bir bakım penceresi sırasında disk depolama alanınızı geçirmeyi zamanlamanız gerekir.
* Diskiniz yönetilmiyorsa, depolama seçenekleri arasında geçiş yapabilmeniz [için önce onu bir yönetilen diske dönüştürün](convert-unmanaged-to-managed-disks.md) .

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Bir VM 'nin tüm yönetilen disklerini Premium ve standart arasında değiştirme

Bu örnek, bir VM 'nin tüm disklerinin standart olan disklerini Premium depolamaya veya Premium 'dan standart depolamaya nasıl dönüştüreceğiniz gösterilmektedir. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
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
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standart ve Premium arasında yönetilen diskleri tek tek değiştirme

Geliştirme ve test iş yükünüz için, maliyetlerinizi azaltmak üzere standart ve Premium disklerin bir karışımını istemeniz gerekebilir. Yalnızca daha iyi performansa ihtiyacı olan diskleri yükseltmeyi tercih edebilirsiniz. Bu örnek, tek bir VM diskinin standart 'dan Premium depolamaya veya Premium 'dan standart depolamaya nasıl dönüştürüleceğini gösterir. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçme şeklini gösterir:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
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
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Yönetilen diskleri standart konumundan Premium 'a dönüştürme Azure portal

Şu adımları uygulayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portaldaki **sanal makineler** listesinden VM 'yi seçin.
3. VM durdurulmamışsa, VM **'ye genel bakış** bölmesinin üst kısmında **Durdur** ' u seçin ve VM 'nin durdurulmasını bekleyin.
3. VM 'nin bölmesinde, menüden **diskler** ' i seçin.
4. Dönüştürmek istediğiniz diski seçin.
5. Menüden **yapılandırma** ' yı seçin.
6. **Standart HDD** **hesap türünü** **Premium SSD**olarak değiştirin.
7. **Kaydet**' e tıklayın ve disk bölmesini kapatın.

Disk türü dönüştürmesi anında gerçekleşir. Dönüştürmeden sonra sanal makinenizin yeniden başlatılmasını sağlayabilirsiniz.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Standart HDD ile Standart SSD arasında yönetilen diskleri değiştirme 

Bu örnek, tek bir VM diskinin Standart HDD Standart SSD veya Standart SSD Standart HDD 'e nasıl dönüştürüleceğini gösterir:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Sonraki adımlar

[Anlık görüntü](snapshot-copy-managed-disk.md)kullanarak VM 'nin salt okunurdur bir kopyasını oluşturun.
