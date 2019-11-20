---
title: Azure 'da PowerShell kullanarak bir Windows sanal makinesine veri diski iliştirme
description: Kaynak Yöneticisi dağıtım modeliyle PowerShell kullanarak yeni veya mevcut bir veri diskini bir Windows VM 'sine bağlama.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033682"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>PowerShell ile bir Windows sanal makinesine veri diski iliştirme

Bu makalede, PowerShell kullanarak yeni ve var olan disklerin her ikisini de bir Windows sanal makinesine nasıl ekleyebileceğiniz gösterilmektedir. 

İlk olarak, bu ipuçlarını gözden geçirin:

* Sanal makinenin boyutu, ekleyebileceğiniz veri disklerinin sayısını denetler. Daha fazla bilgi için [sanal makine boyutları](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Premium SSD 'leri kullanmak için DS serisi veya GS serisi sanal makinesi gibi [Premium depolama özellikli BIR VM türüne](sizes-memory.md)ihtiyacınız vardır.

Bu makale, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içinde PowerShell kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Sanal makineye boş bir veri diski ekleme

Bu örnek, mevcut bir sanal makineye boş bir veri diskinin nasıl ekleneceğini gösterir.

### <a name="using-managed-disks"></a>Yönetilen diskleri kullanma

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Bir kullanılabilirlik bölgesinde yönetilen diskleri kullanma

Bir kullanılabilirlik bölgesinde bir disk oluşturmak için, `-Zone` parametresiyle [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) kullanın. Aşağıdaki örnek, bölge *1*' de bir disk oluşturur.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Diski başlatma

Boş bir disk ekledikten sonra onu başlatmalısınız. Diski başlatmak için bir VM 'de oturum açıp disk yönetimini kullanabilirsiniz. Sanal makineyi oluştururken [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) 'yi ve sertifikayı etkinleştirdiyseniz, uzak PowerShell 'i kullanarak diski başlatabilirsiniz. Özel bir betik uzantısı da kullanabilirsiniz:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Betik dosyası, diskleri başlatmak için kod içerebilir, örneğin:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Mevcut bir veri diskini bir VM 'ye iliştirme

Mevcut bir yönetilen diski bir sanal makineye veri diski olarak ekleyebilirsiniz.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, şablonları kullanarak yönetilen diskler dağıtabilirsiniz. Daha fazla bilgi için, bkz. [Azure Resource Manager şablonlarda yönetilen diskleri kullanma](using-managed-disks-template-deployments.md) veya birden çok veri diski dağıtmak için [hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) .
