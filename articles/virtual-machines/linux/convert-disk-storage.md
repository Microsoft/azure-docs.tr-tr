---
title: Azure yönetilen diskler depolamasını standart 'ten Premium veya Premium 'a standart olarak dönüştürme
description: Azure yönetilen diskler depolama alanını standart iken Premium veya Premium 'a Azure CLı kullanarak dönüştürme.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd3c7112a70850d137f77d28e83c60916a96ea3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036631"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure yönetilen diskler depolamasını standart 'ten Premium veya Premium 'a standart olarak dönüştürme

Azure yönetilen disklerinin dört disk türü vardır: Azure Ultra SSDs (Önizleme), Premium SSD, standart SSD ve standart HDD. Performans gereksinimlerinize göre üç GA disk türü (Premium SSD, standart SSD ve standart HDD) arasında geçiş yapabilirsiniz. Ya da bir ultra SSD 'ye geçiş yapamazsınız, yeni bir tane dağıtmanız gerekir.

Bu işlev, yönetilmeyen diskler için desteklenmez. Ancak, disk türleri arasında geçiş yapabilmesi için, [yönetilmeyen bir diski yönetilen bir diske kolayca dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md) .

Bu makalede, Azure CLı kullanılarak yönetilen disklerin standart olan Premium veya Premium 'a nasıl standart bir şekilde dönüştürüleceğini gösterir. Aracı yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Başlamadan önce

* Disk dönüştürme, sanal makinenin (VM) yeniden başlatılmasını gerektirir, bu nedenle önceden var olan bir bakım penceresi sırasında disk depolamanın geçişini zamanlayın.
* Yönetilmeyen diskler için, depolama seçenekleri arasında geçiş yapabilmeniz için önce [yönetilen disklere dönüştürmeniz](convert-unmanaged-to-managed-disks.md) gerekir.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Bir VM 'nin tüm yönetilen disklerini Premium ve standart arasında değiştirme

Bu örnek, bir VM 'nin tüm disklerinin standart olan disklerini Premium depolamaya veya Premium 'dan standart depolamaya nasıl dönüştüreceğiniz gösterilmektedir. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standart ve Premium arasında yönetilen diskleri tek tek değiştirme

Geliştirme ve test iş yükünüz için, maliyetlerinizi azaltmak üzere standart ve Premium disklerin bir karışımını elde etmek isteyebilirsiniz. Yalnızca daha iyi performansa ihtiyacı olan diskleri yükseltmeyi tercih edebilirsiniz. Bu örnek, tek bir VM diskinin standart 'dan Premium depolamaya veya Premium 'dan standart depolamaya nasıl dönüştürüleceğini gösterir. Premium yönetilen diskleri kullanmak için, sanal makinenizin Premium depolamayı destekleyen bir [VM boyutu](sizes.md) kullanması gerekir. Bu örnek ayrıca Premium depolamayı destekleyen bir boyuta geçiş yapar.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Standart HDD ile Standart SSD arasında yönetilen diskleri değiştirme

Bu örnek, tek bir VM diskinin Standart HDD Standart SSD veya Standart SSD Standart HDD 'e nasıl dönüştürüleceğini gösterir.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Azure portal 'de standart ve Premium arasında yönetilen diskleri değiştirme

Şu adımları uygulayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Sanal makineler**listesinden VM 'yi seçin.
3. VM durdurulmamışsa, VM **'ye genel bakış** bölmesinin üst kısmında **Durdur** ' u seçin ve VM 'nin durdurulmasını bekleyin.
4. VM 'nin bölmesinde, menüden **diskler** ' i seçin.
5. Dönüştürmek istediğiniz diski seçin.
6. Menüden **yapılandırma** ' yı seçin.
7. **Hesap türünü** **Standart HDD** **Premium SSD** veya **Premium SSD** 'den **Standart HDD**olarak değiştirin.
8. **Kaydet**' i seçin ve disk bölmesini kapatın.

Disk türü güncelleştirmesi anında gerçekleşir. Dönüştürmeden sonra sanal makinenizin yeniden başlatılmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Anlık görüntüler](snapshot-copy-managed-disk.md)kullanarak VM 'nin salt okunurdur bir kopyasını oluşturun.
